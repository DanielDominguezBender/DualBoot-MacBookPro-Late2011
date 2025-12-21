# 📘 README.md  
## **Install Windows Natively Using a Virtual Machine (RAW Disk Method)**  
### **“Did you know you can install an OS *natively* through a virtual machine?”**

```
╔══════════════════════════════════════════════════════════════════════╗
║   ____        _ _ _             ____              _                  ║
║  |  _ \ _   _(_) | |_ ___      |___ \   _   _  __| | ___  ___        ║
║  | | | | | | | | | __/ _ \ _____ __) | | | | |/ _` |/ _ \/ __|       ║
║  | |_| | |_| | | | || (_) |_____/ __/  | |_| | (_| |  __/\__ \       ║
║  |____/ \__,_|_|_|\__\___/     |_____|  \__,_|\__,_|\___||___/       ║
║                                                                      ║
║                     D U A L   B O O T   W I N – M A C                ║
║        Native Installation Using a Virtual Machine (RAW Disk)        ║
║                     MacBook Pro 2011 • No BootCamp                   ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

# 📑 **Table of Contents**

- [Introduction](#-introduction)
- [Requirements](#-requirements)
- [1. Preparing the SSD for Windows](#-1-preparing-the-ssd-for-windows)
- [2. Creating a RAW Disk for VirtualBox](#-2-creating-a-raw-disk-for-virtualbox)
- [3. Disabling KVM](#-3-disabling-kvm)
- [4. Configuring the Virtual Machine](#-4-configuring-the-virtual-machine)
- [5. Installing Windows on the Physical Disk](#-5-installing-windows-on-the-physical-disk)
- [6. Booting Windows Natively](#-6-booting-windows-natively)
- [7. Setting Up Dual Boot with GRUB](#-7-setting-up-dual-boot-with-grub)
- [8. Silencing the Boot Chime](#-8-silencing-the-boot-chime)
- [Troubleshooting](#-troubleshooting)
- [Conclusion](#-conclusion)

---

# 🧭 **Introduction**

Older Apple devices—especially **MacBook Pro models from 2010 to 2012**—struggle with native Windows installation due to several limitations:

- The Windows USB installer won't boot  
- Ventoy throws **“Not a Secure Boot Platform 14”**  
- Apple's EFI is not fully compatible with modern Windows bootloaders  
- The installer freezes at the Windows logo  
- Windows refuses to install on MBR-style disks  
- BootCamp is unavailable without macOS  
- GRUB fails to detect Windows after installation  

**The solution:**  
💥 *Install Windows through a virtual machine that writes directly to your physical SSD using RAW Disk passthrough,*  
then boot it **natively** on the Mac.

This method:

- bypasses EFI limitations  
- installs Windows directly onto a real SSD  
- avoids nearly all Windows/Mac installer errors  
- supports dual boot with Ubuntu  
- requires no BootCamp  

Tested on:

🖥️ **MacBook Pro Late 2011**  
Intel i5 • 16GB RAM • Dual SSD • Ubuntu 24.04  

---

## Requirements
  - Ubuntu 22.04/24.04 installed on the MacBook
  - VirtualBox
  - GParted
  - Windows 10 ISO
  - MacBook Pro 2011 or similar Intel-based Mac
  - Secondary SSD for Windows installation
  - Terminal with sudo privileges

  ## 1. Preparing the SSD for Windows
  Use GParted to create a new NTFS partition:
  1. Open GParted
  2. Select the secondary SSD (e.g., `/dev/sdb`)
  3. Create a new NTFS partition of 100–150 GB (e.g., `/dev/sdb3`)
  4. Apply changes

  ## 2. Creating a RAW Disk for VirtualBox
  Run the following command:
  ```bash
  sudo VBoxManage internalcommands createrawvmdk       -filename ~/win10_raw.vmdk       -rawdisk /dev/sdb
  ```

  Fix permissions:
  ```bash
  sudo chown $USER:$USER ~/win10_raw.vmdk
  sudo chmod 666 /dev/sdb
  ```

  ## 3. Disabling KVM
  VirtualBox cannot run with KVM enabled.

  Temporarily disable KVM:
  ```bash
  sudo rmmod kvm_intel
  sudo rmmod kvm
  ```

  Permanently blacklist it:
  ```bash
  echo "blacklist kvm_intel" | sudo tee /etc/modprobe.d/blacklist-kvm.conf
  echo "blacklist kvm" | sudo tee -a /etc/modprobe.d/blacklist-kvm.conf
  sudo update-initramfs -u
  sudo reboot
  ```

  ## 4. Configuring the Virtual Machine
  - Create a new VM → Windows 10 (64-bit)
  - Disable EFI
  - 4–8 GB RAM
  - 2 CPU cores
  - Graphics: VMSVGA
  - Disable 3D acceleration
  - Storage:
    - SATA Port 0 → `win10_raw.vmdk`
    - SATA Port 1 → Windows ISO
    - Host I/O Cache enabled

  ## 5. Installing Windows onto the Physical Disk
  Inside the Windows installer:
  1. Choose the partition created earlier (`/dev/sdb3`)
  2. Format the partition
  3. Begin installation

  **Important:**  
  When Windows says **"Restarting…"**, SHUT DOWN the VM immediately.

  ## 6. Booting Windows Natively
  1. Reboot your Mac
  2. Hold **ALT/Option**
  3. Select **Windows**
  Windows will continue the installation on real hardware.

  ## 7. Setting Up Dual Boot with GRUB
  ```bash
  sudo mount /dev/sda1 /boot/efi
  sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu
  sudo update-grub
  ```
