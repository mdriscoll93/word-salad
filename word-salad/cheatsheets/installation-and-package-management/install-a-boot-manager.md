---
description: Introducing GRUB, GRUB2, and LILO
icon: boot-heeled
---

# Install a Boot Manager

## Overview

* Provide alternative boot locations and backup boot options
* Install and configure a boot loader such as GRUB Legacy
* Perform basic configuration changes for GRUB 2
* Interact with the boot loader

### Boot managers <a href="#boot-managers" id="boot-managers"></a>

A boot manager or boot loader is the intermediate piece of code that helps the hardware and firmware of your system load an operating system for you. This tutorial discusses the PC boot process and the three main boot loaders that are used in Linux:&#x20;

1. **GRUB**
2. **GRUB2**
3. **LILO with MBR formatted disks**

The original GRUB, now called GRUB-Legacy is no longer in active development and has largely been replaced by the newer GRUB2. Even commercial distributions such as Red Hat and SUSE Linux Server switched to GRUB2 in 2014. Development of LILO is scheduled to cease at the end of 2015.

This tutorial focuses on booting with a traditional BIOS and disks formatted with a Master Boot Record (MBR). It also covers some basic information on Extensible Unified Firmware Interface (UEFI) and its associated GUID Partition Table (GPT) and booting issues you might find with these, particularly if you need to boot both Windows® 8 and Linux on a single system.

## Boot Process Overview

Before getting into specific boot loaders, let us review how a traditional PC starts or boots. Code called BIOS (for \_B\_asic \_I\_nput \_O\_utput \_S\_ervice) is stored in non-volatile memory such as ROM, EEPROM, or flash memory. When the PC is turned on or rebooted, this code is executed. Usually it performs a power-on self test (POST) to check the machine. Finally, it loads the first sector from the master boot record (MBR) on the boot drive.

The MBR also contains the partition table, so the amount of executable code in the MBR is less than 512 bytes, which is not very much. Every disk, even a floppy, contains executable code in its MBR, even if the code is only enough to put out a message such as "Non-bootable disk in Drive A:". This code that is loaded by BIOS from this first sector is called the first stage boot loader or the stage 1 boot loader.

The standard hard drive MBR used by MS DOS, PC DOS, and Windows operating systems checks the partition table to find a primary partition on the boot drive that is marked active, loads the first sector from that partition, and passes control to the beginning of the loaded code. This new piece of code is also known as the partition boot record . The partition boot record is actually another stage 1 boot loader, but this one has just enough intelligence to load a set of blocks from the partition. The code in this new set of blocks is called the stage 2 boot loader. As used by MS-DOS nad PC-DOS, the stage 2 loader proceeds directly to load the rest of the operating system. This is how your operating system pulls itself up by its bootstraps until it is up and running.

This works fine for a system with a single operating system. What happens if you want multiple operating systems, say OS/2, Windows XP, and three different Linux distributions? You _can_ use some program (such as the DOS FDISK program) to change the active partition and reboot. This is cumbersome. Furthermore, a disk can have only four primary partitions, and the standard MBR can have only one active primary partition; it cannot boot from a logical partition. But our hypothetical example cited five operating systems, each of which needs a partition. Oops!

The solution lies in using some special code that allows a user to choose which operating system to boot. Examples include:

* **Loadlin**: A DOS executable program that is invoked from a running DOS system to boot a Linux partition. This was popular when setting up a multiboot system was a complex and risky process
* **OS/2 Boot Manager**: A program that was installed in a small dedicated partition. The partition was marked active, and the standard MBR boot process started the OS/2 Boot Manager, which has menu from which you can choose which operating system to boot
* A smart boot loader: A program that can reside on an operating system partition and is invoked either by the partition boot record of an active partition or by the master boot record. Some common Linux Boot Loaders are:
  * **LILO** - the LInux LOader
  * **GRUB** - the GRand Unified Boot Loader (now GRUB-Legacy)
  * **GRUB2** - a newer boot loader that is installed in many common distributions
  * **Syslinux** - a group of lightweight boot loaders for MS-DOS FAT filesystems (SYSLINUX), network booting (PXELINUX), bootable "El Torito" CD-ROMs (ISOLINUX), and Linux ext2, ext3, and ext4 or btrfs filesystems (EXTLINUX

Evidently, if you can pass control of the system to some program that has more than 512 bytes of code to accomplish its task, then it isn't too hard to allow booting from logical partitions, or booting from partitions that are not on the boot drive. All of these solutions allow these possibilities, either because they can load a boot record from an arbitrary partition, or because they have some understanding of what file or files to load to start the boot process.
