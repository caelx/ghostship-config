# Ghostship Homelab Setup

This repository contains the Ansible playbooks and configurations for setting up a homelab on a Mac Studio.

## Factory Reset Mac Studio (macOS)

Follow these steps to perform a factory reset on your Mac Studio running macOS, preparing it for a fresh homelab setup. This process also includes essential preparatory steps for installing Asahi Linux.

**Important:** This process will erase all data on your Mac Studio. Ensure you have backed up all important files before proceeding.

1.  **Back up your data:** Before starting, make sure all your important files, documents, and settings are backed up to an external drive, cloud storage (like iCloud, Google Drive, Dropbox), or another secure location.
2.  **Update macOS:** Ensure your macOS installation is updated to the latest available version. This is crucial for the Asahi Linux installer to function correctly.
3.  **Disable FileVault:** If FileVault is enabled, disable it. Go to System Settings (or System Preferences) > Privacy & Security > FileVault, and turn it off. This is a prerequisite for Asahi Linux installation.
4.  **Ensure sufficient free space:** Asahi Linux requires a dedicated partition. Ensure you have at least 30-50GB of free space. You may need to resize your macOS partition using Disk Utility *before* proceeding with the factory reset if you plan to dual-boot or if your current macOS installation takes up too much space.
5.  **Sign out of Apple services:**
    *   **iCloud:** Go to System Settings (or System Preferences) > your Apple ID > iCloud, and sign out.
    *   **Messages:** Open the Messages app, go to Messages > Settings (or Preferences) > iMessage, and sign out.
    *   **App Store:** Open the App Store, click on your profile picture, and sign out.
6.  **Erase your Mac and Reinstall macOS:**
    *   **For Apple Silicon (M1/M2/M3) Macs:**
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
    *   **For Intel-based Macs:**
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
    *   The installer will guide you through the process. When prompted, choose the **minimal or server installation option** (e.g., Fedora Asahi Remix Minimal or Server). Avoid installing a desktop environment if you intend to run headless.
    *   **Allocate all or nearly all available disk space to Asahi Linux.** Since macOS will not be used after this installation, maximize the space for your Linux environment. The installer will handle the partitioning.
    *   You will be prompted to set an administrator password for Asahi Linux.
5.  **Configure Boot Policy (Permissive Mode):** The installer will likely guide you through this, but you will need to set the boot policy to "permissive mode" to allow Asahi Linux to boot. This typically involves restarting into macOS Recovery and adjusting security settings.
6.  **First Boot into Asahi Linux:** After the installation completes and the boot policy is set, restart your Mac Studio. Ensure Asahi Linux is selected as the default boot option. It should now boot into Asahi Linux.
7.  **Initial Network Configuration (Wi-Fi and SSH):**
    *   Once Asahi Linux boots, you will likely need to connect a keyboard and monitor for the initial setup to configure networking.
    *   **Enable Wi-Fi:**
        1.  **Identify Wi-Fi device:** Run `nmcli device wifi list` to see available Wi-Fi networks and identify your Wi-Fi adapter (e.g., `wlan0`).
        2.  **Connect to your network:** Use the following command, replacing `YOUR_SSID` with your Wi-Fi network name and `YOUR_PASSWORD` with its password:
            ```bash
            sudo nmcli device wifi connect YOUR_SSID password YOUR_PASSWORD
            ```
        3.  **Verify connection:** Run `nmcli device show wlan0` (replace `wlan0` with your device name) and check for an IP address. You can also `ping google.com` to test internet connectivity.
    *   **Configure a static IP address (Optional, but recommended for homelab):**
        *   If you prefer a static IP, you can configure it using `nmcli`. First, get the connection name: `nmcli connection show`. Then, modify it:
            ```bash
            sudo nmcli connection modify "Your_Connection_Name" ipv4.method manual ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns "8.8.8.8,8.8.4.4"
            sudo nmcli connection up "Your_Connection_Name"
            ```
            Replace `"Your_Connection_Name"`, `192.168.1.100/24`, `192.168.1.1`, and DNS servers with your network's details.
    *   **Install and enable SSH server:**
        *   Install OpenSSH server: `sudo dnf install openssh-server`
        *   Enable and start the SSH service: `sudo systemctl enable sshd --now`
        *   Verify SSH access from another machine on your network using `ssh username@your_mac_studio_ip`.
8.  **Enable Automatic Boot on Power Failure (Optional but Recommended for Homelab):**
    *   Once you have SSH access, you can configure the Mac Studio to automatically boot into Asahi Linux after a power failure. Execute the following command in Asahi Linux:
        ```bash
        echo restore | sudo tee /sys/bus/platform/devices/*reboot*/ac_power_mode
        ```

After these steps, your Mac Studio should be running Asahi Linux in a headless configuration, accessible via SSH, and ready for further homelab setup using Ansible.

## Configure Asahi Linux for Ansible Management

Once Asahi Linux is installed and you have SSH access, configure it for Ansible management. This involves creating a dedicated Ansible user, granting it administrative privileges, and setting up SSH key-based authentication.

1.  **Connect via SSH:** SSH into your Mac Studio using the `ansible` user you created during the Asahi Linux installation (or a temporary user if you haven't created one yet).
    ```bash
    ssh your_username@your_mac_studio_ip
    ```
2.  **Create a dedicated Ansible user (non-interactive, no password):**
    *   Create the `ansible` user with a predictable User ID (UID), a home directory, and a non-interactive shell. This user will only be accessible via SSH keys and will not have a password.
    ```bash
    sudo useradd -m -u 990 -g 990 -s /sbin/nologin ansible
    ```
    *   **Note:** UID/GID `990` is chosen as it falls within the typical range for system users (below 1000) and is less likely to conflict with other system accounts or auto-assigned user IDs across various Linux distributions. If this UID/GID is somehow already in use, choose another available value (e.g., `991`, `992`, etc.).
3.  **Grant Ansible user sudo privileges (NOPASSWD):**
    *   Create a new sudoers file for the Ansible user:
        ```bash
        sudo visudo -f /etc/sudoers.d/ansible
        ```
    *   Add the following line to the file, then save and exit:
        ```
        ansible ALL=(ALL) NOPASSWD: ALL
        ```
        **Security Note:** `NOPASSWD` allows the `ansible` user to run `sudo` commands without a password. While convenient for automation, ensure your SSH keys are highly secure.
4.  **Generate SSH Key (on your Ansible Controller Machine):**
    *   On the machine where you will run Ansible (your local development machine), generate an SSH key pair if you don't have one:
        ```bash
        ssh-keygen -t ed25519 -f ~/.ssh/ansible_mac_studio_key
        ```
    *   Follow the prompts. It's recommended to use a passphrase for extra security.
5.  **Copy SSH Public Key to Mac Studio:**
    *   Copy the public key (`~/.ssh/ansible_mac_studio_key.pub`) from your Ansible controller to the Mac Studio:
        ```bash
        ssh-copy-id -i ~/.ssh/ansible_mac_studio_key.pub ansible@your_mac_studio_ip
        ```
    *   If `ssh-copy-id` is not available or fails, you can manually copy it:
        ```bash
        # On your Ansible Controller
        cat ~/.ssh/ansible_mac_studio_key.pub

        # On your Mac Studio (via SSH)
        sudo -u ansible mkdir -p /home/ansible/.ssh
        sudo -u ansible chmod 700 /home/ansible/.ssh
        sudo -u ansible sh -c 'echo "<PASTE_YOUR_PUBLIC_KEY_HERE>" >> /home/ansible/.ssh/authorized_keys'
        sudo -u ansible chmod 600 /home/ansible/.ssh/authorized_keys
        sudo chown -R ansible:ansible /home/ansible/.ssh
        ```
        Replace `<PASTE_YOUR_PUBLIC_KEY_HERE>` with the output from `cat ~/.ssh/ansible_mac_studio_key.pub`.
6.  **Verify SSH Connection with Key:**
    *   From your Ansible controller, test the connection using the new key:
        ```bash
        ssh -i ~/.ssh/ansible_mac_studio_key ansible@your_mac_studio_ip
        ```
    *   You should be logged in without being prompted for a password (unless you set a passphrase for your SSH key).
7.  **Ansible Inventory Setup (in this project):**
    *   Create an `inventory.ini` file in the root of this project (or a designated `ansible` directory) with the following content:
        ```ini
        [ghostship]
        chill_penguin ansible_host="{{ lookup('env', 'HOST_IP') }}" ansible_user="{{ lookup('env', 'ANSIBLE_USER') }}" ansible_password="{{ lookup('env', 'ANSIBLE_PASSWORD') }}" ansible_ssh_common_args='-o StrictHostKeyChecking=no'
        ```
    *   Replace `your_mac_studio_ip` with the actual IP address of your Mac Studio.

Your Mac Studio is now configured to be managed by Ansible using SSH key-based authentication.

## Running the Ansible Playbook for Docker

This playbook installs and configures Docker on the Asahi Linux server. It also creates the `apps` service account and configures user permissions.

### Prerequisites

1.  Ensure you have completed the "Configure Asahi Linux for Ansible Management" section above.
2.  Ensure you have `ansible` installed on your local machine.

### Execution

1.  Export the required environment variables:
    ```bash
    export HOST_IP="your.chill_penguin.ip"
    export ANSIBLE_USER="ansible"
    export ANSIBLE_PASSWORD="your_ssh_password"
    
    # Docker Service Configuration
    export CLOUDFLARED_TUNNEL_TOKEN="your_cloudflared_token"
    export WIREGUARD_PRIVATE_KEY="your_wireguard_private_key"
    export VPN_COUNTRY="your_country" # Optional: e.g. "United States" for closest server
    export TZ="UTC" # Optional: Default is UTC
    ```

2.  Run the playbook from the project root:
    ```bash
    ansible-playbook -i ansible/inventory.ini ansible/playbook.yml
    ```

This will connect to the server, update the system, install Docker (and its plugins), create the `apps` user, and configure the necessary groups.