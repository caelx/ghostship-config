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