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

# ⚙️ **Requirements**

To complete this procedure, you will need:

### ✔️ Hardware
- MacBook Pro (2010–2012), tested on the 2011 model  
- Two SSD units (one for Ubuntu, one for Windows)

### ✔️ Software
- Ubuntu 22.04 / 24.04 installed  
- VirtualBox  
- GParted  
- Windows 10 ISO  
- Terminal with sudo privileges  

### ✔️ Basic Knowledge
- Partition management  
- Linux terminal usage  
- Understanding of RAW Disk passthrough (mapping a physical disk to a VM)

### ✔️ Warning
> ⚠️ *This process writes directly to your physical drive. Selecting the wrong disk may cause data loss.*


