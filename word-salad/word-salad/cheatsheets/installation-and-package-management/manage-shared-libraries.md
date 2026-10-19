---
description: >-
  In this tutorial, learn to find and load the shared libraries that your Linux
  programs need.
icon: book-blank
---

# Manage Shared Libraries

## Objectives

* Determine which libraries a program needs
* Know how the system finds shared libraries
* Load shared libraries

## Shared Libraries

When you write a program, you rely on many pieces of code that someone else has already written to perform routine or specialized functions for you. These pieces of code are stored in shared libraries. To use them, you link them with your code, either when you build or run the program.

### Static and Dynamic Linking

Linux systems have two types of executable programs:

1. **Statically linked executables:** contain all the library functions needed to execute a program; all library functions are linked to the executable. They are complete programs that do not depend on external libraries to run. An advantage of statically linked programs is that they work without installing prerequisites.
2. **Dynamically linked executables**: are much smaller programs; they are incompatible because they require functions from external shared libraries to run. Dynamic linking permits a package to specify prerequisite libraries without needing to include the libraries in the package. By using dynamic linking, many running programs can share one copy of a library rather than occupying memory with many copies of the same code. For these reasons, most modern programs use dynamic linking.

An interesting example of many Linux systems is the `ln` command, which creates links between files - either hard links or soft (symbolic) links. This command uses shared libraries. Shared libraries often involve symbolic links between a generic name for the library and a specific level of the library, so if the links are not present or broken for some reason, then the `ln` command itself might be inoperative, creating a circular problem. To protect against this possibility, some Linux systems include a statically linked version of the `ln` program as the `sln` program. Listing 1 illustrates the difference in size between the dynamically linked version  `ln` and the statically linked version`sln`:

#### Listing 1. Sizes of `ln` and `sln`

```bash
[mdriscoll@localhost] # cat /etc/redhat-release
CentOS Linux release 7.9.2009 (Core)

[mdriscoll@localhost] ls -l /sbin/sln /bin/ln
-rwxr-xr-x. 1 root root  58592 Nov 16  2020 /bin/ln
-rwxr-xr-x. 1 root root 761632 May 18  2022 /sbin/sln
```

### Understanding which libraries are needed

Though not part of the current LPI exam requirements for this topic, you should know that many Linux systems today run hardware that supports both 32-bit and 64-bit executables. Many libraries are thus compiled in 32-bit and 64-bit versions.

The 64-bit versions are usually stored under the `/lib64` tree in the filesystem, while the 32-bit versions live in the traditional `/lib` tree. You will probably find both `/lib/libc-2.11.1.so` an `/lib64/libc-2.11.1.so` on a typical 64-bit Linux system. These two libraries allow both 32-bit and 64-bit `C` programs to run a 64-bit Linux system.

#### Using `ldd`

Apart from knowing that a statically linked program is likely large, how can you tell whether a program is statically linked? And if it is dynamically linked, how do you know what libraries it needs? the `ldd` command answers both questions.

{% hint style="info" %}
If you are running a system such as Debian or Ubuntu, you probably don't have the `sln` executable, so you might also check `/sbin/ldconfig`&#x20;
{% endhint %}

Listing 2 shows the output of the `ldd` command for the `ln` , `sln` , and `ldconfig` executables. Examples came from a Centos7 64-bit and 32-bit Debian 11 system:

<pre class="language-bash"><code class="lang-bash"><strong>[mdriscoll@centos]# ldd /sbin/sln /sbin/ldconfig /bin/ln
</strong>/sbin/sln:
        not a dynamic executable
/sbin/ldconfig:
        not a dynamic executable
/bin/ln:
        linux-vdso.so.1 =>  (0x00007ffdbb196000)
        libc.so.6 => /lib64/libc.so.6 (0x00007fcc56a02000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fcc56dd0000)

[mdriscoll@debian]# cat /etc/*release
PRETTY_NAME="Pengwin"
NAME="Pengwin"
VERSION_ID="11"
VERSION="11 (bullseye)"
ID=debian
ID_LIKE=debian
HOME_URL="https://github.com/whitewaterfoundry/Pengwin"
SUPPORT_URL="https://github.com/WhitewaterFoundry/Pengwin/issues"
BUG_REPORT_URL="https://github.com/WhitewaterFoundry/Pengwin/issues"
VERSION_CODENAME=bullseye
PENGWIN_VERSION="23.03.1"

[mdriscoll@debian]#  ldd /bin/ln
        linux-vdso.so.1 (0x00007ffd73359000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fd7099a3000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fd709baf000)


</code></pre>

Because `ldd` is concerned with dynamic linking, it tells us that both `sln` and `ldconfig` are statically linked by outputting `not a dynamic executable`, while it tells us the names of three shared libraries (`linux-vdso.so.1`, `libc.so.6`, and `/lib64/ld-linux-x86-64.so.2`) that the `ln` command needs. Note that `.so` indicates that these are shared objects or dynamic libraries,<br>

This output also illustrates three different types of information you are likely to see:

* `linux-vdso.so.1` - is the Linux Virtual Dynamic Shared Object. Previous versions of Debian would output `linux-gate.so.1`
* `libc.so.6` - has a pointer to `/lib64/libc.so.6` or `/lib/i386-linux-gnu/libc.so.6`. You can also see this pointing to `/lib/libc.so.6` on older 32-bit systems.
*   `/lib64/ld-linux-x86-64.so.2` - is the absolute path to another library.

    \
    In Listing 3, I use the `ls -l` command to show that the last two libraries are, in turn, symbolic links to specific versions of the libraries. The example is from a Fedora 22 64-bit system. This allows library updates to be installed without relinking all the executables that use the library.

Listing 3. Library symbolic links

```bash
[ian@atticf20 ~]$ # Fedora 22 64‑bit
[ian@atticf20 ~]$ ls ‑l /lib64/libc.so.6 /lib64/ld‑linux‑x86‑64.so.2
lrwxrwxrwx. 1 root root 10 Feb 23 10:33 /lib64/ld‑linux‑x86‑64.so.2 ‑> ld‑2.21.so
lrwxrwxrwx. 1 root root 12 Feb 23 10:33 /lib64/libc.so.6 ‑> libc‑2.21.so
```

Linux Virtual Dynamic Shared Objects

In the early days of x86 processors, communication from user programs to supervisor services occurred through a software interupt. As processor speeds increased, this became a serious bottleneck. Starting with Pentium II processors, Intel introduced a Fast System Call facility to speed up system calls using the `SYSENTER` and `SYSEXIT` instructions instead of interrupts.

The library that you see `linux-vdso.so1` is a virtual library, or Virtual Dynamic Shared Object, that is located only in each program's address space. Some systems call this `linux-gate.so.1`. This virtual library provides the necessary logic to allow user programs to access system functions through the fastest means available on the particular processor, either interrupt or, with most newer processors, Fast Call System.

### Dynamic Loading

From the preceding, you might be surprised to learn that `/lib/ld-linux.so.2` and its 64-bit cousin, `/lib64/ld-linux-x86-64.so.2`Both look like shared libraries and are actually executables in their own right. They are the code that is responsible for dynamic loading. They read the header information from the executable in the **Executable and Linking Format (ELF)** format. From this information, they determine what libraries are required and which must be loaded. They then perform dynamic linking to fix up all the address pointers in your executable and the loaded libraries so that the program will run.

The man page for `ld-linux.so` also describes `ld.so`, which performed similar functions for the earlier _`a.out`_ binary format. Listing 4 illustrates using the `--list` option of the `ld-linux.so` cousins to show the same information for the `ln` command that Listing 2 showed with the `ldd` command.

**Listing 4. Using `ld-linux.so` to display library requirements**

```bash
[ian@atticf20 ~]$ # Fedora 22 64‑bit
[ian@atticf20 ~]$ /lib64/ld‑linux‑x86‑64.so.2 ‑‑list /bin/ln
    linux‑vdso.so.1 (0x00007ffe725f6000)
    libc.so.6 => /lib64/libc.so.6 (0x00007f2179b5d000)
    /lib64/ld‑linux‑x86‑64.so.2 (0x00007f2179f1d000)

ian@attic‑u14:~$ #Ubuntu 14 32‑bit
ian@attic‑u14:~$ /lib/ld‑linux.so.2 ‑‑list /bin/ln
    linux‑gate.so.1 =>  (0xb77bc000)
    libc.so.6 => /lib/i386‑linux‑gnu/libc.so.6 (0xb75f6000)
    /lib/ld‑linux.so.2 (0xb77bf000)


```

Note that the hex addresses might be different between the two listings. They are also likely to be different if you run `ldd` twice.
