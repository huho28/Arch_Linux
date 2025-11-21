# Arch_Linux
Arch Linux for VirtualBox

<!-- Let's say you've already downloaded VirtualBox and are ready to install Arch Linux. -->

## Installation steps
Choose mirror physically closest to your location https://archlinux.org/download/ <br>
Download the file.iso <br>
<!-- It contains 0 tools. It doesn't even have a graphical desktop. -->
### 1. Verify Boot Mode (UEFI vs. BIOS)
Run this command:
```bash
ls /sys/firmware/efi/efivars
```
* **If you see a long list of weird text files:** You are in UEFI mode
* **If it says No such file or directory:** You are in BIOS (Legacy) mode

> [!TIP]
> **VirtualBox Users:** If you get the "No such file" error, it means your VM is set to Legacy. You can proceed with a BIOS install, but if you want to learn the modern way, you might want to shut down the VM, go to **Settings > System > Enable EFI**, and boot again.

<br>

### 2. Verify Internet Connection
```bash
ping -c 4 google.com
```
* **If you see 64 bytes from...:** You are online.
* **If you see Temporary failure in name resolution:** You have no internet.
> [!TIP]
> **Troubleshooting:** In VirtualBox, ensure your Network Adapter is set to NAT or Bridged Adapter.

<br>

### 3. Verify System Clock
```bash
timedatectl
```

* Check the line `System clock synchronized:`. It should say **yes.**

* If it says **no**, or the time is wildly off, run:
```bash
timedatectl set-ntp true
```
(Wait 10 seconds and check timedatectl again).

* To set the correct time zone
```bash
timedatectl set-timezone America/New_York
```

<br>

### 4. Partitioning
* To see your disk name:
```bash
lsblk
```
You will likely see a device named sda or vda that is about 20GB-50GB (however big you made the VM).
<br>
* In this example my device is named sda (if your device is named vda, replace sda with vda).
* Since you are in **UEFI Mode**, we must use the **GPT**

### Step 1: Open the Partition Manager
```bash
cfdisk /dev/sda
```
**Crucial Moment:** It will ask you to `Select label type`.

* Select **gpt**.

* Press **Enter**.

### Step 2: Create the Slices (Partitions)
You will see `Free space` (50GB). We need to create three partitions. Follow these steps carefully using your arrow keys:

#### 1. The Boot Partition (EFI)
* Highlight `Free space` and hit **[ New ]**.

* **Partition Size:** Type `1G` and hit Enter.

* **Type:** It will default to "Linux filesystem". We need to change this. Use the arrow keys to select **[ Type ]**.

* Select **EFI System**. (This is critical. The BIOS looks for this specific tag).

#### 2. The Swap Partition (Virtual RAM)
* Highlight the remaining `Free space` (down arrow) and hit **[ New ]**.

* **Partition Size:** Type `4G` (Good standard size for a VM).

* **Type:** Select **[ Type ]** and choose **Linux swap**.

#### 3. The Root Partition (Where Arch lives)
* Highlight the last chunk of `Free space` and hit **[ New ]**.

* **Partition Size:** Keep the default (it will fill the rest of the 50GB). Hit Enter.

* **Type:** Leave it as **Linux filesystem** (default).

### Step 3: Save Changes
* Use the right arrow key to highlight **[ Write ]**.

* Press Enter.

* Type `yes` (full word) to confirm.

* Highlight **[ Quit ]** and press Enter.

### Step 4: Format the Partitions
* **Format Boot:**
```bash
mkfs.fat -F32 /dev/sda1
```
* **Format Swap:**
```bash
mkswap /dev/sda2
```
* **Format Root:**
```bash
mkfs.ext4 /dev/sda3
```
> [!TIP]
> **For vda:** `mkfs.fat -F32 /dev/sda1` > `mkfs.fat -F32 /dev/vda1`
