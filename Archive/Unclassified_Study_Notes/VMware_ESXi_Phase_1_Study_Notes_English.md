# VMware / ESXi Study Notes

## Phase 1 --- Virtualization, ESXi Fundamentals & Networking Introduction

> **Language:** English\
> **Goal:** Build a strong foundation in virtualization and VMware ESXi
> before moving into practical networking and advanced VMware topics.

------------------------------------------------------------------------

## Index

1.  [What is Virtualization?](#1-what-is-virtualization)
2.  [Virtual Machine (VM)](#2-virtual-machine-vm)
3.  [Host vs Guest](#3-host-vs-guest)
4.  [Benefits of Virtualization](#4-benefits-of-virtualization)
5.  [Types of Virtualization](#5-types-of-virtualization)
6.  [What is a Hypervisor?](#6-what-is-a-hypervisor)
7.  [Type 1 vs Type 2 Hypervisor](#7-type-1-vs-type-2-hypervisor)
8.  [VMware Introduction](#8-vmware-introduction)
9.  [ESX vs ESXi](#9-esx-vs-esxi)
10. [ESXi vs Hyper-V](#10-esxi-vs-hyper-v)
11. [ESXi Installation](#11-esxi-installation)
12. [Customized ESXi ISO](#12-customized-esxi-iso)
13. [DCUI](#13-dcui)
14. [ESXi Boot Process](#14-esxi-boot-process)
15. [ESXi Host Client / vSphere
    Client](#15-esxi-host-client--vsphere-client)
16. [Connecting to ESXi](#16-connecting-to-esxi)
17. [ESXi Licensing](#17-esxi-licensing)
18. [ESXi Services](#18-esxi-services)
19. [ESXi Upgrade](#19-esxi-upgrade)
20. [ESXi Networking Introduction](#20-esxi-networking-introduction)
21. [vSphere Standard Switch (vSS)](#21-vsphere-standard-switch-vss)
22. [vNIC vs vmnic](#22-vnic-vs-vmnic)
23. [VM Port Group](#23-vm-port-group)
24. [VMkernel Port](#24-vmkernel-port)
25. [NIC Teaming](#25-nic-teaming)
26. [Traffic Shaping](#26-traffic-shaping)
27. [Quick Revision Poster](#27-quick-revision-poster)
28. [Interview / Self-Test
    Questions](#28-interview--self-test-questions)
29. [Our Nested ESXi Lab](#29-our-nested-esxi-lab)

------------------------------------------------------------------------

## 1. What is Virtualization?

**Virtualization** is a technology that allows us to use the CPU,
memory, storage, and networking resources of one physical computer or
server to create and run multiple virtual computers.

These virtual computers are called **Virtual Machines (VMs)**.

### Visual Poster

``` text
┌──────────────────────────────────────┐
│          PHYSICAL SERVER             │
│                                      │
│    CPU  │  RAM  │ Storage │ NIC      │
└──────────────────┬───────────────────┘
                   │
                   ▼
          ┌─────────────────┐
          │   HYPERVISOR    │
          └────────┬────────┘
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
     ┌─────┐    ┌─────┐    ┌─────┐
     │ VM1 │    │ VM2 │    │ VM3 │
     │Linux│    │ Win │    │Rocky│
     └─────┘    └─────┘    └─────┘
```

**Easy formula:** `One physical machine → Multiple virtual machines`

------------------------------------------------------------------------

## 2. Virtual Machine (VM)

A **Virtual Machine** is a software-based computer.

Like a physical computer, a VM can have:

-   vCPU
-   RAM
-   Virtual Disk
-   vNIC
-   Operating System
-   Applications

Example:

``` text
VM-01
├── 2 vCPU
├── 4 GB RAM
├── 50 GB Virtual Disk
├── 1 vNIC
└── Rocky Linux
```

The resources assigned to a VM ultimately come from the physical host.

------------------------------------------------------------------------

## 3. Host vs Guest

### Host

The system that provides the resources used for virtualization.

### Guest

The operating system running inside a virtual machine.

Example:

``` text
Physical PC
    ↓
Windows
    ↓
VMware Workstation
    ↓
Rocky Linux VM
```

In this example:

-   **Windows system = Host**
-   **Rocky Linux = Guest**

------------------------------------------------------------------------

## 4. Benefits of Virtualization

Without virtualization:

``` text
Web Server      → Physical Server 1
Database Server → Physical Server 2
Application     → Physical Server 3
Testing         → Physical Server 4
```

With virtualization:

``` text
         ONE PHYSICAL SERVER
                 │
            Hypervisor
                 │
     ┌───────────┼───────────┐
     ▼           ▼           ▼
   Web VM     Database VM   App VM
                              │
                           Test VM
```

### Benefits

-   Better utilization of physical hardware
-   Multiple operating systems can run on one physical server
-   Can reduce the amount of physical hardware required
-   Easier creation and removal of servers for testing
-   VM cloning
-   Snapshot capabilities
-   Workload isolation
-   Additional backup, recovery, and migration options
-   Excellent for training and lab environments

------------------------------------------------------------------------

## 5. Types of Virtualization

### Server Virtualization

One physical server can run multiple virtual servers.

``` text
Physical Server
      ↓
Hypervisor
      ↓
VM1   VM2   VM3
```

VMware ESXi is an important example.

### Desktop Virtualization

Desktop environments can be provided or managed virtually and accessed
by users remotely.

### Network Virtualization

Networking functions and components can be implemented in software.

``` text
VM1 ─┐
     ├── Virtual Switch
VM2 ─┘
```

### Storage Virtualization

Physical storage resources can be logically organized, combined, or
presented to systems.

### Application Virtualization

Applications can be delivered or run using virtualization technologies
instead of relying only on a traditional local installation.

------------------------------------------------------------------------

## 6. What is a Hypervisor?

A **hypervisor** is the software/platform that manages physical hardware
resources and makes those resources available to virtual machines.

``` text
        Physical Hardware
      CPU / RAM / Disk / NIC
               │
               ▼
        ┌──────────────┐
        │  HYPERVISOR  │
        └───────┬──────┘
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
      VM1      VM2      VM3
```

For example, if a physical server has `64 GB RAM`, the hypervisor may
allocate:

``` text
VM1 → 8 GB
VM2 → 16 GB
VM3 → 8 GB
```

The hypervisor manages access to the underlying physical resources.

------------------------------------------------------------------------

## 7. Type 1 vs Type 2 Hypervisor

### Type 1 --- Bare-Metal Hypervisor

A Type 1 hypervisor runs directly on physical server hardware.

``` text
Physical Hardware
        ↓
      ESXi
        ↓
   ┌────┼────┐
   ▼    ▼    ▼
  VM1  VM2  VM3
```

**VMware ESXi is a Type 1 hypervisor.**

### Type 2 Hypervisor

A Type 2 hypervisor runs on top of an existing host operating system.

``` text
Physical Hardware
       ↓
Windows
       ↓
VMware Workstation
       ↓
Virtual Machines
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

## 8. VMware Introduction

VMware has historically been one of the major names in enterprise
virtualization and is now part of **Broadcom**.

Important VMware technologies and products include:

-   ESXi
-   vCenter / vSphere environment
-   vSAN
-   NSX

Our current learning focus is **VMware ESXi**.

------------------------------------------------------------------------

## 9. ESX vs ESXi

### ESX

ESX was VMware's older hypervisor architecture and included a
traditional **Service Console**.

### ESXi

ESXi uses a newer, purpose-built architecture and does not include the
traditional ESX Service Console.

``` text
OLDER

ESX
├── VMkernel
└── Service Console


NEWER ARCHITECTURE

ESXi
├── VMkernel
└── Management components
```

**Remember:** `ESX = older` and
`ESXi = modern VMware bare-metal hypervisor family`

------------------------------------------------------------------------

## 10. ESXi vs Hyper-V

``` text
VMware/Broadcom → ESXi
Microsoft       → Hyper-V
```

Both provide virtualization capabilities, but their architecture,
management tools, features, licensing, and ecosystems differ.

------------------------------------------------------------------------

## 11. ESXi Installation

Basic installation flow:

``` text
ESXi ISO
   ↓
Bootable Media
   ↓
Server Boot
   ↓
ESXi Installer
   ↓
Select Target Disk
   ↓
Install
   ↓
Reboot
   ↓
ESXi Host
```

In our nested lab:

``` text
ESXi ISO
   ↓
VMware Workstation VM
   ↓
Boot from ISO
   ↓
Install ESXi
```

This means we do not need physical USB media for our nested practice
environment.

------------------------------------------------------------------------

## 12. Customized ESXi ISO

Sometimes physical hardware may require vendor-specific components or
drivers that are not available in a standard installation image.

``` text
Standard ESXi Image
        +
Required Components
        ↓
Customized Image
```

**Key point:** Hardware compatibility is very important when installing
ESXi.

------------------------------------------------------------------------

## 13. DCUI

**DCUI = Direct Console User Interface**

The DCUI provides basic configuration and troubleshooting options
directly from the ESXi host console.

Common tasks include:

-   Configure Management Network
-   IP Address
-   Subnet Mask
-   Default Gateway
-   DNS
-   Hostname
-   Restart Management Network
-   Troubleshooting Options

**Easy formula:** `DCUI → Direct/local ESXi configuration`

------------------------------------------------------------------------

## 14. ESXi Boot Process

Simplified boot process:

``` text
Power ON
   ↓
BIOS / UEFI
   ↓
Boot Device
   ↓
ESXi Bootloader
   ↓
VMkernel
   ↓
Drivers / Modules
   ↓
Services
   ↓
ESXi Host Ready
```

**VMkernel** is a core component of the ESXi architecture. We will study
it in more detail in a later phase.

------------------------------------------------------------------------

## 15. ESXi Host Client / vSphere Client

A standalone ESXi host can be managed through a web browser.

``` text
Administrator PC
      │
      │ Browser
      ▼
https://ESXi-IP
      │
      ▼
ESXi Host Client
      │
      ▼
ESXi Host
```

The standalone **ESXi Host Client** and the **vCenter-based vSphere
Client** should not be treated as exactly the same thing. We will study
the distinction in more detail later.

------------------------------------------------------------------------

## 16. Connecting to ESXi

An ESXi host is normally assigned a management IP address.

Example:

``` text
ESXi
Management IP
192.168.1.50
      │
      ▼
Physical Switch
      │
      ▼
Admin PC
192.168.1.20
```

If the network configuration and connectivity are correct, an
administrator can connect to and manage the ESXi host through the
network.

------------------------------------------------------------------------

## 17. ESXi Licensing

Available VMware features and permitted usage can depend on licensing or
subscription entitlements.

> VMware/Broadcom licensing has changed over time. Always verify current
> official information before making licensing or certification
> decisions.

------------------------------------------------------------------------

## 18. ESXi Services

ESXi contains different management and system services/components.

``` text
ESXi
├── Management components
├── SSH
├── Time synchronization
├── Networking
└── VM-related services
```

**Security principle:** Do not enable services unnecessarily. Enable
them according to operational and security requirements.

------------------------------------------------------------------------

## 19. ESXi Upgrade

Example upgrade process:

``` text
ESXi 7
   ↓
Compatibility Check
   ↓
Configuration / Backup Planning
   ↓
Hardware Compatibility
   ↓
Upgrade
   ↓
ESXi 8
   ↓
Post-upgrade Verification
```

In production environments, planning, backups/configuration protection,
and compatibility checks are important before an upgrade.

------------------------------------------------------------------------

## 20. ESXi Networking Introduction

Physical networking:

``` text
PC
 ↓
NIC
 ↓
Switch
 ↓
Router
```

VMware virtual networking:

``` text
VM
 ↓
vNIC
 ↓
Port Group
 ↓
vSwitch
 ↓
vmnic
 ↓
Physical Switch
 ↓
Physical Network
```

> This is one of the most important networking flows from Phase 1.

------------------------------------------------------------------------

## 21. vSphere Standard Switch (vSS)

A **vSphere Standard Switch (vSS)** is a software-based Layer 2 virtual
switch inside an ESXi host.

### Networking Poster

``` text
┌────────────── ESXi HOST ──────────────┐
│                                       │
│ VM1             VM2             VM3   │
│  │               │               │    │
│ vNIC            vNIC            vNIC  │
│  │               │               │    │
│  └───────────────┼───────────────┘    │
│                  ▼                    │
│              VM NETWORK               │
│             PORT GROUP                │
│                  │                    │
│                  ▼                    │
│               vSwitch0                │
│                  │                    │
│                vmnic0                 │
└──────────────────┼────────────────────┘
                   │
                   ▼
             Physical Switch
                   │
                   ▼
              Router / LAN
```

------------------------------------------------------------------------

## 22. vNIC vs vmnic

These terms look similar but represent different things.

### vNIC

A virtual network adapter assigned to a virtual machine.

``` text
VM
 ↓
vNIC
```

### vmnic

A VMware ESXi name representing a physical NIC/uplink available to the
ESXi host.

Examples:

``` text
vmnic0
vmnic1
```

### Quick Poster

``` text
 VM
 │
 ▼
vNIC        ← Virtual
 │
 ▼
vSwitch
 │
 ▼
vmnic0      ← Physical NIC/uplink representation
 │
 ▼
Physical Switch
```

------------------------------------------------------------------------

## 23. VM Port Group

A VM's vNIC normally connects to a **VM Port Group**.

``` text
VM1 ─┐
     │
VM2 ─┼── VM Network Port Group
     │
VM3 ─┘
          │
          ▼
       vSwitch0
```

Port Groups are important for network organization, VLAN configuration,
and network policies.

------------------------------------------------------------------------

## 24. VMkernel Port

Do not confuse a **VM Port Group** with a **VMkernel Port**.

### VM Port Group

Used for virtual-machine network traffic.

``` text
VM
 ↓
vNIC
 ↓
VM Port Group
```

### VMkernel Port

Used by the ESXi host for host networking/services.

``` text
ESXi Host
    ↓
VMkernel Adapter
    ↓
vmk0
    ↓
vSwitch
    ↓
vmnic
```

A management-network example:

``` text
vmk0
IP: 192.168.1.50
       ↓
Management Network
       ↓
vSwitch0
       ↓
vmnic0
       ↓
Physical Network
```

Depending on the design, VMkernel adapters can support services such as
management traffic, vMotion, and certain storage-related traffic.

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

## 25. NIC Teaming

With only one physical uplink:

``` text
vSwitch0
   ↓
vmnic0
```

A physical NIC or link failure may affect connectivity.

With multiple uplinks:

``` text
             vSwitch0
              /    \
             /      \
        vmnic0      vmnic1
           │           │
           ▼           ▼
        Physical Network
```

NIC Teaming can provide redundancy/failover and can distribute traffic
according to the configured teaming/load-balancing policy.

------------------------------------------------------------------------

## 26. Traffic Shaping

Traffic shaping is used to control network traffic/bandwidth rates
according to policy.

``` text
VM Traffic
    ↓
Traffic Shaping Policy
    ↓
Controlled Traffic Rate
    ↓
vSwitch
    ↓
Physical Network
```

Terms we may study in more detail later:

-   Average Bandwidth
-   Peak Bandwidth
-   Burst Size

------------------------------------------------------------------------

## 27. Quick Revision Poster

``` text
╔══════════════════════════════════════════╗
║          VMWARE QUICK REVISION           ║
╠══════════════════════════════════════════╣
║ Virtualization                           ║
║ One physical system → Multiple VMs       ║
║                                          ║
║ Hypervisor                               ║
║ Manages hardware resources for VMs       ║
║                                          ║
║ Type 1                                   ║
║ Hardware → ESXi → VMs                    ║
║                                          ║
║ Type 2                                   ║
║ Hardware → Windows → Workstation → VMs   ║
║                                          ║
║ ESXi                                     ║
║ VMware Type-1 Hypervisor                 ║
║                                          ║
║ DCUI                                     ║
║ Direct/basic ESXi configuration          ║
║                                          ║
║ vSwitch                                  ║
║ Virtual Layer-2 switch                   ║
║                                          ║
║ vNIC                                     ║
║ VM's virtual network card                ║
║                                          ║
║ vmnic                                    ║
║ ESXi physical NIC/uplink                 ║
║                                          ║
║ VM Port Group                            ║
║ VM network traffic                       ║
║                                          ║
║ VMkernel Port                            ║
║ ESXi host/service traffic                ║
║                                          ║
║ NIC Teaming                              ║
║ Multiple uplinks / redundancy            ║
╚══════════════════════════════════════════╝
```

------------------------------------------------------------------------

## 28. Interview / Self-Test Questions

1.  What is virtualization?
2.  What is a virtual machine?
3.  What is the difference between a host and a guest?
4.  What does a hypervisor do?
5.  What is the difference between Type 1 and Type 2 hypervisors?
6.  Is ESXi a Type 1 or Type 2 hypervisor?
7.  Is VMware Workstation a Type 1 or Type 2 hypervisor?
8.  What is the basic difference between ESX and ESXi?
9.  What is DCUI?
10. What is VMkernel?
11. What is a vSphere Standard Switch?
12. What is a vNIC?
13. What does `vmnic0` represent?
14. What is the purpose of a VM Port Group?
15. What is the purpose of a VMkernel Port?
16. What is the difference between a VM Port Group and a VMkernel Port?
17. Why is NIC Teaming used?
18. What does Traffic Shaping control?
19. Why does an ESXi host need management networking?
20. What path can VM traffic take to reach the physical network?

### Question 20 --- Expected Flow

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
Network
```

------------------------------------------------------------------------

## 29. Our Nested ESXi Lab

Our learning lab architecture:

``` text
PHYSICAL HP PC
      ↓
Windows
      ↓
VMware Workstation
      ↓
ESXi VM
      ↓
Virtual Networking
      ↓
Future Nested VMs
```

A typical production-style architecture is:

``` text
PHYSICAL SERVER
      ↓
ESXi
      ↓
VMs
```

### Important

**ESXi is fundamentally a Type-1 hypervisor.** In our learning
environment, we are running ESXi as a VM inside VMware Workstation so
that we can practice without requiring dedicated enterprise server
hardware. This is called **Nested Virtualization**.

------------------------------------------------------------------------

# Phase 1 Complete ✅

## Next Phase

**Phase 2 --- ESXi Networking & Practical Configuration**

We will continue from this point in the next class and keep building the
study guide phase by phase.
