---
author: ["John Nguyen"]
title: "Dual Booting Arch Linux with Windows"
description: "A simple tutorial on installing Arch Linux using archinstall and dual booting with Windows"
date: "2025-10-07"
type: post
translationKey: pagefind
draft: false
tags:  ["linux", "os", "arch", "pc"]
categories: ["tutorials", "pc"]
cover: '/images/archwindows.png'
---

![alt text](/images/archwindows.png)

# Why Dual Boot?
Dual booting lets you have two operating systems (OS) on the same machine. This differs from a Virtual Machine which nests an operating system on a host machine. Virtual machines often don't have GPU acceleration and are more restricted on hardware resources since they have to share with the host system. Virtualization can also be buggy and fall short of the full OS experience. Dual booting lets you pick between which OS you want to use and take full advantage of your PC hardware. People often dual boot Linux and Windows so they can use the benefits of Linux such as privacy and having full control of the OS, while keeping Windows as a fallback for exclusive software.

## Two SSDs
To dual boot, I recommend obtaining a second SSD to install your second operating system. This way, you can avoid changing partitions on your existing SSD and avoid risking the loss of your data. A separate SSD ensures your new OS won't touch the existing data. 

## Obtain and flash your ISO
For my dual boot system, I chose Arch Linux. This provides a minimal OS and opens the door fully to customization. Any apps and services are on the OS because I installed them myself. This means there won't be any bloat like the ones that come with Windows. Arch also is the underlying kernel on the SteamDeck, which is able to run Windows games on Linux. Heading to the arch wiki page https://archlinux.org/download/, you can find a mirror to download the arch .iso file. Then you can flash a separate USB with this .iso as your installation media. You will need a USB that you are okay with wiping its data. Plug in your USB. To flash the USB, you can use Rufus if you are on Windows https://rufus.ie/en/ or Balena Etcher if you are on Linux. Select your .sio file, and USB drive. You can usually leave the defaults and start.

![alt text](/images/rufus.png)

## BIOS
To select your USB to boot, reboot your machine and boot into BIOS. This can be done by pressing F12, Delete, or ESC or some other key depending on your motherboard manufacturer. Once you are in the BIOS, navigate to the boot menu and select your USB to boot in. I recommend disabling Secure Boot if you are installing Arch.

## Installing Arch with archinstall
Newer arch iso files come with archinstall pre-installed. archinstall is a utility tool that automates the installation of Arch so you don't have to manually type out the commands. Before using it, I recommmend updating the program. You will want to setup your WiFi first. Follow the steps from the Arch wiki using iwctl: https://wiki.archlinux.org/title/Iwd#iwctl. Then perform an upgrade command to your packages and then run archinstall:

        sudo pacman -Syu
        
        archinstall

## Archinstall
Archinstall tends to get updated so the settings may be reordered. In this utility, you'll want to setup your time, language, mirror region, keyboard layout, user account, audio drivers, initial packages, partitions, bootloader, and desktop environment. For dual booting, you can use GRUB or systemd-boot in the bootloader. I selected systemd-boot though, GRUB may be easier.
As of now, the settings I kept my language to english, mirror region to United States, audio drivers to pulse audio, set my desktop environment to Hyprland with sddm as the greeter, and let the archinstall do the best partitions. Make sure to select the SSD that doesn't have your existing OS, otherwise you will overwrite the SSD and lose all your data! 

## BIOS Boot order
Once you install arch linux, reboot into BIOS. Change the boot option #1 to the SSD with Linux Boot Manager or whichever OS you would like to be default. Then boot into Linux. You will want to add the Windows efi to your systemd-boot to be able to see Windows as an option.

## Adding Windows to systemd-boot
Boot into arch linux and run the following

1. Run lsblk -f to see your SSDs

        lsblk -f

2. Find the SSD device name for Windows. It usually will have a file system NTFS. 

3. Mount the Windows EFI partition

        sudo mount /dev/<<devicename>>  /mnt/win_efi

3. Copy the EFI partition to the Linux EFI partition. 

        sudo cp -rv /mnt/win_efi/EFI/Microsoft /boot/EFI/

4. On next reboot, you should see two options.

![alt text](/images/dualboot.png)

# Conclusion

![alt text](/images/archdesktop.png)


Now I am able to boot into Arch Linux as my default or select Windows 11 whenever I want to run specific software like MS office or Adobe. There are other ways to get around ditching Windows itself. Proton lets you play Windows game from Steam almost natively. Using Docker and remote desktops lets you run Virtual Machines to run Windows apps with tools like WinBoat and WinApps. For me, I like to have Windows as backup for some games and other apps. Multiplayer games that have anti-cheats or require Secure Boot (like Battlefield 6) won't work on Arch, so I have to boot into Windows to play them. Arch Linux can break or not work out of the box, so I would have to be ready to fix these issues myself but having a backup OS can come in clutch if I am in an emergency. Additionally, if I ever want to distro hop, I can leave my Windows OS as a stable backup while I mess around with different Linux distros.