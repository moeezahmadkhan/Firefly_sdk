# AIBOX-1688 (BM1688) Sophon SDK v2.1 Installation Project

## Project Overview
This project successfully deployed the **Sophon SDK v2.1** on the **AIBOX-1688** edge computing device. Due to hardware sensitivity regarding physical media (SD/USB) partition tables, a **Network (TFTP) Boot** method was utilized to flash the firmware directly into the internal eMMC storage.

---

## 🛠 Hardware Specifications: AIBOX-1688
*   **Computing Chip**: SOPHON BM1688
*   **CPU**: Octa-core ARM Cortex-A53 @ 1.6GHz
*   **TPU Power**: 
    - 32T@INT4 peak computing power
    - 16T@INT8 peak computing power
    - 2T@FP32 computing power
*   **Memory**: 8GB LPDDR4
*   **Storage**: 32GB eMMC (Internal)
*   **Expansion**: M.2 PCIe NVMe SSD support, TF Card slot
*   **OS Support**: Ubuntu 22.04 LTS (Jammy Jellyfish)

---

## 🚀 Installation Method: TFTP Network Boot
This method was chosen for its reliability and ability to bypass physical hardware/partition table compatibility issues.

### 1. Host Setup (Linux Ubuntu PC)
We configured a TFTP server to "serve" the SDK images to the AIBOX.

*   **Service Installed**: `tftpd-hpa`
*   **TFTP Root Directory**: `/var/lib/tftpboot/`
*   **Archive Used**: `sophon-img/tftp.tgz` (extracted to root directory)
*   **Commands**:
    ```bash
    sudo apt install tftpd-hpa
    sudo mkdir -p /var/lib/tftpboot
    sudo chmod 777 /var/lib/tftpboot
    sudo tar -xvf tftp.tgz -C /var/lib/tftpboot/ --strip-components=1
    sudo systemctl restart tftpd-hpa
    ```

### 2. Client Setup (AIBOX-1688 via Serial Console)
Configured the U-Boot environment to communicate with the Host PC over the local network.

*   **U-Boot Configuration**:
    ```bash
    setenv ipaddr 192.168.1.113     # AIBOX IP
    setenv serverip 192.168.1.xxx   # Host PC IP
    saveenv
    ```
*   **Execution**:
    ```bash
    tftpboot 0x120000000 boot.scr
    source 0x120000000
    ```

---

## 🔧 Post-Installation Troubleshooting
After initial flashing, the device may encounter a `Could not find configuration node` error. This is resolved by manually setting the hardware identity in U-Boot:

```bash
setenv bootconf config-cv186ah_wevb_4G
saveenv
reset
```

---

## ✅ Verification & Login
*   **Operating System**: Ubuntu 22.04.5 LTS
*   **Default Credentials**:
    - **User**: `admin`
    - **Password**: `admin`
*   **Primary Diagnostic Command**:
    ```bash
    bm-smi
    ```
    *(Run this to confirm the BM1688 TPU is active and drivers are loaded).*

## 📦 Software Stack
*   **内核 (Kernel)**: Linux 5.10
*   **SDK Components**: `libsophon` (Drivers), `BMRuntime` (TPU execution), `SOPHON-SAIL` (AI conversion).
*   **Containerization**: Docker is pre-installed for isolated AI application deployment.

---
**Status**: DEPLOYED & OPERATIONAL
