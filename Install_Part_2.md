## Step 5: Mount the Partitions

Now we have to attach these disks to your current live system so we can install files onto them. **The order matters!**

---

### 1. Mount Root first (to `/mnt`):

```bash
mount /dev/sda3 /mnt
```

---

### 2. Turn on Swap:

```bash
swapon /dev/sda2
```

---

### 3. Create the boot folder and mount Boot:

First, create the folder so there is a place to mount it.

```bash
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

---

### 4. Check your work

Run `lsblk` one last time. It should look like a "tree" connected to **/mnt**:

```
sda
├─sda1 -> /mnt/boot
├─sda2 -> [SWAP]
└─sda3 -> /mnt
```

## Step 6: Install the Core System

Run this command to install the most important system packages:

```bash
pacstrap -K /mnt base linux linux-firmware nano git networkmanager vim
```

**What these packages do:**
- `base`: Minimal folder structure and essential utilities.
- `linux`: The Kernel (the "brain" of your system).
- `linux-firmware`: Drivers for Wi-Fi, Video, etc.
- `nano git networkmanager vim`: Useful tools for editing, networking, and managing your system.

---

## Step 7: Generate the File System Map (fstab)

Generate your fstab file, which tells Linux how to mount your partitions at boot:

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

**Explanation:**
- `genfstab`: Generates the file.
- `-U`: Uses UUIDs (unique disk identifiers) for reliability.
- `>>`: Appends output. **Type carefully! `>>` is vital. If you use `>`, it overwrites; `>>` appends.**

---

## Step 8: Enter the System ("Chroot" into new install)

Now, enter your new Arch Linux environment on your main partition:

```bash
arch-chroot /mnt
```

**How to know it worked:**
- **Before:** `root@archiso ~ #` (red color, usually)
- **After:** `[root@archiso /]#` (often a different style or color)

You are now "inside" the installed system on your sda3 hard drive!

---

## Step 9: Housekeeping Tasks & Bootloader Setup

---

### 9.1 Set Time & Language

Since you are "inside" the disk, your installed system does not know about the time settings from the Live CD.

**Step 1: Set Timezone**
```bash
ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```

**Step 2: Sync Hardware Clock**
```bash
hwclock --systohc
```

**Step 3: Generate Language (Locale)**
- Edit `/etc/locale.gen`:
    - Use: `vim /etc/locale.gen`
    - Find: `#en_US.UTF-8 UTF-8`
    - Delete the `#` with `x`, then type `:wq` to save & quit.
- Apply the locale:
    ```bash
    locale-gen
    ```
- Set the variable:
    ```bash
    echo "LANG=en_US.UTF-8" > /etc/locale.conf
    ```

---

### 9.2 Name your Computer (Hostname)
Choose your computer's name:

```bash
echo "archlinux" > /etc/hostname
```

---

### 9.3 Set the Root Password

Set the password for the root (admin) account:

```bash
passwd
```
*Type your password when prompted. You won't see characters as you type—this is normal!*

---

### 9.4 The Bootloader (Crucial for UEFI)

Without the bootloader, your system won't boot!

- **Install the Bootloader Software:**
    ```bash
    pacman -S grub efibootmgr
    ```

- **Install GRUB to the Boot Partition:**
    ```bash
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
    ```
    *Check for errors: It should say `Installation finished. No error reported.`*

- **Generate the Config File:**
    ```bash
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

---

### 9.5 Create Your User (Safer Than Root)

It is unsafe to always run as root. Replace `username` with your desired name.

- **Create a new user:**
    ```bash
    useradd -m -G wheel -s /bin/bash username
    ```

- **Set your user password:**
    ```bash
    passwd username
    ```

- **Give "wheel" group sudo access:**
    - Edit the sudoers file:
        - Run: `EDITOR=vim visudo`
        - Find the line: `%wheel ALL=(ALL:ALL) ALL`
        - Remove the `#` at the start to uncomment.
        - Save and quit with `:wq`

---

### 9.6 Enable Internet

Enable NetworkManager to turn on internet at boot:

```bash
systemctl enable NetworkManager
```
*(Note the capital N and M.)*

---

## Step 10: Finish and Reboot!

All done! If the above ran without errors, exit the chroot, unmount your drives, and reboot into your new Arch install:

```bash
exit
umount -R /mnt
reboot
```

## Step 11: Create Your Daily User

Once you’ve set your root password, create a regular user account. (We'll use `kal1` for this example.)

**Step 1: Create the User**
```bash
useradd -m -G wheel -s /bin/bash kal1
```
- `-m`: Create a home folder for the user.
- `-G wheel`: Give admin (sudo) privileges.
- `-s /bin/bash`: Use bash as the default shell.

**Step 2: Set the User Password**
```bash
passwd kal1
```
*(Type your password—characters will not display, just press Enter to confirm.)*

**Step 3: Authorize the User for Sudo**
```bash
EDITOR=vim visudo
```
- Find the following line: `%wheel ALL=(ALL:ALL) ALL`
- Remove the `#` at the start (uncomment the line).
- Save and exit (`:wq`).

**Tip:**  
If you get `command not found` when running `EDITOR=vim visudo`, install sudo:
```bash
pacman -S sudo
```

---

## Step 12: Install the Desktop Stack

Install the core desktop environment and graphical login manager.

**Step 1: Install Packages**
```bash
sudo pacman -S xorg xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
```
*(Press Enter to select default options if prompted.)*

**Step 2: Enable the Login Service**
Tell the system to start the display manager (LightDM) on boot:
```bash
sudo systemctl enable lightdm
```

---

## Step 13: Reboot and Enjoy

Once installation finishes and LightDM is enabled, reboot to enter your new graphical desktop!

```bash
reboot
```

---

Now your system should boot into the XFCE desktop environment with LightDM login.  
You're ready to start your Arch Linux journey!
