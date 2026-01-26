# Ghostship Homelab Setup

This repository contains the Ansible playbooks and configurations for setting up a homelab on a Mac Studio.

## Factory Reset Mac Studio (macOS)

Follow these steps to perform a factory reset on your Mac Studio running macOS, preparing it for a fresh homelab setup. This process also includes essential preparatory steps for installing Asahi Linux.

**Important:** This process will erase all data on your Mac Studio. Ensure you have backed up all important files before proceeding.

1.  **Back up your data:** Before starting, make sure all your important files, documents, and settings are backed up to an external drive, cloud storage (like iCloud, Google Drive, Dropbox), or another secure location.
2.  **Update macOS:** Ensure your macOS installation is updated to the latest available version. This is crucial for the Asahi Linux installer to function correctly.
3.  **Disable FileVault:** If FileVault is enabled, disable it. Go to System Settings (or System Preferences) > Privacy & Security > FileVault, and turn it off. This is a prerequisite for Asahi Linux installation.
4.  **Ensure sufficient free space:** Asahi Linux requires a dedicated partition. Ensure you have at least 30-50GB of free space. You may need to resize your macOS partition using Disk Utility _before_ proceeding with the factory reset if you plan to dual-boot or if your current macOS installation takes up too much space.
5.  **Sign out of Apple services:**
    - **iCloud:** Go to System Settings (or System Preferences) > your Apple ID > iCloud, and sign out.
    - **Messages:** Open the Messages app, go to Messages > Settings (or Preferences) > iMessage, and sign out.
    - **App Store:** Open the App Store, click on your profile picture, and sign out.
6.  **Erase your Mac and Reinstall macOS:**
    - **For Apple Silicon (M1/M2/M3) Macs:**
      1.  Shut down your Mac Studio.
      2.  Press and hold the power button until you see "Loading startup options."
      3.  Click Options, then click Continue.
      4.  Select a user you know the password for, and enter their administrator password.
      5.  In the Recovery app, select Disk Utility and click Continue.
      6.  In the sidebar of Disk Utility, select your internal startup disk (e.g., "Macintosh HD").
      7.  Click the Erase button in the toolbar.
      8.  Enter a name (e.g., "Macintosh HD"), choose APFS as the format, and click Erase.
      9.  After erasing, quit Disk Utility to return to the Recovery app window.
      10. Select "Reinstall macOS" and follow the on-screen instructions. Install a minimal macOS system, as it will primarily serve as a boot environment for Asahi Linux.
    - **For Intel-based Macs:**
      1.  Shut down your Mac Studio.
      2.  Turn on your Mac and immediately press and hold `Command (⌘) + R` until you see the Apple logo or another image.
      3.  If prompted, select a user you know the password for, and enter their administrator password.
      4.  In the Recovery app, select Disk Utility and click Continue.
      5.  In the sidebar of Disk Utility, select your internal startup disk (e.g., "Macintosh HD").
      6.  Click the Erase button in the toolbar.
      7.  Enter a name (e.g., "Macintosh HD"), choose APFS or Mac OS Extended (Journaled) as the format (depending on macOS version), and click Erase.
      8.  After erasing, quit Disk Utility to return to the Recovery app window.
      9.  Select "Reinstall macOS" and follow the on-screen instructions. Install a minimal macOS system, as it will primarily serve as a boot environment for Asahi Linux.

Once macOS is reinstalled, you will have a clean system ready for the homelab setup.

## Install Asahi Linux (Headless)

This section outlines the steps to install Asahi Linux on your Mac Studio in a headless configuration, suitable for a homelab server.

1.  **Boot into macOS:** Ensure your Mac Studio is running the minimal macOS installation you prepared earlier.
2.  **Open Terminal:** Launch the Terminal application in macOS.
3.  **Run the Asahi Linux Installer:** Execute the following command in Terminal. This will download and run the Asahi Linux installer script.
    ```bash
    curl https://alx.sh | sh
    ```
4.  **Follow On-Screen Prompts:**
    - The installer will guide you through the process. When prompted, choose the **minimal or server installation option** (e.g., Fedora Asahi Remix Minimal or Server). Avoid installing a desktop environment if you intend to run headless.
    - **Allocate all or nearly all available disk space to Asahi Linux.** Since macOS will not be used after this installation, maximize the space for your Linux environment. The installer will handle the partitioning.
    - You will be prompted to set an administrator password for Asahi Linux.
5.  **Configure Boot Policy (Permissive Mode):** The installer will likely guide you through this, but you will need to set the boot policy to "permissive mode" to allow Asahi Linux to boot. This typically involves restarting into macOS Recovery and adjusting security settings.
6.  **First Boot into Asahi Linux:** After the installation completes and the boot policy is set, restart your Mac Studio. Ensure Asahi Linux is selected as the default boot option. It should now boot into Asahi Linux.
7.  **Initial Network Configuration (Wi-Fi and SSH):**
    - Once Asahi Linux boots, you will likely need to connect a keyboard and monitor for the initial setup to configure networking.
    - **Enable Wi-Fi:**
      1.  **Identify Wi-Fi device:** Run `nmcli device wifi list` to see available Wi-Fi networks and identify your Wi-Fi adapter (e.g., `wlan0`).
      2.  **Connect to your network:** Use the following command, replacing `YOUR_SSID` with your Wi-Fi network name and `YOUR_PASSWORD` with its password:
          ```bash
          sudo nmcli device wifi connect YOUR_SSID password YOUR_PASSWORD
          ```
      3.  **Verify connection:** Run `nmcli device show wlan0` (replace `wlan0` with your device name) and check for an IP address. You can also `ping google.com` to test internet connectivity.
    - **Configure a static IP address (Optional, but recommended for homelab):**
      - If you prefer a static IP, you can configure it using `nmcli`. First, get the connection name: `nmcli connection show`. Then, modify it:
        ```bash
        sudo nmcli connection modify "Your_Connection_Name" ipv4.method manual ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns "8.8.8.8,8.8.4.4"
        sudo nmcli connection up "Your_Connection_Name"
        ```
        Replace `"Your_Connection_Name"`, `192.168.1.100/24`, `192.168.1.1`, and DNS servers with your network's details.
    - **Install and enable SSH server:**
      - Install OpenSSH server: `sudo dnf install openssh-server`
      - Enable and start the SSH service: `sudo systemctl enable sshd --now`
      - Verify SSH access from another machine on your network using `ssh username@your_mac_studio_ip`.
8.  **Enable Automatic Boot on Power Failure (Optional but Recommended for Homelab):**
    - Once you have SSH access, you can configure the Mac Studio to automatically boot into Asahi Linux after a power failure. Execute the following command in Asahi Linux:
      ```bash
      echo restore | sudo tee /sys/bus/platform/devices/*reboot*/ac_power_mode
      ```

After these steps, your Mac Studio should be running Asahi Linux in a headless configuration, accessible via SSH, and ready for further homelab setup using Ansible.

## Configure Asahi Linux for Ansible Management

Once Asahi Linux is installed and you have SSH access, configure it for Ansible management. This involves creating a dedicated management user (or using an existing one), granting it administrative privileges, and setting up SSH key-based authentication.

1.  **Connect via SSH:** SSH into your Mac Studio using the user you created during the Asahi Linux installation.
    ```bash
    ssh your_username@your_mac_studio_ip
    ```
2.  **Create a Management User (Optional):**

    - If you prefer not to use your primary account for Ansible, you can create a dedicated user (e.g., `ghost_admin`).

    ```bash
    sudo useradd -m -s /bin/bash ghost_admin
    sudo passwd ghost_admin
    ```

    - _If you choose to use your existing user, skip this step and use your username in the following steps._

3.  **Create the 'apps' service account:**

    - Create the `apps` group and user with UID/GID `1000`. This account will be used to run all containerized services.

    ```bash
    sudo groupadd -g 1000 apps
    sudo useradd -m -u 1000 -g 1000 -s /sbin/nologin apps
    ```

4.  **Grant Management User sudo privileges (NOPASSWD):**

    - Create a new sudoers file for your management user (replace `ghost_admin` with your chosen username):
      ```bash
      sudo visudo -f /etc/sudoers.d/ghost_admin
      ```
    - Add the following line to the file, then save and exit:
      ```
      ghost_admin ALL=(ALL) NOPASSWD: ALL
      ```
      **Security Note:** `NOPASSWD` allows the user to run `sudo` commands without a password. While convenient for automation, ensure your SSH keys are highly secure.

5.  **Generate SSH Key (on your Ansible Controller Machine):**

    - On the machine where you will run Ansible (your local development machine), generate an SSH key pair if you don't have one:
      ```bash
      ssh-keygen -t ed25519 -f ~/.ssh/ansible_mac_studio_key
      ```
    - Follow the prompts. It's recommended to use a passphrase for extra security.

6.  **Copy SSH Public Key to Mac Studio:**

    - Copy the public key (`~/.ssh/ansible_mac_studio_key.pub`) from your Ansible controller to the Mac Studio (replace `ghost_admin` and IP with your details):
      ```bash
      ssh-copy-id -i ~/.ssh/ansible_mac_studio_key.pub ghost_admin@your_mac_studio_ip
      ```
    - If `ssh-copy-id` is not available or fails, you can manually copy it:

      ```bash
      # On your Ansible Controller
      cat ~/.ssh/ansible_mac_studio_key.pub

      # On your Mac Studio (via SSH)
      # (Assuming you are logged in as the management user)
      mkdir -p ~/.ssh
      chmod 700 ~/.ssh
      echo "<PASTE_YOUR_PUBLIC_KEY_HERE>" >> ~/.ssh/authorized_keys
      chmod 600 ~/.ssh/authorized_keys
      ```

7.  **Verify SSH Connection with Key:**

    - From your Ansible controller, test the connection using the new key:
      ```bash
      ssh -i ~/.ssh/ansible_mac_studio_key ghost_admin@your_mac_studio_ip
      ```
    - You should be logged in without being prompted for a password.

8.  **Ansible Inventory Setup (in this project):**
    - Create an `inventory.ini` file in the root of this project (or a designated `ansible` directory) with the following content:
      ```ini
      [ghostship]
      chill_penguin ansible_host="{{ lookup('env', 'HOST_IP') }}" ansible_user="{{ lookup('env', 'ANSIBLE_USER') }}" ansible_password="{{ lookup('env', 'ANSIBLE_PASSWORD') }}" ansible_ssh_common_args='-o StrictHostKeyChecking=no'
      ```
    - **Note:** When running Ansible, ensure the `ANSIBLE_USER` environment variable is set to your management user (e.g., `ghost_admin`).

Your Mac Studio is now configured to be managed by Ansible using SSH key-based authentication.

## Running the Ansible Playbook for Docker

This playbook installs and configures Docker on the Asahi Linux server. It also creates the `apps` service account and configures user permissions.

### Prerequisites

1.  Ensure you have completed the "Configure Asahi Linux for Ansible Management" section above.
2.  Ensure you have `ansible` installed on your local machine.
3.  Install the required Ansible collections:
    ```bash
    ansible-galaxy collection install community.general
    ```

### Execution

1.  Export the required environment variables:

    ```bash
    export HOST_IP="your_ghostship_ip"
    export ANSIBLE_USER="your_ansible_user"
    export ANSIBLE_PASSWORD="your_ssh_password"
    export ANSIBLE_INVENTORY="ansible/inventory.ini"

    # Cloudflared Configuration
    export CLOUDFLARED_TUNNEL_TOKEN="your_cloudflared_token"
    export CLOUDFLARED_ACCOUNT_ID="your_cloudflared_account_id" # Found in URL: https://dash.cloudflare.com/<ACCOUNT_ID>
    export CLOUDFLARED_TUNNEL_ID="your_cloudflared_tunnel_id" # Found in Zero Trust > Networks > Tunnels
    export CLOUDFLARED_API_TOKEN="your_cloudflared_api_token" # Requires 'Account:Cloudflare Tunnel:Read' permissions

    # VPN Configuration (PIA)
    export VPN_PIA_USER="your_pia_username"
    export VPN_PIA_PASS="your_pia_password"
    export VPN_PIA_REGION="CA Vancouver" # Optional: defaults to CA Vancouver

    # Plex Configuration
    export PLEX_CLAIM="your_plex_claim_token"
    export PLEX_TOKEN="your_plex_access_token" # For Homepage Plex widget

    # Tautulli Configuration
    export TAUTULLI_API_KEY="your_tautulli_api_key" # For Homepage Tautulli widget

    # Prowlarr Configuration
    export PROWLARR_API_KEY="your_prowlarr_api_key" # For Homepage Prowlarr widget

    # Sonarr Configuration
    export SONARR_API_KEY="your_sonarr_api_key" # For Homepage Sonarr widget

    # Radarr Configuration
    export RADARR_API_KEY="your_radarr_api_key" # For Homepage Radarr widget

    # Bazarr Configuration
    export BAZARR_API_KEY="your_bazarr_api_key" # For Homepage Bazarr widget

    # SABnzbd Configuration
    export SABNZBD_API_KEY="your_sabnzbd_api_key" # For Homepage SABnzbd widget
    export SABNZBD_HOST_WHITELIST="sabnzbd.example.com, 192.168.1.2" # Optional: host whitelist for SABnzbd

    # Home Assistant Configuration
    export HOMEASSISTANT_TOKEN="your_homeassistant_long_lived_access_token"

    # RomM Configuration
    export ROMM_DB_PASSWORD="your_secure_db_password"
    export ROMM_AUTH_SECRET="your_openssl_rand_hex_32_key" # `openssl rand -hex 32`
    export ROMM_IGDB_CLIENT_ID="your_igdb_client_id"
    export ROMM_IGDB_CLIENT_SECRET="your_igdb_client_secret"
    export ROMM_RETROACHIEVEMENTS_API_KEY="your_retroachievements_api_key"
    export ROMM_STEAMGRIDDB_API_KEY="your_steamgriddb_api_key"
    export ROMM_SCREEN_SCRAPER_USER="your_screenscraper_user"
    export ROMM_SCREEN_SCRAPER_PASS="your_screenscraper_password"

    # Arcane Configuration
    # Generate keys by running the following command twice (once for each secret):
    # docker run --rm ghcr.io/getarcaneapp/arcane:latest /app/arcane generate secret
    export ARCANE_ENCRYPTION_KEY="your_generated_32_byte_key" # Must be exactly 32 bytes
    export ARCANE_JWT_SECRET="your_generated_jwt_secret"

    # Booklore Configuration (Optional)
    # export BOOKLORE_DB_PASSWORD="your_secure_password" # Auto-generated if missing

    # Warracker Configuration (Optional)
    # export WARRACKER_DB_PASSWORD="your_secure_password" # Auto-generated if missing
    # export WARRACKER_SECRET_KEY="your_secret_key" # Auto-generated if missing

    # Manyfold Configuration (Optional)
    # export MANYFOLD_SECRET_KEY="your_random_64_char_key" # Auto-generated if missing

    # Activepieces Configuration (Optional)
    export ACTIVEPIECES_FRONTEND_URL="https://activepieces.ghostship.io/"

    # PriceGhost Configuration
    export PRICEGHOST_DB_PASSWORD="your_secure_db_password"
    export PRICEGHOST_JWT_SECRET="your_secure_jwt_secret"

    ```

2.  Run the playbook from the project root:

    ```bash
    ansible-playbook ansible/playbook.yml
    ```

    **Initial Setup (Skip Restarts):**
    For the initial setup or when you want to avoid restarting services (e.g., to prevent interrupting database initialization), use the following command to skip all restart handlers:

    ```bash
    ansible-playbook ansible/playbook.yml --skip-tags restart
    ```

This will connect to the server, update the system, install Docker (and its plugins), create the `apps` user, and configure the necessary groups.

## Services

The following services are configured and managed by the Ansible playbook:

- **Cloudflared**: Secure tunnel to expose services without opening ports.

- **Muximux**: Lightweight portal for managing multiple web applications.

- **Gluetun**: VPN client for secure container networking.

- **SABnzbd**: Efficient Usenet downloader.

- **qBittorrent (VueTorrent)**: BitTorrent client with a modern web UI.

- **Plex**: Media server for streaming movies, TV shows, and music.

- **Plex Auto Languages**: Automated language management for Plex.

- **Tautulli**: Monitoring and tracking tool for Plex Media Server.

- **Prowlarr**: Indexer manager for PVR applications.

- **Sonarr**: Smart TV series downloader.

- **Radarr**: Movie downloader and manager.

- **Bazarr**: Subtitle downloader for Sonarr and Radarr.

- **Huntarr**: Systematic content searcher for Radarr and Sonarr.

- **Recyclarr**: Automated synchronization of TRaSH Guides to Sonarr and Radarr.

- **FlareSolverr**: Proxy server to bypass Cloudflare challenges.

- **MeTube**: Web GUI for youtube-dl.

- **BentoPDF**: Web-based PDF toolkit.

- **ConvertX**: Self-hosted online file converter.

- **IT-Tools**: Handy online tools for developers.

- **Warracker**: Self-hosted warranty tracking application.

- **RomM**: Retro ROM manager and browser.

- **FileFlows**: Video processing and automation.

- **Home Assistant**: Open-source home automation platform.

- Changedetection: Website change monitoring and notification.

- **Sockpuppet Browser**: Headless Chrome browser for scraping.

- Arcane: Web-based Docker management UI.

- Docker Autoheal: Monitor and restart unhealthy Docker containers.

- Activepieces: Open source no-code business automation.

- **Zerobyte**: Backup automation tool.

- **Syncthing**: Continuous file synchronization program.

- **Booklore**: Self-hosted, multi-user ebook manager.

- **Manyfold**: Self-hosted digital asset manager for 3D print files (Solo version).

- **MariaDB**: Unified database server for multiple applications.

- **Homepage**: Highly customizable dashboard for your server.

### Service Configuration Notes

#### qBittorrent / VueTorrent

For the qBittorrent WebUI (and VueTorrent) to function correctly behind the VPN container and with the dashboard widgets, you must configure the following in the qBittorrent WebUI settings:

- **Web UI**: Disable "Enable clickjacking protection".

- **Web UI**: Disable "Enable Cross-Site Request Forgery (CSRF) protection".

- **Web UI**: Add `0.0.0.0/0` to "Bypass authentication for clients in whitelisted IP subnets".

#### Tautulli

For the Tautulli dashboard widget and proxy access to function correctly, you must configure the following in the Tautulli settings:

- **Web Interface**: Click "Show Advanced" and check "Enable HTTP Proxy".
- **Web Interface**: Clear out both "HTTP Username" and "HTTP Password".

#### Prowlarr

For Prowlarr to work properly, you must set `AuthenticationMethod` to `External` in the `config.xml` file.
