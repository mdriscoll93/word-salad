---
description: for querying virtual machine information
icon: rectangle-code
---

# Useful virsh commands

## virsh

the virsh command line interface is the main utility for managing virsh guest domains - i.e. a virtual machine instance managed by the libvirt virtualization API.

In libvirt terminology, each VM is known as a domain, representing an instance of a guest operating system running on a hypervisor

<details>

<summary><code>virsh --help</code></summary>

```bash
[root@thinkbox ~]# virsh --help

virsh [options]... [<command_string>]
virsh [options]... <command> [args...]

  options:
    -c | --connect=URI      hypervisor connection URI
    -d | --debug=NUM        debug level [0-4]
    -e | --escape <char>    set escape sequence for console
    -h | --help             this help
    -k | --keepalive-interval=NUM
                            keepalive interval in seconds, 0 for disable
    -K | --keepalive-count=NUM
                            number of possible missed keepalive messages
    -l | --log=FILE         output logging to file
    -q | --quiet            quiet mode
    -r | --readonly         connect readonly
    -t | --timing           print timing information
    -v                      short version
    -V                      long version
         --version[=TYPE]   version, TYPE is short or long (default short)
  commands (non interactive mode):

 Domain Management (help keyword 'domain')
    attach-device                  attach device from an XML file
    attach-disk                    attach disk device
    attach-interface               attach network interface
    autostart                      autostart a domain
    blkdeviotune                   Set or query a block device I/O tuning parameters.
    blkiotune                      Get or set blkio parameters
    blockcommit                    Start a block commit operation.
    blockcopy                      Start a block copy operation.
    blockjob                       Manage active block operations
    blockpull                      Populate a disk from its backing image.
    blockresize                    Resize block device of domain.
    change-media                   Change media of CD or floppy drive
    console                        connect to the guest console
    cpu-stats                      show domain cpu statistics
    create                         create a domain from an XML file
    define                         define (but don't start) a domain from an XML file
    desc                           show or set domain's description or title
    destroy                        destroy (stop) a domain
    detach-device                  detach device from an XML file
    detach-device-alias            detach device from an alias
    detach-disk                    detach disk device
    detach-interface               detach network interface
    domdisplay                     domain display connection URI
    domfsfreeze                    Freeze domain's mounted filesystems.
    domfsthaw                      Thaw domain's mounted filesystems.
    domfsinfo                      Get information of domain's mounted filesystems.
    domfstrim                      Invoke fstrim on domain's mounted filesystems.
    domhostname                    print the domain's hostname
    domid                          convert a domain name or UUID to domain id
    domif-setlink                  set link state of a virtual interface
    domiftune                      get/set parameters of a virtual interface
    domjobabort                    abort active domain job
    domjobinfo                     domain job information
    domlaunchsecinfo               Get domain launch security info
    domsetlaunchsecstate           Set domain launch security state
    domname                        convert a domain id or UUID to domain name
    domrename                      rename a domain
    dompmsuspend                   suspend a domain gracefully using power management functions
    dompmwakeup                    wakeup a domain from pmsuspended state
    domuuid                        convert a domain name or id to domain UUID
    domxml-from-native             Convert native config to domain XML
    domxml-to-native               Convert domain XML to native config
    dump                           dump the core of a domain to a file for analysis
    dumpxml                        domain information in XML
    edit                           edit XML configuration for a domain
    get-user-sshkeys               list authorized SSH keys for given user (via agent)
    inject-nmi                     Inject NMI to the guest
    iothreadinfo                   view domain IOThreads
    iothreadpin                    control domain IOThread affinity
    iothreadadd                    add an IOThread to the guest domain
    iothreadset                    modifies an existing IOThread of the guest domain
    iothreaddel                    delete an IOThread from the guest domain
    send-key                       Send keycodes to the guest
    send-process-signal            Send signals to processes
    lxc-enter-namespace            LXC Guest Enter Namespace
    managedsave                    managed save of a domain state
    managedsave-remove             Remove managed save of a domain
    managedsave-edit               edit XML for a domain's managed save state file
    managedsave-dumpxml            Domain information of managed save state file in XML
    managedsave-define             redefine the XML for a domain's managed save state file
    memtune                        Get or set memory parameters
    perf                           Get or set perf event
    metadata                       show or set domain's custom XML metadata
    migrate                        migrate domain to another host
    migrate-setmaxdowntime         set maximum tolerable downtime
    migrate-getmaxdowntime         get maximum tolerable downtime
    migrate-compcache              get/set compression cache size
    migrate-setspeed               Set the maximum migration bandwidth
    migrate-getspeed               Get the maximum migration bandwidth
    migrate-postcopy               Switch running migration from pre-copy to post-copy
    numatune                       Get or set numa parameters
    qemu-attach                    QEMU Attach
    qemu-monitor-command           QEMU Monitor Command
    qemu-monitor-event             QEMU Monitor Events
    qemu-agent-command             QEMU Guest Agent Command
    guest-agent-timeout            Set the guest agent timeout
    reboot                         reboot a domain
    reset                          reset a domain
    restore                        restore a domain from a saved state in a file
    resume                         resume a domain
    save                           save a domain state to a file
    save-image-define              redefine the XML for a domain's saved state file
    save-image-dumpxml             saved state domain information in XML
    save-image-edit                edit XML for a domain's saved state file
    schedinfo                      show/set scheduler parameters
    screenshot                     take a screenshot of a current domain console and store it into a file
    set-lifecycle-action           change lifecycle actions
    set-user-sshkeys               manipulate authorized SSH keys file for given user (via agent)
    set-user-password              set the user password inside the domain
    setmaxmem                      change maximum memory limit
    setmem                         change memory allocation
    setvcpus                       change number of virtual CPUs
    shutdown                       gracefully shutdown a domain
    start                          start a (previously defined) inactive domain
    suspend                        suspend a domain
    ttyconsole                     tty console
    undefine                       undefine a domain
    update-device                  update device from an XML file
    update-memory-device           update memory device of a domain
    vcpucount                      domain vcpu counts
    vcpuinfo                       detailed domain vcpu information
    vcpupin                        control or query domain vcpu affinity
    emulatorpin                    control or query domain emulator affinity
    vncdisplay                     vnc display
    guestvcpus                     query or modify state of vcpu in the guest (via agent)
    setvcpu                        attach/detach vcpu or groups of threads
    domblkthreshold                set the threshold for block-threshold event for a given block device or it's backing chain element
    guestinfo                      query information about the guest (via agent)
    domdirtyrate-calc              Calculate a vm's memory dirty rate
    dom-fd-associate               associate a FD with a domain
    domdisplay-reload              Reload domain's graphics display certificates

 Domain Monitoring (help keyword 'monitor')
    domblkerror                    Show errors on block devices
    domblkinfo                     domain block device size information
    domblklist                     list all domain blocks
    domblkstat                     get device block stats for a domain
    domcontrol                     domain control interface state
    domif-getlink                  get link state of a virtual interface
    domifaddr                      Get network interfaces' addresses for a running domain
    domiflist                      list all domain virtual interfaces
    domifstat                      get network interface stats for a domain
    dominfo                        domain information
    dommemstat                     get memory statistics for a domain
    domstate                       domain state
    domstats                       get statistics about one or multiple domains
    domtime                        domain time
    list                           list domains

 Domain Events (help keyword 'events')
    event                          Domain Events

 Host and Hypervisor (help keyword 'host')
    allocpages                     Manipulate pages pool size
    capabilities                   capabilities
    cpu-baseline                   compute baseline CPU
    cpu-compare                    compare host CPU with a CPU described by an XML file
    cpu-models                     CPU models
    domcapabilities                domain capabilities
    freecell                       NUMA free memory
    freepages                      NUMA free pages
    hostname                       print the hypervisor hostname
    hypervisor-cpu-baseline        compute baseline CPU usable by a specific hypervisor
    hypervisor-cpu-compare         compare a CPU with the CPU created by a hypervisor on the host
    maxvcpus                       connection vcpu maximum
    node-memory-tune               Get or set node memory parameters
    nodecpumap                     node cpu map
    nodecpustats                   Prints cpu stats of the node.
    nodeinfo                       node information
    nodememstats                   Prints memory stats of the node.
    nodesevinfo                    node SEV information
    nodesuspend                    suspend the host node for a given time duration
    sysinfo                        print the hypervisor sysinfo
    uri                            print the hypervisor canonical URI
    version                        show version

 Checkpoint (help keyword 'checkpoint')
    checkpoint-create              Create a checkpoint from XML
    checkpoint-create-as           Create a checkpoint from a set of args
    checkpoint-delete              Delete a domain checkpoint
    checkpoint-dumpxml             Dump XML for a domain checkpoint
    checkpoint-edit                edit XML for a checkpoint
    checkpoint-info                checkpoint information
    checkpoint-list                List checkpoints for a domain
    checkpoint-parent              Get the name of the parent of a checkpoint

 Interface (help keyword 'interface')
    iface-begin                    create a snapshot of current interfaces settings, which can be later committed (iface-commit) or restored (iface-rollback)
    iface-bridge                   create a bridge device and attach an existing network device to it
    iface-commit                   commit changes made since iface-begin and free restore point
    iface-define                   define an inactive persistent physical host interface or modify an existing persistent one from an XML file
    iface-destroy                  destroy a physical host interface (disable it / "if-down")
    iface-dumpxml                  interface information in XML
    iface-edit                     edit XML configuration for a physical host interface
    iface-list                     list physical host interfaces
    iface-mac                      convert an interface name to interface MAC address
    iface-name                     convert an interface MAC address to interface name
    iface-rollback                 rollback to previous saved configuration created via iface-begin
    iface-start                    start a physical host interface (enable it / "if-up")
    iface-unbridge                 undefine a bridge device after detaching its device(s)
    iface-undefine                 undefine a physical host interface (remove it from configuration)

 Network Filter (help keyword 'filter')
    nwfilter-define                define or update a network filter from an XML file
    nwfilter-dumpxml               network filter information in XML
    nwfilter-edit                  edit XML configuration for a network filter
    nwfilter-list                  list network filters
    nwfilter-undefine              undefine a network filter
    nwfilter-binding-create        create a network filter binding from an XML file
    nwfilter-binding-delete        delete a network filter binding
    nwfilter-binding-dumpxml       network filter information in XML
    nwfilter-binding-list          list network filter bindings

 Networking (help keyword 'network')
    net-autostart                  autostart a network
    net-create                     create a network from an XML file
    net-define                     define an inactive persistent virtual network or modify an existing persistent one from an XML file
    net-desc                       show or set network's description or title
    net-destroy                    destroy (stop) a network
    net-dhcp-leases                print lease info for a given network
    net-dumpxml                    network information in XML
    net-edit                       edit XML configuration for a network
    net-event                      Network Events
    net-info                       network information
    net-list                       list networks
    net-metadata                   show or set network's custom XML metadata
    net-name                       convert a network UUID to network name
    net-start                      start a (previously defined) inactive network
    net-undefine                   undefine a persistent network
    net-update                     update parts of an existing network's configuration
    net-uuid                       convert a network name to network UUID
    net-port-list                  list network ports
    net-port-create                create a network port from an XML file
    net-port-dumpxml               network port information in XML
    net-port-delete                delete the specified network port

 Node Device (help keyword 'nodedev')
    nodedev-create                 create a device defined by an XML file on the node
    nodedev-destroy                destroy (stop) a device on the node
    nodedev-detach                 detach node device from its device driver
    nodedev-dumpxml                node device details in XML
    nodedev-list                   enumerate devices on this host
    nodedev-reattach               reattach node device to its device driver
    nodedev-reset                  reset node device
    nodedev-event                  Node Device Events
    nodedev-define                 Define or modify a device by an xml file on a node
    nodedev-undefine               Undefine an inactive node device
    nodedev-start                  Start an inactive node device
    nodedev-autostart              autostart a defined node device
    nodedev-info                   node device information
    nodedev-update                 Update a active and/or inactive node device

 Secret (help keyword 'secret')
    secret-define                  define or modify a secret from an XML file
    secret-dumpxml                 secret attributes in XML
    secret-event                   Secret Events
    secret-get-value               Output a secret value
    secret-list                    list secrets
    secret-set-value               set a secret value
    secret-undefine                undefine a secret

 Snapshot (help keyword 'snapshot')
    snapshot-create                Create a snapshot from XML
    snapshot-create-as             Create a snapshot from a set of args
    snapshot-current               Get or set the current snapshot
    snapshot-delete                Delete a domain snapshot
    snapshot-dumpxml               Dump XML for a domain snapshot
    snapshot-edit                  edit XML for a snapshot
    snapshot-info                  snapshot information
    snapshot-list                  List snapshots for a domain
    snapshot-parent                Get the name of the parent of a snapshot
    snapshot-revert                Revert a domain to a snapshot

 Backup (help keyword 'backup')
    backup-begin                   Start a disk backup of a live domain
    backup-dumpxml                 Dump XML for an ongoing domain block backup job

 Storage Pool (help keyword 'pool')
    find-storage-pool-sources-as   find potential storage pool sources
    find-storage-pool-sources      discover potential storage pool sources
    pool-autostart                 autostart a pool
    pool-build                     build a pool
    pool-create-as                 create a pool from a set of args
    pool-create                    create a pool from an XML file
    pool-define-as                 define a pool from a set of args
    pool-define                    define an inactive persistent storage pool or modify an existing persistent one from an XML file
    pool-delete                    delete a pool
    pool-destroy                   destroy (stop) a pool
    pool-dumpxml                   pool information in XML
    pool-edit                      edit XML configuration for a storage pool
    pool-info                      storage pool information
    pool-list                      list pools
    pool-name                      convert a pool UUID to pool name
    pool-refresh                   refresh a pool
    pool-start                     start a (previously defined) inactive pool
    pool-undefine                  undefine an inactive pool
    pool-uuid                      convert a pool name to pool UUID
    pool-event                     Storage Pool Events
    pool-capabilities              storage pool capabilities

 Storage Volume (help keyword 'volume')
    vol-clone                      clone a volume.
    vol-create-as                  create a volume from a set of args
    vol-create                     create a vol from an XML file
    vol-create-from                create a vol, using another volume as input
    vol-delete                     delete a vol
    vol-download                   download volume contents to a file
    vol-dumpxml                    vol information in XML
    vol-info                       storage vol information
    vol-key                        returns the volume key for a given volume name or path
    vol-list                       list vols
    vol-name                       returns the volume name for a given volume key or path
    vol-path                       returns the volume path for a given volume name or key
    vol-pool                       returns the storage pool for a given volume key or path
    vol-resize                     resize a vol
    vol-upload                     upload file contents to a volume
    vol-wipe                       wipe a vol

 Virsh itself (help keyword 'virsh')
    cd                             change the current directory
    echo                           echo arguments. Used for internal testing.
    exit                           quit this interactive terminal
    help                           print help
    pwd                            print the current directory
    quit                           quit this interactive terminal
    connect                        (re)connect to hypervisor
    /
  
```

</details>

Here is a quick breakdown of what each part of the syntax means:

1. `virsh` - a command-line interface tool used to manage virtualization environments and interact with `libvirt`, an API that abstracts various virtualization technologies like KVM (Kernel-based Virtual Machine), QEMU, Xen, VMware, and more.
2. **Guest Domain:**
   1. A **guest** is a virtual machine running on a hypervisor (the host machine). Each guest can have its own operating system, applications, and network configurations.
   2. A **domain** is simply a term `libvirt` uses to refer to a virtual machine. When you create a VM using `virsh` or another `libvirt`-based tool (e.g., `virt-manager`), you are creating a "domain" within the `libvirt` environment.
3. **Domain Characteristics**: - A domain (VM) managed by `virsh` has several characteristics:
   1. **Unique Name and UUID**: Each domain has a name and a universally unique identifier (UUID) to distinguish it from others.
   2. **Configuration**: The domain's configuration is stored in an XML file, which defines aspects like memory, CPU, disk images, network interfaces, and other hardware.
   3. **Lifecycle Management**: The domain can be in different states, such as running, paused, or shut off. `virsh` provides commands to manage these states.

***

## command quick reference

### Get Basic Information About the VM:

* To display basic information about the VM, including its state, memory, CPU, and other configurations: `sudo virsh dominfo <vm_name>`

```bash
[root@thinkbox ~]# virsh dominfo ubuntu-cloud-vm
Id:             2
Name:           ubuntu-cloud-vm
UUID:           71f6f62d-0245-4f71-9aa3-6902a92e11f7
OS Type:        hvm
State:          running
CPU(s):         2
CPU time:       890.2s
Max memory:     2097152 KiB
Used memory:    2097152 KiB
Persistent:     yes
Autostart:      disable
Managed save:   no
Security model: none
Security DOI:   
```

### Show detailed XML Configuration of the VM

* To see the complete XML configuration of the VM, which includes information about disks, network interfaces, devices, and more..

```bash
[root@thinkbox ~]# virsh dumpxml ubuntu-cloud-vm
<domain type='kvm' id='2'>
  <name>ubuntu-cloud-vm</name>
  <uuid>71f6f62d-0245-4f71-9aa3-6902a92e11f7</uuid>
  <metadata>
    <libosinfo:libosinfo xmlns:libosinfo="http://libosinfo.org/xmlns/libvirt/domain/1.0">
      <libosinfo:os id="http://ubuntu.com/ubuntu/20.04"/>
    </libosinfo:libosinfo>
  </metadata>
  <memory unit='KiB'>2097152</memory>
  <currentMemory unit='KiB'>2097152</currentMemory>
  <vcpu placement='static'>2</vcpu>
  <resource>
    <partition>/machine</partition>
  </resource>
  <os>
    <type arch='x86_64' machine='pc-q35-9.1'>hvm</type>
    <boot dev='hd'/>
  </os>
  <features>
    <acpi/>
    <apic/>
    <vmport state='off'/>
  </features>
  <cpu mode='host-passthrough' check='none' migratable='on'/>
  <clock offset='utc'>
    <timer name='rtc' tickpolicy='catchup'/>
    <timer name='pit' tickpolicy='delay'/>
    <timer name='hpet' present='no'/>
  </clock>
  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>destroy</on_crash>
  <pm>
    <suspend-to-mem enabled='no'/>
    <suspend-to-disk enabled='no'/>
  </pm>
  <devices>
    <emulator>/usr/bin/qemu-system-x86_64</emulator>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/ubuntu-cloud-vm.qcow2' index='2'/>
      <backingStore type='file' index='3'>
        <format type='qcow2'/>
        <source file='/var/lib/libvirt/images/ubuntu-20.04-server-cloudimg-amd64.img'/>
        <backingStore/>
      </backingStore>
      <target dev='vda' bus='virtio'/>
      <alias name='virtio-disk0'/>
      <address type='pci' domain='0x0000' bus='0x04' slot='0x00' function='0x0'/>
    </disk>
    <disk type='file' device='cdrom'>
      <driver name='qemu' type='raw'/>
      <source file='/var/lib/libvirt/images/ubuntu-cloud-vm-cloud-init.iso' index='1'/>
      <backingStore/>
      <target dev='sda' bus='sata'/>
      <readonly/>
      <alias name='sata0-0-0'/>
      <address type='drive' controller='0' bus='0' target='0' unit='0'/>
    </disk>
    <controller type='usb' index='0' model='qemu-xhci' ports='15'>
      <alias name='usb'/>
      <address type='pci' domain='0x0000' bus='0x02' slot='0x00' function='0x0'/>
    </controller>
    <controller type='pci' index='0' model='pcie-root'>
      <alias name='pcie.0'/>
    </controller>
    <controller type='pci' index='1' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='1' port='0x10'/>
      <alias name='pci.1'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x0' multifunction='on'/>
    </controller>
    <controller type='pci' index='2' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='2' port='0x11'/>
      <alias name='pci.2'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x1'/>
    </controller>
    <controller type='pci' index='3' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='3' port='0x12'/>
      <alias name='pci.3'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x2'/>
    </controller>
    <controller type='pci' index='4' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='4' port='0x13'/>
      <alias name='pci.4'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x3'/>
    </controller>
    <controller type='pci' index='5' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='5' port='0x14'/>
      <alias name='pci.5'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x4'/>
    </controller>
    <controller type='pci' index='6' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='6' port='0x15'/>
      <alias name='pci.6'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x5'/>
    </controller>
    <controller type='pci' index='7' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='7' port='0x16'/>
      <alias name='pci.7'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x6'/>
    </controller>
    <controller type='pci' index='8' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='8' port='0x17'/>
      <alias name='pci.8'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x7'/>
    </controller>
    <controller type='pci' index='9' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='9' port='0x18'/>
      <alias name='pci.9'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0' multifunction='on'/>
    </controller>
    <controller type='pci' index='10' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='10' port='0x19'/>
      <alias name='pci.10'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x1'/>
    </controller>
    <controller type='pci' index='11' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='11' port='0x1a'/>
      <alias name='pci.11'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x2'/>
    </controller>
    <controller type='pci' index='12' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='12' port='0x1b'/>
      <alias name='pci.12'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x3'/>
    </controller>
    <controller type='pci' index='13' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='13' port='0x1c'/>
      <alias name='pci.13'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x4'/>
    </controller>
    <controller type='pci' index='14' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='14' port='0x1d'/>
      <alias name='pci.14'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x5'/>
    </controller>
    <controller type='sata' index='0'>
      <alias name='ide'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x1f' function='0x2'/>
    </controller>
    <controller type='virtio-serial' index='0'>
      <alias name='virtio-serial0'/>
      <address type='pci' domain='0x0000' bus='0x03' slot='0x00' function='0x0'/>
    </controller>
    <interface type='bridge'>
      <mac address='52:54:00:06:87:83'/>
      <source bridge='ovsbr0'/>
      <virtualport type='openvswitch'>
        <parameters interfaceid='2b4f16c1-d870-4838-ac59-e0042b129b02'/>
      </virtualport>
      <target dev='vnet1'/>
      <model type='virtio'/>
      <alias name='net0'/>
      <address type='pci' domain='0x0000' bus='0x01' slot='0x00' function='0x0'/>
    </interface>
    <serial type='pty'>
      <source path='/dev/pts/6'/>
      <target type='isa-serial' port='0'>
        <model name='isa-serial'/>
      </target>
      <alias name='serial0'/>
    </serial>
    <console type='pty' tty='/dev/pts/6'>
      <source path='/dev/pts/6'/>
      <target type='serial' port='0'/>
      <alias name='serial0'/>
    </console>
    <channel type='unix'>
      <source mode='bind' path='/run/libvirt/qemu/channel/2-ubuntu-cloud-vm/org.qemu.guest_agent.0'/>
      <target type='virtio' name='org.qemu.guest_agent.0' state='disconnected'/>
      <alias name='channel0'/>
      <address type='virtio-serial' controller='0' bus='0' port='1'/>
    </channel>
    <channel type='spicevmc'>
      <target type='virtio' name='com.redhat.spice.0' state='disconnected'/>
      <alias name='channel1'/>
      <address type='virtio-serial' controller='0' bus='0' port='2'/>
    </channel>
    <input type='tablet' bus='usb'>
      <alias name='input0'/>
      <address type='usb' bus='0' port='1'/>
    </input>
    <input type='mouse' bus='ps2'>
      <alias name='input1'/>
    </input>
    <input type='keyboard' bus='ps2'>
      <alias name='input2'/>
    </input>
    <graphics type='spice' port='5900' autoport='yes' listen='127.0.0.1'>
      <listen type='address' address='127.0.0.1'/>
      <image compression='off'/>
    </graphics>
    <sound model='ich9'>
      <alias name='sound0'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x1b' function='0x0'/>
    </sound>
    <audio id='1' type='spice'/>
    <video>
      <model type='virtio' heads='1' primary='yes'/>
      <alias name='video0'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x0'/>
    </video>
    <redirdev bus='usb' type='spicevmc'>
      <alias name='redir0'/>
      <address type='usb' bus='0' port='2'/>
    </redirdev>
    <redirdev bus='usb' type='spicevmc'>
      <alias name='redir1'/>
      <address type='usb' bus='0' port='3'/>
    </redirdev>
    <watchdog model='itco' action='reset'>
      <alias name='watchdog0'/>
    </watchdog>
    <memballoon model='virtio'>
      <stats period='5'/>
      <alias name='balloon0'/>
      <address type='pci' domain='0x0000' bus='0x05' slot='0x00' function='0x0'/>
    </memballoon>
    <rng model='virtio'>
      <backend model='random'>/dev/urandom</backend>
      <alias name='rng0'/>
      <address type='pci' domain='0x0000' bus='0x06' slot='0x00' function='0x0'/>
    </rng>
  </devices>
  <seclabel type='dynamic' model='dac' relabel='yes'>
    <label>+1000:+1000</label>
    <imagelabel>+1000:+1000</imagelabel>
  </seclabel>
</domain>

```

### Check network VM interfaces

* to list the network interfaces used by the VM and see their MAC addresses, bridge connections, etc.: `sudo virsh domiflist <vm_name>`

```bash
[root@thinkbox ~]# virsh domiflist ubuntu-cloud-vm
 Interface   Type     Source   Model    MAC
-----------------------------------------------------------
 vnet1       bridge   ovsbr0   virtio   52:54:00:06:87:83
```

* To get additional information about a specific network interface (e.g., `vnet0`), use: `sudo virsh domifaddr <vm_name>`

```bash
[root@thinkbox ~]# virsh domifaddr rh8-vm01
 Name       MAC address          Protocol     Address
-------------------------------------------------------------------------------
 vnet6      52:54:00:c8:17:6e    ipv4         192.168.122.19/24
 vnet7      52:54:00:04:4d:ac    ipv4         192.168.64.210/24
```

### Inspect VM Disk Information

* view details about VM's disk(s):

```bash
[root@thinkbox ~]# virsh domblklist ubuntu-cloud-vm
Target     Source
---------------------------
vda        /var/lib/libvirt/images/ubuntu-cloud-vm.qcow2
hda        /var/lib/libvirt/images/ubuntu-cloud-vm-cloud-init.iso
```

for more info on a specific disk:

```bash
[root@thinkbox ~]# virsh domblklist ubuntu-cloud-vm --details 
 Type   Device   Target   Source
----------------------------------------------------------------------------------
 file   disk     vda      /var/lib/libvirt/images/ubuntu-cloud-vm.qcow2
 file   cdrom    sda      /var/lib/libvirt/images/ubuntu-cloud-vm-cloud-init.iso
```

### Monitor real-time Performance Metrics

* monitor CPU and memory usage of VM

```bash
[root@thinkbox ~]# virsh domstats ubuntu-cloud-vm
Domain: 'ubuntu-cloud-vm'
  state.state=1
  state.reason=1
  cpu.time=486629513000
  cpu.user=368672472000
  cpu.system=117957040000
  cpu.cache.monitor.count=0
  cpu.haltpoll.success.time=1057833298
  cpu.haltpoll.fail.time=541927945
  balloon.current=2097152
  balloon.maximum=2097152
  balloon.swap_in=0
  balloon.swap_out=0
  balloon.major_fault=0
  balloon.minor_fault=0
  balloon.unused=1921792
  balloon.available=2010804
  balloon.usable=1851408
  balloon.last-update=1728120434
  balloon.disk_caches=68820
  balloon.hugetlb_pgalloc=0
  balloon.hugetlb_pgfail=0
  balloon.rss=1388484
  vcpu.current=2
  vcpu.maximum=2
  vcpu.0.state=1
  vcpu.0.time=115940000000
  vcpu.0.wait=0
  vcpu.0.delay=34399069382
  vcpu.0.tlb_flush.sum=436657
  vcpu.0.directed_yield_successful.sum=205
  vcpu.0.preemption_other.sum=16376
  vcpu.0.insn_emulation_fail.sum=0
  vcpu.0.pf_emulate.sum=998
  vcpu.0.halt_wait_ns.sum=7021710117997
  vcpu.0.mmio_exits.sum=151641
  vcpu.0.nmi_injections.sum=0
  vcpu.0.nmi_window_exits.sum=0
  vcpu.0.blocking.cur=yes
  vcpu.0.pf_taken.sum=99059
  vcpu.0.insn_emulation.sum=691263
  vcpu.0.halt_poll_success_ns.sum=626050059
  vcpu.0.pf_mmio_spte_created.sum=913
  vcpu.0.req_event.sum=1786609
  vcpu.0.irq_window_exits.sum=44868
  vcpu.0.fpu_reload.sum=2576072
  vcpu.0.io_exits.sum=2425489
  vcpu.0.signal_exits.sum=1
  vcpu.0.halt_successful_poll.sum=11970
  vcpu.0.pf_fixed.sum=86766
  vcpu.0.irq_injections.sum=230535
  vcpu.0.directed_yield_attempted.sum=326
  vcpu.0.halt_exits.sum=132437
  vcpu.0.pf_spurious.sum=0
  vcpu.0.halt_poll_invalid.sum=0
  vcpu.0.exits.sum=5468522
  vcpu.0.preemption_reported.sum=43483
  vcpu.0.request_irq_exits.sum=0
  vcpu.0.guest_mode.cur=no
  vcpu.0.pf_fast.sum=0
  vcpu.0.halt_attempted_poll.sum=28345
  vcpu.0.halt_wakeup.sum=119511
  vcpu.0.irq_exits.sum=123134
  vcpu.0.l1d_flush.sum=3375792
  vcpu.0.hypercalls.sum=326
  vcpu.0.notify_window_exits.sum=0
  vcpu.0.invlpg.sum=0
  vcpu.0.pf_guest.sum=0
  vcpu.0.host_state_reload.sum=2675447
  vcpu.0.nested_run.sum=0
  vcpu.0.halt_poll_fail_ns.sum=364832581
  vcpu.1.state=1
  vcpu.1.time=99740000000
  vcpu.1.wait=0
  vcpu.1.delay=35827450441
  vcpu.1.tlb_flush.sum=48149
  vcpu.1.directed_yield_successful.sum=149
  vcpu.1.preemption_other.sum=9968
  vcpu.1.insn_emulation_fail.sum=0
  vcpu.1.pf_emulate.sum=62
  vcpu.1.halt_wait_ns.sum=7044124289564
  vcpu.1.mmio_exits.sum=73893
  vcpu.1.nmi_injections.sum=0
  vcpu.1.nmi_window_exits.sum=0
  vcpu.1.blocking.cur=yes
  vcpu.1.pf_taken.sum=19455
  vcpu.1.insn_emulation.sum=81546
  vcpu.1.halt_poll_success_ns.sum=431783239
  vcpu.1.pf_mmio_spte_created.sum=62
  vcpu.1.req_event.sum=465331
  vcpu.1.irq_window_exits.sum=30318
  vcpu.1.fpu_reload.sum=334105
  vcpu.1.io_exits.sum=260366
  vcpu.1.signal_exits.sum=43
  vcpu.1.halt_successful_poll.sum=10081
  vcpu.1.pf_fixed.sum=19356
  vcpu.1.irq_injections.sum=252944
  vcpu.1.directed_yield_attempted.sum=233
  vcpu.1.halt_exits.sum=166873
  vcpu.1.pf_spurious.sum=1
  vcpu.1.halt_poll_invalid.sum=0
  vcpu.1.exits.sum=1297092
  vcpu.1.preemption_reported.sum=46101
  vcpu.1.request_irq_exits.sum=0
  vcpu.1.guest_mode.cur=no
  vcpu.1.pf_fast.sum=0
  vcpu.1.halt_attempted_poll.sum=22056
  vcpu.1.halt_wakeup.sum=155577
  vcpu.1.irq_exits.sum=126997
  vcpu.1.l1d_flush.sum=636980
  vcpu.1.hypercalls.sum=233
  vcpu.1.notify_window_exits.sum=0
  vcpu.1.invlpg.sum=0
  vcpu.1.pf_guest.sum=0
  vcpu.1.host_state_reload.sum=517086
  vcpu.1.nested_run.sum=0
  vcpu.1.halt_poll_fail_ns.sum=177095364
  net.count=1
  net.0.name=vnet12
  net.0.rx.bytes=194
  net.0.rx.pkts=3
  net.0.rx.errs=0
  net.0.rx.drop=380
  net.0.tx.bytes=0
  net.0.tx.pkts=0
  net.0.tx.errs=0
  net.0.tx.drop=0
  block.count=2
  block.0.name=vda
  block.0.path=/var/lib/libvirt/images/ubuntu-cloud-vm.qcow2
  block.0.backingIndex=2
  block.0.rd.reqs=56551
  block.0.rd.bytes=1537007104
  block.0.rd.times=65945020582
  block.0.wr.reqs=12201
  block.0.wr.bytes=273661952
  block.0.wr.times=10807026951
  block.0.fl.reqs=2066
  block.0.fl.times=9479122446
  block.0.allocation=337772544
  block.0.capacity=21474836480
  block.0.physical=337719296
  block.1.name=sda
  block.1.path=/var/lib/libvirt/images/ubuntu-cloud-vm-cloud-init.iso
  block.1.backingIndex=1
  block.1.rd.reqs=415
  block.1.rd.bytes=1404390
  block.1.rd.times=73240482
  block.1.wr.reqs=0
  block.1.wr.bytes=0
  block.1.wr.times=0
  block.1.fl.reqs=0
  block.1.fl.times=0
  block.1.allocation=0
  block.1.capacity=374784
  block.1.physical=376832
  dirtyrate.calc_status=0
  dirtyrate.calc_start_time=0
  dirtyrate.calc_period=0
  dirtyrate.calc_mode=page-sampling
  vm.max_mmu_rmap_size.max=0
  vm.mmu_pde_zapped.sum=0
  vm.mmu_flooded.sum=0
  vm.remote_tlb_flush.sum=2
  vm.remote_tlb_flush_requests.sum=163
  vm.mmu_unsync.cur=0
  vm.pages_4k.cur=79646
  vm.mmu_pte_write.sum=0
  vm.pages_1g.cur=0
  vm.pages_2m.cur=58
  vm.nx_lpage_splits.cur=165
  vm.mmu_cache_miss.sum=0
  vm.max_mmu_page_hash_collisions.max=0
  vm.mmu_shadow_zapped.sum=0
  vm.mmu_recycled.sum=0

```

### Monitor VM's Virtual NIC stats

```bash
sudo virsh domifstat ubuntu-cloud-vm vnet0
```

### &#x20;**Access VM's Filesystem**

```bash
sudo guestfish --ro -a /var/lib/libvirt/images/ubuntu-cloud-vm.qcow2 -i
```

## Summary

#### **Summary of Useful Commands**

* **VM Information:** `sudo virsh dominfo ubuntu-cloud-vm`
* **VM Configuration XML:** `sudo virsh dumpxml ubuntu-cloud-vm`
* **Network Interfaces:** `sudo virsh domiflist ubuntu-cloud-vm`
* **IP Address:** `sudo virsh domifaddr ubuntu-cloud-vm`
* **Disk Information:** `sudo virsh domblklist ubuntu-cloud-vm`, `sudo virsh domblkinfo ubuntu-cloud-vm vda`
* **Real-Time Stats:** `sudo virsh domstats ubuntu-cloud-vm`
* **Console Access:** `sudo virsh console ubuntu-cloud-vm`
* **Interface Stats:** `sudo virsh domifstat ubuntu-cloud-vm vnet0`
