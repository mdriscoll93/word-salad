---
description: client-side DNS configuration on Linux hosts
---

# Configure client-side DNS

{% tabs %}
{% tab title="overview" %}
* Query remote DNS servers
* Configure local name resolution and use remote DNS servers
* Modify the order in which name resolution takes place
* Debug errors related to name resolution
* Be aware of `systemd-resolved` service
{% endtab %}

{% tab title="DNS" %}
The Domain Name System (DNS) is a decentralized and hierarchical system for managing names associated with computers attached to the Internet. The most important DNS function is to map names to IP addresses, although the system maintains other information as well. A system of authoritative servers is used to manage top-level domains. Administrators can delegate authority over sub domains. The system is therefore both robust and fault-tolerant. This tutorial focuses on the client side of DNS and how you interact with DNS servers.
{% endtab %}

{% tab title="review" %}
## Names

per [#tcp-ip-host-configuration](persistent-network-configuration.md#tcp-ip-host-configuration "mention")

> A Linux system has a name, which is called the hostname, and this name is used within the system (stand-alone or connected to a network) to identify it. Usually, the same host name will be used to identify the system if it is part of a network. When the system is connected to a network or the Internet, it has a more rigorous name as part of the DNS. A DNS name has two parts, the hostname and a domain name. The fully qualified domain name (FQDN) consists of the hostname followed by a period and then the domain name (for example, myhost.mydomain or host99.cybershields.com). Domain names usually consist of multiple parts separated by periods (for example, ibm.com or lpi.org).


{% endtab %}
{% endtabs %}

## Query a remote DNS Server

Using `host` to query info from a DNS server - command 1 shows some basic examples. Note that cybershields.com has an IPv4 and a mail server record, while Google has both IPv4 and IPv6 and multiple mail records:<br>

### `host` command basic usage

```bash
~ $  host cybershields.com
cybershields.com has address 192.185.225.101
cybershields.com mail is handled by 0 mail.cybershields.com.
~ $  host ibm.com
ibm.com has address 23.64.138.216
ibm.com has IPv6 address 2600:1404:d400:2a6::3831
ibm.com has IPv6 address 2600:1404:d400:298::3831
ibm.com mail is handled by 5 mx0b-001b2d05.pphosted.com.
ibm.com mail is handled by 5 mx0a-001b2d05.pphosted.com.
~ $  host acronis.com
acronis.com has address 34.120.97.237
acronis.com mail is handled by 10 bg-edge-1.acronis.com.
acronis.com mail is handled by 10 sg-edge-1.acronis.com.
~ $  host google.com
google.com has address 142.250.114.102
google.com has address 142.250.114.101
google.com has address 142.250.114.138
google.com has address 142.250.114.100
google.com has address 142.250.114.139
google.com has address 142.250.114.113
google.com has IPv6 address 2607:f8b0:4023:1009::64
google.com has IPv6 address 2607:f8b0:4023:1009::66
google.com has IPv6 address 2607:f8b0:4023:1009::8b
google.com has IPv6 address 2607:f8b0:4023:1009::71
google.com mail is handled by 10 smtp.google.com.

```

Another thing you'll come across often is shown in the following command, where an alias allows multiple domain names to be mapped to a single address record that can ease the task of maintaining the name server records. By default, the `host` command will recursively follow aliases to the final resolution or resolutions. In the example shown here, www.cybershields.com is an alias for cybershields.com. On the other hand, www.lpi.org does not use an alias. Finally, the ibm.com domain shows a chain of several aliases that eventually resolve to one IPv4 address and two IPv6 addresses. You can try this with other common subdomains such as mail or ftp. For example, try:<br>

<pre class="language-bash"><code class="lang-bash"><strong>~ $ host mail.google.com
</strong>mail.google.com has address 142.250.138.19
mail.google.com has address 142.250.138.17
mail.google.com has address 142.250.138.18
mail.google.com has address 142.250.138.83
mail.google.com has IPv6 address 2607:f8b0:4023:1002::11
mail.google.com has IPv6 address 2607:f8b0:4023:1002::53
mail.google.com has IPv6 address 2607:f8b0:4023:1002::12
mail.google.com has IPv6 address 2607:f8b0:4023:1002::13

</code></pre>

### Host Aliases

```bash
[ian@attic5-f33 ~]$ host www.cybershields.com
www.cybershields.com is an alias for cybershields.com.
cybershields.com has address 192.185.225.101
cybershields.com mail is handled by 0 mail.cybershields.com.
[ian@attic5-f33 ~]$ host www.lpi.org
www.lpi.org has address 65.39.134.165

[ian@attic5-f33 ~]$ host lpi.org
lpi.org has address 65.39.134.165
lpi.org mail is handled by 0 aspmx.l.google.com.
lpi.org mail is handled by 10 aspmx2.googlemail.com.
lpi.org mail is handled by 5 alt2.aspmx.l.google.com.
lpi.org mail is handled by 10 aspmx5.googlemail.com.
lpi.org mail is handled by 10 aspmx3.googlemail.com.
lpi.org mail is handled by 5 alt1.aspmx.l.google.com.
lpi.org mail is handled by 10 aspmx4.googlemail.com.

[ian@attic5-f33 ~]$ host www.ibm.com
www.ibm.com is an alias for www.ibm.com.cs186.net.
www.ibm.com.cs186.net is an alias for outer-ccdn-dual.ibmcom.edgekey.net.
outer-ccdn-dual.ibmcom.edgekey.net is an alias for outer-ccdn-dual.ibmcom.edgekey.net.globalredir.akadns.net.
outer-ccdn-dual.ibmcom.edgekey.net.globalredir.akadns.net is an alias for e2874.dscx.akamaiedge.net.
e2874.dscx.akamaiedge.net has address 104.68.247.188
e2874.dscx.akamaiedge.net has IPv6 address 2600:1408:5c00:3a2::b3a
e2874.dscx.akamaiedge.net has IPv6 address 2600:1408:5c00:384::b3a
```

The `host` command has several options, including options to search for specific types of records. Table 1 shows some common types of records that you may see.<br>

### Common DNS record types:

| Type  | Request For Comments (RFC) | Use                                                                                                                                                                          |
| ----- | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A     | RFC 1035                   | Address record for IPv4 address.                                                                                                                                             |
| AAA   | RFC 3596                   | Address record for IPv6 address.                                                                                                                                             |
| CNAME | RFC 1035                   | Canonical name or alias of one record to another. Lookup usually continues recursively.                                                                                      |
| MX    | RFC 1035 and RFC 7505      | Mail exchange. Maps a domain name to one or more message transfer agents                                                                                                     |
| PTR   | RFC 1035                   | Pointer to a CNAME. Returns the CNAME without recursing.                                                                                                                     |
| SOA   | RFC 1035 and RFC 2308      | Start of authority. Information about a DNS zone, including the primary name server, the email of the domain administrator, the domain serial number, and other information. |
| TXT   | RFC 1035                   | Originally for human readable text but now often carries machine readable information.                                                                                       |

Use the `-t` option of the `host` command to search only for specific types of DNS records. Listing 3 shows some examples. A default type or set of types is used if no type is specified. The default for a domain name query is to search for A, AAAA, and MX records.

**Search for specific DNS record types.**

```bash
[ian@attic5-f33 ~]$ host -t cname www.cybershields.com
www.cybershields.com is an alias for cybershields.com.
[ian@attic5-f33 ~]$ host -t A www.cybershields.com
www.cybershields.com is an alias for cybershields.com.
cybershields.com has address 192.185.225.101
[ian@attic5-f33 ~]$ host -t MX cybershields.com
cybershields.com mail is handled by 0 mail.cybershields.com.
[ian@attic5-f33 ~]$ host -t AAAA cybershields.com
cybershields.com has no AAAA record
[ian@attic5-f33 ~]$ host -t aaaa www.google.com
www.google.com has IPv6 address 2607:f8b0:4004:814::2004
```

Notice that cybershields.com has no AAAA record and the `host` command reports this. You might also see an error message indicating that the domain has not been found. Now consider the examples shown in Listing 4.

**Different things the host command may return.**

```bash
[ian@attic5-f33 ~]$ # Canonical record found
[ian@attic5-f33 ~]$ host ftp.cybershields.com
ftp.cybershields.com is an alias for cybershields.com.
cybershields.com has address 192.185.225.101
cybershields.com mail is handled by 0 mail.cybershields.com.
[ian@attic5-f33 ~]$ # Specifically requested record type not found
[ian@attic5-f33 ~]$ host -t aaaa cybershields.com
cybershields.com has no AAAA record
[ian@attic5-f33 ~]$ # Domain name not found at all
[ian@attic5-f33 ~]$ host ftp.lpi.org
Host ftp.lpi.org not found: 3(NXDOMAIN)
[ian@attic5-f33 ~]$ # No returned data and no error
[ian@attic5-f33 ~]$ host ftp.ibm.com
```

This indicates that there is some kind of record for ftp.ibm.com but it is not one of the few default types that are returned. Add the `-a` option to have the `host` command request all record types as shown in Listing 5.

**Requesting all DNS records for a host.**

```bash
ian@attic5-f33 ~]$ host -a ftp.ibm.com
Trying "ftp.ibm.com"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32432
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;ftp.ibm.com.            IN    ANY

;; ANSWER SECTION:
ftp.ibm.com.        1799    IN    TXT    "atlassian-domain-verification=WAjTH82C5Zx475WLKAA2nrdlsoA/kN0ej9igrLrED4h15KMHPOm+A5H3GndKAxDC"

Received 136 bytes from 127.0.0.53#53 in 101 ms
```

Notice that using the `-a` option returns more detailed information and also a TXT record.

If you want to see even more output, you can use either the `-d` (debug) or `-v` (verbose output. These two are equivalent. Listing 6 shows an example of all the output from a search of lpi.org.

### Verbose host output.

```bash
[ian@attic5-f33 ~]$ host -va lpi.org
Trying "lpi.org"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 33935
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;lpi.org.            IN    ANY

;; ANSWER SECTION:
lpi.org.        519    IN    TXT    "v=spf1 include:spfa.mailendo.com include:_spf.google.com ip4:69.90.69.224/27 ip4:65.39.134.128/26 ~all"
lpi.org.        519    IN    A    65.39.134.165
lpi.org.        519    IN    NS    dns1.easydns.com.
lpi.org.        519    IN    NS    dns3.easydns.ca.
lpi.org.        519    IN    NS    dns2.easydns.net.
lpi.org.        519    IN    MX    0 aspmx.l.google.com.
lpi.org.        519    IN    MX    10 aspmx4.googlemail.com.
lpi.org.        519    IN    MX    10 aspmx5.googlemail.com.
lpi.org.        519    IN    MX    10 aspmx2.googlemail.com.
lpi.org.        519    IN    MX    10 aspmx3.googlemail.com.
lpi.org.        519    IN    MX    5 alt1.aspmx.l.google.com.
lpi.org.        519    IN    MX    5 alt2.aspmx.l.google.com.
lpi.org.        519    IN    SOA    dns1.easydns.com. zone.easydns.com. 1605290523 3600 600 1209600 300

Received 462 bytes from 127.0.0.53#53 in 25 ms
```

How much more information can you get from the `host` command?\
Several other options are available. See the man or info pages for more details.

### Using a specific DNS server for the host command <a href="#using-a-specific-dns-server-for-the-host-command" id="using-a-specific-dns-server-for-the-host-command"></a>

Note the last line of Listing 6, which shows data coming from 127.0.0.53#53. I will come back to that IP address later in this tutorial. That is a local address on my system, so where is the real DNS information coming from I certainly am not running a full local DNS server. To improve speed, DNS information is cached locally and the systemd-resolved daemon on my system responds to the host command.

You can specify the name server to use, for a host query, by adding its IP address to the command as shown in Listing 7.

### &#x20;**Using a specific DNS server for host searches.**

```bash
[ian@attic5-f33 ~]$ $ Using Google public DNS
bash: $: command not found...
[ian@attic5-f33 ~]$ # Using Google public DNS
[ian@attic5-f33 ~]$ host -t A lpi.org 8.8.8.8
Using domain server:
Name: 8.8.8.8
Address: 8.8.8.8#53
Aliases:

lpi.org has address 65.39.134.165
[ian@attic5-f33 ~]$ #using Sprintlink (Public) DNS
[ian@attic5-f33 ~]$ host -t A lpi.org 204.117.214.10
Using domain server:
Name: 204.117.214.10
Address: 204.117.214.10#53
Aliases:

lpi.org has address 65.39.134.165
```

### Where is my DNS information <a href="#where-is-my-dns-information" id="where-is-my-dns-information"></a>

Three configuration files in /etc are concerned with DNS name resolution.

1. `/etc/hosts` - usually a small file used during boot or for isolated networks. It will minimally contain an entry for the local host, usually as just localhost or localhost. localdomain. I typically add an entry for other Linux systems on my home network.

The next command shows a sample from an Ubuntu 20.03.1 LTS system, followed by the file from my Slackware 14.2 system.

### Example /etc/hosts on Ubuntu 20.04 LTS

```bash
ian@attic5-u20:~$ cat /etc/hosts
127.0.0.1    localhost
127.0.1.1    attic5-u20
192.168.1.24    attic4

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

### **Example /etc/hosts on Slackware 14.2**

```bash
ian@attic4-sl42:~$ cat /etc/hosts
#
# hosts        This file describes a number of hostname-to-address
#        mappings for the TCP/IP subsystem.  It is mostly
#        used at boot time, when no name servers are running.
#        On small systems, this file can be used instead of a
#        "named" name server.  Just add the names, addresses
#        and any aliases to this file...
#
# By the way, Arnt Gulbrandsen <agulbra@nvg.unit.no> says that 127.0.0.1
# should NEVER be named with the name of the machine.  It causes problems
# for some (stupid) programs, irc and reputedly talk. :^)
#

# For loopbacking.
127.0.0.1        localhost
127.0.0.1        attic4-sl42.cybershields.org attic4-sl42
192.168.1.25            attic5

# End of hosts.
```

The second configuration file is /\_etc/\_resolv.conf. This file contains an entry for at least one and up to three name servers. It can also have domain and server records as options. The domain record is derived from your hostname, often localdomain, if you get our IP address using Dynamic Host Configuration Protocol (DHCP). You may also see lan as shown in Listing 10. If you use a short hostname, such as host99, the DNS search will also append the domain name. So, it will also search for host99.lan.

&#x20;**Example /\_etc/\_resolv.conf on Slackware 14.2**

```bash
ian@attic4-sl42:~$ cat /etc/resolv.conf
# Generated by dhcpcd from eth0.dhcp
# /etc/resolv.conf.head can replace this line
domain lan
nameserver 192.168.1.1
# /etc/resolv.conf.tail can replace this line
```

Next shows the resolv.conf file from my Ubuntu 20.04.1 LTS system. Note that this file has a search entry but no domain entry. These two entries are functionally similar, except that the search entry may contain a list of blank or tab-separated domain names to be successively used for adding to a simple name. The domain entry supports only one such name.

**Example /etc/resolv.conf on Ubuntu 20**

```bash
ian@attic5-u20:~$ cat /etc/resolv.conf
# This file is managed by man:systemd-resolved(8). Do not edit.
#
# This is a dynamic resolv.conf file for connecting local clients to the
# internal DNS stub resolver of systemd-resolved. This file lists all
# configured search domains.
#
# Run "resolvectl status" to see details about the uplink DNS servers
# currently in use.
#
# Third party programs must not access this file directly, but only through the
# symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,
# replace this symlink by a static file or a different symlink.
#
# See man:systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.

nameserver 127.0.0.53
options edns0 trust-ad
search lan
```

Notice that the search and domain entries specify a domain of 'lan'. You may see this or local (not recommended) or localdomain as names for the local domain, particularly in automatically generated resolv.conf files as these both are.

I will now add the domain cybershields.com to the search entry on my Ubuntu system. Listing 12 shows two examples of how the search or domain works. Galaxy-A20 is a smart phone on my local lan. The domain cybershields.com has an MX record as I showed you in earlier listings.

**Examples of search with domain**

```bash
ian@attic5-u20:~$ host Galaxy-A20
Galaxy-A20.lan has address 192.168.1.69
Host Galaxy-A20.lan not found: 3(NXDOMAIN)
Host Galaxy-A20.lan not found: 3(NXDOMAIN)
ian@attic5-u20:~$ host mail
mail.cybershields.com has address 192.185.225.101
```

## Using multiple name servers

You can have up to three _nameserver_ entries in /etc/resolv.conf. The order in which you specify them is in which they are used. If the first server is not found, then the second server is tried, and so on.&#x20;

I will add an entry for the 1.1.1.1 name server to illustrate, as shown in Listing 13. Recall that the `-v` option of the `host` command displays which name server provided each piece of information.

### Multiple name servers

```bash
ian@attic5-u20:~$ tail -n 4 /etc/resolv.conf
nameserver 1.1.1.1
nameserver 127.0.0.53
options edns0 trust-ad
search lan cybershields.com
ian@attic5-u20:~$ host -v cybershields.com
Trying "cybershields.com"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52249
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;cybershields.com.        IN    A

;; ANSWER SECTION:
cybershields.com.    13913    IN    A    192.185.225.101

Received 50 bytes from 1.1.1.1#53 in 27 ms
Trying "cybershields.com"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52643
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 0

;; QUESTION SECTION:
;cybershields.com.        IN    AAAA

;; AUTHORITY SECTION:
cybershields.com.    85912    IN    SOA    ns6597.hostgator.com. root.gator3299.hostgator.com. 2020110303 86400 7200 3600000 86400

Received 102 bytes from 1.1.1.1#53 in 27 ms
Trying "cybershields.com"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 25553
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;cybershields.com.        IN    MX

;; ANSWER SECTION:
cybershields.com.    13912    IN    MX    0 mail.cybershields.com.

Received 55 bytes from 1.1.1.1#53 in 31 ms
```

## Name Service Switch and /etc/nsswitch.conf <a href="#name-service-switch-and-etc-nsswitch-conf" id="name-service-switch-and-etc-nsswitch-conf"></a>

Name lookup functions in the glibc library were traditionally configured using files such as /etc/passwd or /etc/hosts. A better solution was needed with the advent of other name services, such as Network Information Service (NIS) and Domain Name Service (DNS). Enter the Name Service Switch (or nss) function, which was designed using a method used by Sun Microsystems in the C library of Solaris 2.

The following command shows the /etc/nsswitch.conf file from my Slackware system.

### Slackware /etc/nsswitch.conf

```bash
ian@attic4-sl42:~$ cat /etc/nsswitch.conf              
#
# /etc/nsswitch.conf
#
# An example Name Service Switch config file. This file should be
# sorted with the most-used services at the beginning.
#
# The entry '[NOTFOUND=return]' means that the search for an
# entry should stop if the search in the previous entry turned
# up nothing. Note that if the search failed due to some other reason
# (like no NIS server responding) then the search continues with the
# next entry.
#
# Legal entries are:
#
#      nisplus or nis+         Use NIS+ (NIS version 3)
#      nis or yp               Use NIS (NIS version 2), also called YP
#      dns                     Use DNS (Domain Name Service)
#      files                   Use the local files
#      [NOTFOUND=return]       Stop searching if not found so far
#

# passwd:     files nis
# shadow:     files nis
# group:      files nis

passwd:     compat
group:      compat

hosts:      files dns
networks:      files

services:       files
protocols:      files
rpc:         files
ethers:      files
netmasks:       files
netgroup:       files
bootparams:     files

automount:      files
aliases:        files
```

The hosts line indicates that the host lookup will first use the local files and then DNS. Another standard entry here is used in my Ubuntu 20 system:\
`hosts: files mdns4_minimal [NOTFOUND=return] dns`

The `mdns4_minimal` Service is multicast DNS (see RFC 6762 for more details). This service searches for hosts on the local LAN in the local domain, such as mylaptop.local. This service is used by Apple's Bonjour and Avahi for discovery of local devices such as printers or scanners.&#x20;

Generally, you do not want searches for local names to be sent to a public DNS server, and so the qualification `[NOTFOUND=return]` causes searches for .local names to terminate and the _not found_ indication to return to the requester.

### Using getent with NSS <a href="#using-getent-with-nss" id="using-getent-with-nss"></a>

Use the `getent` command to query the various Name Service Switch (NSS) data sources. You specify the database and the key that you want. Some databases are enumerated if no key is specified, but not all support this. Listing 15 shows a few simple examples. See the man or info pages for names that are allowed for databases.

### basic use of getent

```bash
ian@attic5-u20:~$ getent hosts attic4
192.168.1.24    attic4
ian@attic5-u20:~$ getent services ssh
ssh                   22/tcp
ian@attic5-u20:~$ getent hosts www.google.com
2607:f8b0:4004:810::2004 www.google.com
ian@attic5-u20:~$ getent ahostsv4 www.google.com
172.217.13.68   STREAM www.google.com
172.217.13.68   DGRAM  
172.217.13.68   RAW
```

**Specifying particular services with getent**

```bash
ian@attic4-sl42:~$ getent -s files hosts attic5          
192.168.1.25    attic5
ian@attic4-sl42:~$ getent -s dns hosts attic5            
ian@attic4-sl42:~$ getent -s files hosts cybershields.com
ian@attic4-sl42:~$ getent -s dns hosts cybershields.com  
192.185.225.101 cybershields.com
```

## Using systemd-resolvd

If your system uses the systemd initialization process rather than init, there are some differences in DNS resolution to consider. The **`systemd-resolvd`** service provides network name resolution and implements a stub DNS resolver. The main configuration file is `/etc/systemd/resolved.conf`. See the man or info pages or additional sources used by systemd-resolved. Here are some commands that can help you determine whether you are using systemd or init and whether you are running systemd-resolved:<br>

### Am I using init or systemd?

```bash
an@attic4-sl42:~$ head -n 2 /etc/os-release
NAME=Slackware
VERSION="14.2"

ian@attic4-sl42:~$ ps -p 1
  PID TTY          TIME CMD
    1 ?        00:00:02 init
    
ian@attic4-sl42:~$ # Slackware 42 uses init

ian@attic4-sl42:~$ head -n 2 /etc/os-release
NAME=Slackware
VERSION="14.2"

ian@attic4-sl42:~$ ps -p 1
  PID TTY          TIME CMD
    1 ?        00:00:02 init

ian@attic5-u20:~$ # Ubuntu 20 uses systemd

ian@attic5-u20:~$ head -n 2 /etc/os-release
NAME="Ubuntu"
VERSION="20.04.1 LTS (Focal Fossa)"

ian@attic5-u20:~$ ps -p 1
    PID TTY          TIME CMD
      1 ?        00:01:02 systemd
      
ian@attic5-u20:~$ pidof systemd-resolved
744
```

I mentioned earlier that the two `resolv.conf` examples I used were automatically generated. The Ubuntu example shows a nameserver value of `127.0.0.53` - the **stub resolver** created by systemd-resolved.&#x20;

In contrast, the Slackware example shows a nameserver of 192.168.1.1, which is also my router. The router provides a DNS proxy service at the same address. Check the examples of the host command I used in this tutorial to see which server is responding.

Needless to say, the changes I made to `/etc/resolv.conf` will be overwritten whenever these files are regenerated. Persistent changes can be made in `/etc/systemd/resolved.conf` if you are using systemd-resolved, or in `/etc/dhclient.conf` if your `resolv.conf` is generated by dhcp.&#x20;

Note that there is likely to be a `/etc/dhclient.conf` example file, which contains examples of things you can set or change.\
\
Use the `resolvectl status` command to find the actual DNS server that your systemd-resolved DNS stub resolver is using upstream. <br>

**Partial output from resolvectl status**

```bash
ian@attic5-u20:~$ resolvectl status | tail -n 13
Link 2 (enp9s0)
      Current Scopes: DNS                                    
DefaultRoute setting: yes                                    
       LLMNR setting: yes                                    
MulticastDNS setting: no                                     
  DNSOverTLS setting: no                                     
      DNSSEC setting: no                                     
    DNSSEC supported: no                                     
  Current DNS Server: 192.168.1.1                            
         DNS Servers: 192.168.1.1                            
                      2603:6081:1902:7ddb:24f7:f0ff:fe02:30df
          DNS Domain: ~.                                     
                      lan
```

As a final note on systemd-resolved, try examples as shown in Listing 16 and see what happens. Did you expect this? Think about which host the stub resolver is running on and how it gets its configuration information.<br>

## Debugging name resolution errors

Most DNS resolution errors are the result of misconfigured DNS servers resulting in a host or domain not being found.&#x20;

Your first step in debugging is to run the host command with a known hostname to see what happens.&#x20;

Listing 20 shows an example where i have changed my resolv.conf file so that there are no valid DNS nameserver entries.

### no valid name servers

```bash
ian@attic5-u20:~$ tail -n 4 /etc/resolv.conf
nameserver 192.168.1.24
# nameserver 127.0.0.53
options edns0 trust-ad
search lan
ian@attic5-u20:~$ host ibm.com
;; connection timed out; no servers could be reached
```

You now know the main configuration files to check and some commands to help you.

One other command that is often used in DNS debugging is the `dig` command. It is extremely flexible and, by default, provides output that is more like the verbose out from the `host` command. A typical invocation of `dig` is of the form:

`dig @server name type`

Note the '@' symbol before the optional DNS server name to use. You can specify a type to look up, such as A, AAAA, or MX. The default type to look up is A, or the IPv4 address. The `-t` option is optional unless required to disambiguate other options. Listing 20 shows two simple examples, first using the default name server to search for the default A record of lpi.org and then using a specific name server to search for an AAAA (IPv6) record for google.

### examples of using dig

```bash
ian@attic5-u20:~$ dig lpi.org

; <<>> DiG 9.16.1-Ubuntu <<>> lpi.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18932
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;lpi.org.            IN    A

;; ANSWER SECTION:
lpi.org.        287    IN    A    65.39.134.165

;; Query time: 0 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Sun Nov 29 13:33:09 EST 2020
;; MSG SIZE  rcvd: 52

ian@attic5-u20:~$ dig @8.8.8.8 google.com aaaa

; <<>> DiG 9.16.1-Ubuntu <<>> @8.8.8.8 google.com aaaa
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 50553
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.            IN    AAAA

;; ANSWER SECTION:
google.com.        299    IN    AAAA    2607:f8b0:4004:82a::200e

;; Query time: 16 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Sun Nov 29 13:33:35 EST 2020
;; MSG SIZE  rcvd: 67
```

Similar to the several command options preceded by '-', `dig` also has a number of query options that are preceded by '+'. These can usually be turned on or off. For example, `+trace` traces delegation path from the root name servers for the name being searched while the default `+notrace` does not do this.

The `nslookup` command is another command similar to `host` and `dig` that you may want to know about. It is not part of the LPI objectives for this topic, but is still often used.

One DNS lookup situation that often comes up in places such as hotels is the need to provide some sort of authentication to be allowed to use the wifi service. This is often implemented using code that hijacks your first browser query and redirects it to a portal or login site. If you have configured your own DNS servers rather than allowing the DHCP system to provide a DNS server, you may not be able to use the wifi service because you never reach the login page. In such cases, you must at least temporarily change your wifi configuration to use the DNS server provided by the DHCP server.

A related problem can be created by some internet service providers (ISPs) who attempt to redirect failing Internet domain requests to a page that may be advertising or providing some form of alternative help. If you have a home or a small office router, you can avoid this problem by configuring your router to use public DNS servers such as 1.1.1.1 or 8.8.8.8.
