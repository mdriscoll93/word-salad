---
description: >-
  learn to shut down or reboot your Linux system, warn users that the system is
  going down, and switch to single-user mode or a more or less restrictive
  runlevel or boot target.
icon: crosshairs-simple
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: false
---

# Runlevels, boot targets, shutdown, and reboot

## Overview:

* Set the default runlevel or boot target
* Change between runlevels or boot targets
* Change to single-user mode
* Shut down or reboot the system from the command line
* Alert users about major system events, including switching to another runlevel or boot target
* Terminate processes properly
* Understand ACPID

Unless otherwise noted, the examples in this tutorial use a Slackware 13.37 system with 2.6.37 kernel. The upstart examples use Ubuntu 14.04 LTS with a 3.16.0 kernel. The systemd examples use Fedora 22 with a 4.0.4 kernel. Your results on other systems may differ.

## Running Linux

Most of the time a Linux system runs as a multiuser system, often as a server with many different processes running under several different user ids. Sometimes it has a graphical user interface and serves mostly a single user, while at other times it is a headless server working for many users.&#x20;

When you need to do some kinds of system maintenance you don't want all those users trying to get things done, so you need to be able to run the system with a single user rather than many. You also need to switch to this mode cleanly, giving appropriate warnings to logged-in users. And you need to get back into regular operation as quickly as possible. This tutorial shows you how.

To get the most from the tutorials in this series, you should have a basic knowledge of Linux and a working Linux system on which to practice the commands covered in this tutorial. Sometimes different versions of a program will format output differently, so your results may not always look exactly like the listings and figures shown here. In particular, the newer upstart and systemd systems are changing many things that might be familiar to users of the traditional System V init process (see for details). This tutorial commences with discussion of the traditional System V init process and its runlevels. We then discuss upstart and systemd which are newer initialization processes.\


## System V Runlevels

The traditional System V _Runlevels_ define what tasks can be accomplished in the current state (or runlevel) of a Linux system. Traditional Linux systems support three basic runlevels, plus one or more runlevels for normal operation. The basic runlevels are shown in Table 1.

#### Table 1. Linux Basic Runlevels

| Level | Purpose                                         |
| ----- | ----------------------------------------------- |
| 0     | Shut down (halt) system                         |
| 1     | Single-user-mode; usually aliased as 's' or 'S' |
| 6     | Reboot system                                   |

Beyond the basics, runlevel usage differs among distributions. One common usage set is shown in Table 2.

#### Table 2. Other common Runlevels

| Level | Purpose                                             |
| ----- | --------------------------------------------------- |
| 2     | Multiuser mode without networking                   |
| 3     | Multiuser mode wit networking                       |
| 5     | Multiuser  mode with networking and X Window System |

The Slackware distribution uses runlevel 4 instead of 5 for a full system running the X Window system. Debian and derivatives, such as Ubuntu, use a single runlevel for any multiuser mode, typically runlevel 2. Be sure to consult the documentation for your distribution.

## System V default runlevel

When a Linux system starts, the default runlevel is determined from the `id:` entry in `/etc/inittab` . Listing 1 illustrates a piece of the file `/etc/inittab` from a Slackware system set to run in non-graphical multiuser mode.

#### Listing 1. Default runlevel in `/etc/inittab`&#x20;

```bash
#These are the default runlevels in Slackware:
#  0 = halt
#  1 = single user mode
#  2 = unused (but configured the same as runlevel 3)
#  3 = multiuser mode (default Slackware runlevel)
#  4 = X11 with KDM/GDM/XDM (session managers)
#  5 = unused (but configured the same as runlevel 3)
#  6 = reboot

#Default runlevel. (Do not set to 0 or 6)
id:3:initdefault:
```

Edit this value if you want your system to start in a different runlevel, say runlevel 4.

## Changing runlevels

There are several ways to change runlevels. To make a permanent change, you can edit /etc/inittab and change the default level that you just saw above.

If you only need to bring the system up in a different runlevel for one boot, you can do this. For example, suppose you just installed a new kernel and need to build some kernel modules after the system booted with the new kernel, but before you start the X Window System. You might want to bring up the system in runlevel 3 to accomplish this. You do this at boot time by editing the kernel line (GRUB or GRUB 2) or adding a parameter after the selected system name (LILO). Use a single digit to specify the desired runlevel (3, in this case). We'll illustrate the process with a GRUB example. Suppose your /boot/grub/menu.lst file contains the stanza shown in Listing 2 to boot CentOS. We've broken the long kernel line for publication using the backslash () character.

**Listing 2. Typical GRUB stanza to boot CentOS 8**

```bash
title CentOS (2.6.32‑504.23.4.el6.x86_64)
    root (hd0,10)
    kernel /boot/vmlinuz‑2.6.32‑504.23.4.el6.x86_64 ro 
        root=UUID=2f60a3b4‑ef6c‑4d4c‑9ef4‑50d7f75124a2 rd_NO_LUKS 
        rd_NO_LVM LANG=en_US.UTF‑8 rd_NO_MD SYSFONT=latarcyrheb‑sun16 
        crashkernel=128M  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM rhgb quiet
```

To bring this system up in runlevel 3, wait till the boot entries are displayed, select this entry and enter 'e' to edit the entry. Depending on your GRUB options, you may need to press a key to display the boot entries and also enter 'p' and a password to unlock editing. The GRUB screen on our CentOS 6 system looks like Figure 1.

Figure 1. Selecting a boot choice in GRUB

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption><p>In this example, you should now see the root, kernel, and initrd lines displayed. Move the cursor to the line starting with "kernel" and press 'e' to edit the line. The GRUB screen on our CentOS 6 system now looks like Figure 2.</p></figcaption></figure>

Figure 2. Selecting the kernel entry for editing

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>Finally, move the cursor to the end of the line, and add a space and the digit '3'. You may remove 'quiet' if you wish, or modify any other parameters as needed. The GRUB screen on our CentOS 6 system now looks like Figure 3.</p></figcaption></figure>

Figure 3. Setting the starting runlevel to 3

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption><p>Finally, press <strong>Enter</strong> to save the changes, then type 'b' to boot the system.</p></figcaption></figure>

#### Notes:

1. The steps for doing this using LILO or GRUB 2 differ from those for GRUB, but the basic principle of editing the way the kernel is started remains. Even GRUB screens on other systems or other distributions may look quite different to those shown here. Prompts will usually be available to help you.
2. On systems that use either upstart or systemd, runlevels are simulated and some things may not work exactly as you might expect. This is particularly true if you try to change a runlevel by using `telinit`

Once you have finished your setup work in runlevel 3, you will probably want to switch to runlevel 5. On a traditional System V init system, you do not need to reboot the system. You can use the `telinit` command to switch to another runlevel. Use the `runlevel` command to show both the previous runlevel and the current one. If the first output character is 'N', the runlevel has not been changed since the system was booted. Listing 3 illustrates verifying and changing the runlevel.

**Listing 3. Verifying and changing the runlevel**

```bash
root@attic4‑cent ~runlevel
N 3
root@attic4‑cent ~telinit 5
```

After you enter `telinit5` you will see several messages flash by and your display will switch to the configured graphical login screen. Open a terminal window and verify that the runlevel has been changed as shown in Runlevel 2.

**Listing 4. Confirming the new runlevel**

```bash
[ian@attic4‑cent ~]$ runlevel
3 5
```

If you use the `ls` command to display a long listing of the `telinit` command on a system that uses System V init, such as Slackware 37, you will see that it really is a symbolic link to the `init` command. We illustrate this in Listing 5. If your system uses upstart of systemd this will probably not be the case.

**Listing 5. telinit - a symbolic link to init on System V style systems**

```bash
root@attic4:~##Slackware 37
root@attic4:~#ls ‑l $(which telinit)
lrwxrwxrwx 1 root root 4 Aug 28  2011 /sbin/telinit ‑> init*
```

The `init` executable knows whether it was called as `init` or `telinit` and behaves accordingly. Since `init` runs as PID 1 at boot time, it is also smart enough to know when you subsequently invoke it using `init` rather than `telinit`. If you do, it will assume you want it to behave as if you had called `telinit` instead. For example, you may use init 5`init5` instead of `telinit 5` to switch to runlevel 5.

## Single-user Mode

In contrast to personal computer operating systems such as DOS or Windows, Linux is inherently a multiuser system. However, there are times when that can be a problem, such as when you need to recover a major filesystem or database, or install and test some new hardware. Runlevel 1, or _single-user mode_, is your answer for these situations. The actual implementation varies by distribution, but you will usually start in a shell with only a minimal system. Usually there will be no networking and no (or very few) daemons running. On some systems, you must authenticate by logging in, but on others you go straight into a shell prompt as root. Single-user mode can be a lifesaver, but you can also destroy your system, so always be careful whenever you are running with root authority. Reboot to normal multiuser mode as soon as you are done.

As with switching to regular multiuser runlevels, you can also switch to single-user mode using `telinit1`. As noted in Table 1, 's' and 'S' are aliases for runlevel 1, so you could, for example, use `telinits` instead.

## Clean shutdown

#### **Listing 6. Shutdown examples**

**Listing 7. Another shutdown example**

## Notifying users with wall <a href="#notifying-users-with-wall" id="notifying-users-with-wall"></a>

**Listing 8. Using wall to warn users**

## Halt, reboot, and poweroff

## System V /etc/inittab

**Listing 9. Full inittab from Slackware 37**

**Listing 10. Trapping Ctrl-Alt-Delete**

## System V Initialization scripts

**Listing 11. Init scripts**

**Listing 12. Slackware 37 Init scripts**

## Beyond Init

**Listing 13. Finding the package that provides init**

Upstart\



**Listing 14. Interacting with upstart init daemon using initctl**\


Systemd\



**Listing 15. Locating the systemd system configuration directory**

\
**Listing 16. Partial output from systemctl**

**Listing 17. Some systemctl actions**

## Advanced Configuration and Power Interface

**Listing 18. Example `acpid` event definition.**

**Listing 19. Using `kacpimon` to monitor ACPI events**

\
\
\
\
\
