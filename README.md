# GhostShip

This project will configure and provision my homelab services on a Mac Studio.

## Resetting macOS to Factory Defaults

If you need to reset your Mac Studio to a clean state before running this playbook, follow these steps:

1.  **Back up your data:** Ensure all important data is backed up to an external drive or cloud service.
2.  **Sign out of Apple ID/iCloud:** Go to System Settings > [Your Name] > Sign Out.
3.  **Erase your Mac:**
    *   Restart your Mac Studio and immediately press and hold the power button until you see 'Loading startup options'.
    *   Click 'Options', then 'Continue'.
    *   Select 'Disk Utility' from the Utilities window, then click 'Continue'.
    *   In the sidebar, select your startup disk (e.g., 'Macintosh HD').
    *   Click 'Erase' in the toolbar. Choose 'APFS' as the format and 'GUID Partition Map' as the scheme.
    *   Click 'Erase Volume Group' (if applicable) or 'Erase'.
    *   After erasing, quit Disk Utility.
4.  **Reinstall macOS:**
    *   In the Utilities window, select 'Reinstall macOS' and click 'Continue'.
    *   Follow the on-screen instructions to reinstall macOS.

## Pre-configuration Steps for Mac Studio

Before running the Ansible playbook, you need to manually prepare your Mac Studio:

1.  **Create the 'ansible' user:**
    Open Terminal on your Mac Studio and run:
    ```bash
    sudo dscl . -create /Users/ansible
    sudo dscl . -create /Users/ansible UserShell /bin/bash
    sudo dscl . -create /Users/ansible RealName "Ansible User"
    sudo dscl . -create /Users/ansible UniqueID "501" # Or next available ID
    sudo dscl . -create /Users/ansible PrimaryGroupID 20 # Staff group
    sudo dscl . -create /Users/ansible NFSHomeDirectory /Users/ansible
    sudo dscl . -passwd /Users/ansible <set_a_strong_password_here>
    sudo createhomedir -c -u ansible
    sudo dscl . -append /Groups/admin GroupMembership ansible
    ```
    **Note:** Replace `<set_a_strong_password_here>` with a strong password.

2.  **Grant 'ansible' user passwordless sudo access:**
    Open Terminal on your Mac Studio and run:
    ```bash
    sudo sh -c "echo 'ansible ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/ansible"
    sudo chmod 0440 /etc/sudoers.d/ansible
    ```
    This allows the 'ansible' user to run all commands with `sudo` without being prompted for a password.

3.  **Set up SSH key-based authentication for 'ansible' user:**
    On your Mac Studio, as the `ansible` user, create the `.ssh` directory and `authorized_keys` file:
    ```bash
    sudo -u ansible mkdir -p /Users/ansible/.ssh
    sudo -u ansible chmod 700 /Users/ansible/.ssh
    sudo -u ansible touch /Users/ansible/.ssh/authorized_keys
    sudo -u ansible chmod 600 /Users/ansible/.ssh/authorized_keys
    ```
    Then, from your Ansible control machine, copy your public SSH key to the Mac Studio:
    ```bash
    ssh-copy-id -i ~/.ssh/id_ed25519.pub ansible@your_mac_studio_ip
    ```
    Alternatively, manually copy the content of `~/.ssh/id_ed25519.pub` from your control machine and append it to `/Users/ansible/.ssh/authorized_keys` on the Mac Studio.

4.  **Enable Remote Login (SSHD):**
    On your Mac Studio, enable Remote Login to allow SSH connections:
    ```bash
    sudo systemsetup -setremotelogin on
    ```

5.  **Install Ansible Collection:**
    On your Ansible control machine, install the `community.general` collection:
    ```bash
    ansible-galaxy collection install community.general
    ```

6.  **Configure NFS Share (Optional):**
    If you plan to use an NFS share for your media, update the following variables in `ansible/roles/ghostship/defaults/main.yml` (or set them as environment variables):
    *   `nfs_server_ip`: The IP address of your NFS server.
    *   `nfs_remote_path`: The path to the NFS share on your NFS server (e.g., `/mnt/nfs_share`).
    Adjust the path and permissions as necessary for your specific media storage location.

## Ollama and LLM Configuration

This playbook installs Ollama, a lightweight framework for running large language models locally, and pulls a specific uncensored Llama 3 8B model.

*   **Framework:** Ollama
*   **Model:** `taozhiyuai/llama-3-8b-lexi-uncensored` (an uncensored fine-tune of Llama 3 8B)
*   **Quantization:** Q6_K (GGUF format), requiring approximately 10.4GB of RAM.

After the playbook runs, Ollama will be installed and the model will be downloaded. The Ollama service will be configured to run automatically.

To interact with the LLM, you can SSH into your Mac Studio and use the `ollama` command-line interface:

```bash
ollama run taozhiyuai/llama-3-8b-lexi-uncensored
```

This will start an interactive session with the LLM. You can also use Ollama's API for programmatic access.


## Organizr Configuration

Organizr is configured to be the default landing page for your `apps_domain`. After the Ansible playbook runs, you will need to manually configure Organizr through its web interface to add Plex, OpenWebUI, and Homepage as iframes, and to set Homepage as the default landing page within Organizr.

1.  **Access Organizr:**
    Open a web browser and navigate to your `apps_domain` (e.g., `https://yourdomain.com`).

2.  **Initial Setup:**
    Follow the Organizr on-screen instructions for initial setup, including creating an admin user.

3.  **Add Applications as Iframes:**
    Navigate to Organizr's settings and add the following applications as iframes:
    *   **Plex:** `http://{{ plex_host }}:32400/web`
    *   **OpenWebUI:** `http://{{ openwebui_container_name }}:{{ openwebui_port }}`
    *   **Homepage:** `http://{{ homepage_container_name }}:{{ homepage_port }}`

4.  **Set Homepage as Default:**
    In Organizr's settings, configure Homepage to be the default page that loads when you access Organizr.

## Environment Variables

The following environment variables can be used to configure the playbook:

*   `ANSIBLE_HOST`: The IP address or hostname of your Mac Studio. This is used by Ansible to connect to the target machine.
    Example: `export ANSIBLE_HOST=192.168.1.50`

*   `PLEX_DATA_DIR`: The custom directory where Plex Media Server will store its application support data (metadata, plugins, etc.).
    Default: `/Volumes/PlexData/Library/Application Support/Plex Media Server`
    Example: `export PLEX_DATA_DIR="/path/to/your/plex/data"`

*   `NFS_SERVER_IP`: The IP address of your NFS server.
    Default: `192.168.1.100`
    Example: `export NFS_SERVER_IP=192.168.1.10`

*   `NFS_REMOTE_PATH`: The path to the NFS share on your NFS server.
    Default: `/mnt/nfs_share`
    Example: `export NFS_REMOTE_PATH="/export/media"`

*   `NFS_LOCAL_MOUNT_POINT`: The local directory on your Mac Studio where the NFS share will be mounted.
    Default: `/Volumes/NFSShare`
    Example: `export NFS_LOCAL_MOUNT_POINT="/Volumes/MediaShare"`

*   `APPS_USER_NAME`: The username for the low-privileged 'apps' account.
    Default: `apps`
    Example: `export APPS_USER_NAME="myappsuser"`

*   `APPS_USER_UID`: The Unique ID for the 'apps' user.
    Default: `502` (or next available ID)
    Example: `export APPS_USER_UID=503`

*   `APPS_USER_GID`: The Primary Group ID for the 'apps' user.
    Default: `20` (staff group)
    Example: `export APPS_USER_GID=20`


*   `CADDY_IMAGE`: The Docker image for Caddy.
    Default: `caddy:latest`
    Example: `export CADDY_IMAGE="caddy:2.7.6-alpine"`

*   `CADDY_CONTAINER_NAME`: The name for the Caddy Docker container.
    Default: `caddy`
    Example: `export CADDY_CONTAINER_NAME="my-caddy"`

*   `CADDY_EMAIL`: The email address for Let's Encrypt certificate registration.
    Default: `your-email@example.com`
    Example: `export CADDY_EMAIL="my-email@mydomain.com"`

*   `CADDY_CLOUDFLARE_API_TOKEN`: The Cloudflare API token for DNS challenge.
    Default: `YOUR_CLOUDFLARE_API_TOKEN`
    Example: `export CADDY_CLOUDFLARE_API_TOKEN="your_cloudflare_api_token_here"`

*   `APPS_DOMAIN`: The base domain for your services. All other service hostnames will be derived from this.
    Default: `example.com`
    Example: `export APPS_DOMAIN="mydomain.com"`

*   `OPENWEBUI_HOSTNAME`: The hostname for OpenWebUI. This is automatically set to `{{ OPENWEBUI_CONTAINER_NAME }}.{{ APPS_DOMAIN }}`.
    Default: `agent.example.com`
    Example: `export OPENWEBUI_HOSTNAME="agent.mydomain.com"`

*   `OLLAMA_HOSTNAME`: The hostname for Ollama. This is automatically set to `ollama.{{ APPS_DOMAIN }}`.
    Default: `ollama.example.com`
    Example: `export OLLAMA_HOSTNAME="ollama.mydomain.com"`

*   `PLEX_HOSTNAME`: The hostname for Plex. This is automatically set to `plex.{{ APPS_DOMAIN }}`.
    Default: `plex.example.com`
    Example: `export PLEX_HOSTNAME="plex.mydomain.com"`

*   `PLEX_HOST`: The hostname or IP address of the Plex Media Server.
    Default: `localhost`
    Example: `export PLEX_HOST=192.168.1.50`

*   `OPENWEBUI_IMAGE`: The Docker image for OpenWebUI.
    Default: `ghcr.io/open-webui/open-webui:main`
    Example: `export OPENWEBUI_IMAGE="ghcr.io/open-webui/open-webui:latest"`

*   `OPENWEBUI_CONTAINER_NAME`: The name for the OpenWebUI Docker container.
    Default: `openwebui`
    Example: `export OPENWEBUI_CONTAINER_NAME="my-openwebui"`

*   `OPENWEBUI_PORT`: The host port to map to the OpenWebUI container's port 8080.
    Default: `8080`
    Example: `export OPENWEBUI_PORT=8000`

*   `OLLAMA_HOST`: The hostname or IP address of the Ollama server.
    Default: `localhost`
    Example: `export OLLAMA_HOST=192.168.1.50`

## Running the Playbook

This playbook is designed to be idempotent, meaning it can be run multiple times without causing unintended side effects and will only make changes when necessary. It also includes tasks to ensure services like Plex Media Server and Docker Desktop are running after installation or updates.

Once the pre-configuration steps are complete and environment variables are set, you can run the Ansible playbook from your control machine:

```bash
ansible-playbook -i ansible/inventory.ini ansible/playbook.yml
```

## Plex Media Server Configuration

After running the Ansible playbook, Plex Media Server will be installed via the `plex` role and configured to launch automatically. Here are some post-installation steps:

1.  **Access Plex Web App:**
    Open a web browser on a computer connected to the same network as your Mac Studio and navigate to:
    ```
    http://<Mac_Studio_IP_Address>:32400/web
    ```
    Follow the on-screen instructions to sign in, claim your server, and set up your media libraries.

2.  **Custom Data Directory:**
    Plex Media Server's application support data (metadata, plugins, etc.) is configured to use a custom directory, which is set via the `PLEX_DATA_DIR` environment variable (or its default in `ansible/roles/plex/defaults/main.yml`).

3.  **Media File Permissions:**
    Ensure that the `ansible` user (which Plex Media Server will run as) has read and execute permissions for your media directories. For example, if your media is located at the `NFS_LOCAL_MOUNT_POINT`:
    ```bash
    sudo chown -R ansible:staff "${NFS_LOCAL_MOUNT_POINT}"
    sudo chmod -R u+rwX,go+rX,go-w "${NFS_LOCAL_MOUNT_POINT}"
    ```
    Adjust the path and permissions as necessary for your specific media storage location.