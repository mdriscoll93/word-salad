---
description: >-
  changes made via the command line like this are not persistent between
  reboots. We'll talk more about persistent network configuration a bit later on
---

# Configuration with iproute2

## Activate and Deactivate interfaces

{% hint style="danger" %}
Notice: making configuration changes to an active interface is ill-advised and should not be done without good reason. ie. diagnostic purposes, testing, resetting interface state in last re·sort situations.
{% endhint %}

Despite the warning above, you should be aware of how to activate / deactivate an interface via the `ip` command:

```bash
[ian@ianattic5-f31 l-lpic1-109-3]$ sudo ip link set enp9s0 down
[ian@ianattic5-f31 l-lpic1-109-3]$ ip -br addr show dev enp9s0
enp9s0           DOWN
[ian@ianattic5-f31 l-lpic1-109-3]$ sudo ip link set enp9s0 up
[ian@ianattic5-f31 l-lpic1-109-3]$ ip -br addr show dev enp9s0
enp9s0           UP             192.168.1.25/24 fe80::efaa:4539:a6ba:27b9/64
```

## Saving current network / route states

`iproute2` gives us the ability to save and restore the state of our network policies and routing tables via a simple command. This saved file is stored as a binary file which can only be restored via it's accompanying commands

* To save configuration:`ip addr save $iface > saved-$iface`&#x20;
* To restore configuration: `ip addr showdump < saved-$iface`

#### Saving and restoring an interfaces' configuration:

```bash
[ian@ianattic5-f31 l-lpic1-109-3]$ ip addr save enp9s0 >saved-enp9s0
[ian@ianattic5-f31 l-lpic1-109-3]$ ip addr showdump < saved-enp9s0
if2:
    inet 192.168.1.25/24 brd 192.168.1.255 scope global dynamic noprefixroute enp9s0
       valid_lft 73568sec preferred_lft 73568sec
if2:
    inet6 2606:a000:1126:c8a8:5a1e:d3c4:ffe:3894/64 scope global dynamic noprefixroute
       valid_lft 86382sec preferred_lft 86382sec
if2:
    inet6 fe80::efaa:4539:a6ba:27b9/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[ian@ianattic5-f31 l-lpic1-109-3]$ ip route save >saved-route
[ian@ianattic5-f31 l-lpic1-109-3]$ ip route showdump < saved-route
default via 192.168.1.1 dev enp9s0 proto dhcp metric 100
default via 192.168.3.1 dev wlp8s0u2 proto dhcp metric 600
192.168.1.0/24 dev enp9s0 proto kernel scope link src 192.168.1.25 metric 100
192.168.3.0/24 dev wlp8s0u2 proto kernel scope link src 192.168.3.21 metric 600
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 linkdown
```

## Configuring interfaces and routes

the `ip` command can be used to configure links, addresses, or routes. Changes made using the ip command are not persistent; meaning you will need to make the same changes after each system reboot or persistently code network configuration within one of the available services.

We will cover persistent network configuration later, but using `ip` can be a great way to experiment and test planned changes before implementing them.

{% hint style="warning" %}
The `ip` command has a **limited amount of configuration ability with physical links** such as Ethernet or wifi links.. a few examples of what can be done:\
\- Alter flags such as multicast\
\- Add or delete IP addresses.
{% endhint %}

{% hint style="success" %}
In contrast, `ip` can be used to configure a wide variety of virtual links: linux bridges, VLANS, channel bonds, etc.\
\- `ip link help` will help you find the various link types and add a type to find the parameters applicable to a particular link type
{% endhint %}

#### **example - parameters available to a bond link:**

```bash
~ $  ip link help
Usage: ip link add [link DEV | parentdev NAME] [ name ] NAME
                    [ txqueuelen PACKETS ]
                    [ address LLADDR ]
                    [ broadcast LLADDR ]
                    [ mtu MTU ] [index IDX ]
                    [ numtxqueues QUEUE_COUNT ]
                    [ numrxqueues QUEUE_COUNT ]
                    [ netns { PID | NAME } ]
                    type TYPE [ ARGS ]

        ip link delete { DEVICE | dev DEVICE | group DEVGROUP } type TYPE [ ARGS ]

        ip link set { DEVICE | dev DEVICE | group DEVGROUP }
                        [ { up | down } ]
                        [ type TYPE ARGS ]
                [ arp { on | off } ]
                [ dynamic { on | off } ]
                [ multicast { on | off } ]
                [ allmulticast { on | off } ]
                [ promisc { on | off } ]
                [ trailers { on | off } ]
                [ carrier { on | off } ]
                [ txqueuelen PACKETS ]
                [ name NEWNAME ]
                [ address LLADDR ]
                [ broadcast LLADDR ]
                [ mtu MTU ]
                [ netns { PID | NAME } ]
                [ link-netns NAME | link-netnsid ID ]
                [ alias NAME ]
                [ vf NUM [ mac LLADDR ]
                         [ vlan VLANID [ qos VLAN-QOS ] [ proto VLAN-PROTO ] ]
                         [ rate TXRATE ]
                         [ max_tx_rate TXRATE ]
                         [ min_tx_rate TXRATE ]
                         [ spoofchk { on | off} ]
                         [ query_rss { on | off} ]
                         [ state { auto | enable | disable} ]
                         [ trust { on | off} ]
                         [ node_guid EUI64 ]
                         [ port_guid EUI64 ] ]
                [ { xdp | xdpgeneric | xdpdrv | xdpoffload } { off |
                          object FILE [ { section | program } NAME ] [ verbose ] |
                          pinned FILE } ]
                [ master DEVICE ][ vrf NAME ]
                [ nomaster ]
                [ addrgenmode { eui64 | none | stable_secret | random } ]
                [ protodown { on | off } ]
                [ protodown_reason PREASON { on | off } ]
                [ gso_max_size BYTES ] | [ gso_max_segs PACKETS ]
                [ gro_max_size BYTES ]

        ip link show [ DEVICE | group GROUP ] [up] [master DEV] [vrf NAME] [type TYPE]
                [nomaster]

        ip link xstats type TYPE [ ARGS ]

        ip link afstats [ dev DEVICE ]
        ip link property add dev DEVICE [ altname NAME .. ]
        ip link property del dev DEVICE [ altname NAME .. ]

        ip link help [ TYPE ]

TYPE := { amt | bareudp | bond | bond_slave | bridge | bridge_slave |
          dsa | dummy | erspan | geneve | gre | gretap | gtp | ifb |
          ip6erspan | ip6gre | ip6gretap | ip6tnl |
          ipip | ipoib | ipvlan | ipvtap |
          macsec | macvlan | macvtap |
          netdevsim | nlmon | rmnet | sit | team | team_slave |
          vcan | veth | vlan | vrf | vti | vxcan | vxlan | wwan |
          xfrm | virt_wifi }

```

#### **IPv4 address and route info for link wlp8s0u2**

here we will demonstrate how to add an IP address to an existing link and how to update the routing table. For simplicity - discussion will be restricted to IPv4, but same idea goes for IPv6\
\
In this scenario the Fedora system has a wifi adapter with an associated IP address of 192.168.3.21. The IPv4 address and route info is shown below:

```bash
[ian@ianattic5-f31 ~]$ ip -4 a show wlp8s0u2
3: wlp8s0u2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc mq state UP group default qlen 1000
    inet 192.168.3.21/24 brd 192.168.3.255 scope global dynamic noprefixroute wlp8s0u2
       valid_lft 53458sec preferred_lft 53458sec
[ian@ianattic5-f31 ~]$ ip -4 r
default via 192.168.1.1 dev enp9s0 proto dhcp metric 100
default via 192.168.3.1 dev wlp8s0u2 proto dhcp metric 600
192.168.1.0/24 dev enp9s0 proto kernel scope link src 192.168.1.25 metric 100
192.168.3.0/24 dev wlp8s0u2 proto kernel scope link src 192.168.3.21 metric 600
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 linkdown
```

Suppose I want to change the address from 192.168.3.21 to 192.168.3.30 and also change the metric to 550.&#x20;

It is not possible to change the metric using the IP command. So, one way to do this is to first add the new address and new metric and then delete the original address.

First, I'll add an address and then see what I accomplished as shown below:

```bash
[ian@ianattic5-f31 ~]$ sudo ip addr add 192.168.3.30/24 dev wlp8s0u2
[ian@ianattic5-f31 ~]$ ip -4 a show wlp8s0u2
3: wlp8s0u2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc mq state UP group default qlen 1000
    inet 192.168.3.21/24 brd 192.168.3.255 scope global dynamic noprefixroute wlp8s0u2
       valid_lft 53338sec preferred_lft 53338sec
    inet 192.168.3.30/24 scope global secondary wlp8s0u2
       valid_lft forever preferred_lft forever
```

So far, so good, but I forgot to add a broadcast address and a metric.

The following shows how to delete the address just added and add it again with a broadcast address and a metric. Note that `brd` is an adequate abbreviation for broadcast and the `+` tells `ip` to derive the broadcast address from the IP address by setting or resetting the host bits of the interface prefix.&#x20;

You can use either `+` or `-` for this purpose or you can spell out the broadcast address in full dotted-quad format.

```bash
[ian@ianattic5-f31 ~]$ sudo ip addr del 192.168.3.30/24  dev wlp8s0u2
[ian@ianattic5-f31 ~]$ sudo ip addr add 192.168.3.30/24 brd + metric 550 dev wlp8s0u2
[ian@ianattic5-f31 ~]$ ip -4 a show wlp8s0u2
3: wlp8s0u2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc mq state UP group default qlen 1000
    inet 192.168.3.21/24 brd 192.168.3.255 scope global dynamic noprefixroute wlp8s0u2
       valid_lft 50563sec preferred_lft 50563sec
    inet 192.168.3.30/24 metric 550 brd 192.168.3.255 scope global secondary wlp8s0u2
       valid_lft forever preferred_lft forever
```

Note that the new address has been added as a secondary address. There is no way to swap a secondary address for a primary address.

Depending on the settings of certain `sysctl` variables secondary IPv4 addresses may be deleted or promoted when the primary address is deleted. This is not a problem for IPv6 addresses where secondaries are promoted if the primary is deleted.

The default values may vary by Linux distribution. On the Ubuntu system, I am using the `sysctl` value related to promoting secondaries are shown in output below:

```bash
ian@attic4-u18:~$ sudo sysctl -a --pattern "net.ipv4.*_secondaries"
net.ipv4.conf.all.promote_secondaries = 1
net.ipv4.conf.default.promote_secondaries = 0
net.ipv4.conf.enp2s0.promote_secondaries = 0
net.ipv4.conf.enp4s0.promote_secondaries = 0
net.ipv4.conf.lo.promote_secondaries = 0
```

Now we will delete 192.168.3.21 and show you how the address I added is promoted to primary and a link scope route was automatically added with metric 550, derived from the value I specified when I added the address.

```basic
[ian@ianattic5-f31 ~]$ sudo ip addr del 192.168.3.21/24 dev wlp8s0u2
[ian@ianattic5-f31 ~]$ ip -4 addr show wlp8s0u2
3: wlp8s0u2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc mq state UP group default qlen 1000
    inet 192.168.3.30/24 metric 550 brd 192.168.3.255 scope global wlp8s0u2
       valid_lft forever preferred_lft forever
[ian@ianattic5-f31 ~]$ ip -4 route
default via 192.168.1.1 dev enp9s0 proto dhcp metric 100
default via 192.168.3.1 dev wlp8s0u2 proto dhcp metric 600
192.168.1.0/24 dev enp9s0 proto kernel scope link src 192.168.1.25 metric 100
192.168.3.0/24 dev wlp8s0u2 proto kernel scope link src 192.168.3.30 metric 550
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 linkdown
```

These changes do not update the default route over the wlp8s0u2 which still has the original metric of 600. You cannot change the metric on a route using the ip command. You have to delete the route and add it again. The addition does not inherit the metric value from the address so you need to specify it explicitly as shown below:

```bash
[ian@ianattic5-f31 ~]$ sudo ip route del default via 192.168.3.1
[ian@ianattic5-f31 ~]$ sudo ip route add default via 192.168.3.1 metric 550
[ian@ianattic5-f31 ~]$ ip -4 route
default via 192.168.1.1 dev enp9s0 proto dhcp metric 100
default via 192.168.3.1 dev wlp8s0u2 metric 550
192.168.1.0/24 dev enp9s0 proto kernel scope link src 192.168.1.25 metric 100
192.168.3.0/24 dev wlp8s0u2 proto kernel scope link src 192.168.3.30 metric 550
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 linkdown
```

#### Now we have accomplished all desired changes set out to demonstrate&#x20;

{% hint style="info" %}
remember that our changes made using `ip` are transient and will be lost at reboot
{% endhint %}



