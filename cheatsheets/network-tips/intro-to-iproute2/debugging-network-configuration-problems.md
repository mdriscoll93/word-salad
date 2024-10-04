---
description: solving common problems with modern tools
---

# Debugging network configuration problems

## Hostname verification

A Linux system has a name which is called the host name and this name is used within the system (stand alone or connected to a network) to identify it. Usually, the same host name will be used to identify the system if it is part of a network.&#x20;

When the system is connected to a network or the Internet, has a more rigorous name as part of the Domain Name System (DNS).&#x20;

A DNS name has two parts, the host name and a domain name. The fully qualified domain name (FQDN) consists of the host name followed by a period and then the domain name (for example, myhost.mydomain).&#x20;

Domain names usually consist of multiple parts separated by periods (for example, ibm.com or lpi.org).

* first, verify your hostname via `hostnamectl`
* The kernel stores the currently active host name in the virtual `/proc` file system in the file, `/proc/sys/kernel/hostname`. The host name and FQDN may also be stored in `/etc/hosts`.

## Link connectivity

* you've verified the link is up via `ip link show`&#x20;

After this, the first check usually involves the ping command which sends an ICMP ECHO request to another host, or even to your own host.&#x20;

Use either the `-4` or `-6` option to force using either IPv4 or IPv6 if you are looking to check a particular connection type.&#x20;

On most systems, `ping6` is an alternate command equivalent to `ping -6`. Use the `-c` option to limit the number of echo requests that are sent, otherwise ping will continue transmitting packets until you terminate it using Ctrl+C.&#x20;

Recall that the normal kernel routing will send traffic destined to a host address on your own system will use the local loopback link. Example below:

### pinging on the local loopback link

```bash
[ian@ianattic5-f31 ~]$ ping -c2 127.0.0.1
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.129 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.070 ms

--- 127.0.0.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1060ms
rtt min/avg/max/mdev = 0.070/0.099/0.129/0.029 ms
[ian@ianattic5-f31 ~]$ ping -c2 ::1
PING ::1(::1) 56 data bytes
64 bytes from ::1: icmp_seq=1 ttl=64 time=0.090 ms
64 bytes from ::1: icmp_seq=2 ttl=64 time=0.074 ms

--- ::1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1038ms
rtt min/avg/max/mdev = 0.074/0.082/0.090/0.008 ms
[ian@ianattic5-f31 ~]$ ping -c2 -4 192.168.3.30
PING 192.168.3.30 (192.168.3.30) 56(84) bytes of data.
64 bytes from 192.168.3.30: icmp_seq=1 ttl=64 time=0.125 ms
64 bytes from 192.168.3.30: icmp_seq=2 ttl=64 time=0.076 ms

--- 192.168.3.30 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.076/0.100/0.125/0.024 ms
[ian@ianattic5-f31 ~]$ ping -c2 -6 2606:a000:1126:c8a2:4dc7:4faf:f930:f31c
PING 2606:a000:1126:c8a2:4dc7:4faf:f930:f31c(2606:a000:1126:c8a2:4dc7:4faf:f930:f31c) 56 data bytes
64 bytes from 2606:a000:1126:c8a2:4dc7:4faf:f930:f31c: icmp_seq=1 ttl=64 time=0.118 ms
64 bytes from 2606:a000:1126:c8a2:4dc7:4faf:f930:f31c: icmp_seq=2 ttl=64 time=0.089 ms

--- 2606:a000:1126:c8a2:4dc7:4faf:f930:f31c ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1020ms
rtt min/avg/max/mdev = 0.089/0.103/0.118/0.014 ms
```

### **Reaching out with ping**

more common uses for ping are to check connectivity to a gateway, to another device on your corporate or local network, to a public DNS server or to an arbitrary host on the Internet.&#x20;

In general, start with the local interface and then move further away. Below shows how to ping my IPv4 gateway, a neighbor on my local network which has both version 4 and version 6 entries in my /etc/hosts file, and the Google public DNS server (8.8.8.8). I have used the -q (for quiet) option to just produce summary output.

```bash
[ian@ianattic5-f31 ~]$ ping -q -c2 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.

--- 192.168.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1046ms
rtt min/avg/max/mdev = 0.391/0.421/0.452/0.030 ms
[ian@ianattic5-f31 ~]$ ping -4 -q -c2 attic4
PING attic4 (192.168.1.24) 56(84) bytes of data.

--- attic4 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1014ms
rtt min/avg/max/mdev = 0.137/0.142/0.148/0.005 ms
[ian@ianattic5-f31 ~]$ ping -6 -q -c2 attic4
PING attic4(attic4 (2606:a000:1126:c8a2:e8c3:113c:6bbd:3647)) 56 data bytes

--- attic4 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1062ms
rtt min/avg/max/mdev = 0.159/0.159/0.160/0.000 ms
[ian@ianattic5-f31 ~]$ ping -q -c2 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.

--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 21.845/25.809/29.773/3.964 ms
```

### **Pinging over a specific interface**

looking at the examples above, its not obvious which interface was used to transmit and receive the ping data.&#x20;

We can use the `-I` flag (for interface) with ping to force use of a certain interface . Note how the attempt to ping in attic4 without specifying -4 or -6 results in ICMP message "Network is unreachable"

In this case, it means that the interface in question is not set up to handle IPv6 traffic and that's what ping was trying to use.

If you run into this add -4 or -6 to check whether you may hav eone or the other kind of connectivity only\


```
[ian@ianattic5-f31 ~]$ ping -I -q -c2 wlp8s0u2 192.168.1.1
ping: wlp8s0u2: Name or service not known
[ian@ianattic5-f31 ~]$ ping -q -c2 -I wlp8s0u2 192.168.1.1
PING 192.168.1.1 (192.168.1.1) from 192.168.3.30 wlp8s0u2: 56(84) bytes of data.

--- 192.168.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1007ms
rtt min/avg/max/mdev = 678.964/1182.229/1685.495/503.265 ms, pipe 2
[ian@ianattic5-f31 ~]$ ping -q -c2 -I wlp8s0u2 192.168.3.1
PING 192.168.3.1 (192.168.3.1) from 192.168.3.30 wlp8s0u2: 56(84) bytes of data.

--- 192.168.3.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 3.589/28.840/54.091/25.251 ms
[ian@ianattic5-f31 ~]$ ping -q -c2 -I wlp8s0u2 attic4
ping: connect: Network is unreachable
[ian@ianattic5-f31 ~]$ ping -4 -q -c2 -I wlp8s0u2 attic4
PING attic4 (192.168.1.24) from 192.168.3.30 wlp8s0u2: 56(84) bytes of data.

--- attic4 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 1.542/460.310/919.078/458.768 ms
```



## Routes and paths <a href="#routes-and-paths" id="routes-and-paths"></a>

If you can't reach a host using ping, you might try either traceroute or tracepath. Both are designed to probe a possible path to a destination.

hey have some similarities, but `tracepath` is newer and designed to not require privileged authority and also `tracepath` does not have as many options. Note that not all `traceroute` options require privilege, but some do.

As with `ping`, each has a `-4` or `-6` option for IPv4 or IPv6. Some systems also have `traceroute6` and `tracepath6` with the obvious meanings. You can specify an interface with `traceroute` using the `-i` (not lower case as compared to ping) option, but this is not possible with `tracepath`.&#x20;

Both commands have a `-m` option to limit the number of hops that will be tried. By default, `tracepath` does not show IP addresses, use the `-n` option for just numeric addresses or the `-b` option to see both. Listing below shows some basic usage of `tracepath`.

### &#x20;**Using tracepath to nearby nodes**

```
[ian@ianattic5-f31 ~]$ tracepath 192.168.3.1
 1?: [LOCALHOST]                      pmtu 1500
 1:  _gateway                                             69.192ms reached
 1:  _gateway                                              1.847ms reached
     Resume: pmtu 1500 hops 1 back 1
[ian@ianattic5-f31 ~]$ tracepath -b attic4
 1?: [LOCALHOST]                        0.008ms pmtu 1500
 1:  attic4 (2606:a000:1126:c8a2:e8c3:113c:6bbd:3647)      0.241ms reached
 1:  attic4 (2606:a000:1126:c8a2:e8c3:113c:6bbd:3647)      0.144ms reached
     Resume: pmtu 1500 hops 1 back 1
[ian@ianattic5-f31 ~]$ tracepath -4 -n attic4
 1?: [LOCALHOST]                      pmtu 1500
 1:  192.168.1.24                                          0.251ms reached
 1:  192.168.1.24                                          0.124ms reached
```

### Using  traceroute to nearby nodes

```
[ian@ianattic5-f31 ~]$ traceroute 192.168.3.1
traceroute to 192.168.3.1 (192.168.3.1), 30 hops max, 60 byte packets
 1  _gateway (192.168.3.1)  2.348 ms  2.241 ms  2.500 ms
[ian@ianattic5-f31 ~]$ # Traffic via the WiFi gateway to the Ethernet host
[ian@ianattic5-f31 ~]$ sudo traceroute -i wlp8s0u2 192.168.1.24
traceroute to 192.168.1.24 (192.168.1.24), 30 hops max, 60 byte packets
 1  _gateway (192.168.3.1)  1411.191 ms  1411.195 ms  1411.163 ms
 2  attic4 (192.168.1.24)  1411.644 ms  1411.644 ms  1411.613 ms
[ian@ianattic5-f31 ~]$ traceroute attic4
traceroute to attic4 (192.168.1.24), 30 hops max, 60 byte packets
 1  attic4 (192.168.1.24)  1.078 ms  0.988 ms  0.857 ms
[ian@ianattic5-f31 ~]$ traceroute6 attic4
traceroute to attic4 (2606:a000:1126:c8a2:e8c3:113c:6bbd:3647), 30 hops max, 80 byte packets
 1  attic4 (2606:a000:1126:c8a2:e8c3:113c:6bbd:3647)  1.163 ms  1.061 ms  0.945 ms
```

When you try to probe further, either of these commands may fail to find a path. Use the `-M` option of `traceroute` to try different methods.&#x20;

Traditionally these are `udp` (default) or `icmp` (ICMP). Because either or both of these may be blocked by firewalls, a newer option, `tcp`, is available on some implementations. You can also use `-I` instead of `-M icmp` , and `-T` instead of `-M tcp`.\
\
The following is a comparison of tracepath and traceroute options to find a path from my system to lpi.org. In this case, only tracepath with the -T option succeeded.

### **Comparing tracepath and traceroute**

```
[ian@ianattic5-f31 ~]$ tracepath -b -m 14 lpi.org
 1?: [LOCALHOST]                      pmtu 1500
 1:  _gateway (192.168.1.1)                                0.438ms
 1:  _gateway (192.168.1.1)                                0.394ms
 2:  no reply
 3:  no reply
 4:  cpe-024-025-041-002.ec.res.rr.com (24.25.41.2)       16.947ms
 5:  be31.chrcnctr01r.southeast.rr.com (24.93.64.186)     20.365ms
 6:  bu-ether11.atlngamq46w-bcr00.tbone.rr.com (66.109.6.34)  20.988ms
 7:  66.109.5.125 (66.109.5.125)                          18.059ms asymm  8
 8:  ae-10.edge4.Atlanta2.Level3.net (4.68.37.73)         35.395ms asymm  9
 9:  ae-0-11.bar2.Toronto1.Level3.net (4.69.151.242)      45.442ms asymm 14
10:  4.28.138.82 (4.28.138.82)                            39.924ms asymm 13
11:  no reply
12:  no reply
13:  no reply
14:  no reply
     Too many hops: pmtu 1500
     Resume: pmtu 1500
[ian@ianattic5-f31 ~]$ sudo traceroute -I -m 14 lpi.org
traceroute to lpi.org (65.39.134.165), 14 hops max, 60 byte packets
 1  _gateway (192.168.1.1)  0.588 ms  0.750 ms  0.897 ms
 2  mta-107-13-224-1.nc.rr.com (107.13.224.1)  13.896 ms  13.946 ms  13.993 ms
 3  cpe-174-111-118-177.triad.res.rr.com (174.111.118.177)  32.620 ms  32.610 ms  32.651 ms
 4  cpe-024-025-041-002.ec.res.rr.com (24.25.41.2)  24.789 ms  24.814 ms  24.854 ms
 5  be31.chrcnctr01r.southeast.rr.com (24.93.64.186)  31.228 ms  31.220 ms  31.263 ms
 6  bu-ether14.atlngamq46w-bcr00.tbone.rr.com (66.109.6.82)  34.075 ms  22.140 ms  28.131 ms
 7  66.109.5.125 (66.109.5.125)  20.755 ms  33.047 ms  33.114 ms
 8  ae-10.edge4.Atlanta2.Level3.net (4.68.37.73)  33.210 ms  33.145 ms  33.240 ms
 9  ae-0-11.bar2.Toronto1.Level3.net (4.69.151.242)  49.789 ms  49.812 ms  49.848 ms
10  4.28.138.82 (4.28.138.82)  51.225 ms  51.249 ms  51.272 ms
11  * * *
12  * * *
13  * * *
14  * * *
[ian@ianattic5-f31 ~]$ sudo traceroute -T -m 14 lpi.org
traceroute to lpi.org (65.39.134.165), 14 hops max, 60 byte packets
 1  _gateway (192.168.1.1)  0.363 ms  0.291 ms  0.512 ms
 2  * * *
 3  * * *
 4  cpe-024-025-041-002.ec.res.rr.com (24.25.41.2)  20.366 ms  20.452 ms  20.348 ms
 5  be31.chrcnctr01r.southeast.rr.com (24.93.64.186)  27.943 ms  27.954 ms  30.316 ms
 6  bu-ether11.atlngamq46w-bcr00.tbone.rr.com (66.109.6.34)  36.744 ms  24.255 ms  32.746 ms
 7  66.109.5.125 (66.109.5.125)  42.821 ms  28.214 ms  28.328 ms
 8  ae-10.edge4.Atlanta2.Level3.net (4.68.37.73)  28.706 ms  21.630 ms  30.302 ms
 9  4.69.216.246 (4.69.216.246)  46.304 ms ae-0-11.bar2.Toronto1.Level3.net (4.69.151.242)  
 39.646 ms 4.69.216.246 (4.69.216.246)  31.685 ms
10  4.28.138.82 (4.28.138.82)  34.332 ms  44.060 ms  43.585 ms
11  * * *
12  65.39.134.165 (65.39.134.165)  44.264 ms  31.267 ms  39.192 ms
```

it can be challenging to verify a path from your host to an arbitrary Internet host. Usually these tools work well in parts of the network under your or your organization's control.

You also saw that using Transmission Control Protocol (TCP) for probes did work in one case where both User Datagram Protocol (UDP) and Internet Control Message Protocol (ICMP) failed. Using TCP effectively attempts to start a conversation that is not typically blocked, such as a browser or perhaps a Secure Shell (SSH) session.&#x20;

If there is no listener on the port, the session is rejected by the target host. If there is a listener, your host receives a positive session-setup response and immediately cancels the session before normal data transfer begins.

## **Sockets and `ss`**

The `ss` command is part of the iproute2 package. Use it to dump socket statistics.

* &#x20;If used without parameters, it dumps a list of open non-listening sockets (for example, TCP/UNIX/UDP) that have established connections. This can be quite long. You can use `grep` for simple filtering, in which case it is useful to add the `-o` (one line) option to get multiline output on a single line. Use the `-t` or `-u` options for TCP or UDP sockets respectively and the `-l` option for listening sockets. The `-s` option shows you a short summary of sockets.

### **Using `ss`**

```
[ian@ianattic5-f31 ~]$ ss -s
Total: 1210
TCP:   29 (estab 6, closed 13, orphaned 0, timewait 0)

Transport Total     IP        IPv6
RAW         2         0         2
UDP        10         7         3
TCP        16        12         4
INET       28        19         9
FRAG        0         0         0

[ian@ianattic5-f31 ~]$ ss -ul
State   Recv-Q  Send-Q         Local Address:Port     Peer Address:Port Process
UNCONN  0       0                    0.0.0.0:37013         0.0.0.0:*
UNCONN  0       0                    0.0.0.0:mdns          0.0.0.0:*
UNCONN  0       0              192.168.122.1:domain        0.0.0.0:*
UNCONN  0       0             0.0.0.0%virbr0:bootps        0.0.0.0:*
UNCONN  0       0        192.168.1.25%enp9s0:bootpc        0.0.0.0:*
UNCONN  0       0                    0.0.0.0:xdmcp         0.0.0.0:*
UNCONN  0       0                  127.0.0.1:323           0.0.0.0:*
UNCONN  0       0                       [::]:mdns             [::]:*
UNCONN  0       0                      [::1]:323              [::]:*
UNCONN  0       0                       [::]:52184            [::]:*

[ian@ianattic5-f31 ~]$ ss -o | grep http
tcp               ESTAB                  0                   0
192.168.1.25:60764                                        35.161.207.109:https
timer:(keepalive,4min2sec,0)
tcp               ESTAB                  0                   0
192.168.1.25:54274                                       192.241.243.150:https
timer:(keepalive,2min14sec,0)
tcp               ESTAB                  0                   0
192.168.1.25:40932                                        172.217.15.110:https
tcp               CLOSE-WAIT             1                   0
192.168.1.25:33990                                          99.84.221.57:https
tcp               ESTAB                  0                   0
192.168.1.25:39522                               172.217.5.234:https
```

**leveraging ss state filters**

The ss command also has the ability to construct quite elaborate state filters as part of the command.

The example below shows how to filter established TCP sockets using the SSH port, either as a source port or a destination port. It shows an example of four SSH sessions, two originating at my Ubuntu host (192.168.1.24) to my Fedora host (192.168.1.25 and 192.168.3.30) and two in the reverse direction, one V4 and one v6.\


```
[ian@ianattic5-f31 ~]$ ss -t state established '( dport = :ssh or sport = :ssh )'
Recv-Q     Send-Q                              Local Address:Port
Peer Address:Port         Process
0          0                                    192.168.1.25:ssh
192.168.1.24:34946
0          0                                    192.168.1.25:55160
192.168.1.24:ssh
0          0                                    192.168.3.30:ssh
192.168.1.24:46028
0          0           [2606:a000:1126:c8a2:4dc7:4faf:f930:f31c]:ssh
[2606:a000:1126:c8a2:cd32:dd01:8c3f:434d]:52854
```

Note that the man page for `ss` says "Please take a look at the official documentation for details regarding filters." Such details do not seem to exist in the official documentation packages as of the time of writing, although the examples in the man page are a very useful start.

### **using ncat to retrieve HTTP headers**

Another socket tool that you can use is `netcat`. There are at least two versions and the name is sometimes `nc` or `ncat`.&#x20;

This is somewhat similar to the `cat` command in that you can use it to transmit text, command output, or files to another host.&#x20;

It operates in connect or listen mode. The listener does not have to be another `netcat` instance.Below, I have shown how to use `ncat` on my Fedora system to connect to the IBM web server on port 80. I typed in two lines to request the HTTP headers and you see the beginning of the response.

```
[ian@ianattic5-f31 ~]$ ncat -C www.ibm.com 80
GET /get HTTP/1.1
Host:www.ibm.com

HTTP/1.1 301 Moved Permanently
Server: AkamaiGHost
Content-Length: 0
Location: https://www.ibm.com/get
Date: Mon, 04 May 2020 15:36:20 GMT
Connection: keep-alive
...
```

## Legacy net-tools commands

Before `iproute2` there was `net-tools`. The `net-tools` package is still in use, a testament to its usefulness. The table shows some of the commands I have used in this tutorial and the legacy command you could use instead. Use the man pages to explore other options of these commands.

| New command	                           | Legacy command	                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------- |
| `ip a`                                 | `ifconfig -a`                                                                    |
| `ip link set enp9s0 down`              | `ifconfig enp9s0 down`                                                           |
| `ip link set enp9s0 up`                | `ifconfig enp9s0 up`                                                             |
| `ip addr add 192.168.3.30/24`          | `ifconfig add 192.168.3.30 dev wlp8s0u2 ifconfig wlp8s0u2 netmask 255.255.255.0` |
| `ip r`                                 | `route`                                                                          |
| `ip route add default via 192.168.3.1` | `route add default gw 192.168.3.1`                                               |
| `ip neigh`                             | `arp -a`                                                                         |
| `ss`                                   | `netstat`                                                                        |
