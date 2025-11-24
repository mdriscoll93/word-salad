---
description: >-
  learn to shut down or reboot your Linux system, warn users that the system is
  going down, and switch to single-user mode or a more or less restrictive
  runlevel or boot target.
icon: crosshairs-simple
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

To get the most from the tutorials in this series, you should have a basic knowledge of Linux and a working Linux system on which to practice the commands covered in this tutorial. Sometimes different versions of a program will format output differently, so your results may not always look exactly like the listings and figures shown here. In particular, the newer upstart and systemd systems are changing many things that might be familiar to users of the traditional System V init process (see for details). This tutorial commences with discussion of the traditional System V init process and its runlevels. We then discuss upstart and systemd which are newer initialization processes.<br>

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

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption><p>In this example, you should now see the root, kernel, and initrd lines displayed. Move the cursor to the line starting with "kernel" and press 'e' to edit the line. The GRUB screen on our CentOS 6 system now looks like Figure 2.</p></figcaption></figure>

Figure 2. Selecting the kernel entry for editing

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption><p>Finally, move the cursor to the end of the line, and add a space and the digit '3'. You may remove 'quiet' if you wish, or modify any other parameters as needed. The GRUB screen on our CentOS 6 system now looks like Figure 3.</p></figcaption></figure>

Figure 3. Setting the starting runlevel to 3

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption><p>Finally, press <strong>Enter</strong> to save the changes, then type 'b' to boot the system.</p></figcaption></figure>

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

In contrast to personal computer operating systems such as DOS or Windows, Linux is inherently a multiuser system. However, there are times when that can be a problem, such as when you need to recover a major filesystem or database, or install and test some new hardware.&#x20;

Runlevel 1, or _single-user mode_, is your answer for these situations. The actual implementation varies by distribution, but you will usually start in a shell with only a minimal system. Usually there will be no networking and no (or very few) daemons running. On some systems, you must authenticate by logging in, but on others you go straight into a shell prompt as root.&#x20;

Single-user mode can be a lifesaver, but you can also destroy your system, so always be careful whenever you are running with root authority. Reboot to normal multiuser mode as soon as you are done.

As with switching to regular multiuser runlevels, you can also switch to single-user mode using `telinit 1`. As noted in Table 1, 's' and 'S' are aliases for runlevel 1, so you could, for example, use `telinits` instead.

## Clean shutdown

While you can use `telinit` or `init` to stop multiuser activity and switch to single-user mode, this can be rather abrupt and cause users to lose work and processes to terminate abnormally.&#x20;

* The preferred method to shutdown or reboot the system is to use the `shutdown` command,&#x20;
* which first sends a warning message to all logged-in users and blocks any further logins.&#x20;
* It then signals `init` to switch runlevels.&#x20;
* The `init` process then sends all running processes a SIGTERM signal, giving them a chance to save data or otherwise properly terminate.&#x20;
* After 5 seconds, or another delay if specified, `init` sends a SIGKILL signal to forcibly end each remaining process.

By default, `shutdown` switches to `runlevel 1` (**single-user mode**). You may specify the `-h` option to halt the system, or the `-r` option to reboot. A standard message is issued in addition to any message you specify. The time may be specified as an absolute time in _`hh:mm`_ format, or as a relative time, _`n`_, where `n` is the number of minutes until shutdown. For immediate shutdown, use _`now`_, which is equivalent to _`+0`_.

If you have issued a delayed shutdown and the time has not yet expired, you may cancel the shutdown by pressing **`Ctrl-c`** if the command is running in the foreground, or by issuing `shutdown` with the `-c` option to cancel a pending shutdown. Listing 6 shows several examples of the use of `shutdown`, along with ways to cancel the command.

#### **Listing 6. Shutdown examples**

```bash
root@attic4‑cent ~]#shutdown 5 File system recovery needed

Broadcast message from ian@attic4‑cent
    (/dev/pts/1) at 18:11 ...

The system is going down for maintenance in 5 minutes!
File system recovery needed 
^Cshutdown: Shutdown cancelled
root@attic4‑cent ~shutdown ‑r 10 Reloading updated kernel&
[1] 5667
root@attic4‑cent ~
Broadcast message from ian@attic4‑cent
    (/dev/pts/1) at 18:11 ...

The system is going down for reboot in 10 minutes!
Reloading updated kernel 
fg
shutdown ‑r 10 Reloading updated kernel
^Cshutdown: Shutdown cancelled
root@attic4‑cent ~shutdown ‑h 23:59&
[1] 5669
root@attic4‑cent ~
Broadcast message from ian@attic4‑cent
    (/dev/pts/1) at 18:11 ...

The system is going down for halt in 348 minutes!

root@attic4‑cent ~shutdown ‑c
shutdown: Shutdown cancelled
[1]+  Done                    shutdown ‑h 23:59
```

Notice in the last example that our broadcast message came out at 18:11, but we asked for shutdown at 23:59, some 6.5 hours later.&#x20;

The traditional System V implementation of shutdown, does not send a warning message if the shutdown is more than 15 minutes away.&#x20;

Rather it waits till 15 minutes before the scheduled shutdown then sends the message. Listing 7 shows a System V example on Slackware 37 and also shows the use of the `-t` option to increase the default delay between `SIGTERM` and `SIGKILL` signals from 5 seconds to 60 seconds.

**Listing 7. Another shutdown example**

```bash
root@attic4:~#date;shutdown ‑t60 17 Time to do backups&
Tue Jul 14 18:27:08 EDT 2015
[1] 2240
root@attic4:~#
Broadcast message from root (tty1) (Tue Jul 14 18:29:08 2015):

Time to do backups
The system is going DOWN to maintenance mode in 15 minutes!
```

## Notifying users with wall <a href="#notifying-users-with-wall" id="notifying-users-with-wall"></a>

If you do cancel a shutdown, you should use the `wall` command to send a warning to all users alerting them to the fact that the system is **not** going down.&#x20;

The `wall` command either takes a warning on the command line or sends a message from a file. A quick way of sending a multi-line message is to use `echo -e` and pipe the output to `wall`. We illustrate this in Listing 8.



**Listing 8. Using wall to warn users**

```bash
root@atticf22 ~wall Scheduled outage at 23:59 has been canceled
                                                                               
Broadcast message from ian@atticf22 (pts/1) (Tue Jul 14 21:07:05 2015):        
                                                                               
Scheduled outage at 23:59 has been canceled
                                                                               
root@atticf22 ~echo ‑e "We are experiencing system problemsOutage rescheduled to 02:30" | wall
                                                                               
Broadcast message from ian@atticf22 (pts/1) (Tue Jul 14 21:07:36 2015):        
                                                                               
We are experiencing system problems                                            
Outage rescheduled to 02:30
```

As we said earlier, it is also possible to use `telinit` (or `init`) to shut down or reboot the system. As with other uses of `telinit`, no warning is sent to users, and the command takes effect immediately, although there is still a delay between `SIGTERM` and `SIGKILL` signals. For additional options of `telinit`, `init`, and `shutdown`, consult the appropriate man pages.

If you don't want to upset your users with sudden system outages, you need to notify them using `wall` and all other means at your disposal.

## Halt, reboot, and poweroff

You should know about a few more commands related to shutdown and reboot.

* The `halt` command halts the system.
* The `poweroff` command is a symbolic link to the `halt` command, which halts the system and then attempts to power it off.
* The `reboot` command is another symbolic link to the `halt` command, which halts the system and then reboots it.

If any of these are called when the system is not in `runlevel 0` or `6`, then the corresponding `shutdown` command will be invoked instead.

For additional options that you may use with these commands, as well as more detailed information on their operation, consult the man page.<br>

## System V /etc/inittab

By now, you may be wondering why pressing **`Ctrl-Alt-Delete`** on some systems causes a reboot, or how all this runlevel stuff is configured.&#x20;

Remember the `id` field in /etc/inittab that we saw earlier? Well, there are several other fields in `/etc/inittab`, along with a set of `init scripts` in directories such as `rc1.d` or `rc5.d`, where the digit identifies the runlevel to which the scripts in that directory apply. Listing 9 shows the full inittab from our Slackware 37 system.

**Listing 9. Full inittab from Slackware 37**

```bash
#inittab    This file describes how the INIT process should set up
#        the system in a certain run‑level.
#
#Version:    @#inittab        2.04    17/05/93    MvS
#                                      2.10    02/10/95        PV
#                                      3.00    02/06/1999      PV
#                                      4.00    04/10/2002      PV
#                                     13.37    2011‑03‑25      PJV
#
#Author:    Miquel van Smoorenburg, <miquels@drinkel.nl.mugnet.org>
#Modified by:    Patrick J. Volkerding, <volkerdi@slackware.com>
#

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

#System initialization (runs when system boots).
si:S:sysinit:/etc/rc.d/rc.S

#Script to run when going single user (runlevel 1).
su:1S:wait:/etc/rc.d/rc.K

#Script to run when going multi user.
rc:2345:wait:/etc/rc.d/rc.M

#What to do at the "Three Finger Salute".
ca::ctrlaltdel:/sbin/shutdown ‑t5 ‑r now

#Runlevel 0 halts the system.
l0:0:wait:/etc/rc.d/rc.0

#Runlevel 6 reboots the system.
l6:6:wait:/etc/rc.d/rc.6

#What to do when power fails.
pf::powerfail:/sbin/genpowerfail start

#If power is back, cancel the running shutdown.
pg::powerokwait:/sbin/genpowerfail stop

#These are the standard console login getties in multiuser mode:
c1:12345:respawn:/sbin/agetty 38400 tty1 linux
c2:12345:respawn:/sbin/agetty 38400 tty2 linux
c3:12345:respawn:/sbin/agetty 38400 tty3 linux
c4:12345:respawn:/sbin/agetty 38400 tty4 linux
c5:12345:respawn:/sbin/agetty 38400 tty5 linux
c6:12345:respawn:/sbin/agetty 38400 tty6 linux

#Local serial lines:
#s1:12345:respawn:/sbin/agetty ‑L ttyS0 9600 vt100
#s2:12345:respawn:/sbin/agetty ‑L ttyS1 9600 vt100

#Dialup lines:
#d1:12345:respawn:/sbin/agetty ‑mt60 38400,19200,9600,2400,1200 ttyS0 vt100
#d2:12345:respawn:/sbin/agetty ‑mt60 38400,19200,9600,2400,1200 ttyS1 vt100

#Runlevel 4 also starts /etc/rc.d/rc.4 to run a display manager for X.
#Display managers are preferred in this order:  gdm, kdm, xdm
x1:4:respawn:/etc/rc.d/rc.4

#End of /etc/inittab
```

As usual, lines starting with # are comments. Other lines have several fields with the following format:\
`id:runlevels:action:process`

* id: is a unique identifier of one to four characters. Older versions limited this to two characters, so you may see only two characters used.
* runlevels: lists the runlevels for which the action for this id should be taken. If no runlevels are listed, do the action for all runlevels
* action: describes which of several possible actions should be taken.
* process: tells which process, if any, should be run when the action on this line is performed.

Some of the common actions that may be specified in `/etc/inittab` are shown in Table 3. See the man pages for inittab for other possibilities.

| Action      | Purpose                                                                                                                                        |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| respawn     | Restart the process whenever it terminates. Usually used for `getty` processes, which monitor for logins                                       |
| wait        | Start the process once when the specified runlevel is entered and wait for its termination before init proceeds.                               |
| once        | Start the process once when the specified runlevel is entered.                                                                                 |
| initdefault | Specifies the runlevel to enter after system boot.                                                                                             |
| ctrlaltdel  | Execute the associated process when init receives the `SIGINT` signal, for example, when someone on the system console presses `CTRL-ALT-DEL`. |

Listing 10 shows just the entry for `Ctrl-Alt-Delete` from Listing 9. So now you see why pressing Ctrl-Alt-Delete causes the system to be rebooted.

**Listing 10. Trapping Ctrl-Alt-Delete**

```bash
#What to do at the "Three Finger Salute".
ca::ctrlaltdel:/sbin/shutdown ‑t5 ‑r now
```

## System V Initialization scripts

You may have noticed several lines in Listing 9, such as:

```bash
#Script to run when going multi user.
rc:2345:wait:/etc/rc.d/rc.M
```

In this example, `init` will run the `/etc/rc.d/M` script (or command) whenever runlevel 2,3,4, or 5 are entered. `init` will wait until this command completes before doing anything else.

These scripts used by `init` when starting the system, changing runlevels, or shutting down are typically stored in the /etc/init.d or /etc/rc.d directory. A series of symbolic links in the r&#x63;_&#x6E;_.d directories, one directory for each runlevel _n_, usually control whether a script is started when entering a runlevel or stopped when leaving it. These links start with either a K or an S, followed by a two-digit number and then the name of the service. Some examples from an older system are shown in Listing 11.

**Listing 11. Init scripts**

```bash
root@pinguino ~find /etc ‑path "rc[0‑9].d/???au"
/etc/rc.d/rc2.d/S27auditd
/etc/rc.d/rc2.d/K72autofs
/etc/rc.d/rc4.d/S27auditd
/etc/rc.d/rc4.d/S28autofs
/etc/rc.d/rc5.d/S27auditd
/etc/rc.d/rc5.d/S28autofs
/etc/rc.d/rc0.d/K72autofs
/etc/rc.d/rc0.d/K73auditd
/etc/rc.d/rc6.d/K72autofs
/etc/rc.d/rc6.d/K73auditd
/etc/rc.d/rc1.d/K72autofs
/etc/rc.d/rc1.d/K73auditd
/etc/rc.d/rc3.d/S27auditd
/etc/rc.d/rc3.d/S28autofs
root@pinguino ~cd /etc/rc.d/rc5.d
root@pinguino rc5.dls ‑l ???a
lrwxrwxrwx 1 root root 16 2008‑04‑07 11:29 S27auditd ‑> ../init.d/auditd
lrwxrwxrwx 1 root root 16 2008‑04‑01 07:51 S28autofs ‑> ../init.d/autofs
lrwxrwxrwx 1 root root 15 2008‑04‑01 14:03 S44acpid ‑> ../init.d/acpid
lrwxrwxrwx 1 root root 13 2008‑04‑01 07:50 S95atd ‑> ../init.d/atd
lrwxrwxrwx 1 root root 22 2008‑04‑01 07:54 S96avahi‑daemon ‑> ../init.d/avahi‑daemon
lrwxrwxrwx 1 root root 17 2008‑11‑17 13:40 S99anacron ‑> ../init.d/anacron
```

Here you see that the `audit` and `autofs` services have &#x4B;_&#x6E;n_ entries in all runlevels and &#x53;_&#x6E;n_ entries for both in runlevels 3 and 5. The S indicates that the service is started when that runlevel is entered, while the K entry indicates that it should be stopped. The _nn_ component of the link name indicates the priority order in which the service should be started or stopped. In this example, `audit` is started before `autofs`, and it is stopped later. Since the K and S links usually link to the same script, the script knows whether it is entering or leaving a level by examining the link name by which it was called.

However, Slackware uses a slightly different approach, as shown in Listing 12. Notice that the /etc/rc\[0-9].d directories are all empty. Entering a particular runlevel is controlled by a script such as the script to go multiuser, /etc/rc.d/rc.M which is used when entering run levels 2, 3, 4, or 5.

**Listing 12. Slackware 37 Init scripts**

```bash
root@attic4:~#find /etc/rc[0‑9].
/etc/rc0.d
/etc/rc1.d
/etc/rc2.d
/etc/rc3.d
/etc/rc4.d
/etc/rc5.d
/etc/rc6.d
root@attic4:~#ls /etc/rc./???a
/etc/rc.d/rc.acpid  /etc/rc.d/rc.atalk
/etc/rc.d/rc.alsa*   /etc/rc.d/rc.autofs
```

Compare the /etc/rc.d/rc.autofs script with the /etc/rc.d/rc2.d/K72autofs and /etc/rc.d/rc4.d/S28autofs symbolic links pointing to /etc/init.d/autofs in the previous example.

Consult the `init` and `inittab` man pages for more information.

## Beyond Init

As we have seen here, the traditional method of booting a Linux system is based on the UNIX System V init process. It involves loading an initial RAM disk (initrd) and then passing control to a program called `init`, a program that is usually installed as part of the sysvinit package. The `init` program is the first process in the system and has PID (Process ID) 1. It runs a series of scripts in a predefined order to bring up the system. If something that is expected is not available, the init process typically waits until it is. While this worked adequately for systems where everything is known and connected when the system starts, modern systems with hot-pluggable devices, network file systems, and even network interfaces that may not be available at start time present new challenges. Certainly, waiting for hardware that may not come available for a long time, or even just a relatively long time, is not desirable.

In the following sections of this tutorial we will describe two alternatives to System V init, _upstart_ and _systemd_.

If you are not sure what system initialization you have, remember that the first process started on your system has Process Id (PID) 1. Find out which program is running as PID1. If it's called "init" then figure out which package provides it. Listing 13 shows how to do this on several systems.

**Listing 13. Finding the package that provides init**

```bash
[ian@atticf22 lpic‑1]$ #Fedora 22
[ian@atticf22 lpic‑1]$ ps ‑p 1 ‑o comm=
systemd
[ian@atticf22 lpic‑1]$ #Ah! It's systemd

ian@attic4:~$ #Slackware 37
ian@attic4:~$ ps ‑p 1 ‑o comm=
init
ian@attic4:~$ grep  sbin/init /var/log/packages/*
/var/log/packages/sysvinit‑2.86‑x86_64‑6:sbin/init.new
/var/log/packages/sysvinit‑2.86‑x86_64‑6:sbin/initscript.sample
/var/log/packages/sysvinit‑functions‑8.53‑x86_64‑2:sbin/initlog
ian@attic4:~$ #System V style init

ian@attic‑u14:~$ #Ubuntu 14
ian@attic‑u14:~$ ps ‑p 1 ‑o comm=
init
ian@attic‑u14:~$ dpkg ‑S which init
upstart: /sbin/init
ian@attic‑u14:~$ #Upstart

ian@yoga‑u15:~$ #Ubuntu 15.04
ian@yoga‑u15:~$ ps ‑p 1 ‑o comm=
systemd
ian@yoga‑u15:~$ #Ubuntu has switched from upstart to systemd

[ian@attic4‑cent ~]$ #CentOS 6
[ian@attic4‑cent ~]$ ps ‑p 1 ‑o comm=
init
[ian@attic4‑cent ~]$ rpm ‑q ‑‑whatprovides which init
upstart‑0.6.5‑13.el6_5.3.x86_64
[ian@attic4‑cent ~]$ #Another upstart system
```

With both upstart and systemd, vestiges of System V Init remain, particularly /etc/fstab and a skeletal /etc/inittab. The concept of runlevel is not directly supported. System V commands such as `telinit` are supported, but their function is mapped internally into activation and deactivation of upstart jobs or systemd units. Needless to say, the mapping may sometimes be less than perfect, so if you boot into runlevel 3 and then use telinit to switch to runlevel 5, your system may or may not work exactly as it would had you rebooted. The "SysVinit to Systemd Cheatsheet" (see [Resources)](https://developer.ibm.com/learningpaths/lpic1-exam-101-topic-101/l-lpic1-101-3/#artrelatedtopics) can help you map the concepts and commands from System V init to systemd.

Upstart<br>
-----------

A new initialization process called _upstart_ was first introduced in Ubuntu 6.10 ("Edgy Eft") in 2006. Fedora 9 through 14 and Red Hat Enterprise Linux (RHEL) 6 used upstart, as did distributions derived from these. Upstart supplanted the init process in Ubuntu among others, although vestiges of init remained. Upstart has not been updated since 2014, although it is still used in Google's proprietary Chrome OS.

In contrast to the static set of init scripts used in earlier systems, the upstart system is driven by _events_. Events may be triggered by hardware changes, starting or stopping or tasks, or by any other process on the system. Events are used to trigger _tasks_ or _services_, collectively known as _jobs_. So, for example, connecting a USB drive might cause the udev service to send a _block-device-added_ event, which would cause a defined task to check /etc/fstab and mount the drive if appropriate. As another example, an Apache web server may be started only when both a network and required filesystem resources are available.

The upstart initialization program replaces /sbin/init. Upstart jobs are defined in the /etc/init directory and its subdirectories. The upstart system can process /etc/inittab and System V init scripts. On systems such as recent Fedora releases, /etc/inittab is likely to contain only the id entry for the initdefault action. Ubuntu systems using Upstart did not have /etc/inittab by default, although you could create one if you wanted to specify a default runlevel.

Upstart also has the initctl command to allow interaction with the upstart init daemon. This allows you to start or stop jobs, list jobs, get status of jobs, emit events, restart the init process, and so on. Listing 14 shows how to use `initctl` to obtain a list of upstart jobs on a Fedora 13 system.

**Listing 14. Interacting with upstart init daemon using initctl**<br>

```bash
ian@attic‑u14:~/data/lpic‑1$ #Ubuntu 14
ian@attic‑u14:~/data/lpic‑1$ initctl list
gnome‑keyring‑gpg stop/waiting
indicator‑application start/running, process 1935
unicast‑local‑avahi stop/waiting
update‑notifier‑crash stop/waiting
update‑notifier‑hp‑firmware stop/waiting
xsession‑init stop/waiting
dbus start/running, process 1734
update‑notifier‑cds stop/waiting
gnome‑keyring‑ssh stop/waiting
gnome‑session (Unity) start/running, process 1802
ssh‑agent stop/waiting
unity7 stop/waiting
unity‑voice‑service stop/waiting
upstart‑dbus‑session‑bridge start/running, process 1837
indicator‑messages start/running, process 1884
logrotate stop/waiting
indicator‑bluetooth start/running, process 1885
unity‑panel‑service start/running, process 1835
hud start/running, process 1798
im‑config start/running
unity‑gtk‑module stop/waiting
session‑migration stop/waiting
upstart‑dbus‑system‑bridge start/running, process 1789
at‑spi2‑registryd start/running, process 1801
indicator‑power start/running, process 1889
update‑notifier‑release stop/waiting
indicator‑datetime start/running, process 1891
unity‑settings‑daemon start/running, process 1794
indicator‑sound start/running, process 1894
upstart‑file‑bridge start/running, process 1790
gnome‑keyring stop/waiting
window‑stack‑bridge start/running, process 1754
indicator‑printers start/running, process 1902
re‑exec stop/waiting
upstart‑event‑bridge start/running, process 1745
unity‑panel‑service‑lockscreen stop/waiting
indicator‑session start/running, process 1918
```

Systemd<br>
-----------

Another new initialization system called _systemd_ is also emerging. Systemd was developed by Lennart Poettering in early 2010. He described the rationale and design in a blog post (see references). Early adopters were Fedora 15, openSUSE 12.1 and Mandriva 2011 among others. With release 15.04 Ubuntu has switched from upstart to systemd.

Many daemon processes communicate using sockets. In order to gain speed and enhance parallelism in the system startup, systemd creates these sockets at startup, but only starts the associated task when a connection request for services on that socket is received. In this way, services can be started only when they are first required and not necessarily at system initialization. Services that need some other facility will block until it is available, so only those services that are waiting for some other process need block while that process starts.

Extending the idea of waiting for services systemd uses autofs to define mount points, so the mount point for a file system is available, but the actual mount may be delayed until some process attempts to open a file on the file system or otherwise use it.

These ideas not only delay startup of services until needed, they also reduce the need for dependency checking between services, as the interface for the service can be ready long before the service itself needs to be available.

Like upstart, systemd can process existing initialization from /etc/inittab. It can also process /etc/fstab to control file system mounting. Native systemd initialization revolves around the concept of _units_, which can be grouped into _control groups_ or _cgroups_. Among the several types of units, you may find the following:

* Service units  are daemons that can be started, stopped, restarted, and reloaded
* Socket units encapsulate a socket in the file-system or on the internet
* Device units encapsulate a device in the Linux Device Tree
* Mount units encapsulate a mount point in the file system hierarchy
* Automount units encapsulate an automount point in the file system hierarchy
* Target units group other units together, providing a single control unit for multiple other units
* Snapshot units reference other units and can be used to save and roll back the state of all services and units of the init system (for example, during suspend)

Units are configured using a configuration file, which includes the unit type as a suffix. For example, cups.service, rpcbind.socket or getty.target. The location of system configuration files (for example, /etc/systemd/system) can be determined by using the `pkg-config` command as shown in Listing 15, which shows the location on a Fedora 17 system. Systemd also checks /usr/local/lib/systemd/system and /usr/lib/systemd/system for configuration information.

**Listing 15. Locating the systemd system configuration directory**

```bash
[ian@atticf22 ~]$ pkg‑config systemd ‑‑variable=systemdsystemconfdir
/etc/systemd/system
```

The `systemctl` command allows you to interrogate and control the systemd daemon, including starting and stopping units or listing their status. Listing 16 illustrates the use of `systemctl` to display the status of some of the systemd units on my Fedora 22 system.

\
**Listing 16. Partial output from systemctl**

```bash
[ian@atticf22 ~]$ systemctl ‑‑no‑pager
  UNIT                          LOAD   ACTIVE SUB       DESCRIPTION
  proc‑sys‑fs‑binfmt_misc.automount loaded active running   Arbitrary Executable File Form
  sys‑devices‑pci0000:00‑0000:00:02.0‑0000:01:00.1‑sound‑card1.device loaded active plugged   
GF119 HDMI Audio Controller
  sys‑devices‑pci0000:00‑0000:00:06.0‑0000:03:00.0‑net‑enp3s0.device loaded active plugged   
RTL8111/8168/8411 PCI Express 
  sys‑devices‑pci0000:00‑0000:00:11.0‑ata1‑host0‑target0:0:0‑0:0:0:0‑block‑sda‑sda1.device 
loaded active plugged   WDC_WD6401AALS‑00L3B2 /grubfil
...
  sys‑devices‑pci0000:00‑0000:00:11.0‑ata1‑host0‑target0:0:0‑0:0:0:0‑block‑sda‑sda9.device 
loaded active plugged   WDC_WD6401AALS‑00L3B2 9
  sys‑devices‑pci0000:00‑0000:00:11.0‑ata1‑host0‑target0:0:0‑0:0:0:0‑block‑sda.device 
loaded active plugged   WDC_WD6401AALS‑00L3B2
...
  sys‑devices‑pci0000:00‑0000:00:12.2‑usb1‑1\x2d6‑1\x2d6:1.2‑sound‑card2.device loaded 
active plugged   Webcam C310
  sys‑devices‑pci0000:00‑0000:00:14.1‑ata6‑host5‑target5:0:0‑5:0:0:0‑block‑sr0.device loaded 
active plugged   Optiarc_DVD_RW_AD‑7240S
...
  sys‑module‑configfs.device    loaded active plugged   /sys/module/configfs
  sys‑module‑fuse.device        loaded active plugged   /sys/module/fuse
...
  sys‑subsystem‑net‑devices‑enp3s0.device loaded active plugged   RTL8111/8168/8411 PCI Express 
  sys‑subsystem‑net‑devices‑virbr0.device loaded active plugged   /sys/subsystem/net/devices/vir
  sys‑subsystem‑net‑devices‑virbr0\x2dnic.device loaded active plugged   /sys/subsystem/net/devices/vir
  ‑.mount                       loaded active mounted   /
...
  cups.path                     loaded active waiting   CUPS Scheduler
  systemd‑ask‑password‑plymouth.path loaded active waiting   Forward Password Requests to P
  systemd‑ask‑password‑wall.path loaded active waiting   Forward Password Requests to W
  session‑1.scope               loaded active abandoned Session 1 of user ian
  session‑2.scope               loaded active running   Session 2 of user ian
  session‑c1.scope              loaded active running   Session c1 of user gdm
...
  crond.service                 loaded active running   Command Scheduler
  cups.service                  loaded active running   CUPS Scheduler
  dbus.service                  loaded active running   D‑Bus System Message Bus
  dracut‑shutdown.service       loaded active exited    Restore /run/initramfs on shut
  fedora‑import‑state.service   loaded active exited    Import network configuration f
  fedora‑readonly.service       loaded active exited    Configure read‑only root suppo
...
● mcelog.service                loaded failed failed    Machine Check Exception Loggin
  NetworkManager.service        loaded active running   Network Manager
  nfs‑config.service            loaded active exited    Preprocess NFS configuration
  packagekit.service            loaded active running   PackageKit Daemon
...
  cups.socket                   loaded active running   CUPS Scheduler
  dbus.socket                   loaded active running   D‑Bus System Message Bus Socke
  dm‑event.socket               loaded active listening Device‑mapper event daemon FIF
...
  sockets.target                loaded active active    Sockets
  sound.target                  loaded active active    Sound Card
  swap.target                   loaded active active    Swap
  sysinit.target                loaded active active    System Initialization
  timers.target                 loaded active active    Timers
  dnf‑makecache.timer           loaded active waiting   dnf makecache timer
  systemd‑tmpfiles‑clean.timer  loaded active waiting   Daily Cleanup of Temporary Dir

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high‑level unit activation state, i.e. generalization of SUB.
SUB    = The low‑level unit activation state, values depend on unit type.

164 loaded units listed. Pass ‑‑all to see loaded but inactive units, too.
To show all installed unit files use 'systemctl list‑unit‑files'.
```

The last part of the unit name (such as device, path, target) is the unit type.

The `systemctl` command allows you to interrogate and control the system units. We show a few examples in where we first interrogate the SSH server daemon (sshd.service). Then we stop it and check the status again. Finally, we start it again.



**Listing 17. Some systemctl actions**

```bash
root@atticf22 ~#Fedora 22
[root@atticf22 ~systemctl status sshd.service
● sshd.service ‑ OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2015‑07‑15 10:24:03 EDT; 5h 42min ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 765 (sshd)
   CGroup: /system.slice/sshd.service
           └─765 /usr/sbin/sshd ‑D

Jul 15 10:24:03 atticf20 systemd[1]: Started OpenSSH server daemon.
Jul 15 10:24:03 atticf20 systemd[1]: Starting OpenSSH server daemon...
Jul 15 10:24:03 atticf20 sshd[765]: Server listening on 0.0.0.0 port 22.
Jul 15 10:24:03 atticf20 sshd[765]: Server listening on :: port 22.
root@atticf22 ~systemctl stop sshd.service
root@atticf22 ~systemctl status sshd.service
● sshd.service ‑ OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: disabled)
   Active: inactive (dead) since Wed 2015‑07‑15 16:06:28 EDT; 3s ago
     Docs: man:sshd(8)
           man:sshd_config(5)
  Process: 765 ExecStart=/usr/sbin/sshd ‑D $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 765 (code=exited, status=0/SUCCESS)

Jul 15 10:24:03 atticf20 systemd[1]: Started OpenSSH server daemon.
Jul 15 10:24:03 atticf20 systemd[1]: Starting OpenSSH server daemon...
Jul 15 10:24:03 atticf20 sshd[765]: Server listening on 0.0.0.0 port 22.
Jul 15 10:24:03 atticf20 sshd[765]: Server listening on :: port 22.
Jul 15 16:06:28 atticf22 systemd[1]: Stopping OpenSSH server daemon...
Jul 15 16:06:28 atticf22 systemd[1]: Stopped OpenSSH server daemon.
root@atticf22 ~systemctl start sshd.service
```

## Advanced Configuration and Power Interface

The Advanced Configuration and Power Interface event daemon (`acpid`) is designed to notify user-space programs of power events. Examples include pressing the power button, or opening or closing the lid on a laptop.

The `acpid` daemon usually runs continuously and can be controlled using the `systemctl` program. It responds to events as configured in /etc/acpi/events. It reacts to configured events by taking actions which are usually defined in /etc/acpi or /etc/acpi/actions. Listing 18 shows the structure of /etc/acpi and a sample event definition for the power button. Note that the action is the `power.sh` shell script in /etc/acpi/actions.

**Listing 18. Example `acpid` event definition.**

```bash
[ian@attic5-f36 ~]$ ls /etc/acpi
actions  events
[ian@attic5-f36 ~]$ ls /etc/acpi/events/
powerconf  videoconf
[ian@attic5-f36 ~]$ cat /etc/acpi/events/powerconf
# ACPID config to power down machine if powerbutton is pressed, but only if
# no gnome-power-manager is running

event=button/power.*
action=/etc/acpi/actions/power.sh
```

Use the `kacpimon` program to monitor ACPI events and ensure that events are being sent. Because it connects to the same sources as `acpid`, use `systemctl` to stop acpid before running `kacpimon`. Listing 19 shows how to confirm that `acpid` is not running and then use `kacpimon` to check the events being sent. In this example the event sources are listed, and then there are two events - key down and key up for the Escape key.

**Listing 19. Using `kacpimon` to monitor ACPI events**

```bash
[ian@attic5-f36 ~]$ sudo kacpimon
Kernel ACPI Event Monitor...
open for /proc/acpi/event: No such file or directory (2)
  (ACPI proc filesystem may not be present)
/dev/input/event0 (Power Button) opened successfully
/dev/input/event1 (Power Button) opened successfully
/dev/input/event10 (PC Speaker) opened successfully
/dev/input/event11 (Eee PC WMI hotkeys) opened successfully
/dev/input/event12 (HDA NVidia HDMI/DP,pcm=3) opened successfully
/dev/input/event13 (HDA NVidia HDMI/DP,pcm=7) opened successfully
/dev/input/event14 (HDA NVidia HDMI/DP,pcm=8) opened successfully
/dev/input/event15 (HDA NVidia HDMI/DP,pcm=9) opened successfully
/dev/input/event16 (HDA ATI HDMI HDMI/DP,pcm=3) opened successfully
/dev/input/event17 (HDA ATI HDMI HDMI/DP,pcm=7) opened successfully
/dev/input/event18 (HDA ATI HDMI HDMI/DP,pcm=8) opened successfully
/dev/input/event19 (HDA ATI HDMI HDMI/DP,pcm=9) opened successfully
/dev/input/event2 (PixArt Lenovo USB Optical Mouse) opened successfully
/dev/input/event20 (HDA ATI SB Front Mic) opened successfully
/dev/input/event21 (HDA NVidia HDMI/DP,pcm=10) opened successfully
/dev/input/event22 (HDA ATI HDMI HDMI/DP,pcm=10) opened successfully
/dev/input/event23 (HDA ATI SB Rear Mic) opened successfully
/dev/input/event24 (HDA ATI SB Line) opened successfully
/dev/input/event25 (HDA NVidia HDMI/DP,pcm=11) opened successfully
/dev/input/event26 (HDA ATI SB Line Out Front) opened successfully
/dev/input/event27 (HDA ATI SB Line Out Surround) opened successfully
/dev/input/event28 (HDA ATI SB Line Out CLFE) opened successfully
/dev/input/event29 (HDA ATI SB Line Out Side) opened successfully
/dev/input/event3 (HID 0c45:7403) opened successfully
/dev/input/event30 (HDA ATI SB Front Headphone) opened successfully
/dev/input/event4 (HID 0c45:7403) opened successfully
/dev/input/event5 (Lenovo Lenovo Black Silk USB Keyboard) opened successfully
/dev/input/event6 (Lenovo Lenovo Black Silk USB Keyboard System Control) opened successfully
/dev/input/event7 (Lenovo Lenovo Black Silk USB Keyboard Consumer Control) opened successfully
/dev/input/event8 (HID 0c45:7403) opened successfully
/dev/input/event9 (HID 0c45:7403) opened successfully
Netlink ACPI Family ID: 27
Netlink ACPI Multicast Group ID: 6
netlink opened successfully
Press Escape to exit, or Ctrl-C if that doesn't work.
Input Layer:  Type: 4  Code: 4  Value: 458792
Input Layer:  Type: 1  Code: 28  Value: 0
Input Layer:  Sync
Input Layer:  Type: 4  Code: 4  Value: 458793
Escape key pressed
Input Layer:  Type: 1  Code: 1  Value: 1
Closing files...
^[Goodbye
[ian@attic5-f36 ~]$
```

\
\
\
\
<br>
