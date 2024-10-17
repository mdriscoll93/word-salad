---
description: learn to solve problems with the iproute2 toolset
cover: ../../../../.gitbook/assets/lofi-dc.jpeg
coverY: -52
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Intro to iproute2

<details>

<summary>Sources</summary>

[http://linux-ip.net/gl/ip-cref/](http://linux-ip.net/gl/ip-cref/)\
[https://www.kernel.org/doc/Documentation/networking/bonding.txt](https://www.kernel.org/doc/Documentation/networking/bonding.txt)\
[https://arstechnica.com/gadgets/2011/07/ethernet-how-does-it-work/](https://arstechnica.com/gadgets/2011/07/ethernet-how-does-it-work/)

</details>

***

### `ip` Help overview:

```bash
~ $  ip
Usage: ip [ OPTIONS ] OBJECT { COMMAND | help }
       ip [ -force ] -batch filename
where  OBJECT := { address | addrlabel | amt | fou | help | ila | ioam | l2tp |
                   link | macsec | maddress | monitor | mptcp | mroute | mrule |
                   neighbor | neighbour | netconf | netns | nexthop | ntable |
                   ntbl | route | rule | sr | tap | tcpmetrics |
                   token | tunnel | tuntap | vrf | xfrm }
       OPTIONS := { -V[ersion] | -s[tatistics] | -d[etails] | -r[esolve] |
                    -h[uman-readable] | -iec | -j[son] | -p[retty] |
                    -f[amily] { inet | inet6 | mpls | bridge | link } |
                    -4 | -6 | -M | -B | -0 |
                    -l[oops] { maximum-addr-flush-attempts } | -br[ief] |
                    -o[neline] | -t[imestamp] | -ts[hort] | -b[atch] [filename] |
                    -rc[vbuf] [size] | -n[etns] name | -N[umeric] | -a[ll] |
                    -c[olor]}
```

`ip` commands typically take the following general form:

```
ip [ OPTIONS ] OBJECT { COMMAND | help }
```

* The command is the action to be performed on the object. Commands vary based on the type of object..&#x20;
* You can get further info from running `help` after a specific object:

```bash
~ $  ip neighbor help
Usage: ip neigh { add | del | change | replace }
                { ADDR [ lladdr LLADDR ] [ nud STATE ] proxy ADDR }
                [ dev DEV ] [ router ] [ use ] [ managed ] [ extern_learn ]
                [ protocol PROTO ]

        ip neigh { show | flush } [ proxy ] [ to PREFIX ] [ dev DEV ] [ nud STATE ]
                                  [ vrf NAME ] [ nomaster ]
        ip neigh get { ADDR | proxy ADDR } dev DEV

STATE := { delay | failed | incomplete | noarp | none |
           permanent | probe | reachable | stale }

```

Invoke `ip OBJECT` where `OBJECT` is some entity, and you'll get summary info for the available objects of its type

### Display neighbors:

```bash
~ # ip neighbor
10.136.5.1 dev eth0 lladdr b8:f0:15:a5:1c:ac REACHABLE
192.168.0.80 dev eth1 lladdr fa:16:3e:dc:7e:44 STALE
192.168.0.50 dev eth1 lladdr fa:16:3e:de:34:97 STALE
192.168.0.247 dev eth1 lladdr fa:16:3e:f4:56:d5 STALE
10.136.5.30 dev eth0 lladdr fa:16:3e:9e:86:3e STALE
```

{% hint style="info" %}
This info comes straight from the [Neighbor Discovery Cache](https://en.wikipedia.org/wiki/Neighbor\_Discovery\_Protocol) (NDC). \
\- `REACHABLE` indicates that a positive confirmation of reachability for the associated ND cache entry was received within a certain time interval (defined as the `ReachableTime`). This positive confirmation could be the receipt of a Neighbor Advertisement following a Neighbor Solicitation or an upper-layer protocol successfully using the NDC entry.\
\- `STALE` means the `ReachableTime` has elapsed before a subsequent confirmation of reachability has been received.
{% endhint %}

### Display interfaces:

```bash
~ # ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc fq state UP mode DEFAULT group default qlen 1000
    link/ether fa:16:3e:09:f6:28 brd ff:ff:ff:ff:ff:ff
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 8913 qdisc fq state UP mode DEFAULT group default qlen 1000
    link/ether fa:16:3e:96:c0:a3 brd ff:ff:ff:ff:ff:ff
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:00:f4:16:ee brd ff:ff:ff:ff:ff:ff
5: br-d86475abb2d2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:da:32:a3:08 brd ff:ff:ff:ff:ff:ff

```

From this output we can see the system's Ethernet links `eth0` and `eth1` are both up (showing `LOWER_UP` indicating the underlying media is connected) while the bridge interface `br-d86475abb2d2` is currently down.&#x20;

This bridge likely acts as a virtual switch for containers/vms, and if there are no virtual interfaces such as `veth` pairs connected to it, the bridge will display `NO-CARRIER`, similar to how a physical switch missing a cable would..

#### Digging into options

The `ip` command is capable of displaying data in several useful ways - it can drill down on interface statistics, or deliver terse output useful for quick insight or scripting:

```bash
~ # ip --brief link show
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>
eth0             UP             fa:16:3e:09:f6:28 <BROADCAST,MULTICAST,UP,LOWER_UP>
eth1             UP             fa:16:3e:96:c0:a3 <BROADCAST,MULTICAST,UP,LOWER_UP>
docker0          DOWN           02:42:00:f4:16:ee <NO-CARRIER,BROADCAST,MULTICAST,UP>
br-d86475abb2d2  DOWN           02:42:da:32:a3:08 <NO-CARRIER,BROADCAST,MULTICAST,UP>

~ # ip --statistics link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    RX: bytes  packets  errors  dropped overrun mcast
    33607067931 68259236 0       0       0       0
    TX: bytes  packets  errors  dropped carrier collsns
    33607067931 68259236 0       0       0       0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc fq state UP mode DEFAULT group default qlen 1000
    link/ether fa:16:3e:09:f6:28 brd ff:ff:ff:ff:ff:ff
    RX: bytes  packets  errors  dropped overrun mcast
    37317649466 227472199 0       0       0       0
    TX: bytes  packets  errors  dropped carrier collsns
    492123099  2973523  0       0       0       0
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 8913 qdisc fq state UP mode DEFAULT group default qlen 1000
    link/ether fa:16:3e:96:c0:a3 brd ff:ff:ff:ff:ff:ff
    RX: bytes  packets  errors  dropped overrun mcast
    2354855983 19527324 0       10      0       0
    TX: bytes  packets  errors  dropped carrier collsns
    13725878784 1272531  0       0       0       0
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:00:f4:16:ee brd ff:ff:ff:ff:ff:ff
    RX: bytes  packets  errors  dropped overrun mcast
    0          0        0       0       0       0
    TX: bytes  packets  errors  dropped carrier collsns
    0          0        0       0       0       0
5: br-d86475abb2d2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:da:32:a3:08 brd ff:ff:ff:ff:ff:ff
    RX: bytes  packets  errors  dropped overrun mcast
    0          0        0       0       0       0
    TX: bytes  packets  errors  dropped carrier collsns
    0          0        0       0       0       0

```

### Display IP Addresses:

To view IP addresses associated with our links, we need to use the `addr` object:

```bash
~ # ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc fq state UP group default qlen 1000
    link/ether fa:16:3e:09:f6:28 brd ff:ff:ff:ff:ff:ff
    inet 10.136.5.81/24 brd 10.136.5.255 scope global noprefixroute dynamic eth0
       valid_lft 55968sec preferred_lft 55968sec
    inet6 fe80::f46b:51b6:95d5:7074/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 8913 qdisc fq state UP group default qlen 1000
    link/ether fa:16:3e:96:c0:a3 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.219/24 brd 192.168.0.255 scope global noprefixroute dynamic eth1
       valid_lft 65869sec preferred_lft 65869sec
    inet6 fe80::7ad6:af73:4db6:38b8/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:00:f4:16:ee brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
5: br-d86475abb2d2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:da:32:a3:08 brd ff:ff:ff:ff:ff:ff
    inet 172.19.0.1/16 brd 172.19.255.255 scope global br-d86475abb2d2
       valid_lft forever preferred_lft forever

```

#### Viewing additional link address types:

```bash
~ # ip -4 --brief addr
lo               UNKNOWN        127.0.0.1/8
eth0             UP             10.136.5.81/24
eth1             UP             192.168.0.219/24
docker0          DOWN           172.17.0.1/16
br-d86475abb2d2  DOWN           172.19.0.1/16

~ # ip -6 --brief addr
lo               UNKNOWN        ::1/128
eth0             UP             fe80::f46b:51b6:95d5:7074/64
eth1             UP             fe80::7ad6:af73:4db6:38b8/64

lo               UNKNOWN        127.0.0.1/8
eth0             UP             10.136.5.81/24
eth1             UP             192.168.0.219/24
docker0          DOWN           172.17.0.1/16
br-d86475abb2d2  DOWN           172.19.0.1/16

```

### Displaying route information

in this case, we see our machine has multiple default routes configured across its two eth interfaces. This is typically a bad practice in networking, but in the output we see the different metrics assigned to both routes,

```bash
~ # ip route
default via 10.136.5.1 dev eth0 proto dhcp metric 100
default via 192.168.0.1 dev eth1 proto dhcp metric 101
10.136.5.0/24 dev eth0 proto kernel scope link src 10.136.5.81 metric 100
169.254.169.254 via 10.136.5.30 dev eth0 proto dhcp metric 100
169.254.169.254 via 192.168.0.50 dev eth1 proto dhcp metric 101
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1
172.19.0.0/16 dev br-d86475abb2d2 proto kernel scope link src 172.19.0.1
192.168.0.0/24 dev eth1 proto kernel scope link src 192.168.0.219 metric 101

```

you might wonder why traffic from 10.136.5.81 to itself would appear to be routed out through the Ethernet adapter rather than just being looped inside the host. And a similar question for 192.168.0.219 . And how is traffic to 127.0.0.1 routed?&#x20;

The answer is that the kernel maintains a local routing table for high-priority routing of broadcast and loopback traffic.

#### Using `ip get` and `oif` to get more detail on routes

```bash
~ # ip route get 127.0.0.1
local 127.0.0.1 dev lo src 127.0.0.1
    cache <local>
~ # ip route get 10.136.5.81
local 10.136.5.81 dev lo src 10.136.5.81
    cache <local>
~ # ip route get 192.168.0.219
local 192.168.0.219 dev lo src 192.168.0.219
    cache <local>
~ # ip route get 1.1.1.1
1.1.1.1 via 10.136.5.1 dev eth0 src 10.136.5.81
    cache
~ # ip route get 192.168.0.247
192.168.0.247 dev eth1 src 192.168.0.219
    cache

~ # ip route get 1.1.1.1 oif eth0
1.1.1.1 via 10.136.5.1 dev eth0 src 10.136.5.81
    cache
~ # ip route get 1.1.1.1 oif eth1
1.1.1.1 via 192.168.0.1 dev eth1 src 192.168.0.219
    cache

```
