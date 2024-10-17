---
icon: boot
description: >-
  This tutorial helps you understand the boot sequence from BIOS to boot
  completion
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# Booting the system

## Overview

1. Give common commands to the boot loader
2. Give options to the kernel at boot time
3. Check boot events in the log files

## Booting a Computer

When you turn on a computer or restart it, the computer must load an operating system before you can do any useful work. This process is called _booting_ the computer. The computer pulls itself up by the bootstraps, using a _boot loader_.&#x20;

The process is mostly automatic, but there are times when you might need to intervene, for example to boot installation media, boot to a repair mode, or boot a different operating system to your usual default.&#x20;

I show you how Linux booting works with the common Linux boot loaders and how you can interact with and then check the process.

Some aspects of the boot process are common to most systems, but some hardware-related aspects are specific to a particular architecture. The material in this tutorial is directed specifically at x86 and x86\_64 architecture systems.&#x20;

Until relatively recently, these systems used BIOS to boot the system. A newer system using Extensible Firmware Interface (EFI) and the GUID Partition Table (GPT) was developed for the Intel Itanium and IA64 architectures to overcome several limitations of the 16-bit x86 BIOS architecture, especially for the large servers that Itanium was intended for.&#x20;

Intel stopped developing EFI in 2005 at version 1.10 and contributed it to the Unified Extensible Firmware Interface Forum, which now manages it as Unified Extensible Firmware Interface (UEFI). UEFI is gaining popularity, particularly for systems having drives larger than 2TB in size. It is also required for computers running Windows® 8.

This tutorial covers both BIOS and UEFI booting.

## BIOS or MBR boot sequence

Before we get into boot loaders, such as LILO, GRUB and GRUB2, let's review how a PC has historically started, or _booted_.&#x20;

* Code called _BIOS_ (for \_B\_asic \_I\_nput \_O\_utput \_S\_ervice) is stored in non-volatile memory such as a ROM, EEPROM, or flash memory.&#x20;
* When the PC is turned on or rebooted, this code is executed and performs a power-on self test (POST) to check the machine.&#x20;
* It also determines the boot drive from the available removable or fixed storage devices and loads the first sector from the Master Boot Record (MBR) on that drive.&#x20;
* The drive can be a traditional hard drive, solid-state drive, USB stick or drive, or a drive with removable media such as diskette, CD, or DVD. For the remainder of this tutorial, we'll focus on hard drives, but the process is similar for the other types of storage devices.

the MBR also contains the partition table, so the amount of executable code in the MBR is less than 512 bytes, which is not very much code.&#x20;

Note that every disk, even a floppy, CD, or DVD, or solid-state device such as a USB stick, contains executable code in its MBR, even if the code is only enough to put out a message such as "Non-bootable disk in drive A:". This code that is loaded by BIOS from this first sector is called the _first stage boot loader_, or the _stage 1 boot loader_.

The standard hard drive MBR used by MS DOS, PC DOS, and Windows operating systems:&#x20;

* checks the partition table to find a primary partition on the boot drive that is marked as _active_
* loads the first sector from that partition
* passes control to the beginning of the loaded code. This new piece of code is also known as the _partition boot record_.&#x20;
* The partition boot record is actually another stage 1 boot loader, but this one has just enough intelligence to load a set of blocks from the partition.&#x20;
* The code in this new set of blocks is called the _stage 2 boot loader_. As used by MS-DOS and PC-DOS, the stage 2 loader proceeds directly to load the rest of operating system. This is how your operating system pulls itself up by its bootstraps until it is up and running.

This works fine for a system with a single operating system. What happens when you want multiple operating systems, say OS/2, Windows 7, and three different Linux distributions?&#x20;

You _could_ use some program (such as the DOS FDISK program) to change the active partition and reboot. This is cumbersome. Furthermore, a disk can have only four primary partitions, and the standard MBR can have only one active primary partition; it cannot boot from a logical partition. But our hypothetical example cited five operating systems, each of which needs a partition. Oops!

Typical solutions used some special code that allowed a user to choose which operating system to boot. Several solutions have been used over the years. Examples include:

* Loadlin: A DOS executable program that is invoked from a running DOS system to boot a Linux partition. This was popular when setting up a multiboot system was a complex and risky process.
* OS/2 Boot Manager: A program that was installed in a small dedicated partition. The partition was marked active, and the standard MBR boot process started the OS/2 Boot Manager, which has menu from which you can choose which operating system to boot.
* A smart boot loader: A program that can reside on an operating system partition and is invoked either by the partition boot record of an active partition or by the master boot record. Some common Linux boot loaders are:
  * LILO, the Linux Loader
  * GRUB, the Grand Unified Boot loader (now referred to as GRUB Legacy)
  * GRUB2, a newer boot loader that is installed in many common distributions
  * Syslinux, a group of lightweight boot loaders for MS-DOS FAT filesystems (SYSLINUX), network booting (PXELINUX), bootable "El Torito" CD-ROMs (ISOLINUX), and Linux ext2/ext3/ext4 or btrfs filesystems (EXTLINUX)

Evidently, if you can pass control of the system to some program that has more than 512 bytes of code to accomplish its task, then it isn't too hard to allow booting from logical partitions, or booting from partitions that are not on the boot drive.

&#x20;All of these solutions allow such possibilities, either because they can load a boot record from an arbitrary partition, or because they have some understanding of what file or files to load to start the boot process.

## Chain Loading

When a boot manager gets control, one thing that it can load is another boot manager. This is called _chain loading_, and it most frequently occurs when the boot manager that is located in the MBR loads the boot loader that is in a partition boot record.&#x20;

This is almost always done when a Linux boot loader is asked to boot a Windows or DOS partition, but it can also be done when the Linux boot loader for one system is asked to load the boot loader for another system.&#x20;

For example, you might use LILO in one partition to chain load GRUB in another partition in order to access the GRUB menu for that partition.

## EFI and UEFI boot sequence

As noted in the introduction, EFI was developed by Intel to overcome several limitations of the x86 BIOS architecture, notably the 16-bit especially for the large servers that Itanium was intended for. Intel stopped development of EFI in 2005 at version 1.10 and contributed it to the Unified Extensible Firmware Interface Forum, which now manages it as Unified Extensible Firmware Interface (UEFI). Since 2013, the UEFI Forum has also managed the Advanced Configuration and Power Interface or ACPI, a specification for device configuration and power management.

In contrast to 16-bit BIOS, UEFI is either 32-bit or 64-bit. It is possible to have a 32-bit UEFI on a 64-bit processor (for example some Intel Atom processors used in many tablets and small notebook computers).&#x20;

The boot loader needs to match the UEFI: a 32-bit boot loader on 32-bit UEFI and a 64-bit boot loader on 64-bit UEFI. Many, but not all, UEFI implementations allow booting in either UEFI mode or in Legacy BIOS mode. Such systems allow booting with a boot loader that does not support UEFI. However, if the system only supports UEFI, you need an appropriate UEFI boot loader, such as GRUB2.

In addition to helping boot the system, UEFI supports networking and remote diagnostic capabilities as well as post-boot services such as date, time and time zone. UEFI has graphical capabilities which allow more advanced configuration and control of your system and its booting process.

The UEFI code uses an EFI system partition to help boot the system. UEFI selects code from this partition to boot the system. Boot code in this partition can be signed. Enabling a feature called secure boot will boot using only signed binaries. These signed binaries are checked against a key embedded in the machine's firmware.&#x20;

One key is provided for Microsoft's own signed binaries and another key for binaries that Microsoft signs for third parties, such as Linux distributors. After loading, the signed binary registers itself with the firmware and proceeds with booting.&#x20;

In practice, many Linux systems use a small signed binary (typically shim.efi, shimx64.efi, or shimia32.efi) which does the registration and then loads the GRUB2 EFI boot loader. The GRUB2 loader is also signed using a key embedded in the shim program or managed by the user through a Machine Owner Key (MOK) list. Another program called PreLoader performs a similar kind of function to shim. Using secure boot avoids the problem historically associated with viruses that infected the boot sector on a BIOS-based machine.

UEFI APIs allow the eventually loaded operating system to interrogate the UEFI to confirm that it was loaded using secure boot.

## Linux boot loaders

From here on, we focus on GRUB2, the boot loader included with most newer Linux distributions. LILO was used on many early Linux distributions. GRUB is newer.&#x20;

The original GRUB has now become _GRUB Legacy_, and GRUB2 is the newer version that is developed under the auspices of the Free Software Foundation (see the resources side for details).&#x20;

A new version of LILO is called _ELILO_ (which was developed for booting systems that use EFI. However, it is no longer in active development. See the resources for additional information about ELILO.

### boot process summary:

#### MBR or BIOS Systems:

1. When a PC is turned on, the Basic Input Output Service (BIOS) performs a self test.
2. When the machine passes its self test, the BIOS loads the MBR, usually from the first 512-byte sector of the boot drive. The boot drive is most often the first hard drive on the system, but might also be a diskette, CD, or USB key.
3. For a hard drive, the MBR loads a stage 1 boot loader, which is typically either the LILO or GRUB stage1 boot loader on a Linux system. This is another 512-byte, single-sector record.
4. The stage 1 boot loader usually loads a sequence of records called the stage 2 boot loader (or sometimes the stage 1.5 loader).
5. The stage 2 loader loads the operating system. For Linux, this is the kernel and possibly an initial RAM disk (initrd or initramfs).

#### UEFI systems

1. The UEFI code performs a self test.
2. When the machine passes its self test, the UEFI firmware checks the EFI partition for a boot loader. If the system is multiboot with more than one loadable kernel, the UEFI will select one. Most Linux systems will set themselves to be the first bootable system but you can use the `efibootmgr` command to set a different selection order or just to choose which system will boot next. If the system is set for secure boot, the code loaded must be signed.
3. The firmware will then load the selected binary. Typically this will be shimx64.efi which will register itself with the firmware, then load the actual GRUB2 boot loader, for example grubx64.efi. The grubx64.efi loader can be launched with shim if the system is not using secure boot, but shim must be used if the system is using secure boot.
4. The boot loader then loads the operating system, much as for BIOS systems.

To influence your system's boot process, you can:

* Change the device from which you boot. If you normally boot from a hard drive, you might need to boot from a floppy disk, a USB memory key, a CD or DVD, or a network. Setting up such alternate boot devices requires your BIOS or UEFI to be appropriately configured and can require a particular keystroke during boot to display choices.&#x20;
* Figure 1 illustrates the choices on one of my BIOS systems.&#x20;
* Figure 2 illustrates how this looks on one of my UEFI systems. The method for setting up boot devices and selecting a boot device at startup is specific to your system and its BIOS. It is also beyond the scope of this LPI objective's requirements, so consult your system documentation.\


<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Interact with the boot loader to select which of several possible configurations to boot or to edit the boot configuration. You learn how to do this for GRUB2 in this tutorial.
* Pass parameters to the kernel to control the way that your kernel starts the system once it has been loaded by the boot loader.

### The GRUB2 boot menu <a href="#the-grub2-boot-menu" id="the-grub2-boot-menu"></a>

When GRUB2 loads, it presents information from a configuration file, normally located in /boot/grub/grub.cfg or /boot/grub2/grub.cfg. Listing 1 shows a small part of a grub.cfg file for a Fedora 22 system. I broke some lines for publication and indicate these with a trailing backslash (\\).

### Listing 1. GRUB2 configuration menu entry to boot Fedora 22 

```bash
###BEGIN /etc/grub.d/10_linux ###
menuentry 'Fedora (4.0.6‑300.fc22.x86_64) 22 (Twenty Two)' ‑‑class fedora 
‑‑class gnu‑linux ‑‑class gnu ‑‑class os ‑‑unrestricted $menuentry_id_option 
'gnulinux‑4.0.6‑300.fc22.x86_64‑advanced‑7aefe7a0‑97d5‑45ec‑a92e‑00a6363fb1e4' {
 load_video
 set gfxpayload=keep
 insmod gzio
 insmod part_msdos
 insmod ext2
 set root='hd0,msdos5'
 if [ x$feature_platform_search_hint = xy ]; then
   search ‑‑no‑floppy ‑‑fs‑uuid ‑‑set=root ‑‑hint‑bios=hd0,msdos5 
        ‑‑hint‑efi=hd0,msdos5 ‑‑hint‑baremetal=ahci0,msdos5  
        7aefe7a0‑97d5‑45ec‑a92e‑00a6363fb1e4
 else
   search ‑‑no‑floppy ‑‑fs‑uuid ‑‑set=root 7aefe7a0‑97d5‑45ec‑a92e‑00a6363fb1e4
 fi
 linux16 /boot/vmlinuz‑4.0.6‑300.fc22.x86_64 
        root=UUID=7aefe7a0‑97d5‑45ec‑a92e‑00a6363fb1e4 ro rhgb quiet 
 initrd16 /boot/initramfs‑4.0.6‑300.fc22.x86_64.img
}
```

When GRUB2 reads the configuration file, it normally presents a menu such as the one shown in . Typically, you have a short timeout, after which a default entry is booted. The default entry is highlighted, and you might see a timer counting down. Here, you see the menu entry from the previous listing as the default choice. If no action is taken, it boots in 35 seconds.

#### Figure 3. Selecting a boot choice in GRUB2

<figure><img src="../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

When the menu is displayed, you can press Enter to boot the selected entry immediately, or you can press another key to stop the timeout. You can then highlight and boot another entry, enter `e` to edit a selected entry, or `c` to enter a GRUB2 command prompt. **Note:** if your timeout is set to 0, GRUB2 proceeds immediately to boot your system. In this case, you need to boot rescue media from another device.

When you use GRUB2 on a UEFI system, you will usually have an additional menu entry for the UEFI firmware which allows you to interact with the firmware. Figure 4 shows an example from OpenSUSE Tumbleweed.

#### Figure 4. Using GRUB2 to access the UEFI interface

<figure><img src="../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

In addition to using the UEFI firmware itself to configure the default boot order, you can use the `efibootmgr` command to check the boot order, set the entry to boot on the next reboot, or reset the default order. Listing 2 shows the commands needed to display the boot order, reset the default order so that Fedora (0000) is chosen first, and finally set the next boot to Ubuntu (0002).

#### **Listing 2. Using the `efibootmgr` commands**

```bash
[ian@attic5-f36 ~]$ sudo efibootmgr
BootCurrent: 0000
Timeout: 10 seconds
BootOrder: 0001,0002,0000,0005,000E,000F
Boot0000* fedora
Boot0001* opensuse-secureboot
Boot0002* ubuntu
Boot0005* UEFI OS
Boot000E* UEFI OS
Boot000F* ubuntu
[ian@attic5-f36 ~]$ # set boot order using  -o or --bootorder
[ian@attic5-f36 ~]$ sudo efibootmgr -o 0000,0001,0002,0005,000E,000F
BootCurrent: 0000
Timeout: 10 seconds
BootOrder: 0000,0001,0002,0005,000E,000F
Boot0000* fedora
Boot0001* opensuse-secureboot
Boot0002* ubuntu
Boot0005* UEFI OS
Boot000E* UEFI OS
Boot000F* ubuntu
[ian@attic5-f36 ~]$ # set next boot (one time) using -n or --bootnext
[ian@attic5-f36 ~]$ sudo efibootmgr -n 2
BootNext: 0002
BootCurrent: 0000
Timeout: 10 seconds
BootOrder: 0000,0001,0002,0005,000E,000F
Boot0000* fedora
Boot0001* opensuse-secureboot
Boot0002* ubuntu
Boot0005* UEFI OS
Boot000E* UEFI OS
Boot000F* ubuntu
```

## Kernel Parameters

Kernel parameters (sometimes called boot parameters) are used to supply the kernel with information about hardware parameters that it might not determine on its own, to override values that it might otherwise detect, or to avoid detection of inappropriate values.&#x20;

Some older kernel levels required a parameter to enable large memory support on systems with more than a certain amount of RAM. You might want to boot in single-user mode to repair your system, boot an SMP system in uniprocessor mode, or specify an alternate root filesystem. Or you might need to specify a previously working kernel so that you can find out why your newly built custom kernel won't boot. You can accomplish all of these tasks using boot parameters.

Boot parameters are supplied on the kernel command line. Most have the following form:

```bash
name[=value_1][,value_2]...[,value_10]
```

Where 'name' is a unique keyword that identifies what part of the kernel should receive the associated values. The parameters are checked in the following order:

1. The kernel checks to see if the argument is any of the special arguments 'root=', 'nfsroot=', 'nfsaddrs=', 'ro', 'rw', 'debug' or 'init'.
2. It walks a list of setup functions to see if the specified argument string has been associated with a setup function.
3. Anything of the form 'foo=bar' that is not accepted as a setup function is then interpreted as an environment variable to be set.
4. Any remaining arguments that were not picked up by the kernel or interpreted as environment variables are then passed onto process one, which is usually the init program.

You can find out more about boot parameters from the man pages using info bootparam`infobootparam` or man bootparam`manbootparam`. Up-to-date information is also supplied in the kernel-parameters.txt file, which should be available in the kernel-doc package. **Note:** At the time of writing, Fedora 22 does not appear to have a kernel-doc package or kernel-parameters file.

Suppose you want to modify kernel parameters or add new ones or otherwise change the values from the menu entry in the grub.cnf file. When you see the GRUB2 menu, you can edit the entry that you want to modify by selecting it and then pressing e. You then see a screen like Figure 5, where you recognize information from the first few lines of Listing 1.\


#### Figure 5. Editing the Fedora 22 GRUB2 menu entry

<figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

The most common argument that is passed to the init process is the word 'single' which instructs it to boot the computer in single user mode, and not launch all the usual daemons. You typically use this for some kind of rescue operation. If you move the cursor down, the screen will scroll and you will see the line that starts with 'linux16' and ends with 'ro rhgb quiet'.&#x20;

For our example, we'll position the cursor at the end of this line and then use the backspace key to erase the words 'quiet', then 'rhgb'. This will stop the Red Hat Graphical Boot screen that fedora normally display during boot and also stop suppressing many of the messages that are normally generated. Finally we'll add the word 'single' to the line to boot in single user mode. The display is shown in Figure 6.

#### Figure 6. Setting Fedora 22 to boot in single user mode

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption><p>Now press Ctrl-x to boot the system into single user mode. After several messages scroll by, your screen has a few lines like Listing 3.</p></figcaption></figure>

You can now give the root password and do whatever you need to rescue the system. You can then reboot, or continue into your normal mode of running.

Exactly how single user mode appears differs by system. Some systems take you directly to a root prompt with no need to enter a password. Others, like our example, require a password. How you proceed also differs according to whether your system uses the classic System V init process or the newer upstart or systemd.

Now let's look more at the different init processes.

## The init process

When the kernel finishes loading, it usually starts `/sbin/init`. This program remains running until the system is shut down. It is always assigned process ID 1, On systems using the traditional System V init process, or on systems using the newer Upstart initialization, the process will be named init. However, on systems using the Systemd initialization process, /sbin/init is a link to the systemd init process in /lib/systemd/systemd. We illustrate this for Fedora 22 in Listing 4.

#### init process

```bash
['ian@atticf22 ~]$ ls ‑l /sbin/init
lrwxrwxrwx. 1 root root 22 Jun  9 09:16 /sbin/init ‑> ../lib/systemd/systemd
[ian@atticf22 ~]$ ps ‑‑pid 1
  PID TTY          TIME CMD
    1 ?        00:00:02 systemd
```

In normal operation, a traditional System V init process runs in one of 4 possible multiuser _runlevels_, 2 through 5. Different systems use runlevels differently, but the highest one used is usually for graphical operation with the X Windows system.&#x20;

The file /etc/inittab controls which processes are started at each runlevel. Systems using upstart have a concept of _jobs_, while systems with systemd use _units_ to define what things are started and each maps appropriate jobs or units to the traditional System V runlevels.&#x20;

This allows many commands such as `telinit` to have meaning with the new startup processes. Many of the systemd units are stored in /usr/lib/systemd/system/. Listing 5 shows the mapping from runlevel-equivalent targets to the corresponding systemd targets.

#### **Listing 5. Systemd runlevel target units**

```bash
[ian@atticf22 l‑lpic1‑101‑2]$ cd /usr/lib/systemd/system/
[ian@atticf22 system]$ ls ‑l runlevel*.target
lrwxrwxrwx. 1 root root 15 Jun  9 09:16 runlevel0.target ‑> poweroff.target
lrwxrwxrwx. 1 root root 13 Jun  9 09:16 runlevel1.target ‑> rescue.target
lrwxrwxrwx. 1 root root 17 Jun  9 09:16 runlevel2.target ‑> multi‑user.target
lrwxrwxrwx. 1 root root 17 Jun  9 09:16 runlevel3.target ‑> multi‑user.target
lrwxrwxrwx. 1 root root 17 Jun  9 09:16 runlevel4.target ‑> multi‑user.target
lrwxrwxrwx. 1 root root 16 Jun  9 09:16 runlevel5.target ‑> graphical.target
lrwxrwxrwx. 1 root root 13 Jun  9 09:16 runlevel6.target ‑> reboot.target
```

Traditionally single user mode was equivalent to runlevel 1. Indeed, you could have typed '1' instead of 'single' to bring the system up in single user mode (runlevel 1). Similarly '3' would bring the system up in full multiuser mode with networking but without the X Windows system while '5' would bring the system up to the full graphical desktop. You can see from the above symbolic links that systemd now uses the rescue.target unit to bring the system up in single user mode. So specifying:

* single
* 1
* s
* systemd.unit=runlevel1.target
* systemd.unit=rescue.target

are all equivalent ways of getting to single user mode. You can find out more about systemd units and targets in the man pages. Try info systemd`infosystemd` or man systemd`mansystemd`.

Whichever initialization you use, the initialization program boots the rest of your system by running a series of scripts. For System V init, these scripts typically live in /etc/rc.d/init.d or /etc/init.d. They perform services such as setting the system's hostname, checking the filesystem for errors, mounting additional filesystems, enabling networking, starting print services, and so on.&#x20;

When the scripts complete, `init` starts a program called`getty`, which displays the login prompt on consoles. Graphical login screens are handled with a graphical display manager, such as GDM for Gnome.

If your system loads a kernel but cannot run `init` successfully, you can try to recover by specifying an alternate initialization program. For example, specifying `init=/bin/sh` boots your system into a shell prompt with root authority, from which you might be able to repair the system.

Needless to say, if you have to apply the same set of additional parameters every time you boot, you should add them to the configuration file.

## Initial RAM Disk

You might have noticed the last couple of lines in our GRUB2 example configuration, shown again in Listing 6. The linux16 line specifies what kernel to load and the initrd16 lines specifies ad _Initial RAM Disk_. You've already seen how to modify the kernel parameters, but what is an initial RAM disk?

#### **Listing 6. GRUB2 example configuration**

```bash
linux16 /boot/vmlinuz‑4.0.6‑300.fc22.x86_64 
root=UUID=7aefe7a0‑97d5‑45ec‑a92e‑00a6363fb1e4 ro rhgb quiet 
initrd16 /boot/initramfs‑4.0.6‑300.fc22.x86_64.img
```

Back at the dawn of UNIX, devices were few and kernels were small. Most devices were physically attached to a computer all the time and a kernel was typically built to support exactly the installed hardware. As time went by and the number and type of devices proliferated, device support was frequently shifted into loadable kernel modules. Kernel modules are like a library of functions with well-defined entry points that allow a kernel to use a device whose support was not compiled in to the kernel. This allowed a kernel to be loaded and then load support only for devices that were actually installed in the system. But without full disk and filesystem support, how do you find and load these modules from disk?

The answer is the initial RAM disk or _initrd_ or _initramfs_, which is a file containing loadable kernel modules. It is usually compressed and is loaded into RAM by the boot loader. The kernel is given access to it as if it were a mounted file system. If you examine an initrd file using the `file` command, you can find what compression it uses. If you then uncompress a copy of it, you usually can find a `cpio` archive inside. On some systems (such as Fedora since Fedora 12), the initrd file is created with a utility called `dracut`. In this case, it appears as a cpio archive, but if you unpack it, you might not find very much: much less than the initrd file size would suggest. You can use the `lsinitrd` command to list the contents of an initrd file.

## Boot Events

### `dmesg`

#### Listing 7. Partial `dmesg` output

```bash
[root@atticf22 ~]# dmesg | head ‑n 30
[    0.000000] Initializing cgroup subsys cpuset
[    0.000000] Initializing cgroup subsys cpu
[    0.000000] Initializing cgroup subsys cpuacct
[    0.000000] Linux version 4.0.6‑300.fc22.x86_64 (mockbuild@bkernel02.phx2.fedoraproject.org) 
(gcc version 5.1.1 20150618 (Red Hat 5.1.1‑4) (GCC) ) #1 SMP Tue Jun 23 13:58:53 UTC 2015
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz‑4.0.6‑300.fc22.x86_64 
root=UUID=7aefe7a0‑97d5‑45ec‑a92e‑00a6363fb1e4 ro single
[    0.000000] tseg: 0000000000
[    0.000000] e820: BIOS‑provided physical RAM map:
[    0.000000] BIOS‑e820: [mem 0x0000000000000000‑0x000000000009ebff] usable
[    0.000000] BIOS‑e820: [mem 0x000000000009ec00‑0x000000000009ffff] reserved
[    0.000000] BIOS‑e820: [mem 0x00000000000e4000‑0x00000000000fffff] reserved
[    0.000000] BIOS‑e820: [mem 0x0000000000100000‑0x00000000cff7ffff] usable
[    0.000000] BIOS‑e820: [mem 0x00000000cff80000‑0x00000000cff8dfff] ACPI data
[    0.000000] BIOS‑e820: [mem 0x00000000cff8e000‑0x00000000cffcffff] ACPI NVS
[    0.000000] BIOS‑e820: [mem 0x00000000cffd0000‑0x00000000cfffffff] reserved
[    0.000000] BIOS‑e820: [mem 0x00000000ff700000‑0x00000000ffffffff] reserved
[    0.000000] BIOS‑e820: [mem 0x0000000100000000‑0x000000012fffffff] usable
[    0.000000] NX (Execute Disable) protection: active
[    0.000000] SMBIOS 2.5 present.
[    0.000000] DMI: System manufacturer System Product Name/M3A78‑EM, BIOS 2003    10/12/2009
[    0.000000] e820: update [mem 0x00000000‑0x00000fff] usable ==> reserved
[    0.000000] e820: remove [mem 0x000a0000‑0x000fffff] usable
[    0.000000] e820: last_pfn = 0x130000 max_arch_pfn = 0x400000000
[    0.000000] MTRR default type: uncachable
[    0.000000] MTRR fixed ranges enabled:
[    0.000000]   00000‑9FFFF write‑back
[    0.000000]   A0000‑EFFFF uncachable
[    0.000000]   F0000‑FFFFF write‑protect
[    0.000000] MTRR variable ranges enabled:
[    0.000000]   0 base 000000000000 mask FFFF80000000 write‑back
[    0.000000]   1 base 000080000000 mask FFFFC0000000 write‑back
```

The kernel ring buffer is also used for some events after the system is booted. These include certain program failures and hot-plug events. Listing 8 shows several entries related to plugging in a USB memory key. Note the warning that the volume had not been properly unmounted.

**Listing 8. Later events in kernel ring buffer**

```bash
[root@atticf22 ~]#dmesg | tail ‑n 21
[68209.847331] usb 4‑2.4: new full‑speed USB device number 5 using ohci‑pci
[68209.928078] usb 4‑2.4: device descriptor read/64, error ‑62
[68225.055771] usb 4‑2.4: device descriptor read/64, error ‑110
[68225.231218] usb 4‑2.4: new full‑speed USB device number 6 using ohci‑pci
[68225.340868] usb 4‑2.4: New USB device found, idVendor=19ae, idProduct=2403
[68225.340875] usb 4‑2.4: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[68225.340877] usb 4‑2.4: Product: MSC
[68225.340879] usb 4‑2.4: Manufacturer: ATMEL ASF
[68225.340881] usb 4‑2.4: SerialNumber: 123123123123
[68225.343447] usb‑storage 4‑2.4:1.0: USB Mass Storage device detected
[68225.352501] scsi host15: usb‑storage 4‑2.4:1.0
[68226.357737] scsi 15:0:0:0: Direct‑Access     ATMEL    SD/MMC Card Slot 1.00 PQ: 0 ANSI: 3
[68226.358727] sd 15:0:0:0: Attached scsi generic sg7 type 0
[68226.367683] sd 15:0:0:0: [sdf] 7774208 512‑byte logical blocks: (3.98 GB/3.70 GiB)
[68226.377646] sd 15:0:0:0: [sdf] Write Protect is off
[68226.377653] sd 15:0:0:0: [sdf] Mode Sense: 0f 00 00 00
[68226.387610] sd 15:0:0:0: [sdf] No Caching mode page found
[68226.387618] sd 15:0:0:0: [sdf] Assuming drive cache: write through
[68226.442450]  sdf:
[68226.484318] sd 15:0:0:0: ]sdf] Attached SCSI removable disk
[68227.882986] FAT‑fs (sdf): Volume was not properly unmounted. Some data may be corrupt.
Please run fsck.
```

## Logging System Messages

Once your system has started to the point of running `/sbin/init`, the kernel still logs events in the ring buffer as you just saw. However, processes use a daemon to log messages. In traditional System V init systems, this is the syslogd daemon, which usually logs to /var/log/messages. Systemd-based systems use the systemd-journald daemon to log messages, which can be inspected using the `journalctl` command.

### /var/log/messages

In contrast to the ring buffer, each syslog line has a timestamp, and the file persists between system restarts. This file is where you should first look for errors that occurred during the init scripts stage of booting.

Most daemons have names that end in 'd'. Listing 9 shows how to see the last few daemon status messages after a reboot. It also shows the last 10 messages of any type. This example is from a CentOS 6 system.

#### Listing 9. Daemon messages from /var/log/messages

```bash
[root@attic4‑cent ~]#grep "Jul 22.........[^:]*d\:" /var/log/messages
Jul 22 21:45:23 attic4‑cent rsyslogd: [origin software="rsyslogd" swVersion="5.8.10" 
x‑pid="2051" x‑info="https://www.rsyslog.com/"] start
Jul 22 21:45:23 attic4‑cent cpuspeed: Enabling ondemand cpu frequency scaling governor
Jul 22 21:45:26 attic4‑cent acpid: starting up
Jul 22 21:45:26 attic4‑cent acpid: 1 rule loaded
Jul 22 21:45:26 attic4‑cent acpid: waiting for events: event logging is off
Jul 22 21:45:27 attic4‑cent acpid: client connected from 242368:68Jul 22 21:45:27 attic4‑cent acpid: 1 client rule loaded
Jul 22 21:45:32 attic4‑cent abrtd: Init complete, entering main loop
Jul 22 21:45:33 attic4‑cent libvirtd: Could not find keytab file: /etc/libvirt/krb5.tab: 
No such file or directory
[root@attic4‑cent ~]#tail /var/log/messages
Jul 22 21:46:00 attic4‑cent kernel: XFS (sda6): Mounting Filesystem
Jul 22 21:46:00 attic4‑cent kernel: XFS (sda6): Ending clean mount
Jul 22 21:46:00 attic4‑cent kernel: TECH PREVIEW: btrfs may not be fully supported.
Jul 22 21:46:00 attic4‑cent kernel: Please review provided documentation for limitations.
Jul 22 21:46:00 attic4‑cent kernel: Btrfs loaded
Jul 22 21:46:00 attic4‑cent kernel: device fsid d9cb309d‑e15d‑4b00‑9b2f‑e3cc08db412c 
devid 1 transid 52 /dev/sda9
Jul 22 21:46:00 attic4‑cent kernel: btrfs: disk space caching is enabled
Jul 22 21:46:00 attic4‑cent kernel: BTRFS: couldn't mount because of unsupported 
optional features (140).
Jul 22 21:46:00 attic4‑cent kernel: btrfs: open_ctree failed
Jul 22 21:46:47 attic4‑cent pulseaudio[3252]: ratelimit.c: 9 events suppressed
```

Shutdown events are also logged, so if your system is not shutting down cleanly, you might be able to find the cause in the log information that is left from the last shutdown.

You can also find logs for many other system programs in /var/log. For example, you can see the startup log for your X Window system.

### `journalctl`

Along with the large amount of function that has come under the new systemd umbrella is a new journalling function managed by the `systemd-journald` daemon. If your system is using this daemon, your log is managed by it and you can use the `journalctl` command to view the messages. Listing 10 shows the first page of output from the `journalctl -xb` command which displays journal entries from the current boot.

#### Listing 10. **Using `journalctl -xb` to display log entries from the current boot**

```bash
‑‑ Logs begin at Tue 2015‑06‑02 12:38:08 EDT, end at Wed 2015‑07‑22 22:04:23 EDT
Jul 22 22:02:25 atticf20 systemd‑journal[106]: Runtime journal is using 8.0M (ma
Jul 22 22:02:25 atticf20 systemd‑journal[106]: Runtime journal is using 8.0M (ma
Jul 22 22:02:25 atticf20 kernel: Initializing cgroup subsys cpuset
Jul 22 22:02:25 atticf20 kernel: Initializing cgroup subsys cpu
Jul 22 22:02:25 atticf20 kernel: Initializing cgroup subsys cpuacct
Jul 22 22:02:25 atticf20 kernel: Linux version 4.0.6‑300.fc22.x86_64 (mockbuild@
Jul 22 22:02:25 atticf20 kernel: Command line: BOOT_IMAGE=/boot/vmlinuz‑4.0.6‑30
Jul 22 22:02:25 atticf20 kernel: tseg: 0000000000
Jul 22 22:02:25 atticf20 kernel: e820: BIOS‑provided physical RAM map:
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x0000000000000000‑0x0000000000
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x000000000009ec00‑0x0000000000
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x00000000000e4000‑0x0000000000
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x0000000000100000‑0x00000000cf
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x00000000cff80000‑0x00000000cf
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x00000000cff8e000‑0x00000000cf
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x00000000cffd0000‑0x00000000cf
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x00000000ff700000‑0x00000000ff
Jul 22 22:02:25 atticf20 kernel: BIOS‑e820: [mem 0x0000000100000000‑0x000000012f
Jul 22 22:02:25 atticf20 kernel: NX (Execute Disable) protection: active
Jul 22 22:02:25 atticf20 kernel: SMBIOS 2.5 present.
Jul 22 22:02:25 atticf20 kernel: DMI: System manufacturer System Product Name/M3
Jul 22 22:02:25 atticf20 kernel: e820: update [mem 0x00000000‑0x00000fff] usable
lines 1‑23
```
