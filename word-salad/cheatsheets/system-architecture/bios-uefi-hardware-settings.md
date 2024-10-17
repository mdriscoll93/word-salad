---
icon: browsers
description: >-
  Determine and configure hardware settings --  cover some common hardware
  settings that you might make on current computers, and explore the commands
  that Linux has for listing and managing hardware.
---

# BIOS / UEFI Hardware Settings

## Overview

* [ ] Enable / Disable integrated peripherals
* [ ] Distinguish different types of mass storage devices
* [ ] Know which hardware resources devices use
* [ ] Use tools to list and manipulate devices including USB drives
* [ ] Understand `sysfs`, `procfs`, `udev`, and `dbus`

## Hardware Settings

In modern systems, we usually configure settings by using either BIOS or UEFI firmware. (_Unless a setting is specific to UEFI, I use the term BIOS settings to cover both BIOS and UEFI settings_)

You can access BIOS settings when you turn on your computer. Usually you do this by pressing a key such as Del, F1, or F8 after turning on the computer but before the operating system load starts.

* As machines have become faster and solid-state drives (SSDs) have reduced boot time to a few seconds, you might find that your system has a special button or key sequence to use.
* Check your system documentation to find out how to access the BIOS settings.

The examples that are shown here illustrate the kinds of settings you might find, but the actual layout of your BIOS interface will probably be different from mine.

### Figure 1. BIOS main screen with date and time settings

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption><p>example of a BIOS main screen for a system</p></figcaption></figure>

some things to do from this screen:

* change system date and time
* change parameters for legacy drives
* see summary of installed drives

If your system supports legacy peripheral interfaces, you might have a place to configure them. In my case, I can use the Advanced menu to configure the serial and parallel port interrupt requests (IRQs) and I/O port starting addresses.

### F**igure 2. Configuring serial, parallel, and on-board devices with BIOS**

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Your BIOS can also have settings that you can use to boot without a keyboard or mouse. When there is a boot error or maybe a change in configuration (such as the addition of memory), many systems — particularly older ones — require you to press a key to enter BIOS setup. What happens if the system has no keyboard?

* Today, you might plug in a Universal Serial Bus (USB) keyboard, but older systems with PS2 keyboard connectors could sometimes be damaged when a keyboard was plugged in
* So, on many BIOS systems, you can disable either the keyboard itself or the warning that you might get if the keyboard is not present.

### **Figure 3. Disabling keyboard or keyboard boot error with BIOS**

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>Another important setting that you might need to make is the order of boot devices.</p></figcaption></figure>

### Figure 4. Configuring boot device order with BIOS

<figure><img src="../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption><p>For UEFI systems, many settings are similar to those for traditional BIOS, including date, time, and whether on-board devices are enabled or not</p></figcaption></figure>

### Figure 5. Configuring date, time, and on-board devices with UEFI

<figure><img src="../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

One major difference with UEFI systems is, of course, support for UEFI booting. Your system might support UEFI-only booting or it might also support legacy BIOS-like booting.

When I choose Legacy Support, I can boot a legacy system or a UEFI system. My system will only boot UEFI systems if I choose UEFI.

### Figure 6. Choosing UEFI boot mode

<figure><img src="../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

If you select UEFI-only booting, then you will probably not see some items that are related to legacy support, such as the legacy USB support option that you saw in Figure 5

You will also probably have options for support of secure boot, which allows only signed binaries to be booted.

### Figure 7. Secure boot options with UEFI

<figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

Figure 7 shows some of the security options that you might see if you select UEFI-only booting. On my system, there are both status values and options that you can change.

For example, the Platform Mode setting of User confirms that security keys have been installed. If no keys are installed, the value is likely to be Setup instead. I can use the Reset to Setup Mode to install new keys, or I can use the Restore Factory Keys to restore the preinstalled factory keys.

## Mass Storage Devices

Computers use several types of mass storage, and this storage can be attached in various ways. Today, most mass storage devices use standard interfaces, and either the device is detected when connected or the BIOS configuration is mostly automatic.

Later in this tutorial, you'll see how to determine which resources a device needs. For now, let's look at the different types of common mass storage devices.

### Hard Drives

Hard drives come in a sealed unit and contain several recording surfaces or platters. A bit of history:

* The ST-506 introduced in 1980 had a 5MB capacity and used a controller card to translate requests from the computer to the internal commands needed to access the data on the disk platters.
* In 1984, IBM introduced the IBM PC/AT computer with a 16-bit AT bus, later known as an Industry Standard Architecture (ISA) bus. The AT attachment interface (ATA) standardized the interface between the controller card and the computer, preserving compatibility with the ST-506 command set.
* In the early 1980s, Western Digital moved the controller function inside the drive housing under the name Integrated Drive Electronics (IDE). You will find several later standards and terms, including ATA-1, ATA-2, ATA-4, and EIDE, among others.
* When Serial ATA (SATA) was introduced in 2003, the original ATA interface became known as Parallel ATA (PATA).

Another popular disk-attachment interface is the Small Computer System Interface (SCSI). SCSI was developed around 1978 for attaching various devices, including hard drives and printers. SCSI disks are still popular in larger servers.

The original ATA interface was designed for hard drives. The ATA interface was extended to the ATA Packet Interface (ATAPI) for use with other devices, such as floppy drives, Zip drives, or CD drives. ATAPI added capability to detect media presence, or eject media, among other features not needed for a normal hard drive. The ATAPI interface can carry SCSI commands in packets and thus support various device types.

Today, internal hard drives usually use either SCSI or SATA attachment. Drives can also be attached externally using USB, IEE-1394 (FireWire), External Serial Advanced Technology Attachment (eSATA), or Apple's Thunderbolt.

{% hint style="info" %}
Hard drives can be partitioned to divide their space for different uses, and they can be combined into arrays for redundancy. Hard drives can also be grouped together using tools such as Logical Volume Manager to make several smaller disks appear as one or more larger ones.
{% endhint %}

### CD and DVD drives <a href="#cd-and-dvd-drives" id="cd-and-dvd-drives"></a>

CD, DVD, and Blu-Ray drives use optical storage to hold much more data than a floppy drive. Early CD drives were often attached through a sound card, and the interfaces were many and varied. Later CD and then DVD drives standardized on the ATAPI interface.&#x20;

Today, you will find CD drives internally connected using SATA, or externally connected using the same kinds of interfaces used for hard drives. Software packages are often distributed on CD or DVD, which were initially read-only media. Now, using devices and media, users can create (_burn_) their own CDs and DVDs for backup or data storage.

### Flash or thumb drives

Perhaps the most common means of exchanging data today is the flash or thumb drive. These drives use nonvolatile RAM to store data. They are much more reliable, faster, and higher-capacity than floppy disks and can be reused many times, unlike CD or DVD media. Most are about the size of a human thumb — hence the name — and usually attach to a USB port.&#x20;

Most support USB 2.0, which has a maximum speed of 480Mbps. Newer devices support USB 3.0 with a speed of up to 5Gbps. At the time of writing, typical sizes range from 1GB to 256GB, with a few larger 512GB and 1TB models available.&#x20;

You can partition thumb drives like hard drives. If you download a new Linux distribution, you might burn it to a USB thumb drive so you can install it from that device.

### Solid-state drives

Solid-state drives (SSDs) are a newer form of drive that is being used in many notebook computers. Compared to the traditional hard drive, SSDs are lighter and faster, use less power, and do not suffer from mechanical failure. Initially, SSDs were often housed like 2.5-inch notebook drives and used the same SATA interfaces. To handle even thinner laptops, a variant called mini-SATA or mSATA was developed. The connector is smaller than a standard SATA connector and drives are approximately 1 inch by 2 inch and very thin.

Next Generation Form Factor (NGFF) SSDs were developed to replace mSATA. They are essentially similar to SATA drives but use a new connector known as M.2. The M.2 connector supports USB and PCI Express (PCIe) devices if the motherboard supports these. Earlier motherboards may only support NGFF M.2 devices.

SATA interfaces can limit the theoretical capabilities of an SSD. A newer attachment called NVM Express (NVMe) has been developed for PCIe-based SSDs. The specification is designed to better use SSDs over PCIe. These also use the M.2 connector and may operate at SATA speeds or even faster PCIe speeds.

## Virtual files for hardware and system information

{% hint style="info" %}
Several virtual file systems provide an in-memory look at your system as the kernel sees it. These file systems are virtual in the sense that no real files exist on storage devices.&#x20;

Rather, the virtual filesystems provide insight into kernel data structures and into your system hardware.
{% endhint %}

### `sysfs` and `/sys` <a href="#sysfs-and-sys" id="sysfs-and-sys"></a>

`Sysfs` is a virtual filesystem that the Linux kernel uses to export information about kernel objects to processes running in user space.&#x20;

It was introduced in the 2.6 kernel and was originally derived from `ramfs`, which dated from the 2.4 kernel.&#x20;

As a virtual filesystem, `sysfs` is an in-memory filesystem that is mounted at `/sys`. If it is not mounted during initialization by an entry in `/etc/fstab`, you can always mount it using the command:

```bash
mount -t sysfs sysfs /sys
```

`Sysfs` is a simple filesystem with files that represent object attributes.&#x20;

* The objects themselves are represented by directories.&#x20;
* Symbolic links represent relationships between objects.&#x20;
* The top-level directories in sysfs represent major subsystems.&#x20;

Listing 1 shows examples of the top-level `/sys` directory and the `/sys/bus` and `/sys/devices` directories.

#### **Listing 1. Some entries from the sysfs filesystem**

```bash
[root@thinkbox ~]# ls -ltrah /sys
total 4.0K
drwxr-xr-x  19 root root 4.0K Apr  9 02:25 ..
drwxr-xr-x 314 root root    0 Oct  1 21:01 module
drwxr-xr-x  16 root root    0 Oct  1 21:01 kernel
drwxr-xr-x   2 root root    0 Oct  1 21:01 hypervisor
drwxr-xr-x   8 root root    0 Oct  1 21:01 fs
drwxr-xr-x   6 root root    0 Oct  1 21:01 firmware
drwxr-xr-x  25 root root    0 Oct  1 21:01 devices
drwxr-xr-x  78 root root    0 Oct  1 21:01 class
drwxr-xr-x  47 root root    0 Oct  1 21:01 bus
drwxr-xr-x   4 root root    0 Oct  1 21:01 dev
drwxr-xr-x   2 root root    0 Oct  1 21:01 block
drwxr-xr-x   3 root root    0 Oct  1 21:01 power
dr-xr-xr-x  13 root root    0 Oct  4 10:18 .

[root@thinkbox ~]# ls -ltrah /sys/bus
total 0
drwxr-xr-x  5 root root 0 Oct  1 21:01 pci
drwxr-xr-x  4 root root 0 Oct  1 21:01 acpi
drwxr-xr-x  4 root root 0 Oct  1 21:01 pnp
drwxr-xr-x  4 root root 0 Oct  1 21:01 platform
drwxr-xr-x  4 root root 0 Oct  1 21:01 scsi
drwxr-xr-x  4 root root 0 Oct  1 21:01 xen
drwxr-xr-x  4 root root 0 Oct  1 21:01 serial-base
drwxr-xr-x  4 root root 0 Oct  1 21:01 isa
drwxr-xr-x  4 root root 0 Oct  1 21:01 gpio
drwxr-xr-x  4 root root 0 Oct  1 21:01 xen-backend
drwxr-xr-x  4 root root 0 Oct  1 21:01 soc
drwxr-xr-x  4 root root 0 Oct  1 21:01 machinecheck
drwxr-xr-x  4 root root 0 Oct  1 21:01 dax
drwxr-xr-x  4 root root 0 Oct  1 21:01 cpu
drwxr-xr-x  4 root root 0 Oct  1 21:01 usb
drwxr-xr-x  4 root root 0 Oct  1 21:01 spi
drwxr-xr-x  4 root root 0 Oct  1 21:01 pci_express
drwxr-xr-x  4 root root 0 Oct  1 21:01 hid
drwxr-xr-x  4 root root 0 Oct  1 21:01 event_source
drwxr-xr-x  4 root root 0 Oct  1 21:01 edac
drwxr-xr-x  4 root root 0 Oct  1 21:01 container
drwxr-xr-x  4 root root 0 Oct  1 21:01 clockevents
drwxr-xr-x  4 root root 0 Oct  1 21:01 auxiliary
drwxr-xr-x  4 root root 0 Oct  1 21:01 usb-serial
drwxr-xr-x  4 root root 0 Oct  1 21:01 serial
drwxr-xr-x  4 root root 0 Oct  1 21:01 nvmem
drwxr-xr-x  4 root root 0 Oct  1 21:01 mipi-dsi
drwxr-xr-x  4 root root 0 Oct  1 21:01 memory_tiering
drwxr-xr-x  4 root root 0 Oct  1 21:01 workqueue
drwxr-xr-x  4 root root 0 Oct  1 21:01 virtio
drwxr-xr-x  4 root root 0 Oct  1 21:01 node
drwxr-xr-x  4 root root 0 Oct  1 21:01 memory
drwxr-xr-x  4 root root 0 Oct  1 21:01 i2c
drwxr-xr-x  4 root root 0 Oct  1 21:01 clocksource
drwxr-xr-x  4 root root 0 Oct  1 21:01 serio
drwxr-xr-x  4 root root 0 Oct  1 21:01 typec
drwxr-xr-x  4 root root 0 Oct  1 21:01 wmi
drwxr-xr-x  4 root root 0 Oct  1 21:01 cec
drwxr-xr-x  4 root root 0 Oct  1 21:01 mei
drwxr-xr-x  4 root root 0 Oct  1 21:01 hdaudio
drwxr-xr-x  4 root root 0 Oct  1 21:01 media
drwxr-xr-x  4 root root 0 Oct  1 21:01 ac97
drwxr-xr-x  4 root root 0 Oct  1 21:01 rmi4
drwxr-xr-x  4 root root 0 Oct  1 21:01 snd_seq
drwxr-xr-x  4 root root 0 Oct  4 08:24 mdio_bus
dr-xr-xr-x 13 root root 0 Oct  4 10:18 ..
drwxr-xr-x 47 root root 0 Oct  4 10:18 .

[root@thinkbox ~]# ls -ltrah /sys/devices
total 0
drwxr-xr-x 22 root root 0 Oct  1 21:01 virtual
drwxr-xr-x 20 root root 0 Oct  1 21:01 pci0000:00
drwxr-xr-x 23 root root 0 Oct  1 21:01 LNXSYSTM:00
drwxr-xr-x 15 root root 0 Oct  1 21:01 pnp0
drwxr-xr-x 30 root root 0 Oct  1 21:01 platform
drwxr-xr-x 10 root root 0 Oct  1 21:01 system
drwxr-xr-x  4 root root 0 Oct  1 21:01 uprobe
drwxr-xr-x  3 root root 0 Oct  1 21:01 software
drwxr-xr-x  6 root root 0 Oct  1 21:01 cpu
drwxr-xr-x  3 root root 0 Oct  1 21:01 tracepoint
drwxr-xr-x  5 root root 0 Oct  1 21:01 msr
drwxr-xr-x  4 root root 0 Oct  1 21:01 kprobe
drwxr-xr-x  5 root root 0 Oct  1 21:01 intel_pt
drwxr-xr-x  3 root root 0 Oct  1 21:01 breakpoint
drwxr-xr-x  5 root root 0 Oct  1 21:01 i915
drwxr-xr-x  5 root root 0 Oct  1 21:01 uncore_imc
drwxr-xr-x  5 root root 0 Oct  1 21:01 uncore_cbox_1
drwxr-xr-x  5 root root 0 Oct  1 21:01 uncore_cbox_0
drwxr-xr-x  4 root root 0 Oct  1 21:01 uncore_arb
drwxr-xr-x  5 root root 0 Oct  1 21:01 cstate_pkg
drwxr-xr-x  5 root root 0 Oct  1 21:01 cstate_core
drwxr-xr-x  5 root root 0 Oct  1 21:01 power
drwxr-xr-x  3 root root 0 Oct  1 21:01 isa
dr-xr-xr-x 13 root root 0 Oct  4 10:18 ..
drwxr-xr-x 25 root root 0 Oct  4 10:18 .
```

If you look at /sys/block, you find your block devices. These are actually links to directories under the /sys/devices directory.

If you follow these links, you find the partitions of sda. Looking at the size file, you see that the disk has 2048000 sectors, confirmed by running `fdisk -l` on partition:

```bash
[root@thinkbox ~]# ls -ltrah /sys/block
total 0
lrwxrwxrwx  1 root root 0 Oct  1 21:01 sda -> ../devices/pci0000:00/0000:00:17.0/ata3/host2/target2:0:0/2:0:0:0/block/sda
lrwxrwxrwx  1 root root 0 Oct  1 21:01 sdb -> ../devices/pci0000:00/0000:00:14.0/usb2/2-3/2-3:1.0/host3/target3:0:0/3:0:0:0/block/sdb
dr-xr-xr-x 13 root root 0 Oct  4 10:18 ..
drwxr-xr-x  2 root root 0 Oct  4 10:18 .

[root@thinkbox ~]# ls -ltrah /sys/devices/pci0000\:00/0000\:00\:17.0/ata3/host2/target2\:0\:0/2\:0\:0\:0/block/sda/sda1/
total 0
-rw-r--r--  1 root root 4.0K Oct  1 21:01 uevent
lrwxrwxrwx  1 root root    0 Oct  1 21:01 subsystem -> ../../../../../../../../../../class/block
drwxr-xr-x 12 root root    0 Oct  1 21:01 ..
-r--r--r--  1 root root 4.0K Oct  1 21:01 partition
-r--r--r--  1 root root 4.0K Oct  1 21:01 start
-r--r--r--  1 root root 4.0K Oct  1 21:01 size
-r--r--r--  1 root root 4.0K Oct  1 21:01 dev
-r--r--r--  1 root root 4.0K Oct  1 21:01 ro
drwxr-xr-x  2 root root    0 Oct  1 21:01 trace
drwxr-xr-x  2 root root    0 Oct  1 21:01 power
drwxr-xr-x  2 root root    0 Oct  1 21:01 holders
-r--r--r--  1 root root 4.0K Oct  4 10:23 stat
-r--r--r--  1 root root 4.0K Oct  4 10:23 inflight
-r--r--r--  1 root root 4.0K Oct  4 10:23 discard_alignment
-r--r--r--  1 root root 4.0K Oct  4 10:23 alignment_offset
drwxr-xr-x  5 root root    0 Oct  4 10:23 .

[root@thinkbox ~]# cat /sys/devices/pci0000\:00/0000\:00\:17.0/ata3/host2/target2\:0\:0/2\:0\:0\:0/block/sda/sda1/size 
2048000

[root@thinkbox ~]# fdisk -l /dev/sda1
Disk /dev/sda1: 1000 MiB, 1048576000 bytes, 2048000 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x00000000
```

The `tree` command is a useful tool for exploring /sys. Note also that you have to escape special characters such as the colon (`:`) by enclosing them in quotation marks or using the backslash (`\`) character.

### `procfs` and `/proc`

The `procfs` file system provides insight into many of the **kernel data structures** and even allows a few to be changed on the fly. `procfs` is usually mounted at `/proc`. Listing 3 shows a listing of `/proc` on my system.

#### Listing 3. listing `/proc`

```bash
[root@thinkbox ~]# ls -tah /proc
84554  82994  77984  66416  71466  70830  50182		fb	      scsi	     4565	  1518  1193	456	 794	454	 swaps  30  50  72	  cmdline
84542  82983  76524  66417  71447  70819  21445		fs	      slabinfo       3238	  1423  1186	528	 772	mounts	 12	31  51  73	  cpuinfo
83972  80463  74177  67049  71427  64592  21459		interrupts    softirqs       3239	  tty	1177	529	 770	410	 14	32  52  74	  net
83979  80610  73745  67050  71415  61267  21551		iomem	      sysrq-trigger  modules	  1070  asound  530	 764	uptime	 15	33  53  75	  self
83981  80615  73810  67645  71381  61003  21514		ioports       sysvipc	     1940	  1175  1159	531	 702	133	 16	34  54  76	  sys
84035  81786  73742  67646  71233  60873  21481		irq	      timer_list     2004	  1185  1150	534	 651	232	 17	36  55  77	  thread-self
84208  81076  73743  68270  71224  59532  21472		kallsyms      vmallocinfo    1998	  1203  1149	535	 646	297	 18	37  56  78	  ..
84290  80603  73717  68271  71169  59537  21443		keys	      vmstat	     filesystems  1339  1089	623	 stat	308	 19	38  6	79
84366  80039  73188  68867  71145  59527  21442		key-users     zoneinfo       1994	  1338  1126	697	 620	323	 2	39  61  80
84492  80167  73165  68868  71012  59028  21439		kmsg	      6682	     1988	  1337  1122	765	 619	324	 20	4   62  81
84493  80166  73121  69477  70994  56792  acpi		kpagecgroup   12201	     1937	  1336  1115	1058	 616	326	 21	40  63  9
84480  80048  73111  69478  70984  52026  bootconfig	kpagecount    12176	     1936	  1335  1114	1033	 615	336	 22	42  64  90
83454  80043  73079  70206  70965  51078  buddyinfo	kpageflags    12153	     1927	  1332  1107	1031	 612	337	 23	43  65  92
83458  79783  72863  70207  70925  51077  config.gz	loadavg       9225	     1904	  1316  1098	1025	 613	338	 24	44  66  94
83492  79556  64323  70823  70875  51033  crypto	locks	      8890	     1902	  1296  1096	954	 610	339	 25	45  67  pressure
83879  79547  64992  70824  70874  50944  diskstats	misc	      driver	     1827	  1251  1088	version  608	340	 26	46  68  kcore
83882  79504  64993  70832  70872  50184  dma		pagetypeinfo  bus	     1552	  1249  1084	mtrr	 592	consoles  27	47  69  .
83880  77880  65815  70841  70839  50187  dynamic_debug  partitions    4698	     1549	  1223  1069	820	 589	meminfo   28	49  7	1
83877  77988  65816  71499  70838  50185  execdomains	schedstat     4596	     1530	  1201  1057	805	 563	devices   3	5   70  cgroups
```

You see a large number of numbered directories — one for each running process on your system. The number is the process ID (`PID`).&#x20;

Listing 4 shows the first few entries for a running bash command prompt (`PID=$$` — the current PID). Note that almost all entries in the procfs file system have a length of `0`, even though they might not be empty and you can see data if you use the `cat` command.&#x20;

Most entries are not terminated with a newline character, so I use the `echo` command in Listing 4 for clarity.

#### Listing 4. Showing `/proc` entries for the current process

```bash
[root@thinkbox ~]# ls -l /proc/$$ | head -n 15
total 0
-r--r--r--  1 root root 0 Oct  4 10:32 arch_status
dr-xr-xr-x  2 root root 0 Oct  4 10:32 attr
-rw-r--r--  1 root root 0 Oct  4 10:32 autogroup
-r--------  1 root root 0 Oct  4 10:32 auxv
-r--r--r--  1 root root 0 Oct  4 10:32 cgroup
--w-------  1 root root 0 Oct  4 10:32 clear_refs
-r--r--r--  1 root root 0 Oct  4 10:18 cmdline
-rw-r--r--  1 root root 0 Oct  4 10:32 comm
-rw-r--r--  1 root root 0 Oct  4 10:32 coredump_filter
-r--r--r--  1 root root 0 Oct  4 10:32 cpu_resctrl_groups
-r--r--r--  1 root root 0 Oct  4 10:32 cpuset
lrwxrwxrwx  1 root root 0 Oct  4 10:32 cwd -> /root
-r--------  1 root root 0 Oct  4 10:32 environ
lrwxrwxrwx  1 root root 0 Oct  4 10:18 exe -> /usr/bin/bash
```

The parameters that you can control by setting values in the procfs file system can also be controlled via the `sysctl` command. Refer to the `sysctl` and `procfs` man pages for more information.

The `procfs` file system also provides information about **interrupt**, **Direct Memory Access (DMA)**, and **I/O port resources** used by your system.

Listing 5 shows the contents of `/proc/interrupts` on my system.&#x20;

* Older single-processor computers using the `ISA` bus use certain **well-known interrupts** (or `IRQs`), **I/O ports**, and **DMA resources**. These were in limited supply, so adding a sound card that needed `IRQ 5` might prevent use of a second parallel port that also needed `IRQ 5`.
* The **PCI bus** includes a capability for **Message Signalling Interrupts (MSI)**, which has significantly eased the resource limitations found in older systems.
* **ISA bu**s device interrupts are numbered from `0` through `15`.
* system does have a parallel printer port using `IRQ7` but does not have serial ports, which normally use `IRQ3` or `IRQ 4`.
* If you have error messages or a device that does not work and you suspect interrupt conflicts, looking at `/proc/interrupts` is a good place to start.

#### **Listing 5. Listing of `/proc/interrupts`**

```bash
[root@thinkbox ~]# cat /proc/interrupts 
            CPU0       CPU1       CPU2       CPU3       
   1:       3797          0          0          0  IR-IO-APIC    1-edge      i8042
   8:          0          0          0          0  IR-IO-APIC    8-edge      rtc0
   9:      80676          0          0          0  IR-IO-APIC    9-fasteoi   acpi
  12:         17          0          0       1432  IR-IO-APIC   12-edge      i8042
  16:     889054     164682          0          0  IR-IO-APIC   16-fasteoi   i801_smbus
 123:    5703416          0          0          0  IR-PCI-MSI-0000:00:17.0    0-edge      ahci[0000:00:17.0]
 124:    1098297          0       7819          0  IR-PCI-MSI-0000:00:14.0    0-edge      xhci_hcd
 129:    7615472     137325          0          0  IR-PCI-MSI-0000:00:02.0    0-edge      i915
 130:          0         43          0          0  IR-PCI-MSI-0000:00:16.0    0-edge      mei_me
 131:     863387          0          0      38381  IR-PCI-MSI-0000:04:00.0    0-edge      iwlwifi
 132:          0          0       6014          0  IR-PCI-MSI-0000:00:1f.6    0-edge      enp0s31f6
 133:          0          0          0        348  IR-PCI-MSI-0000:00:1f.3    0-edge      snd_hda_intel:card0
 134:     222214      41124          2          2     dummy   44  rmi4_smbus
 135:          0          0          0          0      rmi4    0  rmi4-00.fn34
 136:          0          0          0          0      rmi4    1  rmi4-00.fn01
 137:          2         44        349          0      rmi4    2  rmi4-00.fn03
 138:        840     133917     128371          0      rmi4    3  rmi4-00.fn11
 139:          0          0          0          0      rmi4    4  rmi4-00.fn11
 140:          0          0          7          0      rmi4    5  rmi4-00.fn30
 144:          0     288210          0          0  IR-PCI-MSI-0000:3f:00.0    0-edge      xhci_hcd
 NMI:          0          0          0          0   Non-maskable interrupts
 LOC:   13292079   12371735   13168887   12660298   Local timer interrupts
 SPU:          0          0          0          0   Spurious interrupts
 PMI:          0          0          0          0   Performance monitoring interrupts
 IWI:    3472587     171073     125288     219692   IRQ work interrupts
 RTR:          0          0          0          0   APIC ICR read retries
 RES:    1063469    1661281    1551335    1562545   Rescheduling interrupts
 CAL:    2625620    2653441    2541253    2595450   Function call interrupts
 TLB:    1372746    1457308    1426559    1442516   TLB shootdowns
 TRM:       4356       4356       4356       4356   Thermal event interrupts
 THR:          0          0          0          0   Threshold APIC interrupts
 DFR:          0          0          0          0   Deferred Error APIC interrupts
 MCE:          0          0          0          0   Machine check exceptions
 MCP:        126        126        126        126   Machine check polls
 ERR:          0
 MIS:          0
 PIN:          0          0          0          0   Posted-interrupt notification event
 NPI:          0          0          0          0   Nested posted-interrupt event
 PIW:          0          0          0          0   Posted-interrupt wakeup event
 PMN:          0          0          0          0   Posted MSI notification event
```

**DMA** was a technique used in **ISA** **bus** systems whereby fast devices such as hard drives could transfer data to computer memory without involving the processor.&#x20;

**DMA** frees up the processor to do other work while the data is being read from or written to the device. The **PCI bus** architecture uses **bus mastering** to achieve a similar goal.&#x20;

Listing 6 shows the contents of `/proc/dma` on my system. If you have **ISA bus** resources, you usually see more than this, and you might need this information to help diagnose conflicts.

#### **Listing 6. Listing of `/proc/interrupts`**

```bash
[root@thinkbox ~]# cat /proc/dma
 4: cascade
```

Another resource list you can use to help resolve problems is the listing of **I/O ports** in `/proc/ioports`. Several I/O ports used in **ISA bus systems** are well known, such as the range `0378` to `037a` usually used for the **first parallel port**. Note that the ports are specified in hexadecimal.

#### **Listing 7. Listing of `/proc/ioports`**

```bash
[root@thinkbox ~]# cat /proc/ioports 
0000-0cf7 : PCI Bus 0000:00
  0000-001f : dma1
  0020-0021 : pic1
  0040-0043 : timer0
  0050-0053 : timer1
  0060-0060 : keyboard
  0061-0061 : PNP0800:00
  0062-0062 : PNP0C09:00
    0062-0062 : EC data
  0064-0064 : keyboard
  0066-0066 : PNP0C09:00
    0066-0066 : EC cmd
  0070-0077 : rtc0
  0080-008f : dma page reg
  00a0-00a1 : pic2
  00c0-00df : dma2
  00f0-00ff : fpu
  0400-041f : iTCO_wdt
    0400-041f : iTCO_wdt
  0680-069f : pnp 00:03
  0800-087f : pnp 00:08
  0880-08ff : pnp 00:08
  0900-097f : pnp 00:08
  0980-09ff : pnp 00:08
  0a00-0a7f : pnp 00:08
  0a80-0aff : pnp 00:08
  0b00-0b7f : pnp 00:08
  0b80-0bff : pnp 00:08
0cf8-0cff : PCI conf1
0d00-ffff : PCI Bus 0000:00
  15e0-15ef : pnp 00:08
  164e-164f : pnp 00:03
  1800-18fe : pnp 00:03
    1800-1803 : ACPI PM1a_EVT_BLK
    1804-1805 : ACPI PM1a_CNT_BLK
    1808-180b : ACPI PM_TMR
    1810-1815 : ACPI CPU throttle
    1850-1850 : ACPI PM2_CNT_BLK
    1854-1857 : pnp 00:05
    1880-189f : ACPI GPE0_BLK
  2000-2fff : PCI Bus 0000:02
  3000-3fff : PCI Bus 0000:07
  e000-e03f : 0000:00:02.0
  e060-e07f : 0000:00:17.0
    e060-e07f : ahci
  e080-e087 : 0000:00:17.0
    e080-e087 : ahci
  e088-e08b : 0000:00:17.0
    e088-e08b : ahci
  efa0-efbf : 0000:00:1f.4
    efa0-efbf : i801_smbus
  ff00-fffe : pnp 00:02
  ffff-ffff : pnp 00:03
    ffff-ffff : pnp 00:03
      ffff-ffff : pnp 00:03
```

### `lsdev` command

The **procinfo** **package** contains the `lsdev` command, which simplifies the presentation of the information from /proc by gathering the **DMA, IRQ**, and **I/O port** information for each device into a simple tabular format. Listing 8 shows the `lsdev` output for my system.

#### Listing 8. lsdev output

```bash
[root@thinkbox ~]# lsdev
Device            DMA   IRQ  I/O Ports
------------------------------------------------
 0  rmi4-00.fn34        135 
 1  rmi4-00.fn01        136 
 2  rmi4-00.fn03        137 
 3  rmi4-00.fn11        138 
 4  rmi4-00.fn11        139 
 5  rmi4-00.fn30        140 
0000:00:02.0                   e000-e03f
0000:00:17.0                   e060-e07f   e080-e087   e088-e08b
0000:00:1f.4                   efa0-efbf
44  rmi4_smbus          134 
ACPI                             1800-1803     1804-1805     1808-180b     1810-1815     1850-1850     1880-189f
acpi                      9 
ahci                             e060-e07f     e080-e087     e088-e08b
ahci[0000:00:17.0]        123 
cascade             4       
dma                            0080-008f
dma1                           0000-001f
dma2                           00c0-00df
EC                               0062-0062     0066-0066
enp0s31f6               132 
fpu                            00f0-00ff
i801_smbus               16      efa0-efbf
i8042                  1 12 
i915                    129 
iTCO_wdt                       0400-041f     0400-041f
iwlwifi                 131 
keyboard                       0060-0060   0064-0064
mei_me                  130 
PCI                          0000-0cf7 0cf8-0cff 0d00-ffff   2000-2fff   3000-3fff
pic1                           0020-0021
pic2                           00a0-00a1
pnp                            0680-069f   0800-087f   0880-08ff   0900-097f   0980-09ff   0a00-0a7f   0a80-0aff   0b00-0b7f   0b80-0bff   15e0-15ef   164e-164f   1800-18fe     1854-1857   ff00-fffe   ffff-ffff     ffff-ffff       ffff-ffff
PNP0800:00                     0061-0061
PNP0C09:00                     0062-0062   0066-0066
rtc0                      8    0070-0077
snd_hda_intel:card0        133 
timer0                         0040-0043
timer1                         0050-0053
xhci_hcd              124 144 
```

The **procinfo package** also contains the `procinfo` command, which summarizes other information from `/proc`. Try it or see the man page for details.

```bash
[root@thinkbox ~]# procinfo
Memory:        Total        Used        Free     Buffers                       
RAM:        32595344    31661160      934184     1472544                       
Swap:       35857816      453664    35404152                                   

Bootup: Tue Oct  1 21:01:10 2024   Load average: 1.55 1.25 1.28 2/1477 85666   

user  :      05:45:19.06  14.4%  page in :         93506814                    
nice  :      00:24:58.50   1.0%  page out:         94623739                    
system:      02:06:27.64   5.3%  page act:          9274696                    
IOwait:      00:15:44.04   0.7%  page dea:                0                    
hw irq:      00:17:35.63   0.7%  page flt:        221895169                    
sw irq:      00:12:07.30   0.5%  swap in :          1609382                    
idle  :   1d 06:53:43.75  77.3%  swap out:          1966620                    
uptime:   2d 13:43:59.40         context :        197084751                    

irq   1:       3797  1-edge i8042        irq 132:       6203  0-edge enp0s31f6 
irq   8:          0  8-edge rtc0         irq 133:        348  0-edge snd_hda_in
irq   9:      81228  9-fasteoi acpi      irq 134:     263342                   
irq  12:       1449  12-edge i8042       irq 135:          0                   
irq  16:    1053736  16-fasteoi i801_s   irq 136:          0                   
irq 123:    5723324  0-edge ahci[0000:   irq 137:        395                   
irq 124:    1144903  0-edge xhci_hcd     irq 138:     263128                   
irq 129:    7881622  0-edge i915         irq 139:          0                   
irq 130:         43  0-edge mei_me       irq 140:          7                   
irq 131:     909052  0-edge iwlwifi      irq 144:     308201  0-edge xhci_hcd  


bond0        TX 0.00B         RX 0.00B         ovs-system  TX 0.00B         RX 0.00B        
enp0s31f6    TX 0.00B         RX 0.00B         ovsbr0      TX 180.00B       RX 350.01KiB    
enp63s0u1u4  TX 0.00B         RX 0.00B         vnet1       TX 225.92KiB     RX 73.38KiB     
lo           TX 16.68MiB      RX 16.68MiB      vnet2       TX 87.74KiB      RX 84.64KiB     
ovs-port0    TX 214.05KiB     RX 147.67KiB     wlan0       TX 144.96MiB     RX 3.65GiB      
```

## `udev` and `/dev`

`udev` is responsible for the dynamic device management needed for hot plugging devices. Information about configured and active devices is contained in the `/dev` **virtual filesystem**.

* When a device is added to or removed from the system, or it changes state, the kernel sends an event to the `systemd-udevd.service` daemon, which manages `udev` events.
* The daemon searches configured rules to match the event with a rule to identify the device.
* The kernel usually assigns a name that is neither meaningful nor repeatable, so `udev` usually assigns a more meaningful and consistent name.

The `udev` rules are in `/usr/lib/udev/rules.d`, with additional local rules in `/etc/udev/rules.d`. You can create additional rules in the volatile `/run/udev/rules.d` directory. You can configure `udev` using the `/etc/udev/udev.conf` file.

{% hint style="info" %}
Some systems place rules in `/lib/udev/rules.d` rather than `/usr/lib/udev/rules.d`. Check the udev man page on your system.
{% endhint %}

The `/dev` filesystem describes the devices on your system. In a long listing, you find one of four values in the first column:

| value | definition                                                                   |
| ----- | ---------------------------------------------------------------------------- |
| b     | block device (disk drive, iSCSI drive, etc.)                                 |
| c     | character device (terminal, printer, /dev/null)                              |
| d     | directory                                                                    |
| l     | symbolic link to another directory of a file, either in /dev, /proc, or /run |

#### **Listing 9. Partial listing of /dev**

```bash
[root@thinkbox ~]# ls -l /dev | head -n30
total 0
crw-------  1 root      root    10,   120 Oct  1 21:01 acpi_thermal_rel
crw-r--r--  1 root      root    10,   235 Oct  1 21:01 autofs
drwxr-xr-x  2 root      root          140 Oct  1 21:01 block
drwxr-xr-x  2 root      root           80 Oct  1 21:01 bsg
crw-------  1 root      root    10,   234 Oct  1 21:01 btrfs-control
drwxr-xr-x  3 root      root           60 Oct  1 21:01 bus
drwxr-xr-x  2 root      root         5220 Oct  4 09:40 char
crw--w----  1 root      tty      5,     1 Oct  4 05:40 console
lrwxrwxrwx  1 root      root           11 Oct  1 21:01 core -> /proc/kcore
drwxr-xr-x  6 root      root          120 Oct  4 07:18 cpu
crw-------  1 root      root    10,   122 Oct  1 21:01 cpu_dma_latency
crw-------  1 root      root    10,   203 Oct  1 21:01 cuse
drwxr-xr-x  9 root      root          180 Oct  1 21:01 disk
drwxr-xr-x  2 root      root           60 Oct  1 21:01 dma_heap
drwxr-xr-x  3 root      root          100 Oct  1 21:01 dri
crw-------  1 root      root   241,     0 Oct  1 21:01 drm_dp_aux0
crw-------  1 root      root   241,     1 Oct  1 21:01 drm_dp_aux1
crw-------  1 root      root   241,     2 Oct  1 21:01 drm_dp_aux2
crw-------  1 root      root   241,     3 Oct  4 08:24 drm_dp_aux3
crw-------  1 root      root   241,     4 Oct  4 08:24 drm_dp_aux4
crw-------  1 root      root   241,     5 Oct  4 08:24 drm_dp_aux5
crw-rw----  1 root      video   29,     0 Oct  1 21:01 fb0
lrwxrwxrwx  1 root      root           13 Oct  1 21:01 fd -> /proc/self/fd
crw-rw-rw-  1 root      root     1,     7 Oct  1 21:01 full
crw-rw-rw-  1 root      root    10,   229 Oct  1 21:01 fuse
crw-rw----+ 1 root      root   244,     0 Oct  4 08:35 hidraw0
crw-rw----+ 1 root      root   244,     1 Oct  4 08:35 hidraw1
crw-------  1 root      root   244,     2 Oct  4 08:24 hidraw2
crw-rw----+ 1 root      root   244,     3 Oct  4 08:24 hidraw3
```

Listing 10 shows the linkage between some kernel-assigned names and some more-familiar names for hard-drive partitions.

#### **Listing 10. Symbolic links to familiar hard drive names**

```bash
[root@thinkbox ~]# ls -l /dev/block/8\:?
lrwxrwxrwx 1 root root 6 Oct  1 21:01 /dev/block/8:0 -> ../sda
lrwxrwxrwx 1 root root 7 Oct  1 21:01 /dev/block/8:1 -> ../sda1
lrwxrwxrwx 1 root root 7 Oct  1 21:01 /dev/block/8:2 -> ../sda2
lrwxrwxrwx 1 root root 7 Oct  1 21:01 /dev/block/8:3 -> ../sda3
```

If you want to manage or monitor udev events, you can use the `udevadm` command. See the man pages for more information.

## Tools and Utilities

Some useful tools are available for determining information about your PCI and USB devices. Let's look at `lspci` and `lsusb`.

### PCI and lspci

Use `lspci` without options to see a basic listing of your PCI devices.&#x20;

What you see depends on your own system hardware.&#x20;

#### **Listing 11. Using lspci to display desktop PCI devices**

```bash
[root@thinkbox ~]# lspci
00:00.0 Host bridge: Intel Corporation Xeon E3-1200 v6/7th Gen Core Processor Host Bridge/DRAM Registers (rev 02)
00:02.0 VGA compatible controller: Intel Corporation HD Graphics 620 (rev 02)
00:04.0 Signal processing controller: Intel Corporation Xeon E3-1200 v5/E3-1500 v5/6th Gen Core Processor Thermal Subsystem (rev 02)
00:08.0 System peripheral: Intel Corporation Xeon E3-1200 v5/v6 / E3-1500 v5 / 6th/7th/8th Gen Core Processor Gaussian Mixture Model
00:14.0 USB controller: Intel Corporation Sunrise Point-LP USB 3.0 xHCI Controller (rev 21)
00:14.2 Signal processing controller: Intel Corporation Sunrise Point-LP Thermal subsystem (rev 21)
00:16.0 Communication controller: Intel Corporation Sunrise Point-LP CSME HECI #1 (rev 21)
00:17.0 SATA controller: Intel Corporation Sunrise Point-LP SATA Controller [AHCI mode] (rev 21)
00:1c.0 PCI bridge: Intel Corporation Sunrise Point-LP PCI Express Root Port #1 (rev f1)
00:1c.6 PCI bridge: Intel Corporation Sunrise Point-LP PCI Express Root Port #7 (rev f1)
00:1d.0 PCI bridge: Intel Corporation Sunrise Point-LP PCI Express Root Port #9 (rev f1)
00:1f.0 ISA bridge: Intel Corporation Sunrise Point LPC/eSPI Controller (rev 21)
00:1f.2 Memory controller: Intel Corporation Sunrise Point-LP PMC (rev 21)
00:1f.3 Audio device: Intel Corporation Sunrise Point-LP HD Audio (rev 21)
00:1f.4 SMBus: Intel Corporation Sunrise Point-LP SMBus (rev 21)
00:1f.6 Ethernet controller: Intel Corporation Ethernet Connection (4) I219-V (rev 21)
04:00.0 Network controller: Intel Corporation Wireless 8265 / 8275 (rev 78)
07:00.0 PCI bridge: Intel Corporation JHL6240 Thunderbolt 3 Bridge (Low Power) [Alpine Ridge LP 2016] (rev 01)
08:00.0 PCI bridge: Intel Corporation JHL6240 Thunderbolt 3 Bridge (Low Power) [Alpine Ridge LP 2016] (rev 01)
08:01.0 PCI bridge: Intel Corporation JHL6240 Thunderbolt 3 Bridge (Low Power) [Alpine Ridge LP 2016] (rev 01)
08:02.0 PCI bridge: Intel Corporation JHL6240 Thunderbolt 3 Bridge (Low Power) [Alpine Ridge LP 2016] (rev 01)
3f:00.0 USB controller: Intel Corporation JHL6240 Thunderbolt 3 USB 3.1 Controller (Low Power) [Alpine Ridge LP 2016] (rev 01)Listing 12. Using lspci to display notebook PCI devices
```

The `lspci` command looks up much of its data in the `/usr/share/hwdata/pci.ids` file, which contains a list of **all known IDs** (vendors, devices, classes, and subclasses).

Vendor and device codes are assigned numbers. Use the `-n` option if you want to see numbers instead of names.&#x20;

If you have a new device that is not yet in your local database, use the `-q` option to query the central PCI ID server database using a DNS lookup.&#x20;

If a device is found there, the information is saved in `~/.pciids-cache`, and the device is recognized on later runs even if you do not specify `-q`.

PCI topology is described by four levels:&#x20;

1. **domain**&#x20;
2. **bus**
3. **slot**
4. **function**&#x20;

You can restrict output by using any of these components along with the `-s` option.&#x20;

The `-t` option shows the output in a tree view.&#x20;

Other options of `lspci` control the verbosity and format of the output. You can have more or less verbose output, and the output can be suitable for machine or human reading.

#### **Listing 13. Output of lspci -tv**

```bash
[root@thinkbox ~]# lspci -tvv -s \:00
-[0000:00]-+-00.0  Intel Corporation Xeon E3-1200 v6/7th Gen Core Processor Host Bridge/DRAM Registers
           +-1c.6-[04]----00.0  Intel Corporation Wireless 8265 / 8275
           \-1d.0-[07-3f]----00.0-[08-3f]--+-00.0-[09]--
                                           +-01.0-[0a-3e]--
                                           \-02.0-[3f]----00.0  Intel Corporation JHL6240 Thunderbolt 3 USB 3.1 Controller (Low Power) [Alpine Ridge LP 2016]
```

You can also restrict output by **vendor ID**, **device**, or **class** by using the `-d` option. And the `-k` option tells you which kernel driver is handling the device and which kernel modules are capable of handling it.&#x20;

Listing 14 shows this output for the NVIDIA graphics and sound devices on my system. The vendor ID for Intel is `8086`, which you can find out by using the `-nn` option of `lspci`.

```bash
[root@thinkbox ~]# lspci -d 8086:5916 -k
00:02.0 VGA compatible controller: Intel Corporation HD Graphics 620 (rev 02)
	Subsystem: Lenovo Device 225a
	Kernel driver in use: i915
	Kernel modules: i915
```

See the man pages for the many other options for `lspci`. Some of them give you access to detailed information on PCI devices.&#x20;

There is also a `setpci` command, which enables a root user to query and configure PCI devices. Again, see the man pages for details.

### USB and lsusb

Use the `lsusb` command to display information about your USB devices. As with `lspci`, the `-t` option displays information in a tree layout, as shown in Listing 15.

```bash
[root@thinkbox ~]# lsusb -t
/:  Bus 001.Port 001: Dev 001, Class=root_hub, Driver=xhci_hcd/12p, 480M
    |__ Port 002: Dev 013, If 0, Class=Human Interface Device, Driver=usbhid, 12M
    |__ Port 002: Dev 013, If 1, Class=Human Interface Device, Driver=usbhid, 12M
    |__ Port 002: Dev 013, If 2, Class=Chip/SmartCard, Driver=[none], 12M
    |__ Port 007: Dev 002, If 0, Class=Wireless, Driver=btusb, 12M
    |__ Port 007: Dev 002, If 1, Class=Wireless, Driver=btusb, 12M
    |__ Port 008: Dev 003, If 0, Class=Video, Driver=uvcvideo, 480M
    |__ Port 008: Dev 003, If 1, Class=Video, Driver=uvcvideo, 480M
    |__ Port 009: Dev 011, If 0, Class=Vendor Specific Class, Driver=[none], 12M
/:  Bus 002.Port 001: Dev 001, Class=root_hub, Driver=xhci_hcd/6p, 5000M
    |__ Port 003: Dev 002, If 0, Class=Mass Storage, Driver=usb-storage, 5000M
/:  Bus 003.Port 001: Dev 001, Class=root_hub, Driver=xhci_hcd/2p, 480M
    |__ Port 001: Dev 002, If 0, Class=Hub, Driver=hub/5p, 480M
        |__ Port 003: Dev 003, If 0, Class=Hub, Driver=hub/6p, 480M
            |__ Port 001: Dev 005, If 0, Class=Human Interface Device, Driver=usbhid, 12M
            |__ Port 001: Dev 005, If 1, Class=Human Interface Device, Driver=usbhid, 12M
            |__ Port 001: Dev 005, If 2, Class=Human Interface Device, Driver=usbhid, 12M
            |__ Port 001: Dev 005, If 3, Class=Human Interface Device, Driver=usbhid, 12M
            |__ Port 005: Dev 006, If 0, Class=Human Interface Device, Driver=usbhid, 480M
        |__ Port 005: Dev 004, If 0, Class=Human Interface Device, Driver=usbhid, 480M
/:  Bus 004.Port 001: Dev 001, Class=root_hub, Driver=xhci_hcd/2p, 10000M
    |__ Port 001: Dev 002, If 0, Class=Hub, Driver=hub/4p, 10000M
        |__ Port 003: Dev 003, If 0, Class=Hub, Driver=hub/4p, 5000M
        |__ Port 004: Dev 004, If 0, Class=Vendor Specific Class, Driver=r8152, 5000M
```

USB devices connect to a hub. The root hubs are at the top level and can support USB 1, 2, or 3.&#x20;

* The OHCI (or UHCI) driver supports USB 1.1 (12Mbps)&#x20;
* the EHCI driver supports USB 2.0 (480Mbps), &#x20;
* the XHCI driver supports USB 3.0 (5Gbps).&#x20;
* USB Attached SCSI (UAS) is a faster protocol developed for USB 3.0 that also supports USB 2.0 speeds. UAS was introduced into Linux at kernel 3.15.

Attached devices fall into several classes:

* Human-interface devices include keyboards, mice, and other such devices.
* Communications devices include modems and serial, Ethernet, or WiFi interfaces.
* Mass storage devices include hard drives, CD and DVD drives, flash drives, memory card readers, and cameras.
* Audio devices include sound cards, MIDI devices, speakers, and microphones.
* IrDA devices use infrared communication and include medical devices and test equipment.
* Printer devices include printers and other devices such as numerically controlled machines.
* Video devices include webcams.

Many attached devices use a **class driver**, such as usb-storage for a storage device, `usbhid` for a human-interface device such as mouse or keyboard, or hub for a cascaded hub.

As with PCI devices, you can limit the output by USB topology or by vendor or device ID

#### Limiting output of lsusb

```bash
[root@thinkbox ~]# lsusb -d 046d:
Bus 003 Device 005: ID 046d:c548 Logitech, Inc. Logi Bolt Receiver
[root@thinkbox ~]# lsusb -s 01:
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 002: ID 8087:0a2b Intel Corp. Bluetooth wireless interface
Bus 001 Device 003: ID 5986:2113 Bison Electronics Inc. SunplusIT Integrated Camera
Bus 001 Device 011: ID 06cb:009a Synaptics, Inc. Metallica MIS Touch Fingerprint Reader
Bus 001 Device 013: ID 1050:0407 Yubico.com Yubikey 4/5 OTP+U2F+CCID
```

There is also a `usbview` command that you can use to view USB information graphically. The data is presented in tree view in the left pane. Selecting an entry shows the details in the right pane.

## Message passing and D-Bus <a href="#message-passing-and-d-bus" id="message-passing-and-d-bus"></a>

When new hardware is plugged in, or a printer runs out of paper, a user often needs to be notified. **D-Bus** (also known as _`dbus`_) is a **message-passing bus** developed under the auspices of freedesktop.org.&#x20;

A single system daemon allows communication between the kernel and user space for events such as new hardware found. **D-Bus messages** can drive notifications to a user, and the receiving application can then suggest actions such as opening a file browser when a CD or DVD is inserted in a drive or when a USB thumb drive is plugged in.

There is also a per-user-session daemon that is launched at login time. The user daemon provides **interprocess communication** between the user applications. The **D-Bus protocol** is a general one-to-one message-passing framework. Two applications could even communicate using the framework without messages passing through the dbus daemon.

See resources on the right side of the article for additional information on D-Bus.

## Kernel Modules

The `lsmod` command formats the information from `/proc/modules` to give you a current status of the modules in your Linux system. The `lsmod` command has no options. Listing 17 shows a partial listing of the module status on my system.

```bash
[root@thinkbox ~]# lsmod
Module                  Size  Used by
r8153_ecm              12288  0
cdc_ether              24576  1 r8153_ecm
usbnet                 61440  2 r8153_ecm,cdc_ether
r8152                 167936  1 r8153_ecm
mii                    16384  2 usbnet,r8152
libphy                225280  1 r8152
vhost_net              36864  2
vhost                  65536  1 vhost_net
vhost_iotlb            16384  1 vhost
tap                    32768  1 vhost_net
tun                    69632  5 vhost_net
xt_MASQUERADE          16384  1
nft_compat             24576  1
bridge                454656  0
stp                    12288  1 bridge
llc                    16384  2 bridge,stp
hid_multitouch         36864  0
usbhid                 86016  0
hid_sensor_custom      32768  0
hid_sensor_hub         32768  1 hid_sensor_custom
hid_generic            12288  0
uinput                 20480  1
ccm                    20480  6
snd_seq_dummy          12288  0
rfcomm                102400  7
snd_hrtimer            12288  1
snd_seq               131072  7 snd_seq_dummy
snd_seq_device         16384  1 snd_seq
bonding               270336  0
tls                   151552  1 bonding
nft_fib_inet           12288  1
nft_fib_ipv4           12288  1 nft_fib_inet
nft_fib_ipv6           12288  1 nft_fib_inet
nft_fib                12288  3 nft_fib_ipv6,nft_fib_ipv4,nft_fib_inet
nft_reject_inet        12288  10
nf_reject_ipv4         12288  1 nft_reject_inet
nf_reject_ipv6         20480  1 nft_reject_inet
nft_reject             12288  1 nft_reject_inet
nft_ct                 28672  7
nft_chain_nat          12288  4
uhid                   20480  2
cmac                   12288  3
algif_hash             12288  1
algif_skcipher         12288  1
af_alg                 32768  6 algif_hash,algif_skcipher
nf_tables             380928  257 nft_ct,nft_compat,nft_reject_inet,nft_fib_ipv6,nft_fib_ipv4,nft_chain_nat,nft_reject,nft_fib,nft_fib_inet
rmi_smbus              16384  0
rmi_core              126976  1 rmi_smbus
nfnetlink_cttimeout    20480  0
snd_soc_avs           237568  0
snd_soc_hda_codec      28672  1 snd_soc_avs
snd_soc_skl           241664  0
openvswitch           237568  5
nsh                    12288  1 openvswitch
intel_uncore_frequency    12288  0
nf_conncount           24576  1 openvswitch
bnep                   36864  2
intel_uncore_frequency_common    16384  1 intel_uncore_frequency
nf_nat                 61440  3 openvswitch,nft_chain_nat,xt_MASQUERADE
snd_soc_hdac_hda       28672  1 snd_soc_skl
nf_conntrack          200704  6 nf_nat,nft_ct,nfnetlink_cttimeout,openvswitch,nf_conncount,xt_MASQUERADE
snd_hda_ext_core       36864  4 snd_soc_avs,snd_soc_hda_codec,snd_soc_hdac_hda,snd_soc_skl
nf_defrag_ipv6         24576  1 nf_conntrack
nf_defrag_ipv4         12288  1 nf_conntrack
libcrc32c              12288  4 nf_conntrack,nf_nat,openvswitch,nf_tables
iTCO_wdt               16384  0
snd_soc_sst_ipc        20480  1 snd_soc_skl
intel_tcc_cooling      12288  0
x86_pkg_temp_thermal    16384  0
intel_pmc_bxt          16384  1 iTCO_wdt
intel_powerclamp       20480  0
snd_soc_sst_dsp        40960  1 snd_soc_skl
iTCO_vendor_support    12288  1 iTCO_wdt
ee1004                 16384  0
snd_soc_acpi_intel_match   106496  1 snd_soc_skl
snd_soc_acpi           16384  2 snd_soc_acpi_intel_match,snd_soc_skl
snd_hda_codec_hdmi     98304  1
coretemp               20480  0
snd_soc_core          458752  4 snd_soc_avs,snd_soc_hda_codec,snd_soc_hdac_hda,snd_soc_skl
mei_hdcp               28672  0
kvm_intel             430080  8
intel_rapl_msr         20480  0
mei_pxp                20480  0
snd_compress           28672  2 snd_soc_avs,snd_soc_core
snd_hda_codec_realtek   208896  1
ac97_bus               12288  1 snd_soc_core
snd_hda_codec_generic   114688  1 snd_hda_codec_realtek
snd_pcm_dmaengine      16384  1 snd_soc_core
snd_hda_scodec_component    20480  1 snd_hda_codec_realtek
snd_hda_intel          65536  1
kvm                  1368064  5 kvm_intel
snd_intel_dspcfg       40960  3 snd_soc_avs,snd_hda_intel,snd_soc_skl
snd_intel_sdw_acpi     16384  1 snd_intel_dspcfg
rapl                   20480  0
snd_ctl_led            24576  0
intel_cstate           20480  0
snd_hda_codec         212992  8 snd_hda_codec_generic,snd_soc_avs,snd_hda_codec_hdmi,snd_soc_hda_codec,snd_hda_intel,snd_hda_codec_realtek,snd_soc_hdac_hda,snd_soc_skl
uvcvideo              180224  0
iwlmvm                749568  0
btusb                  86016  0
videobuf2_vmalloc      20480  1 uvcvideo
intel_uncore          266240  0
snd_hda_core          147456  10 snd_hda_codec_generic,snd_soc_avs,snd_hda_codec_hdmi,snd_soc_hda_codec,snd_hda_intel,snd_hda_ext_core,snd_hda_codec,snd_hda_codec_realtek,snd_soc_hdac_hda,snd_soc_skl
mac80211             1609728  1 iwlmvm
pcspkr                 12288  0
uvc                    12288  1 uvcvideo
snd_hwdep              20480  1 snd_hda_codec
i2c_i801               45056  0
videobuf2_memops       16384  1 videobuf2_vmalloc
btrtl                  32768  1 btusb
libarc4                12288  1 mac80211
btintel                65536  1 btusb
snd_pcm               200704  9 snd_soc_avs,snd_hda_codec_hdmi,snd_hda_intel,snd_hda_codec,snd_compress,snd_soc_core,snd_soc_skl,snd_hda_core,snd_pcm_dmaengine
i2c_smbus              20480  1 i2c_i801
processor_thermal_device_pci_legacy    12288  0
think_lmi              36864  0
e1000e                376832  0
videobuf2_v4l2         40960  1 uvcvideo
btbcm                  24576  1 btusb
ptp                    45056  2 iwlmvm,e1000e
firmware_attributes_class    12288  1 think_lmi
videodev              393216  2 videobuf2_v4l2,uvcvideo
intel_wmi_thunderbolt    16384  0
i2c_mux                16384  1 i2c_i801
videobuf2_common       94208  4 videobuf2_vmalloc,videobuf2_v4l2,uvcvideo,videobuf2_memops
wmi_bmof               12288  0
processor_thermal_device    20480  1 processor_thermal_device_pci_legacy
pps_core               32768  1 ptp
iwlwifi               593920  1 iwlmvm
btmtk                  12288  1 btusb
uas                    36864  0
processor_thermal_wt_hint    16384  1 processor_thermal_device
snd_timer              53248  3 snd_seq,snd_hrtimer,snd_pcm
processor_thermal_rfim    28672  1 processor_thermal_device
processor_thermal_rapl    16384  1 processor_thermal_device
thinkpad_acpi         196608  0
mc                     90112  4 videodev,videobuf2_v4l2,uvcvideo,videobuf2_common
usb_storage            90112  1 uas
intel_rapl_common      49152  2 intel_rapl_msr,processor_thermal_rapl
platform_profile       12288  1 thinkpad_acpi
bluetooth            1105920  44 btrtl,btmtk,btintel,btbcm,bnep,btusb,rfcomm
mei_me                 57344  2
intel_pmc_core        122880  0
sparse_keymap          12288  1 thinkpad_acpi
processor_thermal_wt_req    12288  1 processor_thermal_device
mei                   204800  5 mei_hdcp,mei_pxp,mei_me
intel_vsec             20480  1 intel_pmc_core
processor_thermal_power_floor    12288  1 processor_thermal_device
snd                   155648  18 snd_ctl_led,snd_hda_codec_generic,snd_seq,snd_seq_device,snd_hda_codec_hdmi,snd_hwdep,snd_hda_intel,snd_hda_codec,snd_hda_codec_realtek,snd_timer,snd_compress,thinkpad_acpi,snd_soc_core,snd_pcm
processor_thermal_mbox    12288  4 processor_thermal_power_floor,processor_thermal_wt_req,processor_thermal_rfim,processor_thermal_wt_hint
pmt_telemetry          16384  1 intel_pmc_core
joydev                 28672  0
int3400_thermal        20480  0
vfat                   24576  1
int3403_thermal        16384  0
intel_xhci_usb_role_switch    12288  0
intel_pch_thermal      20480  0
fat                   106496  1 vfat
intel_soc_dts_iosf     16384  1 processor_thermal_device_pci_legacy
soundcore              16384  2 snd_ctl_led,snd
mousedev               24576  0
int340x_thermal_zone    16384  2 int3403_thermal,processor_thermal_device
pmt_class              16384  1 pmt_telemetry
acpi_thermal_rel       20480  1 int3400_thermal
acpi_pad               24576  0
mac_hid                12288  0
loop                   45056  0
dm_mod                225280  0
nfnetlink              20480  6 nft_compat,nfnetlink_cttimeout,nf_tables
ip_tables              36864  0
x_tables               65536  3 nft_compat,ip_tables,xt_MASQUERADE
i915                 4493312  118
ext4                 1155072  1
crc32c_generic         12288  0
crc16                  12288  2 bluetooth,ext4
mbcache                16384  1 ext4
crct10dif_pclmul       12288  1
crc32_pclmul           12288  0
crc32c_intel           16384  3
jbd2                  208896  1 ext4
wl                   6516736  0
polyval_clmulni        12288  0
polyval_generic        12288  1 polyval_clmulni
gf128mul               16384  1 polyval_generic
i2c_algo_bit           20480  1 i915
psmouse               237568  0
serio_raw              20480  0
drm_buddy              24576  1 i915
ghash_clmulni_intel    16384  0
ttm                   102400  1 i915
sha512_ssse3           53248  0
atkbd                  40960  0
intel_gtt              28672  1 i915
sha256_ssse3           36864  0
udl                    24576  0
libps2                 20480  2 atkbd,psmouse
pkcs8_key_parser       12288  0
vivaldi_fmap           12288  1 atkbd
drm_display_helper    262144  1 i915
sha1_ssse3             32768  0
ucsi_acpi              12288  0
cfg80211             1372160  4 wl,iwlmvm,iwlwifi,mac80211
video                  81920  2 thinkpad_acpi,i915
aesni_intel           364544  8
typec_ucsi             73728  1 ucsi_acpi
crypto_simd            16384  1 aesni_intel
roles                  16384  2 typec_ucsi,intel_xhci_usb_role_switch
i8042                  57344  0
xhci_pci               24576  0
cryptd                 28672  3 crypto_simd,ghash_clmulni_intel
rfkill                 40960  10 iwlmvm,bluetooth,thinkpad_acpi,cfg80211
xhci_pci_renesas       24576  1 xhci_pci
cec                    94208  2 drm_display_helper,i915
typec                 110592  1 typec_ucsi
serio                  28672  8 rmi_core,serio_raw,atkbd,psmouse,i8042
wmi                    32768  4 video,intel_wmi_thunderbolt,wmi_bmof,think_lmi
vfio_pci               16384  0
vfio_pci_core          98304  1 vfio_pci
vfio_iommu_type1       49152  0
vfio                   77824  3 vfio_pci_core,vfio_iommu_type1,vfio_pci
iommufd               110592  1 vfio
i2c_dev                28672  0
crypto_user            20480  0
```

### using modinfo

Use the `modinfo` command to get information about a module. You can give a full filename or just the module name. Listing 18 shows information for the vfat module, which handles the various forms of FAT-formatted drives.

#### Listing 18. Info about the vfat module

```bash
[root@thinkbox ~]# modinfo vfat
filename:       /lib/modules/6.10.10-arch1-1/kernel/fs/fat/vfat.ko.zst
author:         Gordon Chaffee
description:    VFAT filesystem support
license:        GPL
alias:          fs-vfat
srcversion:     10BEEDD71D6D70CF4F2B734
depends:        fat
retpoline:      Y
intree:         Y
name:           vfat
vermagic:       6.10.10-arch1-1 SMP preempt mod_unload 
sig_id:         PKCS#7
signer:         Build time autogenerated kernel key
sig_key:        76:91:ED:E7:98:9D:E5:F2:3B:77:21:3A:22:E3:68:1E:5B:CA:A8:B5
sig_hashalgo:   sha512
signature:      30:65:02:31:00:ED:BC:1D:AA:60:2D:17:EC:E8:E3:14:D9:30:B8:EF:
		65:A1:B6:2A:C6:71:67:8F:C2:4D:E4:B4:A6:45:D5:D3:57:9B:24:80:
		9E:BA:C5:E2:69:22:59:AA:EF:5A:83:C3:3D:02:30:42:45:3F:6F:02:
		D0:36:67:D7:96:2B:91:7A:AD:80:ED:06:28:F8:1F:0A:D6:A5:FB:0B:
		CF:EF:46:14:BB:2E:2F:B8:DF:91:7E:F6:2A:94:C6:A1:BB:F5:9D:BC:
		0F:1A:D0
```

The module information includes the full path to the file and information about any other names (aliases) the module might be known by. Dependencies, if any, are also listed. So, from Mod 2, you can see that the vfat module is also known as fs-vfat and depends on another module called fat. Try running `modinfo` with these module names.

You can use the `-F` or `--field` option to limit the output to a specific field. This option is useful for scripts.

If you don't specify a full filename, `modinfo` searches for a module in `/lib/modules/`_`version`_`/kernel`, where _version_ is your kernel release as given by `uname-r`. In the example in Listing 18, the file `vfat.ko.xz` (the vfat kernel module) is found in `/lib/modules/4.1.6-200.fc22.x86_64/kernel/fs/fat`.&#x20;

Listing 19 illustrates how you might start looking for module files on your system.

#### **Listing 19. Locating module files on your system**

```bash
[root@thinkbox ~]# ls -ltrah /lib/modules/$(uname -r)
total 20M
-rw-r--r--  1 root root  13M Sep 12 12:21 vmlinuz
-rw-r--r--  1 root root    6 Sep 12 12:21 pkgbase
-rw-r--r--  1 root root 244K Sep 12 12:21 modules.order
-rw-r--r--  1 root root  67K Sep 12 12:21 modules.builtin.modinfo
-rw-r--r--  1 root root 6.7K Sep 12 12:21 modules.builtin
drwxr-xr-x 12 root root 4.0K Sep 27 08:57 kernel
drwxr-xr-x 21 root root 4.0K Sep 27 08:57 build
drwxr-xr-x  4 root root 4.0K Sep 27 08:57 ..
drwxr-xr-x  2 root root 4.0K Sep 27 08:57 extramodules
drwxr-xr-x  3 root root 4.0K Sep 27 08:58 updates
-rw-r--r--  1 root root 831K Sep 30 23:39 modules.dep
-rw-r--r--  1 root root 1.1M Sep 30 23:39 modules.dep.bin
-rw-r--r--  1 root root 1.6M Sep 30 23:39 modules.alias
-rw-r--r--  1 root root 1.6M Sep 30 23:39 modules.alias.bin
-rw-r--r--  1 root root 2.8K Sep 30 23:39 modules.softdep
-rw-r--r--  1 root root   55 Sep 30 23:39 modules.weakdep
-rw-r--r--  1 root root 707K Sep 30 23:39 modules.symbols
-rw-r--r--  1 root root 863K Sep 30 23:39 modules.symbols.bin
-rw-r--r--  1 root root 9.0K Sep 30 23:39 modules.builtin.bin
-rw-r--r--  1 root root 8.1K Sep 30 23:39 modules.builtin.alias.bin
-rw-r--r--  1 root root  437 Sep 30 23:39 modules.devname
drwxr-xr-x  6 root root 4.0K Sep 30 23:39 .
```

You can also find several plain-text files in your /lib/modules/$(uname -r) directory — including modules.dep, which lists dependencies, and modules.alias, which lists aliases.&#x20;

The modules.builtin file lists modules that are built in to the kernel. These include drivers needed for core functionality on most systems.&#x20;

If you try to use `modinfo` to find out about the ehci-pci driver that you might have noticed in the `lsusb` output, you probably won't find it because it is a built-in driver.&#x20;

Listing 20 illustrates this scenario.

#### **Listing 20. Finding built-in drivers**

<pre class="language-bash"><code class="lang-bash">[root@thinkbox ~]# modinfo ehci‑pci
modinfo: ERROR: Module ehci‑pci not found.
<strong>
</strong><strong>[root@thinkbox ~]# grep ehci /lib/modules/6.10.10-arch1-1/modules.builtin
</strong>kernel/drivers/usb/host/ehci-hcd.ko
kernel/drivers/usb/host/ehci-pci.ko
</code></pre>

### using modprobe

As you've seen from the output of `lspci`, `lsusb`, `lsmod`, and `modinfo`, your system uses several kernel modules to drive devices. You have also seen that some of these modules have dependencies.&#x20;

So, loading the right modules in the right order is important. Fortunately, the `modprobe` command has taken over the earlier manual work of `insmod` and `rmmod`, which can each insert or remove one module from the system.

**Listing 21. Module information for irdma**

```bash
[root@thinkbox ~]# modinfo irdma 
filename:       /lib/modules/6.10.10-arch1-1/kernel/drivers/infiniband/hw/irdma/irdma.ko.zst
license:        Dual BSD/GPL
description:    Intel(R) Ethernet Protocol Driver for RDMA
author:         Intel Corporation, <e1000-rdma@lists.sourceforge.net>
alias:          i40iw
srcversion:     D737DFB7C012C4FD2C90536
alias:          auxiliary:i40e.iwarp
alias:          auxiliary:ice.roce
alias:          auxiliary:ice.iwarp
depends:        ib_core,i40e,ib_uverbs,ice
retpoline:      Y
intree:         Y
name:           irdma
vermagic:       6.10.10-arch1-1 SMP preempt mod_unload 
sig_id:         PKCS#7
signer:         Build time autogenerated kernel key
sig_key:        76:91:ED:E7:98:9D:E5:F2:3B:77:21:3A:22:E3:68:1E:5B:CA:A8:B5
sig_hashalgo:   sha512
signature:      30:65:02:30:26:44:C3:3B:24:E0:90:3A:80:7B:32:98:4B:71:A3:FA:
		AA:F2:C0:4E:50:26:B3:E7:F5:65:8D:EF:0E:AB:DC:69:E3:A1:04:EC:
		14:23:FA:62:1A:B1:11:3A:45:68:7E:94:02:31:00:94:0A:80:FC:7F:
		57:8D:76:7D:63:2F:33:91:0A:DF:8C:1A:C4:C9:B3:8E:08:5B:6E:00:
		15:1F:90:D5:63:96:7A:D7:C7:A2:6E:B7:5D:43:53:11:2E:C9:35:49:
		54:11:E4
```

This module has a dependency on ib\_core, i40e, ib\_uverbs, and ice. Each of these drivers is used by many other drivers.

If you want to manually load or unload a driver, use `modprobe` with the `-a` (or `--all`) option to load or insert the module and the `-r` option to remove or unload the module. The `-n`, `--dry-run`, or `--show` option shows you what will be done, without doing it. You usually use the `-v` option with these to see more information. Listing 22 shows what will happen if I try to load the irda module.

```bash
ian@attic‑u16:~$ modprobe ‑nav irnet
insmod /lib/modules/4.4.0‑59‑generic/kernel/lib/crc‑ccitt.ko 
insmod /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irda.ko 
insmod /lib/modules/4.4.0‑59‑generic/kernel/net/irda/irnet/irnet.ko 
```

The output shows the `insmod` commands needed to load each required module with dependencies resolved.

The `modprobe` command takes into account modules that are already loaded. In Listing 22, I first load the crc-ccitt module, then do another dry run, and finally load the irnet module. Note that the actual loading or unloading requires root authority.

### loading the <> module

### Unloading the <> module

## Module Parameters

Some modules have parameters. For example, a device driver might need to know which IRQ or I/O port to use. Listing 24 shows module information for the nsc-ircc module, which has several such parameters.

```bash
ian@attic‑u16:~$ modinfo nsc‑ircc
filename:       /lib/modules/4.4.0‑59‑generic/kernel/drivers/net/irda/nsc‑ircc.ko
license:        GPL
description:    NSC IrDA Device Driver
author:         Dag Brattli <dagb@cs.uit.no>
srcversion:     9AA374A6DFC1C886D632DC3
alias:          acpi:IBM0071:
alias:          pnp:dIBM0071
alias:          acpi:HWPC224:
alias:          pnp:dHWPC224
alias:          acpi:NSC6001:
alias:          pnp:dNSC6001*
depends:        irda
intree:         Y
vermagic:       4.4.0‑59‑generic SMP mod_unload modversions 
parm:           qos_mtt_bits:Minimum Turn Time (int)
parm:           io:Base I/O addresses (array of int)
parm:           irq:IRQ lines (array of int)
parm:           dma:DMA channels (array of int)
parm:           dongle_id:Type‑id of used dongle (int)
```

You specify module parameters on the `modprobe` command line when loading the module. See the man page for more details and for details on the other available `modprobe` options.

