# Arch Linux - Troubleshooting Guide

## Screen Size & VirtualBox Guest Additions

This guide will help you fix screen size issues and enable seamless copy-paste between your Windows host and your Arch Linux VM.

---

## Step 1: Install & Enable VirtualBox Guest Utilities

1. **Install the Guest Utilities**

   The utilities enable features like clipboard sharing and display resizing.

   ```bash
   sudo pacman -S virtualbox-guest-utils
   ```
   *(If prompted for a provider, choose the default option.)*

2. **Enable the Guest Service**

   This ensures the drivers start automatically on boot.

   ```bash
   sudo systemctl enable vboxservice
   ```

---

## Step 2: Verify the Guest Service

Check if the service is running correctly.

1. **Check Service Status**

   ```bash
   systemctl status vboxservice
   ```

   - **Green Dot** (`active (running)`): Service is active, all good!
   - **Red Dot** (`failed / dead`): Service crashed or did not start.
   - **Message**: If you see any mention of "kernel modules," note it for further troubleshooting.

---

## Step 3: Check VirtualBox Kernel Modules

Ensure the required drivers are loaded for proper screen resizing.

1. **List VirtualBox Modules**

   ```bash
   lsmod | grep vbox
   ```

   - You should see a list containing `vboxvideo`.
   - **If the list is empty**: Drivers are installed, but not loaded.
   - **If you see `vboxguest` but NOT `vboxvideo`**: There is a graphics driver issue.

---

## Step 4: Run the VBox Client (XFCE fix)

In your Arch XFCE desktop terminal (no `sudo` needed):

```bash
VBoxClient-all
```

---

## Step 5: The Ultimate Fix - Change VM Graphics Settings

If the above steps did not work:

1. **Power Off the VM Completely**

   Use the shutdown (poweroff) command or VirtualBox's power-off feature.

2. **Adjust Graphics Controller in VirtualBox on Windows**

   - Right-click your Arch VM in the VirtualBox menu → **Settings**
   - Go to **Display**
   - Change **Graphics Controller** from `VMSVGA` to `VBoxSVGA`
   - *Ignore* the "Invalid Settings" warning if it appears.

3. **Start your VM again**

   Your screen size should now be adjustable!

---

## Quick Reference: Common Commands

| Command                      | Description                       |
|------------------------------|-----------------------------------|
| `sudo pacman -S virtualbox-guest-utils` | Install guest utilities     |
| `sudo systemctl enable vboxservice`     | Enable guest service        |
| `systemctl status vboxservice`          | Check service status        |
| `lsmod \| grep vbox`                     | List loaded vbox modules    |
| `VBoxClient-all`                        | Run guest client in XFCE    |

