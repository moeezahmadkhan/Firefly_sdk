# Sophon SDK v2.1 Installation Guide for AIBOX-1688

This document outlines the procedures to install the Sophon SDK v2.1 on your AIBOX-1688 (BM1688). There are two primary methods:

1.  **Full System Re-imaging (Recommended)**: Flashes the entire OS and SDK to the device using an SD card. This ensures a clean, compatible environment.
2.  **Component Update**: Installs specific SDK libraries (libsophon, soc-media) on an existing system using Debian packages.

---

## Method 1: Full System Re-imaging (SD Card)
**Best for:** Clean installation, upgrading from an old version, or if the system is unstable.

### Prerequisites
*   A microSD card (at least 8GB recommended).
*   A microSD card reader.
*   Your Host PC (Linux or Windows).
*   The SDK file: `sophonsdk_edge_v2.1_official_release/sophon-img/sdcard.tgz`.

### Step 1: Prepare the SD Card

#### On Linux (Ubuntu)
1.  Insert your SD card into the reader.
2.  Identify the device name (e.g., `/dev/sdX`) using `lsblk`. **Be strictly careful to choose the correct device.**
3.  Format the partition to FAT32.
    ```bash
    # Unmount the partition if mounted
    sudo umount /dev/sdX1
    
    # Format (replace X1 with your partition)
    sudo mkfs.vfat /dev/sdX1
    ```

#### On Windows
1.  Insert the SD card.
2.  Open "This PC" (My Computer).
3.  Right-click the SD card drive > **Format**.
4.  Select **FAT32** as the file system.
5.  Click **Start**.

### Step 2: Copy Files to SD Card
You need to extract the *contents* of the `sdcard` folder inside the `.tgz` archive to the root of the SD card.

#### On Linux
1.  Navigate to the image directory:
    ```bash
    cd /home/moeez/Documents/Sophgo/sophonsdk_edge_v2.1_official_release/sophon-img
    ```
2.  Mount your SD card (e.g., to `/mnt/sdcard`).
3.  Extract the archive:
    ```bash
    # Extract specifically the contents of the 'sdcard' folder to the mount point
    sudo tar -xvf sdcard.tgz -C /mnt/sdcard --strip-components=1
    ```
    *Note: `--strip-components=1` removes the top-level `sdcard/` folder so the files (`boot.scr`, `rootfs...`, etc.) sit directly in the root of the SD card.*
4.  Sync to ensure all data is written:
    ```bash
    sync
    ```
5.  Unmount and remove the card.

#### On Windows
1.  Use a tool like 7-Zip or WinRAR to open `sdcard.tgz`.
2.  Open the inner tar file if necessary.
3.  Enter the `sdcard` folder inside the archive.
4.  Drag and drop all files (`boot.scr`, `rootfs...`, etc.) directly to the root of your SD card drive (e.g., `D:\`).

### Step 3: Flash the Device
1.  Power off the AIBOX-1688.
2.  Insert the prepared microSD card into the slot.
3.  Power on the device.
4.  **Wait**: The device will detect the card and begin the flashing process automatically. This typically takes several minutes.
    *   *Indicator*: Watch for LED activity. Often, a specific blinking pattern indicates flashing, and a solid light (or off) indicates completion. Refer to your specific device manual for exact LED codes.
5.  Once finished, power off the device.
6.  **Remove the SD card**.
7.  Power on the device again. It should boot into the new system.

---

## Method 2: Component Update (Debian Packages)
**Best for:** Updating libraries on a working system without losing user data or configuration.

### Prerequisites
*   AIBOX-1688 must be running and accessible (via SSH or terminal).
*   The `.deb` files located in `sophonsdk_edge_v2.1_official_release/sophon-img/bsp-debs/`.

### Steps
1.  **Transfer Packages**: Copy the `.deb` files from your host PC to the AIBOX.
    ```bash
    # Example using scp (run on your Host Linux PC)
    cd /home/moeez/Documents/Sophgo/sophonsdk_edge_v2.1_official_release/sophon-img/bsp-debs
    scp *.deb user@<AIBOX_IP>:/tmp/
    ```
    *(Replace `<AIBOX_IP>` with the actual IP address of your device).*

2.  **Install Packages**: SSH into the AIBOX and install.
    ```bash
    ssh user@<AIBOX_IP>
    cd /tmp
    sudo dpkg -i *.deb
    ```
3.  **Fix Dependencies** (if errors occur):
    ```bash
    sudo apt-get install -f
    ```
4.  **Reboot**:
    ```bash
    sudo reboot
    ```

---

## Verification
After installation, verify the SDK version:

1.  Log in to the AIBOX.
2.  Run the query command (common for Sophon devices):
    ```bash
    bm-smi
    # OR
    cat /etc/issue
    ```
3.  You should see references to the SDK version (v2.1) or the kernel build date matching the files.

> [!IMPORTANT]
> **Safety Notice**: Flashing the firmware (Method 1) will **erase all data** on the device's internal storage (eMMC). Back up any important files before proceeding.
