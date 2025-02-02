---
icon: ethernet
cover: >-
  https://images.unsplash.com/photo-1661634499138-9e615a57962d?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwxfHx0ZWxjb3xlbnwwfHx8fDE3Mjk5ODgzMjN8MA&ixlib=rb-4.0.3&q=85
coverY: 0
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
    visible: false
---

# Using RouterOS to VLAN your Network

### Key VLAN Terminologies:

{% tabs %}
{% tab title="Ingress" %}
When a frame enters the switch coming from the end  device
{% endtab %}

{% tab title="Egress" %}
When the frame exits the switch going to an end device or another switch
{% endtab %}

{% tab title="Tagged" %}
The frame has a VLAN tag or is tagged when forwarded.
{% endtab %}

{% tab title="Un-Tagged" %}
The VLAN tag is removed from the frame when forwarded.
{% endtab %}

{% tab title="Access Port" %}
Switch port connected  to an end device which untagged frames are leaving from the switch to the end device and tag them on the ingress.

These ports define the entry into your VLAN. They represent groups of devices that need access to each other but not other networks. You will group them by ID. In this documentation we use colors like Blue, Green, and Red to help us to visualize the ID numbers. Access ports are configured in a way that means ingress (incoming) packets must not have tags and thus will get a tag applied. The egress (outgoing) packets (that are replying back to whatever was plugged in) get tags removed.
{% endtab %}

{% tab title="Trunk Port" %}
Normally used between 2 connected switches to allow more than 1 VLAN's traffic pass from one switch to another.

These ports are what carry everything you care about between VLANs. If Access ports represent groups of things, think of Trunk ports as what enables these groups to get to places they need to go, like other areas of the switch or network. Trunk ports are configured such that ingress packets must have tags and egress packets will have tags.
{% endtab %}

{% tab title="Hybrid Port" %}
These ports are for special situations and requirements. They share qualities and behaviors of Access and Trunk ports. Basically, they function as an Access port for ingress traffic without tags. When incoming traffic is tagged, and the tag is on the allowed list, it will then function as a Trunk port.
{% endtab %}
{% endtabs %}

{% hint style="info" %}
When designing your VLAN, you'll have reached your first step when you can logically think about Access port grouping and Trunk port interconnections. How many VLANs and devices will you need to work with? Who gets access to what? Don't rush this step. Take time to [diagram](https://www.draw.io/) your VLAN.
{% endhint %}

<figure><img src="https://i.ibb.co/cgPNYS3/vlanlogo.png" alt=""><figcaption></figcaption></figure>

### Native, Base, & MGMT (management) VLAN:

As you create your VLANs and pick VLAN IDs for each one, understand that the base network that you used to initiate your first connection to a router or switch is often termed the _Native_ VLAN.&#x20;

In our examples, we do **not** use this default network. Instead we implement a Base VLAN (our name for the management VLAN) with an ID of 99.&#x20;

Over this network will be device to device traffic (routing, etc.). We also default [Winbox](https://wiki.mikrotik.com/wiki/Manual:Winbox) availability here as well.

{% hint style="warning" %}
A word of caution if you are thinking of using VLAN 1 in your network design. Most vendors use VLAN 1 as the native VLAN for their hardware.&#x20;

MikroTik uses VLAN 0. If you try to create a VLAN 1 scenario with MikroTik, and expecting tagged frames, it will be incompatible with other vendors who default VLAN 1 as _untagged_.

Therefore, unless you are prepared to change the default behavior in MikroTik and/or other vendors, it is simpler to use VLAN 2 and higher.
{% endhint %}

### IP Addressing & Routing

Since every VLAN you create should have a different _IP Addressing_ scheme, you'll use something different for each VLAN.&#x20;

If you set the Base VLAN to 192.168.0.x, your Blue VLAN might be 10.0.10.x, Green is 10.0.20.x, and Red set to 10.0.30.x. Just make sure that all VLANs are unique.

With an IP addressing scheme in mind, you'll set your core equipment with manual assignments. So, a router might be set to 192.168.0.1, a core switch 192.168.0.2, a WiFi AP 192.168.0.3, and so on.&#x20;

The router can now become the default gateway _routing_ your VLAN, switches, and connected devices. Using IP Services, you will make information available in an automated way.

### IP Services:

The most well known is DHCP. Generally, every VLAN has its own DHCP server ensuring devices know about gateways and DNS servers they should use. When everything in the VLAN has an IP address, they'll talk to each other over the ethernet protocol making broadcasts and generating other network traffic between each other.

When you have more than one VLAN, you have a segmented set of networks.&#x20;

* If you plug a PC into a Blue VLAN, it can see and communicate only with other devices on Blue but not anything on Green or Red.&#x20;
* If a printer is plugged into Green VLAN, only devices on the Green network could access it.&#x20;
* You can share resources across VLANs while still not allowing inter-VLAN access. Just one of the benefits you'll be reading about in this document.

### Reasons to VLAN

You need to partition and isolate networks and devices from each other using the same physical hardware. Enforcing QOS policies and general network organization are other valid reasons.

### VLAN Types

Sometimes you see other terms alongside VLAN, such as Port Based VLAN, MAC Based VLAN, Native VLAN, and Voice VLAN. Some of these are really just names for what all is really the same thing. Maybe they use a different approach (automated vs manual) to get there, but ultimately, network devices are segmented.

This document will focus on a manual [Tag Based VLAN](https://en.wikipedia.org/wiki/IEEE_802.1Q) approach. Dynamic VLAN assignment may be added later.

Technically speaking, we can break Mikrotik VLANs into these sub-categories:

* <mark style="color:blue;">**Port-Based VLAN**</mark>: simple approach where a VLAN ID is directly assigned to an interface (port). Each interface could theoretically be assigned its own VLAN ID effectively separating network traffic via port.
  * Does not require a bridge or additional filtering and is the most straightforward way to isolate VLANs
* <mark style="color:blue;">**Bridge VLAN Filtering**</mark>: allows VLAN configuration across a bridge interface - allows control of tagging/untagging on a per-port basis, while also allowing for trunking (multiple VLANs on a single port)
  * this method caters to more complex networks
    * VLANs are assigned to bridge interfaces with specific filtering rules
    * Each port in the bridge can be configured as  a member of of one or more VLANs, allowing the creation of both Access and Trunk ports
    * VLANs are defined and filtered using the `bridge vlan` feature
* <mark style="color:blue;">**Service VLAN (Q-in-Q)**</mark>: used to encapsulate one VLAN tag into another allowing for hierarchical VLAN structures. Often used by service providers to tunnel multiple customer VLANs across networks while keeping the individual tags separate&#x20;
* <mark style="color:blue;">**Hybrid VLAN (mixed trunk & access ports)**</mark>: Hybrid setups may have certain ports on the bridg acting as trunk ports while others act as access ports - common in environments where some devices need access to multiple VLANs while others may only need one
* <mark style="color:blue;">**Router-on-a-stick**</mark>: this is a VLAN configuration where a single interface/port is used to handle traffic from multiple VLANs
  * <mark style="color:green;">**Inter-VLAN Routing**</mark>: single physical interface with multiple subinterfaces; ideal for routing traffic through a single port.
