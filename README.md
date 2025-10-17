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
    *   Allocate a suitable amount of disk space for Asahi Linux. The installer will help you resize partitions if necessary.
    *   You will be prompted to set an administrator password for Asahi Linux.
5.  **Configure Boot Policy (Permissive Mode):** The installer will likely guide you through this, but you will need to set the boot policy to "permissive mode" to allow Asahi Linux to boot. This typically involves restarting into macOS Recovery and adjusting security settings.
6.  **First Boot into Asahi Linux:** After the installation completes and the boot policy is set, restart your Mac Studio. It should now boot into Asahi Linux.
7.  **Initial Network Configuration (SSH):**
    *   Once Asahi Linux boots, you will likely need to connect a keyboard and monitor for the initial setup to configure networking.
    *   Configure a static IP address or ensure DHCP is working.
    *   Install and enable SSH server (e.g., `sudo dnf install openssh-server` and `sudo systemctl enable sshd --now` for Fedora).
    *   Verify SSH access from another machine on your network.
8.  **Enable Automatic Boot on Power Failure (Optional but Recommended for Homelab):**
    *   Once you have SSH access, you can configure the Mac Studio to automatically boot into Asahi Linux after a power failure. Execute the following command in Asahi Linux:
        ```bash
        echo restore | sudo tee /sys/bus/platform/devices/*reboot*/ac_power_mode
        ```

After these steps, your Mac Studio should be running Asahi Linux in a headless configuration, accessible via SSH, and ready for further homelab setup using Ansible.