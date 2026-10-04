# VMware / ESXi Study Notes

## Phase 1 --- Virtualization, ESXi Fundamentals & Networking Introduction

> **Language:** Roman Urdu + English technical terms\
> **Goal:** Basic concepts ko clear karna aur future VMware/ESXi
> practical labs ke liye strong foundation banana.

------------------------------------------------------------------------

## Index

1.  [Virtualization kya hai?](#1-virtualization-kya-hai)
2.  [Virtual Machine (VM)](#2-virtual-machine-vm)
3.  [Host vs Guest](#3-host-vs-guest)
4.  [Benefits of Virtualization](#4-benefits-of-virtualization)
5.  [Types of Virtualization](#5-types-of-virtualization)
6.  [Hypervisor](#6-hypervisor)
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

## 1. Virtualization kya hai?

**Virtualization** ek technology hai jis ki madad se hum ek physical
computer/server ke CPU, RAM, storage aur networking resources ko use
karke **multiple virtual computers** bana sakte hain.

In virtual computers ko **Virtual Machines (VMs)** kehte hain.

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

VM ek **software-based computer** hai. Physical computer ki tarah VM ke
paas bhi resources hote hain:

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

Ye resources ultimately physical host ke resources se milte hain.

------------------------------------------------------------------------

## 3. Host vs Guest

### Host

Woh system jo virtualization resources provide karta hai.

### Guest

VM ke andar chalne wala operating system.

``` text
Physical PC
    ↓
Windows
    ↓
VMware Workstation
    ↓
Rocky Linux VM
```

Is example mein:

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

-   Hardware utilization better hota hai.
-   Multiple operating systems ek physical server par run ho sakte hain.
-   Hardware cost reduce ho sakti hai.
-   VM create/delete karna relatively easy hota hai.
-   Testing aur labs easy hote hain.
-   VM cloning possible hoti hai.
-   Snapshots useful ho sakte hain.
-   Workloads ko isolate kiya ja sakta hai.
-   Backup, recovery aur migration ke additional options milte hain.

------------------------------------------------------------------------

## 5. Types of Virtualization

### Server Virtualization

``` text
Physical Server
      ↓
Hypervisor
      ↓
VM1   VM2   VM3
```

VMware ESXi iska important example hai.

### Desktop Virtualization

Desktop environment ko centrally/virtually provide karna.

### Network Virtualization

``` text
VM1 ─┐
     ├── Virtual Switch
VM2 ─┘
```

### Storage Virtualization

Physical storage resources ko logically organize/combine/present karna.

### Application Virtualization

Applications ko traditional local installation se alag
virtualization/delivery mechanism ke through provide karna.

------------------------------------------------------------------------

## 6. Hypervisor

**Hypervisor** woh software/platform hai jo physical hardware resources
ko VMs ke liye manage aur allocate karta hai.

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

Example: agar physical server ke paas `64 GB RAM` hai:

``` text
VM1 → 8 GB
VM2 → 16 GB
VM3 → 8 GB
```

Hypervisor in resources ko manage karta hai.

------------------------------------------------------------------------

## 7. Type 1 vs Type 2 Hypervisor

### Type 1 --- Bare-Metal

Directly physical server hardware par run/install hota hai.

``` text
Physical Hardware
        ↓
      ESXi
        ↓
   ┌────┼────┐
   ▼    ▼    ▼
  VM1  VM2  VM3
```

**VMware ESXi = Type 1 Hypervisor**

### Type 2

Existing operating system ke upar application ki tarah run hota hai.

``` text
Physical Hardware
       ↓
Windows
       ↓
VMware Workstation
       ↓
Virtual Machines
```

  Type 1                          Type 2
  ------------------------------- ----------------------------
  Hardware ke directly upar       Host OS ke upar
  ESXi example                    VMware Workstation example
  Server/data-center use common   Desktop/lab use common

------------------------------------------------------------------------

## 8. VMware Introduction

VMware virtualization industry ka historically bohat important vendor
raha hai. VMware ab **Broadcom** ka hissa hai.

Important technologies/products mein:

-   ESXi
-   vCenter / vSphere environment
-   vSAN
-   NSX

Hamari current learning ka main focus **VMware ESXi** hai.

------------------------------------------------------------------------

## 9. ESX vs ESXi

### ESX

VMware ka older hypervisor architecture tha aur ismein traditional
**Service Console** included thi.

### ESXi

Newer, purpose-built architecture hai jisme traditional ESX Service
Console nahi hoti.

``` text
OLD

ESX
├── VMkernel
└── Service Console


NEWER ARCHITECTURE

ESXi
├── VMkernel
└── Management components
```

**Remember:** `ESX = old` and
`ESXi = modern VMware bare-metal hypervisor family`

------------------------------------------------------------------------

## 10. ESXi vs Hyper-V

``` text
VMware/Broadcom → ESXi
Microsoft       → Hyper-V
```

Dono ka fundamental purpose virtualization hai, lekin architecture,
management tools, features, licensing aur ecosystem different ho sakte
hain.

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
Target Disk
   ↓
Install
   ↓
Reboot
   ↓
ESXi Host
```

Hamare nested lab mein:

``` text
ESXi ISO
   ↓
VMware Workstation VM
   ↓
Boot from ISO
   ↓
Install ESXi
```

------------------------------------------------------------------------

## 12. Customized ESXi ISO

Kabhi physical hardware ke liye standard ESXi image mein required vendor
components/drivers ki zarurat ho sakti hai.

``` text
Standard ESXi Image
        +
Required Components
        ↓
Customized Image
```

**Key point:** ESXi installation mein hardware compatibility important
hai.

------------------------------------------------------------------------

## 13. DCUI

**DCUI = Direct Console User Interface**

ESXi host ke local/direct console par basic configuration ke liye
interface.

Common tasks:

-   Management Network
-   IP Address
-   Subnet Mask
-   Default Gateway
-   DNS
-   Hostname
-   Restart Management Network
-   Troubleshooting Options

**Easy formula:** `DCUI → Local/basic ESXi configuration`

------------------------------------------------------------------------

## 14. ESXi Boot Process

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

**VMkernel** ESXi architecture ka core component hai. Isko future phase
mein detail se cover karenge.

------------------------------------------------------------------------

## 15. ESXi Host Client / vSphere Client

Standalone ESXi host ko browser ke through manage kiya ja sakta hai.

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

ESXi Host Client aur vCenter-based vSphere Client ko future phase mein
separately detail se distinguish karenge.

------------------------------------------------------------------------

## 16. Connecting to ESXi

ESXi ko management network par IP address diya jata hai.

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

Agar network configuration aur connectivity sahi ho to administrator
browser se ESXi host ko manage kar sakta hai.

------------------------------------------------------------------------

## 17. ESXi Licensing

VMware environment mein available features aur permitted usage
licensing/subscription par depend kar sakte hain.

> VMware/Broadcom licensing time ke saath change hui hai. Current
> licensing ya certification decision ke liye latest official
> information verify karni chahiye.

------------------------------------------------------------------------

## 18. ESXi Services

ESXi mein different management/system services aur components hote hain.

``` text
ESXi
├── Management components
├── SSH
├── Time synchronization
├── Networking
└── VM-related services
```

**Security rule:** Har service ko bina requirement enable nahi karna
chahiye.

------------------------------------------------------------------------

## 19. ESXi Upgrade

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

Production environment mein upgrade se pehle planning aur compatibility
checking important hoti hai.

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

VMware environment:

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

> Ye Phase 1 ka sab se important networking flow hai.

------------------------------------------------------------------------

## 21. vSphere Standard Switch (vSS)

**vSphere Standard Switch (vSS)** ESXi host ke andar software-based
Layer 2 virtual switch hai.

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

### vNIC

Virtual Machine ka virtual network adapter.

``` text
VM
 ↓
vNIC
```

### vmnic

ESXi host ki physical NIC/uplink ko ESXi mein represent karne wala naam.

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

VM ka vNIC normally ek **VM Port Group** se connect hota hai.

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

Port Groups network organization, VLAN configuration aur policies ke
liye important hain.

------------------------------------------------------------------------

## 24. VMkernel Port

**VM Port Group aur VMkernel Port ko confuse nahi karna.**

### VM Port Group

Virtual Machines ke traffic ke liye.

``` text
VM
 ↓
vNIC
 ↓
VM Port Group
```

### VMkernel Port

ESXi host ke apne networking/services ke liye.

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

Management example:

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

### Golden Rule

``` text
VM ko network chahiye
        ↓
VM PORT GROUP


ESXi host/service ko network chahiye
        ↓
VMKERNEL PORT
```

------------------------------------------------------------------------

## 25. NIC Teaming

Single uplink:

``` text
vSwitch0
   ↓
vmnic0
```

Agar NIC/link fail ho jaye to connectivity impact ho sakti hai.

Multiple uplinks:

``` text
             vSwitch0
              /    \
             /      \
        vmnic0      vmnic1
           │           │
           ▼           ▼
        Physical Network
```

NIC Teaming ka use redundancy/failover aur configured policies ke
mutabiq traffic distribution ke liye kiya ja sakta hai.

------------------------------------------------------------------------

## 26. Traffic Shaping

Traffic shaping ka purpose network traffic/bandwidth rate ko control
karna hai.

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

Future networking phase mein hum ye terms detail se dekhenge:

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
║ Hardware resources → Manage VMs          ║
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
║ VM traffic                               ║
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

1.  Virtualization kya hai?
2.  VM kya hoti hai?
3.  Host aur Guest mein kya difference hai?
4.  Hypervisor kya karta hai?
5.  Type 1 aur Type 2 hypervisor mein kya difference hai?
6.  ESXi Type 1 hai ya Type 2?
7.  VMware Workstation Type 1 hai ya Type 2?
8.  ESX aur ESXi mein basic difference kya hai?
9.  DCUI kya hai?
10. VMkernel kya hai?
11. vSphere Standard Switch kya hai?
12. `vNIC` kya hai?
13. `vmnic0` kya represent karta hai?
14. VM Port Group ka purpose kya hai?
15. VMkernel Port ka purpose kya hai?
16. VM Port Group aur VMkernel Port mein difference kya hai?
17. NIC Teaming kyun use karte hain?
18. Traffic Shaping kya control karti hai?
19. ESXi host ko browser se access karne ke liye management networking
    kyun zaroori hai?
20. Ek VM ka traffic physical network tak kis path se ja sakta hai?

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

Hamare learning lab ka architecture:

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

Production-style architecture normally:

``` text
PHYSICAL SERVER
      ↓
ESXi
      ↓
VMs
```

### Important

**ESXi fundamentally ek Type-1 hypervisor hai.** Hamare learning lab
mein hum ESXi ko VMware Workstation ke andar VM ke taur par run kar rahe
hain. Is setup ko **Nested Virtualization** kehte hain.

------------------------------------------------------------------------

# Phase 1 Complete ✅

### Next Phase

**Phase 2 --- ESXi Networking & Practical Configuration**

Hum next class mein isi point se continue karenge aur notes ko
phase-by-phase build karte jayenge.
