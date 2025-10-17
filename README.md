# Ghostship Homelab Setup

This repository contains the Ansible playbooks and configurations for setting up a homelab on a Mac Studio.

## Factory Reset Mac Studio (macOS)

Follow these steps to perform a factory reset on your Mac Studio running macOS, preparing it for a fresh homelab setup.

**Important:** This process will erase all data on your Mac Studio. Ensure you have backed up all important files before proceeding.

1.  **Back up your data:** Before starting, make sure all your important files, documents, and settings are backed up to an external drive, cloud storage (like iCloud, Google Drive, Dropbox), or another secure location.
2.  **Sign out of Apple services:**
    *   **iCloud:** Go to System Settings (or System Preferences) > your Apple ID > iCloud, and sign out.
    *   **Messages:** Open the Messages app, go to Messages > Settings (or Preferences) > iMessage, and sign out.
    *   **App Store:** Open the App Store, click on your profile picture, and sign out.
3.  **Erase your Mac and Reinstall macOS:**
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
        10. Select "Reinstall macOS" and follow the on-screen instructions.
    *   **For Intel-based Macs:**
        1.  Shut down your Mac Studio.
        2.  Turn on your Mac and immediately press and hold `Command (⌘) + R` until you see the Apple logo or another image.
        3.  If prompted, select a user you know the password for, and enter their administrator password.
        4.  In the Recovery app, select Disk Utility and click Continue.
        5.  In the sidebar of Disk Utility, select your internal startup disk (e.g., "Macintosh HD").
        6.  Click the Erase button in the toolbar.
        7.  Enter a name (e.g., "Macintosh HD"), choose APFS or Mac OS Extended (Journaled) as the format (depending on macOS version), and click Erase.
        8.  After erasing, quit Disk Utility to return to the Recovery app window.
        9.  Select "Reinstall macOS" and follow the on-screen instructions.

Once macOS is reinstalled, you will have a clean system ready for the homelab setup.
