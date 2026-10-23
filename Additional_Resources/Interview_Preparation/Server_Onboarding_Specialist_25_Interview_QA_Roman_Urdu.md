# Server Onboarding Specialist --- 25 Interview Questions & Answers (Roman Urdu)

## Study Notes

**Target Role:** Server Onboarding Specialist\
**Focus:** Linux, bare-metal servers, BMC/IPMI/iDRAC/iLO, BIOS,
RAID/JBOD, firmware, PXE, networking, configuration management, hardware
troubleshooting aur server onboarding.

> **Interview Rule:** Apne real Linux aur server experience ke bare mein
> confidently baat karein. Puppet, PXE-based provisioning aur GPU server
> onboarding mein agar direct hands-on experience nahi hai to usay claim
> na karein. Concept samjhayen aur apni related skills ke saath connect
> karein.

------------------------------------------------------------------------

# Index

1.  [Server onboarding kya hai?](#1-server-onboarding-kya-hai)
2.  [Bare-metal server kya hota hai?](#2-bare-metal-server-kya-hota-hai)
3.  [BMC kya hai?](#3-bmc-kya-hai)
4.  [IPMI kya hai?](#4-ipmi-kya-hai)
5.  [iDRAC, iLO, BMC aur IPMI mein
    difference](#5-idrac-ilo-bmc-aur-ipmi-mein-kya-difference-hai)
6.  [Out-of-band management kyun important
    hai?](#6-out-of-band-management-kyun-important-hai)
7.  [Newly racked server par kya check
    karein?](#7-newly-racked-server-par-kya-check-karein)
8.  [RAID kya hai?](#8-raid-kya-hai)
9.  [RAID 0, 1, 5 aur
    10](#9-raid-0-raid-1-raid-5-aur-raid-10-mein-kya-difference-hai)
10. [JBOD kya hai?](#10-jbod-kya-hai)
11. [Firmware kya hai?](#11-firmware-kya-hai)
12. [BIOS kya hai?](#12-bios-kya-hai)
13. [PXE boot kya hai?](#13-pxe-boot-kya-hai)
14. [PXE boot troubleshoot kaise
    karein?](#14-server-pxe-boot-nahi-kar-raha-to-kya-check-karein)
15. [DHCP kya hai?](#15-dhcp-kya-hai)
16. [VLAN kya hai?](#16-vlan-kya-hai)
17. [IPv4 aur IPv6](#17-ipv4-aur-ipv6-mein-kya-difference-hai)
18. [Linux server unreachable ho to kya
    karein?](#18-newly-provisioned-linux-server-unreachable-hai-kaise-troubleshoot-karein)
19. [Linux mein disk
    information](#19-linux-mein-disk-information-kaise-check-karein)
20. [Hardware vs OS
    problem](#20-hardware-problem-aur-os-problem-mein-kaise-difference-karein)
21. [DOA server kya hai?](#21-doa-server-kya-hai)
22. [Configuration management kya
    hai?](#22-configuration-management-kya-hai)
23. [Automation job fail ho
    jaye](#23-onboarding-ke-dauran-automation-job-fail-ho-jaye-to-kya-karein)
24. [Multiple servers ko onboard
    karna](#24-multiple-servers-onboarding-ke-liye-wait-kar-rahe-hon-to-kaise-handle-karein)
25. [Aap is role ke liye good fit kyun
    hain?](#25-aap-is-server-onboarding-specialist-role-ke-liye-good-fit-kyun-hain)

------------------------------------------------------------------------

## 1. Server onboarding kya hai?

**Answer:**\
Server onboarding ka matlab newly installed ya newly racked physical
server ko production ke liye ready karna hai.

Is process mein aam tor par hardware verify karna, BMC/IPMI
check/configure karna, BIOS aur storage check karna, operating system
provision/install karna, configuration apply karna, network aur server
health validate karna, inventory update karna aur akhir mein server ko
production ke liye handoff karna shamil hota hai.

### Simple Flow

``` text
Rack / Hardware Handoff
        ↓
Hardware Validation
        ↓
BMC / BIOS / Storage
        ↓
OS Provisioning
        ↓
Configuration
        ↓
Network + Health Validation
        ↓
Inventory / Documentation
        ↓
Production Handoff
```

------------------------------------------------------------------------

## 2. Bare-metal server kya hota hai?

**Answer:**\
Bare-metal server ek actual physical server hota hai. Yeh VM nahi hota
jo kisi doosre host ke upar run kar raha ho.

Example:

``` text
Dell PowerEdge
HP ProLiant
```

Agar Linux directly physical server ke hardware par run ho raha hai to
hum usay bare-metal environment keh sakte hain.

------------------------------------------------------------------------

## 3. BMC kya hai?

**Answer:**\
BMC ka full form **Baseboard Management Controller** hai.

Yeh server ke andar dedicated management controller hota hai jo
operating system se independent server ko remotely manage aur monitor
karne deta hai.

BMC ke through hum:

-   Hardware health check kar sakte hain
-   Server power on/off kar sakte hain
-   Hardware alerts dekh sakte hain
-   Sensors check kar sakte hain
-   Remote console access kar sakte hain
-   OS down hone par bhi server troubleshoot kar sakte hain

------------------------------------------------------------------------

## 4. IPMI kya hai?

**Answer:**\
IPMI ka full form **Intelligent Platform Management Interface** hai.

Yeh out-of-band server management ke liye ek standard/interface hai. Is
ke through power control, sensors, hardware status aur remote management
jaisi functionality mil sakti hai.

------------------------------------------------------------------------

## 5. iDRAC, iLO, BMC aur IPMI mein kya difference hai?

**Answer:**

-   **BMC:** Server ka management controller.
-   **IPMI:** Management ka standard/interface.
-   **iDRAC:** Dell ka remote server management solution.
-   **iLO:** HP/HPE ka remote server management solution.

### Easy Memory

``` text
Dell → iDRAC
HPE  → iLO
BMC  → Management Controller
IPMI → Management Standard / Interface
```

------------------------------------------------------------------------

## 6. Out-of-band management kyun important hai?

**Answer:**\
Out-of-band management ka sab se bara faida yeh hai ke agar operating
system down ho ya server network se normally reachable na ho, tab bhi
hum management interface ke through server ko access kar sakte hain.

Example: Linux boot nahi ho raha. Main iDRAC/iLO ke through hardware
health check, console access, alerts inspect ya server power-cycle kar
sakta hoon.

------------------------------------------------------------------------

## 7. Newly racked server par kya check karein?

**Answer:**\
Main sab se pehle company ka approved runbook follow karunga.

Typical checks:

1.  Server identity aur inventory verify karunga.
2.  Power aur hardware health check karunga.
3.  BMC/iDRAC/iLO connectivity verify karunga.
4.  BIOS settings check karunga.
5.  Disks aur RAID/JBOD configuration verify karunga.
6.  Firmware status check karunga.
7.  NIC aur network connectivity verify karunga.
8.  Approved OS provisioning process run karunga.
9.  Required configuration apply karunga.
10. Server health aur connectivity validate karunga.
11. Inventory/documentation update karunga.
12. Server ko production ke liye handoff karunga.

------------------------------------------------------------------------

## 8. RAID kya hai?

**Answer:**\
RAID ka full form **Redundant Array of Independent Disks** hai.

RAID multiple disks ko combine karta hai taake performance, redundancy
ya dono hasil kiye ja saken. Yeh RAID level par depend karta hai.

------------------------------------------------------------------------

## 9. RAID 0, RAID 1, RAID 5 aur RAID 10 mein kya difference hai?

  RAID      Basic Concept              Redundancy
  --------- -------------------------- ------------
  RAID 0    Striping                   Nahi
  RAID 1    Mirroring                  Haan
  RAID 5    Striping + Single Parity   Haan
  RAID 10   Mirroring + Striping       Haan

**Interview point:**\
RAID 0 performance deta hai lekin redundancy nahi. RAID 1 data mirror
karta hai. RAID 5 ek disk failure tolerate kar sakta hai. RAID 10
mirroring aur striping dono use karta hai.

------------------------------------------------------------------------

## 10. JBOD kya hai?

**Answer:**\
JBOD ka full form **Just a Bunch Of Disks** hai.

Traditional RAID array banane ke bajaye disks individually operating
system ya software/storage layer ko present ki ja sakti hain.

------------------------------------------------------------------------

## 11. Firmware kya hai?

**Answer:**\
Firmware low-level software hota hai jo hardware device ke andar stored
hota hai.

Server mein firmware examples:

-   BIOS/UEFI
-   BMC/iDRAC/iLO
-   RAID controller
-   NIC
-   Storage device

Firmware update bugs, security issues, compatibility ya stability
problems ko fix kar sakta hai.

------------------------------------------------------------------------

## 12. BIOS kya hai?

**Answer:**\
BIOS firmware hota hai jo server hardware ko initialize karta hai aur
boot process start karne mein help karta hai.

Server onboarding ke waqt hum settings check kar sakte hain:

-   Boot order
-   PXE/network boot
-   CPU options
-   Virtualization settings
-   Storage/controller settings

------------------------------------------------------------------------

## 13. PXE boot kya hai?

**Answer:**\
PXE ka full form **Preboot Execution Environment** hai.

PXE server ko local installation media ke bajaye network ke through boot
ya OS installation process start karne deta hai.

### Simplified Flow

``` text
Server Power On
      ↓
PXE / Network Boot
      ↓
DHCP
      ↓
Boot Information / Files
      ↓
Installer / Image
      ↓
OS Provisioning
```

**Interview honesty:**\
Agar aap ne production mein direct PXE provisioning nahi ki to yeh claim
na karein. Concept aur troubleshooting flow explain karein.

------------------------------------------------------------------------

## 14. Server PXE boot nahi kar raha to kya check karein?

**Answer:**\
Main systematic troubleshooting karunga:

1.  Power aur hardware health check.
2.  Physical/network link verify.
3.  Correct NIC check.
4.  BIOS boot order check.
5.  PXE/network boot enabled hai ya nahi.
6.  DHCP available hai ya nahi.
7.  VLAN/network configuration check.
8.  Required boot services/files available hain ya nahi.
9.  Known-good server ke saath compare.
10. Logs aur runbook check karke zarurat par evidence ke saath escalate.

------------------------------------------------------------------------

## 15. DHCP kya hai?

**Answer:**\
DHCP ka full form **Dynamic Host Configuration Protocol** hai.

DHCP automatically network configuration provide kar sakta hai:

-   IP address
-   Subnet mask/prefix
-   Default gateway
-   DNS information

DHCP PXE/network boot workflow mein bhi important role play kar sakta
hai.

------------------------------------------------------------------------

## 16. VLAN kya hai?

**Answer:**\
VLAN ka full form **Virtual Local Area Network** hai.

VLAN devices ko logically different Layer 2 broadcast domains mein
separate karta hai, chahe woh same physical switching infrastructure use
kar rahe hon.

------------------------------------------------------------------------

## 17. IPv4 aur IPv6 mein kya difference hai?

**Answer:**

**IPv4:** 32-bit addressing use karta hai.

Example:

``` text
192.168.1.10
```

**IPv6:** 128-bit addressing use karta hai aur bohat bara address space
provide karta hai.

------------------------------------------------------------------------

## 18. Newly provisioned Linux server unreachable hai. Kaise troubleshoot karein?

**Answer:**\
Main layer-by-layer troubleshoot karunga:

1.  BMC/power status check.
2.  Confirm karunga Linux boot hua hai.
3.  NIC/link check.
4.  IP address check.
5.  Route/default gateway check.
6.  VLAN/network assignment verify.
7.  Gateway/connectivity test.
8.  Firewall check.
9.  Agar hostname issue hai to DNS check.
10. Logs review aur findings document.

### Useful Commands

``` bash
ip link
ip addr
ip route
ping <gateway>
ss -tulpn
journalctl
dmesg
```

------------------------------------------------------------------------

## 19. Linux mein disk information kaise check karein?

**Answer:**

``` bash
lsblk
blkid
df -h
fdisk -l
```

### Yaad Rakhein

-   `lsblk` → block devices aur partitions
-   `blkid` → UUID/filesystem information
-   `df -h` → mounted filesystem usage
-   `fdisk -l` → disk/partition information

------------------------------------------------------------------------

## 20. Hardware problem aur OS problem mein kaise difference karein?

**Answer:**\
Main pehle hardware layer check karunga.

BMC/iDRAC/iLO mein:

-   Hardware alerts
-   Sensors
-   Disk/controller status
-   Memory errors
-   Failed components

check karunga.

Agar hardware healthy lagta hai to Linux/OS side investigate karunga:

-   Boot/kernel messages
-   Services
-   Filesystems/storage
-   Networking
-   CPU/memory
-   System/application logs

Is approach se problem kis layer par hai usay isolate karna easy hota
hai.

------------------------------------------------------------------------

## 21. DOA server kya hai?

**Answer:**\
DOA ka matlab **Dead on Arrival** hai.

Agar newly delivered server ya component initial validation mein fail ho
jaye to usay DOA consider kiya ja sakta hai.

Main failure document karunga, hardware/error information collect
karunga aur company ke repair/replacement ya RMA process ko follow
karunga.

------------------------------------------------------------------------

## 22. Configuration management kya hai?

**Answer:**\
Configuration management ka purpose servers ko consistent aur desired
configuration mein maintain karna hai.

Examples:

-   Ansible
-   Puppet

### Aap ke liye interview answer

> "Mera stronger hands-on experience Ansible aur Ansible Tower ke saath
> hai, Puppet ke saath nahi. Lekin mujhe configuration management ka
> concept samajh hai aur main established Puppet automation ko learn,
> run aur troubleshoot karne mein comfortable hoon."

------------------------------------------------------------------------

## 23. Onboarding ke dauran automation job fail ho jaye to kya karein?

**Answer:**\
Main job ko blindly rerun nahi karunga.

Main:

1.  Failed task identify karunga.
2.  Error aur logs read karunga.
3.  Connectivity check karunga.
4.  Credentials/access check karunga.
5.  Configuration check karunga.
6.  Repository/package dependencies check karunga.
7.  Hardware/network dependencies check karunga.
8.  Runbook ke mutabiq root issue correct karunga.
9.  Approved process dobara run karunga.
10. Final state validate aur result document karunga.

------------------------------------------------------------------------

## 24. Multiple servers onboarding ke liye wait kar rahe hon to kaise handle karein?

**Answer:**\
Main established workflow aur priorities follow karunga.

Accurate inventory/status maintain karunga, approved automation use
karunga aur successful servers ko blocked servers se separate track
karunga.

Agar ek server hardware ya network issue ki wajah se blocked hai to us
blocker ko document aur appropriate team ko escalate karunga. Agar
process allow karta hai to baqi servers ka onboarding continue karunga.

Goal yeh hai ke high-volume onboarding pipeline efficiently move karti
rahe lekin validation aur documentation compromise na ho.

------------------------------------------------------------------------

## 25. Aap is Server Onboarding Specialist role ke liye good fit kyun hain?

### Suggested Interview Answer

> "Mera background Linux system administration ke saath hands-on server
> aur infrastructure support ka combination hai. Main RHEL aur CentOS
> environments ke saath kaam kar chuka hoon aur Dell PowerEdge, HP
> ProLiant, iDRAC/iLO, RAID, firmware, bare-metal systems, Linux
> troubleshooting, networking, Bash aur Ansible ka experience rakhta
> hoon.
>
> Mere paas enterprise production-support experience bhi hai, is liye
> main procedures aur runbooks follow karne, systematic troubleshooting,
> issues document karne aur different technical teams ke saath
> coordinate karne mein comfortable hoon.
>
> Puppet aur PXE-based provisioning jaisi kuch technologies mein mera
> direct hands-on experience limited hai. Mera stronger automation
> experience Ansible ke saath hai, lekin mujhe underlying concepts
> samajh hain aur main organization ke established tools aur processes
> ko quickly learn karne ke liye comfortable hoon."

------------------------------------------------------------------------

# Quick Revision Sheet

``` text
BMC   = Baseboard Management Controller
IPMI  = Intelligent Platform Management Interface
iDRAC = Dell Remote Server Management
iLO   = HPE Remote Server Management
RAID  = Redundant Array of Independent Disks
JBOD  = Just a Bunch Of Disks
PXE   = Preboot Execution Environment
DHCP  = Dynamic Host Configuration Protocol
VLAN  = Virtual Local Area Network
DOA   = Dead on Arrival
RMA   = Return Merchandise Authorization
```

------------------------------------------------------------------------

# Core Server Onboarding Flow

``` text
Physical Server
      ↓
BMC / iDRAC / iLO
      ↓
BIOS + Firmware
      ↓
Disk / RAID / JBOD
      ↓
Network / PXE
      ↓
Linux OS
      ↓
Configuration Management
      ↓
Validation
      ↓
Inventory + Documentation
      ↓
Production
```

------------------------------------------------------------------------

# Troubleshooting Pattern

``` text
Problem Confirm
      ↓
Hardware Check
      ↓
OS Check
      ↓
Network Check
      ↓
Configuration / Automation Check
      ↓
Logs Review
      ↓
Fix ya Escalate
      ↓
Validate
      ↓
Document
```

## Study Tip

Answers ko word-by-word memorize na karein. **Flow aur concept
samjhein.** Interview mein apni language mein naturally explain karein.

Jahan direct experience limited ho, wahan honestly batayein aur related
experience ke saath connect karein:

``` text
Puppet → Ansible / Configuration Management
PXE    → DHCP / Network Boot / OS Provisioning
BMC    → iDRAC / iLO / Hardware Management
```
