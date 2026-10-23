# VMware Teacher Lecture Notes - Explained in English

**Prepared:** 16 October 2026 (America/Chicago)  
**Source files:** `Day2(1).txt`, `Day4.txt`, `Day5(1).txt`, `VMware(2).txt`  
**Purpose:** Understand the teacher's lecture reminders through structured explanations, examples and technical corrections.

> These files contain short lecture reminders, not a complete textbook. The original points are explained below. Where the teacher's intended meaning is unclear, an interpretation is identified as such. Company inventory, an unclear exam code and uncertain abbreviations are not presented as verified facts.

## Index

1. [Certifications and support levels](#1-certifications-and-support-levels)
2. [VMware product relationships](#2-vmware-product-relationships)
3. [ESXi installation requirements](#3-esxi-installation-requirements)
4. [ESXi, VMvisor, ESX and Host](#4-esxi-vmvisor-esx-and-host)
5. [Server types and preparation](#5-server-types-and-preparation)
6. [Legacy CPU boot option](#6-legacy-cpu-boot-option)
7. [Hardware virtualization and software](#7-hardware-virtualization-and-software)
8. [Test, Production, DR, RPO and RTO](#8-test-production-dr-rpo-and-rto)
9. [ESXi and NSX](#9-esxi-and-nsx)
10. [Management client history](#10-management-client-history)
11. [SSO and permissions](#11-sso-and-permissions)
12. [vSS, vDS, uplinks and port groups](#12-vss-vds-uplinks-and-port-groups)
13. [Management, storage and VM networks](#13-management-storage-and-vm-networks)
14. [Project examples and IT processes](#14-project-examples-and-it-processes)
15. [Revision and study order](#15-revision-and-study-order)
16. [References and source coverage](#16-references-and-source-coverage)

## 1. Certifications and support levels

The teacher relates certifications to company support levels:

| Term | Meaning | Teacher's likely point |
|---|---|---|
| VCTA | VMware Certified Technical Associate | Introductory concepts and basic knowledge |
| VCP | VMware Certified Professional | Administration and professional practical skills |
| VCAP | VMware Certified Advanced Professional | Advanced deployment/design knowledge |
| SME | Subject Matter Expert | An experienced specialist in a technology |

**L1-L5 are company support levels**, not an official certification-to-job mapping. Earning a certificate does not automatically make someone an L2, L3 or SME. Experience, responsibilities and company structure matter. Also distinguish historical certification names from current availability.

### DCV, VVF and VCF

| Term | Focus |
|---|---|
| DCV - Data Center Virtualization | Traditional vSphere virtualization administration |
| VVF - VMware vSphere Foundation | Administration of a vSphere Foundation environment |
| VCF - VMware Cloud Foundation | A broader private-cloud stack: compute, storage, networking and lifecycle management |

“DCV older / VCF new” appears to be a short reminder about certification direction. VCF is not only storage/vSAN. Broadcom's certification FAQ describes VVF and VCF as separate role-based certifications; VCF covers broader components such as NSX, vSAN and SDDC Manager [R1].

**Mismatch in the notes:** the supplied link points to `vcp-vvf-administrator`, but the text beside it says `VCP-VCF 16.25`. VVF and VCF are not the same certification. The meaning of `16.25` is unclear from the supplied text; do not treat it as a verified exam code. Before booking an exam, verify the current official objectives and correct certification page. Do not automatically assume that older tracks listed in the notes remain available.

## 2. VMware product relationships

The teacher's core map:

| Component | Function | Example |
|---|---|---|
| ESXi | Virtualizes compute resources and runs VMs | Windows Server and Linux VMs |
| vCenter Server | Centrally manages multiple hosts | Hosts, clusters, permissions and inventory |
| vSAN | Organizes supported host storage into software-defined storage | Storage resources across a cluster |
| NSX | Provides software-defined networking/security | Logical networking, routing and distributed firewall |

**vCSA** means vCenter Server Appliance: deploying vCenter in appliance form. **vSphere** is the platform name; ESXi and vCenter are important components. Do not interpret “vSphere Suite” as just one VM or a hypervisor binary.

Your current practice is on ESXi and the Host Client. Installing ESXi alone does not automatically deploy vCenter, vSAN or NSX.

## 3. ESXi installation requirements

The teacher's original reminder:

```text
CPU: 2 cores
Memory: 4 GB minimum / 8 GB recommended
Storage: 250 GB
Network: 1 NIC
Installer: Hypervisor ISO
```

| Item | Correct context |
|---|---|
| 2 CPU cores | A basic lab allocation; actual CPU compatibility and features are also required. |
| 4 GB minimum RAM | Outdated for ESXi 8. The corrected minimum boot memory is 8 GB [R4]. |
| 250 GB disk | Your lab's selected capacity, not a universal installation minimum. |
| 1 NIC | A compatible interface for basic connectivity; production redundancy is designed separately. |
| ISO | A bootable installer image. |

Broadcom specifies at least 32 GB of persistent boot storage for ESXi 8 [R5]. Do not treat boot storage, system partitions and the VM datastore as the same capacity calculation.

In your lab, the ESXi VM was allocated 8 GiB of RAM. ESXi overhead and nested guests must fit within that allocation; the outer Windows system also needs RAM. Minimum boot RAM is not production workload sizing.

## 4. ESXi, VMvisor, ESX and Host

| Term | Meaning |
|---|---|
| ESXi | Hypervisor product |
| VMvisor | Historical name associated with the ESXi architecture |
| ESX | Historical name of the classic product; also used in newer product naming |
| Host | System that provides virtualization resources |

Example: “HOST01 is an ESXi host.” HOST01 is the hostname; ESXi is the hypervisor.

The teacher's `ESX 1-3 / ESXi 4-9` line is oversimplified. Classic ESX and ESXi coexisted during the version 4.x period. Understand the difference between classic ESX's Linux-based Service Console and the ESXi architecture rather than memorizing a strict version boundary. New ESX 9 branding does not imply that the old Service Console has returned.

## 5. Server types and preparation

| Server type | Explanation |
|---|---|
| Tower | Standalone server chassis resembling a desktop tower |
| Rack | Server mounted in a standard rack |
| Blade | Modular server that fits inside a shared enclosure |

RAID, RMM, firmware and hypervisor installation are listed under rack servers. These are server-preparation topics.

### RAID

Organizing multiple disks for performance, redundancy or both. Different RAID levels have different trade-offs. **RAID is not a backup**: recoverable backups are still needed for deletion, ransomware and other failures.

### RMM

The exact expansion is not given in the supplied notes. In this server context, the teacher likely means remote management, such as iDRAC, iLO or BMC/IPMI. RMM can also mean Remote Monitoring and Management in other contexts, so the intended term can be confirmed with the teacher.

Server remote management can provide console, power and hardware-health access; appropriate out-of-band management remains useful when the OS is unavailable.

### Firmware

Low-level software for hardware: BIOS/UEFI, RAID controller firmware, NIC firmware and BMC firmware. Plan hypervisor installation after checking firmware/driver compatibility.

### Hypervisor installation

Install ESXi after verifying the target disk, CPU, memory, storage/NIC compatibility and installation image. Do not apply assumptions from a new empty lab VM to a server containing existing data.

## 6. Legacy CPU boot option

Original reminder:

```text
allowLegacyCPU=True
```

A related option was used in your old desktop CPU lab:

```text
allowLegacyCPU=true
```

In some lab situations, this is used to bypass a legacy CPU check. **A bypass does not make the CPU supported** and cannot create missing CPU instructions. Installer boot, installed-host boot, management login and nested guest power-on are separate verification stages.

Do not treat this as a normal production recommendation or a fix for every CPU error. This document is not instructing you to change boot options again.

## 7. Hardware virtualization and software

| Layer | Example | Role |
|---|---|---|
| Hardware capability | Intel VT-x | CPU virtualization support |
| Hypervisor software | ESXi / Workstation | Uses hardware capability to run VMs |

Enabling virtualization in firmware makes the capability available; hypervisor software uses it. Hardware capability and the running state of the Windows hypervisor/VBS are separate concepts.

`Palo Alto - R&D - Intel + VMware` is an incomplete reminder. It does not confidently establish a specific partnership, research history or technical architecture. The clear learning point is the relationship between hardware virtualization support and the software hypervisor. Treat VMware Player as a historical desktop virtualization example, not a claim about current availability.

## 8. Test, Production, DR, RPO and RTO

| Environment | Purpose |
|---|---|
| Test | Verify changes, updates and experiments |
| Production | Run services for real users and the business |
| DR location | An alternate location for recovering services/data after a major failure |

**24x7** means that service may be continuously required. Planned maintenance still takes place; it needs an availability and communication plan.

### RPO - Recovery Point Objective

The acceptable data-loss window.

```text
Failure:                  3:00 PM
Latest recoverable data:  2:00 PM
Potential lost changes:   1 hour
```

**A 1-hour RPO does not mean that the service must be restored within one hour.** If the business accepts a maximum of one hour of data loss, the recovery design should meet that objective. Evidence of the actual recoverable point comes from backup/replication results.

### RTO - Recovery Time Objective

How quickly the service should be restored.

| Term | Question |
|---|---|
| RPO | How much recent data is acceptable to lose? |
| RTO | How long should it take to restore service? |

An alternate DR location alone does not meet these objectives. Replication/backups, a failover process and recovery testing are needed.

## 9. ESXi and NSX

The teacher's comparison:

```text
ESXi: server/compute virtualization
NSX: network virtualization
```

ESXi manages VMs and compute resources. NSX provides software-defined networking/security. “Network hypervisor” is conceptual shorthand; do not treat NSX as a hypervisor that runs guest operating systems in the same way as ESXi.

A logical switch does not necessarily need to be a separate VM resembling a physical switch. Some functions are distributed and others are implemented through appliance-based components. Distinguish logical switching/routing functions from management appliances.

## 10. Management client history

| Interface | Meaning |
|---|---|
| Legacy Windows/C# vSphere Client | Older installed desktop client |
| Flash-based vSphere Web Client | Older browser interface |
| HTML5 vSphere Client | Modern vCenter browser interface |
| ESXi Host Client | Browser interface for an individual ESXi host |

Do not treat the teacher's `vSphere Client - 6.5` and `Web Client - 6.7-9.1` lines as exact historical boundaries. The Flash Web Client is unavailable in vSphere 7.0 and later [R2]. Calling a modern browser interface a generic “web client” is different from using the historical Flash product name.

Your example:

```text
https://10.0.0.11
```

This is the desktop ESXi 8 **Host Client**, not vCenter. Installing the old Windows C# client is not a prerequisite for managing ESXi 8/9.

## 11. SSO and permissions

**SSO - Single Sign-On:** an authentication arrangement that reduces repeated logins between supported applications/services.

| Concept | Question |
|---|---|
| Authentication | Who are you? |
| Authorization | What are you permitted to do? |

A user can authenticate to vCenter but lack permission on a host/VM. Successful login does not automatically provide administrator rights.

Your command:

```powershell
ssh desktop-esxi8
```

Uses SSH key authentication. Do not call it vCenter SSO. A local ESXi account, a vCenter identity and an SSH client alias are separate concepts.

## 12. vSS, vDS, uplinks and port groups

### vSS - vSphere Standard Switch

A virtual switch configured on an individual ESXi host. Each host's standard-switch configuration is managed separately.

### vDS - vSphere Distributed Switch

A centrally managed distributed-switch configuration through vCenter that helps maintain consistent networking across participating hosts. **vCenter does not become the packet-forwarding device**; forwarding takes place in host-side networking.

### Physical NIC and uplink

An interface leading to the physical network. ESXi shows identifiers such as `vmnic0` and `vmnic1`. Your nested ESXi receives a Workstation-presented virtual NIC; ESXi treats it as its network device.

### Port group

A group for applying common networking policies to related connections, such as VLAN/security settings.

| Connection | What connects? | Where is the IP address? |
|---|---|---|
| VM port group | A VM's virtual network adapter | Normally inside the guest OS |
| VMkernel adapter connection | An ESXi host networking service | On a VMkernel adapter such as `vmk0` |

**Correction:** The IP is assigned to the VMkernel adapter, not to the port group itself. The teacher's “VMkernel port group - IP is must” likely refers to the VMkernel networking interface.

Example: `vmk0` is assigned a management IP for the management service. A guest VM communicates through its own virtual NIC and guest OS IP. Guests do not all need to share the host's management IP.

### vSwitch0

Management networking is often on `vSwitch0` after a default installation. The name does not mean “reserved only for management.” The same switch can have both management and VM port groups.

### NSX and vDS

The teacher's `NSX - vDS` note introduces a relationship. vDS and NSX are not the same product; creating a vDS alone does not deploy NSX. Detailed integration depends on the exact NSX/vSphere versions and design.

## 13. Management, storage and VM networks

The teacher's example design:

| Purpose | Network | Mask | Example gateway |
|---|---|---|---|
| Core / management | 192.168.0.0/24 | 255.255.255.0 | 192.168.0.20 |
| Storage | 172.16.0.0/16 | 255.255.0.0 | 172.16.0.20 |
| Virtual machines | 10.0.0.0/8 | 255.0.0.0 | 10.0.0.20 |

These are example ranges, not mandatory VMware ranges. The `.0` network addresses in this table identify subnets; choose individual device IPs separately.

### Why separate the networks?

- Management access control and organization.
- Storage bandwidth/policies and troubleshooting.
- Separately designing guest workload connectivity.
- Improving security boundaries and traffic visibility.

**Different IP ranges alone do not create VLAN isolation.** Switching, VLANs, routing and firewall/access policies must also be configured.

### Why use .20 as the gateway?

A gateway does not have to end in `.1`. `.20` is valid if an actual router interface is configured at that address. Writing an address in the notes does not create a router.

Direct communication within the same subnet normally does not use the default gateway. Traffic to different networks requires suitable routes/a router. A storage network does not require a gateway in every design.

### NAS, SAN and vSAN

| Term | Explanation |
|---|---|
| NAS | File-based network storage, such as NFS |
| SAN | Block-storage infrastructure, such as iSCSI or Fibre Channel |
| vSAN | Software-defined storage using supported host storage resources |

An IP storage network can be relevant to NFS, iSCSI and vSAN. A Fibre Channel SAN does not depend on an ordinary IP subnet. Therefore, do not treat a “storage subnet” as a universal requirement for every SAN protocol.

### Comparison with your home lab

The teacher's `10.0.0.0/8` example and your home LAN's `10.0.0.x/24` do not have the same subnet size. Do not assume the mask by looking only at an address. Your supplied home outputs showed `255.255.255.0`. Do not apply the teacher's example networks to your running lab without planning.

## 14. Project examples and IT processes

The teacher's original classroom examples:

| Organization label | Numbers in the notes |
|---|---|
| Google | 2000 servers |
| Microsoft | 250 physical / 1200 virtual (VMware) / 950 cloud (Azure) |

These are **unverified classroom examples**, not the companies' actual/current inventories. Their likely purpose is to explain infrastructure categories.

| Category | Example |
|---|---|
| Physical servers | Hardware machines |
| Virtual servers | VMs on a hypervisor |
| Cloud servers | Cloud-hosted instances/VMs |
| Operating systems | Windows / Linux, including Ubuntu |
| Networking | Router, switch, load balancer, firewall |
| Operations/security | Monitoring, antivirus and access controls |

A cloud server can also be a VM. Do not treat physical, virtual and cloud as mutually exclusive categories without context; inventory classification must be defined.

| Term | Purpose |
|---|---|
| ITIL | IT service management practices |
| Agile | An iterative planning and delivery approach |
| PMP | Project Management Professional certification |

These are not hypervisors; they relate to organizing, managing and delivering infrastructure work.

## 15. Revision and study order

Suggested sequence:

1. The relationship between ESXi and vCenter.
2. vSS, uplinks and port groups.
3. The VMkernel adapter and its management IP.
4. Management/storage/guest networks, subnet masks and routing.
5. SSO, authentication and permissions.
6. DR, RPO and RTO.

### Quick revision

| Question | Answer |
|---|---|
| What does ESXi do? | Runs VMs and provides compute virtualization. |
| What does vCenter do? | Central management. |
| What is vSAN's domain? | Software-defined storage. |
| What is NSX's domain? | Software-defined networking/security. |
| What is the difference between vmnic and vmk? | A network uplink device identifier versus a VMkernel network interface. |
| Does a port group have its own management IP? | No; the IP is on the relevant VMkernel adapter/guest interface. |
| Are SSO and an SSH alias the same? | No. |
| What does a 1-hour RPO mean? | An acceptable data-loss window, not recovery completion time. |
| Is RAID a backup? | No. |
| Does a legacy CPU bypass establish support? | No. |

## 16. References and source coverage

| Uploaded file | Covered topics |
|---|---|
| Day2(1).txt | Certification shorthand, ESXi requirements, host terminology, server types, RAID/RMM/firmware, legacy CPU option, hardware/software virtualization |
| Day4.txt | Test/production/DR, RPO, ESXi/NSX, history shorthand, edition labels |
| Day5(1).txt | Management clients, SSO, vSS/vDS, port groups, VMkernel and example networks |
| VMware(2).txt | Certification links/levels, classroom project examples, infrastructure categories, ITIL/Agile/PMP |

Treat Day4's **Standard, Enterprise, Enterprise Plus, Tanzu** labels as historical/product shorthand. Do not automatically call Tanzu an interchangeable ESXi edition. Without verifying current packaging/entitlements, this list is not a purchasing guide.

Primary references used for technical context:

- **[R1] Broadcom certification FAQ - VVF/VCF distinction:** https://docs.broadcom.com/doc/vmw-certification-faqs
- **[R2] Broadcom KB 422681 - Flash Web Client unavailable in 7.0 and later:** https://knowledge.broadcom.com/external/article/422681/nec-istorage-plugin-deploy-task-hangs-at.html
- **[R3] Official certification pages supplied in the lecture:** https://www.broadcom.com/support/education/vmware/certification and https://www.broadcom.com/support/education/vmware/certification/vcp-vvf-administrator
- **[R4] ESXi 8 release notes - minimum boot memory:** https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/8-0/release-notes/vmware-vsphere-80-release-notes.html
- **[R5] Broadcom KB 313156 - minimum boot-device guidance:** https://knowledge.broadcom.com/external/article/313156/guidance-on-minimum-boot-device-size-req.html

The official certification web pages provided limited readable text during review; the unexplained `16.25` entry was not verified. No certification registration, host configuration or networking changes were performed while preparing these notes.
