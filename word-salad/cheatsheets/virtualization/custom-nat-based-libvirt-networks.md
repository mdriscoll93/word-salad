---
description: >-
  The default NAT-based network has some limitations. Follow the steps below to
  overcome these limitations and take control of your server environment. There
  are four main components: a dummy network in
---

# Custom NAT-based libvirt networks

{% hint style="info" %}
If you are uncomfortable with iptables, you might prefer to stick with the default [_NAT-based network_](https://jamielinux.com/docs/libvirt-networking-handbook/nat-based-network.html).
{% endhint %}

## Disable the default network

To prevent libvirt from altering the firewall rules, stop and disable the default network. Make sure ther eare no active virtual machines still using this network.

```bash
virsh net-destroy default
virsh net-autostart --disable default
```

### Create a dummy interface

A bridge inherits the MAC address of the first interface that it is attached to, so it will keep changing unless the same VM is always powered on first. To keep the MAC address constant, create a dummy network interface with a chosen MAC address and attach it to the bridge before anything else.

Choose a MAC address for the virtual bridge. Use `hexdump` to generate a random MAC address in the format that libvirt expects - `52:54:00:xx:xx:xx`) for KVM, `00:16:3e:xx:xx:xx` for Xen.

```bash
hexdump -vn3 -e '/3 "52:54:00"' -e '/1 ":%02x"' -e '"\n"' /dev/urandom
52:54:00:7b:54:98
```

Next, Create a dummy network interface called `virbr10-dummy` and set the MAC address to the one generated above.

```bash
ip link add virbr10-dummy address 52:54:00:7b:54:98 type dummy
```
