# VMware / ESXi Phase 1

## 20 Interview & Self-Test Questions with Answers

> **Language:** English\
> **Focus:** Virtualization, Hypervisors, ESXi Fundamentals, and ESXi
> Networking

------------------------------------------------------------------------

## Index

1.  [What is virtualization?](#1-what-is-virtualization)
2.  [What is a VM?](#2-what-is-a-vm)
3.  [What is the difference between a Host and a
    Guest?](#3-what-is-the-difference-between-a-host-and-a-guest)
4.  [What does a hypervisor do?](#4-what-does-a-hypervisor-do)
5.  [What is the difference between Type 1 and Type 2
    hypervisors?](#5-what-is-the-difference-between-type-1-and-type-2-hypervisors)
6.  [Is ESXi Type 1 or Type 2?](#6-is-esxi-type-1-or-type-2)
7.  [Is VMware Workstation Type 1 or Type
    2?](#7-is-vmware-workstation-type-1-or-type-2)
8.  [What is the basic difference between ESX and
    ESXi?](#8-what-is-the-basic-difference-between-esx-and-esxi)
9.  [What is DCUI?](#9-what-is-dcui)
10. [What is VMkernel?](#10-what-is-vmkernel)
11. [What is a vSphere Standard
    Switch?](#11-what-is-a-vsphere-standard-switch)
12. [What is a vNIC?](#12-what-is-a-vnic)
13. [What does vmnic0 represent?](#13-what-does-vmnic0-represent)
14. [What is the purpose of a VM Port
    Group?](#14-what-is-the-purpose-of-a-vm-port-group)
15. [What is the purpose of a VMkernel
    Port?](#15-what-is-the-purpose-of-a-vmkernel-port)
16. [What is the difference between a VM Port Group and a VMkernel
    Port?](#16-what-is-the-difference-between-a-vm-port-group-and-a-vmkernel-port)
17. [Why is NIC Teaming used?](#17-why-is-nic-teaming-used)
18. [What does Traffic Shaping
    control?](#18-what-does-traffic-shaping-control)
19. [Why is management networking required to access an ESXi host
    through a
    browser?](#19-why-is-management-networking-required-to-access-an-esxi-host-through-a-browser)
20. [What path can VM traffic take to reach the physical
    network?](#20-what-path-can-vm-traffic-take-to-reach-the-physical-network)

------------------------------------------------------------------------

## 1. What is virtualization?

**Answer:**\
Virtualization is a technology that allows us to use the CPU, RAM,
storage, and networking resources of one physical server to run multiple
**Virtual Machines (VMs)**.

``` text
Physical Server
      ↓
Hypervisor
      ↓
VM1   VM2   VM3
```

**Interview line:**\
\> One physical machine can provide resources to multiple independent
virtual machines.

------------------------------------------------------------------------

## 2. What is a VM?

**Answer:**\
A **Virtual Machine (VM)** is a software-based computer. It can have its
own virtual CPU, RAM, disk, network adapter, operating system, and
applications, while the underlying resources come from the physical
host.

``` text
VM-01
├── 2 vCPU
├── 4 GB RAM
├── 50 GB Virtual Disk
├── 1 vNIC
└── Rocky Linux
```

------------------------------------------------------------------------

## 3. What is the difference between a Host and a Guest?

**Answer:**\
A **Host** is the system that provides resources for virtualization. A
**Guest** is an operating system running inside a virtual machine.

``` text
Physical PC
    ↓
Windows              ← Host
    ↓
VMware Workstation
    ↓
Rocky Linux VM       ← Guest
```

------------------------------------------------------------------------

## 4. What does a hypervisor do?

**Answer:**\
A hypervisor manages the physical server's **CPU, memory, storage, and
networking resources** and makes those resources available to virtual
machines.

``` text
CPU / RAM / Disk / NIC
         ↓
     Hypervisor
         ↓
   VM1   VM2   VM3
```

------------------------------------------------------------------------

## 5. What is the difference between Type 1 and Type 2 hypervisors?

**Answer:**\
A **Type 1 hypervisor** runs directly on physical hardware. A **Type 2
hypervisor** runs on top of an existing host operating system.

``` text
TYPE 1:
Hardware → ESXi → VMs

TYPE 2:
Hardware → Windows/Linux → VMware Workstation → VMs
```

  -----------------------------------------------------------------------
  Type 1                              Type 2
  ----------------------------------- -----------------------------------
  Runs directly on hardware           Runs on a host OS

  ESXi is an example                  VMware Workstation is an example

  Common in server/data-center        Common in desktop/lab environments
  environments                        
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 6. Is ESXi Type 1 or Type 2?

**Answer:**\
VMware ESXi is a **Type 1 (Bare-Metal) Hypervisor**.

``` text
Physical Hardware
       ↓
      ESXi
       ↓
      VMs
```

It is designed to run directly on physical server hardware.

------------------------------------------------------------------------

## 7. Is VMware Workstation Type 1 or Type 2?

**Answer:**\
VMware Workstation is a **Type 2 hypervisor** because it runs on top of
a host operating system such as Windows or Linux.

``` text
Physical Hardware
       ↓
Windows / Linux
       ↓
VMware Workstation
       ↓
VMs
```

------------------------------------------------------------------------

## 8. What is the basic difference between ESX and ESXi?

**Answer:**\
**ESX** was VMware's older hypervisor architecture and included a
traditional Linux-based **Service Console**.

**ESXi** uses a newer, purpose-built architecture and does not include
the traditional ESX Service Console.

``` text
ESX (Older)
├── VMkernel
└── Service Console

ESXi
├── VMkernel
└── Management components
```

**Remember:**\
`ESX = Older architecture`\
`ESXi = Modern VMware bare-metal hypervisor family`

------------------------------------------------------------------------

## 9. What is DCUI?

**Answer:**\
**DCUI = Direct Console User Interface.**

It is the local console interface of an ESXi host and can be used for
basic configuration and troubleshooting.

Common tasks include:

-   Configure Management Network
-   IP Address
-   Subnet Mask
-   Default Gateway
-   DNS
-   Hostname
-   Restart Management Network
-   Troubleshooting Options

**Easy formula:**\
`DCUI → Direct/local ESXi configuration`

------------------------------------------------------------------------

## 10. What is VMkernel?

**Answer:**\
**VMkernel is a core component of ESXi.** It handles important functions
such as CPU scheduling, memory management, storage, networking, and
access to hardware resources for virtual machines.

**Easy memory:**\
\> VMkernel = Core of ESXi.

------------------------------------------------------------------------

## 11. What is a vSphere Standard Switch?

**Answer:**\
A **vSphere Standard Switch (vSS)** is a software-based Layer 2 virtual
switch inside an ESXi host.

It provides network connectivity for virtual machines and VMkernel
networking and can connect to the physical network through physical
uplinks.

``` text
VMs
 ↓
Port Group
 ↓
vSwitch
 ↓
vmnic
 ↓
Physical Network
```

------------------------------------------------------------------------

## 12. What is a `vNIC`?

**Answer:**\
A `vNIC` is a **Virtual Network Interface Card** assigned to a virtual
machine.

``` text
VM
 ↓
vNIC
 ↓
Port Group
```

**Easy memory:**\
Physical computer → NIC\
Virtual Machine → vNIC

------------------------------------------------------------------------

## 13. What does `vmnic0` represent?

**Answer:**\
`vmnic0` represents a **physical network adapter/uplink** available to
the ESXi host.

``` text
vSwitch0
   ↓
vmnic0
   ↓
Physical Switch
```

Other examples include:

``` text
vmnic0
vmnic1
vmnic2
```

------------------------------------------------------------------------

## 14. What is the purpose of a VM Port Group?

**Answer:**\
A VM Port Group provides **network connectivity and common network
configuration/policies for virtual machines**.

``` text
VM1 ─┐
VM2 ─┼── VM Network Port Group
VM3 ─┘
          ↓
       vSwitch0
```

Port Groups can also be used for settings such as VLAN configuration and
networking policies.

------------------------------------------------------------------------

## 15. What is the purpose of a VMkernel Port?

**Answer:**\
A VMkernel Port/adapter is used for the **ESXi host's own network
traffic and services**.

Examples can include:

-   Management traffic
-   vMotion
-   Certain storage traffic such as iSCSI/NFS, depending on
    configuration

Common interface names include:

``` text
vmk0
vmk1
vmk2
```

Example:

``` text
ESXi Host
    ↓
vmk0
    ↓
Management Network
    ↓
vSwitch0
    ↓
vmnic0
    ↓
Physical Network
```

------------------------------------------------------------------------

## 16. What is the difference between a VM Port Group and a VMkernel Port?

**Answer:**

  -----------------------------------------------------------------------
  VM Port Group                       VMkernel Port
  ----------------------------------- -----------------------------------
  Used for VM traffic                 Used for ESXi host/service traffic

  VM's vNIC connects to it            Uses a VMkernel adapter (`vmk`)

  Example: VM Network                 Example: Management Network

  Guest workload networking           Management, vMotion,
                                      storage-related host traffic, etc.
  -----------------------------------------------------------------------

### Golden Rule

``` text
VM needs network connectivity
          ↓
     VM PORT GROUP

ESXi host/service needs network connectivity
          ↓
      VMKERNEL PORT
```

------------------------------------------------------------------------

## 17. Why is NIC Teaming used?

**Answer:**\
NIC Teaming uses multiple physical NIC uplinks to provide
**redundancy/failover** and can also distribute traffic according to the
configured teaming/load-balancing policy.

``` text
              vSwitch0
               /    \
          vmnic0    vmnic1
             ↓        ↓
          Physical Network
```

If one uplink fails, another uplink may maintain connectivity depending
on the configuration.

------------------------------------------------------------------------

## 18. What does Traffic Shaping control?

**Answer:**\
Traffic Shaping controls the **rate/bandwidth of network traffic**
according to a configured policy.

Important terms include:

-   Average Bandwidth
-   Peak Bandwidth
-   Burst Size

``` text
VM Traffic
    ↓
Traffic Shaping Policy
    ↓
Controlled Traffic Rate
    ↓
Network
```

**Easy memory:**\
\> Traffic Shaping = Controlling the network traffic rate according to
policy.

------------------------------------------------------------------------

## 19. Why is management networking required to access an ESXi host through a browser?

**Answer:**\
The administrator's computer and the ESXi host communicate over the
network. Therefore, the ESXi host needs a properly configured
**management IP address, subnet configuration, and network
connectivity**.

A default gateway is required when routing to other networks is
necessary. DNS is useful for hostname-based name resolution.

``` text
Admin PC
   ↓
Physical Network
   ↓
vmnic
   ↓
vSwitch
   ↓
Management Network
   ↓
vmk0
   ↓
ESXi Host
```

Example:

``` text
https://192.168.1.50
```

------------------------------------------------------------------------

## 20. What path can VM traffic take to reach the physical network?

**Answer:**\
A typical VM traffic path from the virtual environment to the physical
network is:

``` text
┌──────────── ESXi HOST ────────────┐
│                                   │
│              VM                   │
│              ↓                    │
│             vNIC                  │
│              ↓                    │
│        VM Port Group              │
│              ↓                    │
│           vSwitch0                │
│              ↓                    │
│            vmnic0                 │
│                                   │
└──────────────┼────────────────────┘
               ↓
        Physical Switch
               ↓
        Router / LAN
               ↓
       Physical Network
```

### Must-Memorize Flow

``` text
VM
 ↓
vNIC
 ↓
VM Port Group
 ↓
vSwitch
 ↓
vmnic
 ↓
Physical Switch
 ↓
Physical Network
```

------------------------------------------------------------------------

# Quick Interview Revision

``` text
ESXi             = Type 1 Hypervisor
VMware Workstation = Type 2 Hypervisor
vNIC             = VM's virtual NIC
vmnic            = ESXi host physical NIC/uplink
vSwitch          = Virtual Layer 2 switch inside ESXi
VM Port Group    = VM network traffic
VMkernel Port    = ESXi host/service traffic
NIC Teaming      = Multiple uplinks / redundancy
Traffic Shaping  = Network bandwidth/rate control
```

------------------------------------------------------------------------

# Self-Test Checklist

Make sure you can explain these concepts without looking at the answers:

-   [ ] Virtualization
-   [ ] Virtual Machine
-   [ ] Host vs Guest
-   [ ] Hypervisor
-   [ ] Type 1 vs Type 2
-   [ ] ESXi
-   [ ] ESX vs ESXi
-   [ ] DCUI
-   [ ] VMkernel
-   [ ] vSphere Standard Switch
-   [ ] vNIC
-   [ ] vmnic
-   [ ] VM Port Group
-   [ ] VMkernel Port
-   [ ] NIC Teaming
-   [ ] Traffic Shaping
-   [ ] Complete VM-to-physical-network traffic flow

------------------------------------------------------------------------

**Phase 1 Interview & Self-Test Q&A Complete**
