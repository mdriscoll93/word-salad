---
description: >-
  This is the most recommended configuration for a business and forms the basis
  for all other examples. Everything lives on a switch until it needs to access
  the Internet or another specific VLAN or net
icon: chart-scatter-3d
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

# Switch with a separate router (RoaS)

## Switch Configuration Overview:

<figure><img src="https://i.ibb.co/nMkmhBX/switch.png" alt=""><figcaption></figcaption></figure>

### Access Ports

Segment the network across three different VLANs:

* <mark style="background-color:blue;">**Blue**</mark> = <mark style="color:blue;">VLAN 10</mark>
* <mark style="background-color:green;">**Green**</mark> = <mark style="color:green;">VLAN 20</mark>
* <mark style="background-color:red;">**Red**</mark> = <mark style="color:red;">VLAN 30</mark>

All of these will be configured as Access ports. These are ports in which end users may have physical access.

### Trunk Ports

On this switch, the purple fiber sfp inputs will be our Trunk ports. Use ethernet interfaces if you prefer - we'll also show two ports to illustrate a switch linking itself to a router on sfp1 and another to a switch on spf2.&#x20;

Use the number / combination that suits your environment. In real-world situations, most enterprises will have traffic coming down into a switch, interacting with devices there, then replying back out the same port or going out another port to hit additional networks. Imagine a Blue network spread across three physical switches.

### Hybrid Ports

These types of ports are shown in a separate configuration file linked below because their behavior and descriptions have not been illustrated in the diagrams. Here we change Blue VLAN ports _Hybrid_ by setting a Green egress behavior on them.

* When traffic does **not** have a tag, it will be placed on the Blue VLAN
* If arriving packets do have tags, and the VLAN ID is Green, those packets are allowed to ingress, placed on the Green VLAN, and the correct egress behavior is honored for both types of packets.
  * If any other VLAD ID tries to ingress, it will be dropped.

### IP Addressing & Routing

While it is possible with RouterOS to do everything on the switch, that is not something you would truly do in practice unless you never want anything to leave the switch (full hardware isolation). Our example will show the router handling the creation and management of _IP Services_ instead of the switch. First, we need to setup the switch's IP address.

#### Switch IP Address:

Before the router can serve IP Services on the different VLANs, the switch and router should be on their own VLAN, which we call the Base VLAN. Others might term it the Management VLAN.

The router's IP address will be 192.168.0.1, so we’ll make our switch 192.168.0.2. Set the switch's default route (gateway) to be the router, and anything else you might want, like NTP, DNS, etc.

## **Router Configuration at a glance:**

<figure><img src="https://i.ibb.co/G5BYs2Z/router.png" alt=""><figcaption></figcaption></figure>

### **IP Services:**

With the switch complete, time to setup the router to provide IP Services. On the router we create three different IP Service _interfaces_: Blue, Green, and Red. Each one of these correspond to the VLANs it will be serving. For each one of these service groups, we turn on at least one item, a DHCP server. You can add or point to more as necessary.

Now, when we connect the Purple Trunk port on the router to the Purple Trunk port on the switch, anything following Ethernet protocol begins to function.

#### How it works in action:

A PC plugged into the Blue VLAN starts broadcasting for a DHCP address. The switch keeps this from all other VLANs. The incoming packets from this PC get tagged with a Blue ID, and since the Purple Trunk port can pass Blue, it sends the packet out that port which goes to the router. The router's Purple Trunk port sees a Blue DHCP request and allows a Blue DHCP server, running on a configured Blue VLAN interface, to respond to the request, sending the packet back out its Trunk port.

Meanwhile, a PC plugged into the Red VLAN tries to ping a Blue VLAN device. The ping reply goes out the switch's Purple Trunk port to the router's Purple Trunk port. The router would have sent the reply on to the Blue VLAN device except that a firewall rule drops interVLAN access.
