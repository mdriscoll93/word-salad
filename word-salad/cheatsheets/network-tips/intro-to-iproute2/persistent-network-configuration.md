---
description: >-
  we will review several different ways to manage / facilitate persistent
  network configuration in Linux
---

# Persistent network configuration

## Networking in Linux

## Static, Dynamic, and wifi Configuration

In the early days of computer networking, every device connected to a network was assigned an IP address and the device was then configured with the address and other information that, most importantly, allowed it to connect to the rest of the network through a gateway and discover other devices using a name server. Later on, special servers were able to automatically assign an address from an available pool. This is known as Dynamic Host Configuration and the protocol used for the exchange, is called _Dynamic Host Configuration Protocol_ or _DHCP_. The IP address was usually reserved for a period of time, so that if the client system went to sleep or temporarily lost connection it would reconnect with the same address. Client systems can also request a particular address, such as the last one assigned, and the DHCP server can honor that request if the address is not being used for another device.

With the introduction of wifi networks, dynamic configuration became event more important. Instead of plugging an Ethernet cable into a wall socket and a port on your laptop, your laptop (or other device) can roam around a building or even a campus. Your system must be able to identify the wireless access points and connect to a suitable one to begin accessing a network. Frequently, you need to provide a password for the wifi network to begin. After connecting to wifi, the normal DHCP address assignment occurs and you can then connect to the Internet or your internal network. As you move around the area covered by the network and possibly move out of range of your initial access point, your system will usually connect automatically to another access point with a stronger signal. If you move out of range of the network altogether, you will go through discovery, authentication, and address assignment for a new network.

We will introduce you to the basic files and commands that make all this happen in a modern Linux system. Most of the examples use IPv4.

## TCP/IP host configuration

A Linux system has a name which is called the host name and this name is used within the system (stand alone or connected to a network) to identify it. Usually, the same host name will be used to identify the system if it is part of a network.&#x20;

When the system is connected to a network or the Internet, has a more rigorous name as part of the Domain Name System (DNS). A DNS name has two parts, the host name and a domain name. The fully qualified domain name (FQDN) consists of the host name followed by a period and then the domain name (for example, myhost.mydomain). Domain names usually consist of multiple parts separated by periods (for example, ibm.com or lpi.org).

The kernel sets the host name value during boot, usually from configuration files.

### finding your hostname

Many Linux systems store the host name in file /etc/hostname. Most of the systems also have a `hostname` command that can be used to display or set the host name.

#### Displaying the hostname

```bash
[ian@attic5-f29 ~]$ # Fedora 29
[ian@attic5-f29 ~]$ ls /etc/hostname
/etc/hostname
[ian@attic5-f29 ~]$ cat /etc/hostname
attic5-f29
[ian@attic5-f29 ~]$ hostname
attic5-f29

ian@attic4-sl42 ~]$ # Slackware 14.2
[ian@attic4-sl42 ~]$ ls /etc/hostname
/bin/ls: cannot access '/etc/hostname': No such file or directory
[ian@attic4-sl42 ~]$ hostname
attic4-sl42
```

The kernel stores the currently active host name in the virtual `/proc` file system in the file, `/proc/sys/kernel/hostname`. The host name and FQDN may also be stored in `/etc/hosts`.

#### changing the hostname

using the `hostname` command as root does not update the value in `/etc/hostname`\


```bash
[ian@attic5-f29 ~]$ cat /etc/hostname
attic5-f29
[ian@attic5-f29 ~]$ hostname
attic5-f29
[ian@attic5-f29 ~]$ sudo hostname attic5-f29-a
[ian@attic5-f29 ~]$ hostname
attic5-f29-a
[ian@attic5-f29 ~]$ cat /etc/hostname
attic5-f29
[ian@attic5-f29 ~]$ cat /proc/sys/kernel/hostname
attic5-f29-a
```

Note that /etc/hostname has not been updated while the virtual /proc/sys/kernel/hostname does show the updated value. If you want to make this change permanent, you need to update /etc/hostname yourself. You may also need to update /etc/hosts or other files.

If your system uses the systemd system and service manager, use the `hostnamectl` command.&#x20;

The `hostnamectl` command has several commands to show status, set host names, or set other values. If used with no commands, or with the `status` command, it displays the current host name status.

**Displaying host name using the hostnamectl command**

```bash
[ian@attic5-f29 ~]$ hostnamectl status
   Static hostname: attic5-f29
Transient hostname: attic5-f29-a
         Icon name: computer-desktop
           Chassis: desktop
        Machine ID: 434ef6f0139941b8bbdeb5b2950278d0
           Boot ID: 3f2201af05364f819287617d8c215ec7
  Operating System: Fedora 29 (Workstation Edition)
       CPE OS Name: cpe:/o:fedoraproject:fedora:29
            Kernel: Linux 5.0.14-200.fc29.x86_64
      Architecture: x86-64
```

You see that the old host name, attic5-f29, is shown as the static host name while the new name, attic5-f29-a, is shown as the transient host name.&#x20;

The `hostnamectl` command distinguishes a third name called the pretty name which can be a descriptive name such as **Ian's UEFI computer**.&#x20;

Use the `set-hostname` command to set one or all of these names. If you do not specify a particular one, all three will be updated to the same new value. Below is how to set the hostname to attic5-f29-b and verify that the /etc/hostname file has been updated.

I also show how to set a pretty host name. The status no longer shows a distinct transient name as it is now the same as the static host name.

#### setting hostname via hostnamectl

```bash
[ian@attic5-f29 ~]$ cat /etc/hostname
attic5-f29
[ian@attic5-f29 ~]$ sudo hostnamectl set-hostname attic5-f29-b
[ian@attic5-f29 ~]$ sudo find /etc -type f -mmin -5
/etc/hostname
[ian@attic5-f29 ~]$ cat /etc/hostname
attic5-f29-b
[ian@attic5-f29 ~]$ sudo hostnamectl --pretty set-hostname "Ian's UEFI desktop"
[ian@attic5-f29 ~]$ hostnamectl
   Static hostname: attic5-f29-b
   Pretty hostname: Ian's UEFI desktop
         Icon name: computer-desktop
           Chassis: desktop
        Machine ID: 434ef6f0139941b8bbdeb5b2950278d0
           Boot ID: 3f2201af05364f819287617d8c215ec7
  Operating System: Fedora 29 (Workstation Edition)
       CPE OS Name: cpe:/o:fedoraproject:fedora:29
            Kernel: Linux 5.0.14-200.fc29.x86_64
      Architecture: x86-64
```

A third way to change the host name is to use the Network Manager command line interface (`nmcli`) to interact with the Network Manager daemon.&#x20;

As with `hostnamectl`, the `nmcli` command has several commands within it. Use the `general` command with the `hostname` option to view or change the host name.&#x20;

As you might expect, you don't need any authority to view the host name but you need root authority to change the host name. here is how to view and set the host name using the `nmcli general` command.

&#x20;**Setting a host name using the nmcli command**

```bash
ian@attic5-f29 ~]$ nmcli general hostname
attic5-f29-b
[ian@attic5-f29 ~]$ sudo nmcli general hostname attic5-f29
[ian@attic5-f29 ~]$ cat /etc/hostname
[ian@attic5-f29 ~]$ hostname
attic5-f29
attic5-f29
[ian@attic5-f29 ~]$ hostnamectl
   Static hostname: attic5-f29
   Pretty hostname: Ian's UEFI desktop
Transient hostname: attic5-f29-b
         Icon name: computer-desktop
           Chassis: desktop
        Machine ID: 434ef6f0139941b8bbdeb5b2950278d0
           Boot ID: 3f2201af05364f819287617d8c215ec7
  Operating System: Fedora 29 (Workstation Edition)
       CPE OS Name: cpe:/o:fedoraproject:fedora:29
            Kernel: Linux 5.0.14-200.fc29.x86_64
      Architecture: x86-64
```

Note that the `nmcli general` command updates /etc/hostname and changes the host name as displayed by `hostname` and the static host name as displayed by `hostnamectl`.&#x20;

The transient host name as displayed by `hostnamectl` is not affected immediately, but will shortly change to the same as the new static host name.&#x20;

The pretty name stored by `hostnamectl` is not affected.

## Finding other hosts

For a network to be useful you need to be able to find other computers and connect to them for services. During boot you don't have any network connections, and you will typically have some minimal information on your computer so you can at least know where to start looking for connections.&#x20;

The /etc/hosts file serves as a minimal address book for host names and will always have an IP v4 entry for local host and often localhost.localdomain.&#x20;

If IPv6 is enabled, you will have additional entries for the IPv6 local host. Your host name and perhaps the domain name may also be stored in /etc/hosts.

Below shows the `/etc/hosts` file from my Ubuntu 18.04 LTS system.

**/etc/hosts on my Ubuntu 18.04 LTS system**

```bash
127.0.0.1 localhost
127.0.1.1 attic5-u18

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

192.168.1.24 attic4 attic4-stw attic4-f28
```

Note that local host uses the usual loopback address of 127.0.0.1, while my host name of attic5-0u18 is shown with a different loopback address, 127.0.1.1.&#x20;

There are also several entries for IP v6 including ::1 for the default loopback address with names ip6-localhost and ip6-loopback.&#x20;

The final line contains several short aliases for IP address 192.168.1.24, another system on my network that boots into one of the several possible systems.

**Pinging local hosts**

```bash
ian@attic5-u18:~$ ping -c 2 localhost
PING localhost (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.032 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.037 ms

--- localhost ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1010ms
rtt min/avg/max/mdev = 0.032/0.034/0.037/0.006 ms
ian@attic5-u18:~$ ping -c2 ip6-loopback
PING ip6-loopback(ip6-localhost (::1)) 56 data bytes
64 bytes from ip6-localhost (::1): icmp_seq=1 ttl=64 time=0.072 ms
64 bytes from ip6-localhost (::1): icmp_seq=2 ttl=64 time=0.065 ms

--- ip6-loopback ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1012ms
rtt min/avg/max/mdev = 0.065/0.068/0.072/0.009 ms
ian@attic5-u18:~$ ping -c2 attic5-u18
PING attic5-u18 (127.0.1.1) 56(84) bytes of data.
64 bytes from attic5-u18 (127.0.1.1): icmp_seq=1 ttl=64 time=0.052 ms
64 bytes from attic5-u18 (127.0.1.1): icmp_seq=2 ttl=64 time=0.039 ms

--- attic5-u18 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1029ms
rtt min/avg/max/mdev = 0.039/0.045/0.052/0.009 ms
ian@attic5-u18:~$ ping -c2 attic4-f28
PING attic4 (192.168.1.24) 56(84) bytes of data.
64 bytes from attic4 (192.168.1.24): icmp_seq=1 ttl=64 time=0.133 ms
64 bytes from attic4 (192.168.1.24): icmp_seq=2 ttl=64 time=0.088 ms

--- attic4 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1014ms
rtt min/avg/max/mdev = 0.088/0.110/0.133/0.024 ms
```

**/etc/hosts on my Fedora 29 system**

```bash
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

When you want to connect to a system such as www.ibm.com or lpi.org, you need more information. The DNS is like a telephone book for domain names. A DNS resolver accepts requests and attempts to resolve names to IP addresses or IP addresses to names. The DNS consists of a number of hierarchically organized servers. Each server caches the results of searches so you can get names resolved as quickly as possible without searching the whole Internet.

The file /etc/resolv.conf tells your system where to start searching. This file is often automatically generated, particularly if the system is configured using DHCP or uses systemd. Below shows a minimal resolv.conf file from a CentOS 7 system.&#x20;

The _nameserver_ option specifies the name server to contact. In this case it is 192.168.1.1, which is the name server in my router. In turn, a router often uses a name server provided by an Internet service provider (ISP) or a public name server such as the Google name servers 8.8.8.8 or 8.8.4.4.

#### basic /etc/resolv.conf

```bash
# Generated by NetworkManager
search lan
nameserver 192.168.1.1
```

If your system uses systemd, the _nameserver_ value in resolv.conf is likely to be 127.0.0.53 which is an internal DNS stub resolver that is part of systemd-resolved. If you run `systemd-resolved --status` on a system similar to the above, you will see that the stub also connects to 192.168.1.1.&#x20;

The /etc/resolv.conf file is also likely to be a symbolic link to /run/systemd/resolve/stub-resolv.conf. If you want to create your own resolv.conf, you should first break this link.

There are other things you can specify in resolv.conf, including a list of domains to search for names that are not fully qualified. See the man page for resolv.conf for additional information.

The Name Service Switch file, /etc/nsswitch.conf, provides additional configuration, including the sources or so-called databases to use for name lookup.&#x20;

Below shows /etc/nsswitch.conf from my Ubuntu 18.04 LTS system. In this example, host names are resolved according to the specification in the hosts line.&#x20;

1. First, search for files (/etc/hosts),&#x20;
2. then use mdns4\_minimal (multicast DNS used for searching small local networks using semantics of regular DNS searches),&#x20;
3. then use DNS,&#x20;
4. and finally see if the current host name matches the search.&#x20;

Now you see why Fedora might choose not to create an entry for the host name in /etc/hosts. Try `dig $(hostname)` to look up your host name on such a system.\


#### /etc/nsswitch.conf Ubuntu 18.04 LTS

```bash
# /etc/nsswitch.conf
#
# Example configuration of GNU Name Service Switch functionality.
# If you have the `glibc-doc-reference' and `info' packages installed, try:
# `info libc "Name Service Switch"' for information about this file.

passwd:         compat systemd
group:          compat systemd
shadow:         compat
gshadow:        files

hosts:          files mdns4_minimal [NOTFOUND=return] dns myhostname
networks:       files

protocols:      db files
services:       db files
ethers:         db files
rpc:            db files

netgroup:       nis
```

You see several other lines in /etc/nsswitch.conf. These specify how other kinds of names should be found.&#x20;

For example, you might use a Lightweight Directory Access Protocol (LDAP) database to store login information for users in your company or school where available computers are shared among whoever can log in.&#x20;

These workstations might automatically reboot after a user logs out. When the next user successfully authenticates, the system might connect to his or her home directory on some kind of networked storage. See the man or information pages for nsswitch.conf for more information on other things that can be in the file.

#### Configuring Ethernet and wifi connections <a href="#configuring-ethernet-and-wifi-connections8" id="configuring-ethernet-and-wifi-connections8"></a>

* an example from legacy /etc/sysconfig/network-scripts/ifcfg-enp4s0 configuration file:

```bash
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
PEERDNS=no
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp4s0
UUID=132a0ea9-e934-4961-b278-0c70dbab84a2
DEVICE=enp4s0
ONBOOT=no
PROXY_METHOD=none
BROWSER_ONLY=no
IPADDR=192.168.1.66
PREFIX=24
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4
```

#### Activating and deactivating interfaces <a href="#activating-and-deactivating-interfaces9" id="activating-and-deactivating-interfaces9"></a>

there are legacy commands, such as the traditional ifup and ifdown to activate and deactivate an interface.

### using ifup / ifdown

```bash
[ian@attic4-ce7 ~]$ sudo ifdown enp4s0
Device 'enp4s0' successfully disconnected.
[ian@attic4-ce7 ~]$ sudo ifup enp4s0
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/9)
```

#### Command line configuration using nmcli <a href="#command-line-configuration-using-nmcli10" id="command-line-configuration-using-nmcli10"></a>

The nmcli command can also manipulate many other aspects of your network configuration. This shows how to display the status of all devices and then how to show the details of the static link that I just configured.

### displaying device status with nmcli

```bash
[ian@attic4-ce7 ~]$ nmcli device status
DEVICE      TYPE      STATE      CONNECTION
enp2s0      ethernet  connected  enp2s0
enp4s0      ethernet  connected  enp4s0
virbr0      bridge    connected  virbr0
lo          loopback  unmanaged  --
virbr0-nic  tun       unmanaged  --
[ian@attic4-ce7 ~]$ nmcli device show enp4s0
GENERAL.DEVICE:                         enp4s0
GENERAL.TYPE:                           ethernet
GENERAL.HWADDR:                         00:23:54:33:F3:EE
GENERAL.MTU:                            1500
GENERAL.STATE:                          100 (connected)
GENERAL.CONNECTION:                     enp4s0
GENERAL.CON-PATH:                       /org/freedesktop/NetworkManager/ActiveCo
WIRED-PROPERTIES.CARRIER:               on
IP4.ADDRESS[1]:                         192.168.1.66/24
IP4.GATEWAY:                            192.168.1.1
IP4.ROUTE[1]:                           dst = 192.168.1.0/24, nh = 0.0.0.0, mt =
IP4.ROUTE[2]:                           dst = 0.0.0.0/0, nh = 192.168.1.1, mt =
IP4.DNS[1]:                             8.8.8.8
IP4.DNS[2]:                             8.8.4.4
IP6.ADDRESS[1]:                         fe80::6d6c:67f7:72a1:e950/64
IP6.GATEWAY:                            --
IP6.ROUTE[1]:                           dst = fe80::/64, nh = ::, mt = 103
IP6.ROUTE[2]:                           dst = ff00::/8, nh = ::, mt = 256, tabl
```

According to the information page for nmcli, Network Manager _stores all network configuration as "connections", which are collections of data (Layer2 details, IP addressing, etc.) that describe how to create or connect to a network. A connection is "active" when a device uses that connection's configuration to create or connect to a network. There may be multiple connections that apply to a device, but only one of them can be active on that device at any given time. The additional connections can be used to allow quick switching between different networks and configurations._

You can show the connection status for a connection such as enp4s0 using the `nmcli connection show enp4s0` command. Indeed `nmcli` can handle several different types of objects or commands as summarized in Table:

| Command/Object	 | Meaning to nmcli                                                                                                                                                                                                 |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| help            | Get brief help from nmcli.                                                                                                                                                                                       |
| general         | Show Network Manager status and permissions. Get and change system host name, or Network Manager logging level and domains.                                                                                      |
| networking      | Query Network Manager networking status and enable and disable networking.                                                                                                                                       |
| radio           | Show status of various radio switches. Enable and disable radio switches.                                                                                                                                        |
| connection      | Show the collections of network data.                                                                                                                                                                            |
| device          | Show or manage network interfaces.                                                                                                                                                                               |
| agent           | Run nmcli as a Network Manager secret agent or polkit agent.                                                                                                                                                     |
| monitor         | See Network Manager activity. Watch for changes in connectivity state, devices, or connection profiles. Note that you can also monitor connection or device objects. See the information pages fro more details. |

Now I will show you how to clone the static configuration and make some modifications using nmcli.&#x20;

Below, I first clone enp4s0 as enp4s0new.&#x20;

Next, I ensure that the connection will not auto connect and I change the IP address to 192.168.1.67/24.

&#x20;Finally, I use the - prefix on `ipv4.dns` to remove the second DNS address.&#x20;

Containers such as a list of DNS servers are numbered origin 0.&#x20;

So, -ipv4.dns 1 deletes the second one (which I set to 8.8.4.4 earlier).&#x20;

Finally, I selectively show the values of the affected fields and confirm that I am still using interface enp4s0.

### cloning and modifying a connection using nmcli

```bash
[ian@attic4-ce7 ~]$ sudo nmcli connection clone enp4s0 enp4s0new
enp4s0 (132a0ea9-e934-4961-b278-0c70dbab84a2) cloned as enp4s0new (ded070be-f416-4313-acfd-f09899c4b27a).
[ian@attic4-ce7 ~]$ sudo nmcli connection modify enp4s0new connection.autoconnect no
[ian@attic4-ce7 ~]$ sudo nmcli connection modify enp4s0new ipv4.addresses 192.168.1.67/24
[ian@attic4-ce7 ~]$ sudo nmcli connection modify enp4s0new -ipv4.dns 1
[ian@attic4-ce7 ~]$ nmcli -f \ipv4.addresses,ipv4.dns,connection.autoconnect,connection.interface-name conn show enp4s0new
[ian@attic4-ce7 ~]$ nmcli -f \
> ipv4.addresses,ipv4.dns,connection.autoconnect,connection.interface-name \
> conn show enp4s0new
ipv4.addresses:                         192.168.1.67/24
ipv4.dns:                               8.8.8.8
connection.autoconnect:                 no
connection.interface-name:              enp4s0
```

## The systemd-networkd daemon

Since systemd version 210, systemd has included networking capability in the form of the _systemd-networkd_ and _systemd-resolved_ daemons for network management and DNS resolution.&#x20;

The _systemd-networkd_ daemon is in active development, so may be missing features that you expect from Network Manager. It has been available since Fedora 21, Ubuntu 15.04, and Debian 8 for example.

Check the version of systemd on your system using `systemctl --version`

```bash
[ian@attic5-f29 ~]$ systemctl --version
systemd 239
+PAM +AUDIT +SELINUX +IMA -APPARMOR +SMACK +SYSVINIT +UTMP +LIBCRYPTSETUP +GCRYPT +GNUTLS +ACL +XZ +LZ4 +SECCOMP +BLKID +ELFUTILS +KMOD +IDN2 -IDN +PCRE2 default-hierarchy=hybrid
```

{% hint style="info" %}
Your system may have both Network Manager and systemd-networkd installed. Use the systemctl status command to see which one you are running. Listing 21 shows that my Fedora 29 system is running Network Manager.
{% endhint %}

```bash
[ian@attic5-f29 ~]$ systemctl status NetworkManager
• NetworkManager.service - Network Manager
   Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2019-06-18 23:09:06 EDT; 17h ago
     Docs: man:NetworkManager(8)
 Main PID: 973 (NetworkManager)
    Tasks: 4 (limit: 4915)
   Memory: 19.0M
   CGroup: /system.slice/NetworkManager.service
           ├─ 973 /usr/sbin/NetworkManager --no-daemon
           └─1531 /sbin/dhclient -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-enp9s0.pid -lf /var/lib/NetworkManager/dhc>

Jun 19 09:03:16 attic5-f29 dhclient[1531]: DHCPACK from 192.168.1.1 (xid=0xb4abcb6b)
Jun 19 09:03:16 attic5-f29 NetworkManager[973]: <info>  [1560949396.1270] dhcp4 (enp9s0):   address 192.168.1.25
Jun 19 09:03:16 attic5-f29 NetworkManager[973]: <info>  [1560949396.1275] dhcp4 (enp9s0):   plen 24 (255.255.255.0)
Jun 19 09:03:16 attic5-f29 NetworkManager[973]: <info>  [1560949396.1275] dhcp4 (enp9s0):   gateway 192.168.1.1
Jun 19 09:03:16 attic5-f29 NetworkManager[973]: <info>  [1560949396.1275] dhcp4 (enp9s0):   lease time 86400
Jun 19 09:03:16 attic5-f29 NetworkManager[973]: <info>  [1560949396.1276] dhcp4 (enp9s0):   hostname 'attic5-f29'
Jun 19 09:03:16 attic5-f29 NetworkManager[973]: <info>  [1560949396.1276] dhcp4 (enp9s0):   nameserver '192.168.1.1'
Jun 19 09:03:16 attic5-f29 NetworkManager[973]: <info>  [1560949396.1276] dhcp4 (enp9s0):   domain name 'lan'
Jun 19 09:03:16 attic5-f29 NetworkManager[973]: <info>  [1560949396.1276] dhcp4 (enp9s0): state changed bound -> bound
Jun 19 09:03:16 attic5-f29 dhclient[1531]: bound to 192.168.1.25 -- renewal in 37454 seconds.
[ian@attic5-f29 ~]$ systemctl status systemd-networkd
• systemd-networkd.service - Network Service
   Loaded: loaded (/usr/lib/systemd/system/systemd-networkd.service; enabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:systemd-networkd.service(8)
```

You see that Network Manager is running and that systemd-networkd is installed and available.

The configuration files used by `systemd-networkd` have an extension of `.network`.

&#x20;They are stored in `/usr/lib/systemd/network,/run/systemd/network` and `/etc/systemd/network`.&#x20;

The files are sorted and processed in lexical order, so that when a device appears, the first match found is applied.

A minimal file to provide a static IPv4 address of 192.168.1.68 instead of the DHCP assigned  might be stored in /etc/systemd/network/.&#x20;

### Sample .network file for systemd-networkd

```bash
[ian@attic5-f29 ~]$ cat /etc/systemd/network/20-static-enp9s0.network
[Match]
Name=enp9s0

[Network]
Address=192.168.1.68/24
Gateway=192.168.1.1
DNS=8.8.8.8
```

To switch from Network Manager to `systemd-networkd`, use the `systemctl` command to first disable Network manager and then enable and start `systemd-networkd` and `systemd-resolved`.&#x20;

Note that `systemd-resolved` will create its own `resolv.conf` file under the `/run/systemd` directory.&#x20;

Since other system services may depend on the one in /etc, it is better to create a symbolic link to the new one.
