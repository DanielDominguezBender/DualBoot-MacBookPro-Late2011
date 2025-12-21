
╔══════════════════════════════════════════════════════════════════════╗
║   ____        _ _ _             ____              _                  ║
║  |  _ \ _   _(_) | |_ ___      |___ \   _   _  __| | ___  ___        ║
║  | | | | | | | | | __/ _ \ _____ __) | | | | |/ _` |/ _ \/ __|       ║
║  | |_| | |_| | | | || (_) |_____/ __/  | |_| | (_| |  __/\__ \       ║
║  |____/ \__,_|_|_|\__\___/     |_____|  \__,_|\__,_|\___||___/       ║
║                                                                      ║
║      NATIVE WINDOWS INSTALLATION USING A VIRTUAL MACHINE (RAW DISK)  ║
║                 Dual Boot: Ubuntu  ◀────▶  Windows 10                ║
║                     MacBook Pro 2011 • No BootCamp                   ║
╚══════════════════════════════════════════════════════════════════════╝




## Instalar Windows nativo usando una Máquina Virtual (caso real MacBook Pro 2011)


# 📑 Índice

- [Introducción](#introducción)
- [Requisitos](#requisitos)
- [Preparación del SSD](#1-preparación-del-ssd-para-windows)
- [Crear Disco RAW](#2-crear-un-disco-raw-para-virtualbox)
- [Desactivar-KVM](#3-desactivar-kvm-para-usar-virtualbox)
- [Configurar la VM](#4-configurar-la-máquina-virtual)
- [Instalar Windows en RAW Disk](#5-instalación-de-windows-en-el-disco-físico)
- [Arranque Nativo en Mac](#6-arrancar-windows-de-forma-nativa)
- [Dual Boot con GRUB](#7-configuración-del-arranque-dual)
- [Troubleshooting](#9-troubleshooting)
- [Conclusión](#conclusión)
- [English Version](#english-version)


🧭 Introducción

En muchos equipos Apple antiguos (MacBook Pro 2010–2012), instalar Windows de forma nativa puede ser un verdadero desafío:

El instalador USB no arranca

Ventoy muestra el error “Not a Secure Boot Platform 14”

La EFI del Mac no soporta el instalador de Windows

El instalador se queda congelado en el logo

Windows exige GPT/UEFI aunque el Mac no lo soporte

GRUB no detecta Windows después de instalarlo

BootCamp ya no existe o no funciona sin macOS

Entonces…
¿Cómo instalar Windows 10 nativamente en un Mac antiguo sin BootCamp, sin EFI y sin USB?

💥 La solución es sorprendente:
Instalar Windows dentro de una máquina virtual, pero apuntando directamente al SSD físico del Mac (RAW disk), y luego arrancar Windows nativamente.

Esto permite:

Saltarse las limitaciones del firmware

Instalar Windows directamente en un disco real

Evitar todos los errores de EFI

Tener dual boot Ubuntu + Windows

Dar una segunda vida a hardware antiguo

📌 Este procedimiento fue probado en un MacBook Pro Late 2011 (i5, 16GB RAM, doble SSD).


---


🛠️ 1. Requisitos

- Ubuntu instalado en el MacBook
- Un segundo SSD donde instalar Windows (ej: /dev/sdb)
- ISO de Windows 10
- VirtualBox
- Privilegios sudo
- GParted instalado

---


🛠️ 2. Preparar el SSD con una partición para Windows

Abrir GParted:

- Seleccionar el segundo SSD → /dev/sdb
- Crear partición NTFS de ±150GB (ejemplo)
- Aplicar cambios → esta partición será sdb3

---


🛠️ 3. Crear un Disco RAW para VirtualBox

VirtualBox puede utilizar un disco físico real mediante un archivo VMDK especial.

```bash
sudo VBoxManage internalcommands createrawvmdk \
    -filename ~/win10_raw.vmdk \
    -rawdisk /dev/sdb
```

Corregir permisos:

```bash
sudo chown $USER:$USER ~/win10_raw.vmdk
sudo chmod 666 /dev/sdb
```

---


🛠️ 4. Desactivar KVM (si no, VirtualBox no arranca)

```bash
sudo rmmod kvm_intel
sudo rmmod kvm
```

Tambien está la opción de crear una "Blacklist permanente", y así evitas realizar el paso anteriro cada vez que reinicies el ordenador:

```bash
echo "blacklist kvm_intel" | sudo tee /etc/modprobe.d/blacklist-kvm.conf
echo "blacklist kvm" | sudo tee -a /etc/modprobe.d/blacklist-kvm.conf
sudo update-initramfs -u
sudo reboot
```

---


🛠️ 5. Crear y configurar la Máquina Virtual

Configuración recomendada:

Sistema → Motherboard

- ❌ Desactivar EFI (muy importante)
- ✔️ IO-APIC

Sistema → Processor

- 2 núcleos mínimo
- Nested VT-x desactivado

Display

- Video RAM: 128MB
- Graphics Controller: VMSVGA
- ❌ 3D Acceleration desactivado

Storage

- win10_raw.vmdk → SATA Port 0
- ISO de Windows → SATA Port 1
- ✔️ Use Host I/O Cache

---


🛠️ 6. Instalar Windows dentro de la VM (pero en disco real)

Arranca la VM.

1) Cuando aparezca la selección de particiones:
2) Seleccionar Drive 0 Partition 2 (sdb3)
3) Pulsar Format
4) Pulsar Next

Windows copiará archivos → expandirá → instalará características.

⚠️ ATENCIÓN:
Cuando la VM diga: "Restarting…"

APAGA LA VM inmediatamente.
(No dejes que Windows arranque dentro de la VM o romperá la instalación, bueno, más bien la complicará)

---


🛠️ 7. Arrancar Windows de forma nativa en el MacBook

1) Reiniciar el Mac
2) Mantener pulsado ALT (Option)
3) Seleccionar Windows
4) La instalación continuará en hardware real
5) Completar configuración (Idioma, teclado, WiFi, usuario…)

---


🛠️ 8. Añadir Windows al menú GRUB (dual boot)

En Ubuntu:

```bash
sudo apt install grub-efi-amd64 shim-signed
sudo mount /dev/sda1 /boot/efi
sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu
```

Activar os-prober:

```bash
sudo nano /etc/default/grub
```

Asegurarse de incluir:

```bash
GRUB_DISABLE_OS_PROBER=false
```

Actualizar GRUB:

```bash
sudo update-grub
```

Reiniciar.

GRUB debe mostrar:

- Ubuntu
- Windows Boot Manager
