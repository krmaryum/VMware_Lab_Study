# ESXi 8 and ESX/ESXi 9 - Combined Service Table

**Language:** English  
**Updated:** 16 October 2026  
**Purpose:** A combined study reference explaining services, why they are needed, and relevant version differences.

> **Available does not mean Running.** Actual service names and availability vary by release/build, vendor image, installed components and enabled features. This document is not a live inventory of your hosts. “Verify” means availability has not been established for your particular build; it does not mean the service is absent.

## Index

1. [Common management services](#1-common-management-services)
2. [Feature-specific and advanced services](#2-feature-specific-and-advanced-services)
3. [Legacy services and version changes](#3-legacy-services-and-version-changes)
4. [Which services to learn first](#4-which-services-to-learn-first)
5. [Checking services on your host](#5-checking-services-on-your-host)
6. [References](#6-references)

## 1. Common management services

| Service / identifier | ESXi 8 | ESX 9 | Description - what it does and why needed |
|---|---|---|---|
| **Host Management - `hostd`** | Available | Available | Handles host and VM management operations, including requests from management clients. Important for normal host administration. |
| **vCenter Agent - `vpxa`** | Available | Available | Connects host management with vCenter. Relevant when the host is managed by vCenter. |
| **Direct Console UI - DCUI** | Available | Available | Provides the local console for management network settings, troubleshooting and shutdown/restart operations. |
| **ESXi Shell - `TSM`** | Available | Available | Provides local command-line administration. Enable when local shell troubleshooting is needed. |
| **SSH - `TSM-SSH`** | Available | Available | Provides encrypted remote command-line access, normally over **TCP 22**. Your passwordless SSH aliases use this service. |
| **NTP Daemon - `ntpd`** | Available | Available | Synchronizes the host clock with upstream NTP servers using **UDP 123**. Accurate time helps authentication, logs and distributed operations. |
| **PTP Daemon - `ptpd`** | Configuration-dependent | Configuration-dependent | Provides precision time synchronization for workloads requiring tighter timing accuracy and compatible infrastructure. |
| **Syslog - `vmsyslogd`** | Available | Available | Processes host logs and supports forwarding them to a remote collector for troubleshooting and retention. |

### Host management relationship

`hostd` and `vpxa` run on the ESXi host. **`vpxd` runs on vCenter Server**, so it is not an ESXi host service. The vpxa agent communicates between vCenter's vpxd and the host's hostd [R1-R2].

A standalone host showing “not connected to any vCenter Server” is not necessarily unhealthy. It may simply not have been added to vCenter.

### Time service clarification

ESXi normally uses NTP as a **client**. Starting `ntpd` does not automatically turn the host into a time server for the LAN [R3]. A Running service does not prove successful synchronization; check the selected source and synchronization status separately. Choose the appropriate supported time synchronization method rather than trying to run NTP and PTP as competing active time sources.

## 2. Feature-specific and advanced services

Verify the presence and requirements of these services on the **exact host build**. They do not all need to run in your basic lab. Descriptions of advanced services are high-level learning guidance, not a complete deployment or compatibility matrix.

| Service | ESXi 8 applicability | ESX 9 applicability | Description - what it does and why needed |
|---|---|---|---|
| **SNMP Server** | Monitoring-dependent | Verify image/configuration | Supplies monitoring information and notifications to an SNMP monitoring system. It does not replace Syslog. |
| **PC/SC Smart Card Daemon** | Authentication-dependent | Verify image/configuration | Supports smart card reader/authentication integration. Not needed for ordinary SSH key authentication. |
| **Active Directory Service** | Domain integration | Verify supported integration | Supports directory-based authentication. Domain users still need appropriate host permissions. |
| **Load-Based Teaming Daemon - LBTD** | Networking-dependent | Verify image/features | Supports load-based NIC teaming in applicable distributed-switch configurations. It is not required for every teaming method. |
| **X.Org Server** | Graphics-mode-dependent | Graphics-mode-dependent | Supports applicable shared graphics configurations. It is not the service supplying the ordinary DCUI screen. |
| **GPU Manager** | GPU-dependent | GPU-dependent | Supports applicable GPU management workflows. Requirements depend on GPU hardware, driver and virtualization mode. |
| **VDTC** | Diagnostics-dependent | Verify build/features | Distributed tracing collection infrastructure used for diagnostic investigation. |
| **ENTROPYD** | Security infrastructure | Verify build | Supports cryptographic entropy/randomness infrastructure used by security operations. |
| **ATTESTD** | Trust/attestation-dependent | Verify trust configuration | Supports host attestation workflows that evaluate platform trust and measurements. |
| **KMXD** | Trusted infrastructure-dependent | Verify trust configuration | Supports key-management communication in applicable trusted infrastructure workflows. It is not needed simply to use SSH keys. |
| **VLTD** | Data-protection-dependent | Verify protection product support | Communication infrastructure associated with particular data-protection and recovery workflows. |
| **DPD** | Data-protection-dependent | Verify protection product support | Supports applicable data-protection operations, including LWD-related workflows. Not a universal requirement for every backup product. |
| **API Forwarder** | Build/feature-dependent | Verify build/features | Supports forwarding of applicable API requests. Do not treat it as another name for `hostd`. |
| **INFRAVISOR** | Relevant to embedded vCLS in applicable updates | Verify build/features | Manages embedded infrastructure workloads, including applicable vCLS pod workflows. |
| **HBRSRV** | Replication-dependent | Verify replication product support | Associated with host-based replication infrastructure. Not required for ordinary browser or SSH access. |
| **NVMF-DISCOVERYD** | Storage-dependent | Verify storage configuration | Supports NVMe over Fabrics discovery. An ordinary local virtual SCSI disk does not require this feature. |

### Graphics correction

Broadcom documents that NVIDIA vGPU mode does not require Xorg starting with vSphere 7.0 U1, including ESX 9.x. Applicable **vSGA/shared graphics** configurations can still require Xorg [R4]. Therefore, a stopped Xorg service is not automatically a vGPU fault.

### Infrastructure and data-protection context

INFRAVISOR is documented in embedded vCLS workflows [R5]. Its presence on a standalone host does not establish that vCLS is deployed. Broadcom documents DPD/VLTD together in particular recovery workflows [R8]; do not generalize this dependency to all backup methods or product versions.

## 3. Legacy services and version changes

| Service | ESXi 8 | ESX 9 | Description and change |
|---|---|---|---|
| **CIM Server / SFCB** | Present in applicable images; **deprecated** | **Removed** | Provided hardware health/management information through CIM providers. For version 9, use supported vendor monitoring alternatives. |
| **SLPD - Service Location Protocol** | **Deprecated** | Verify exact release inventory; removal was announced for the following major release | Legacy service discovery associated with locating management services. Do not enable it merely to match an old classroom list. |

Broadcom explicitly documents CIM/SFCB removal in ESX 9 [R6]. For SLP, the cited ESXi 8 U3 notice documents deprecation and planned removal [R7]. That notice alone should not be treated as proof of the exact service inventory on your particular version 9 build.

**Deprecated** means the feature is being phased out; it does not necessarily mean it is already removed in the release that announces deprecation. **Removed** means the component is no longer supplied in the stated release.

## 4. Which services to learn first

For your home lab, study these in order:

1. **DCUI:** local access to management networking and basic troubleshooting.
2. **SSH:** remote access through your Windows SSH aliases.
3. **ESXi Shell:** local command-line troubleshooting when needed.
4. **hostd:** host/VM management and associated logs.
5. **vpxa:** how a host communicates with vCenter when centrally managed.
6. **NTP:** correct host time and confirmation of actual synchronization.
7. **Syslog:** locating logs and understanding centralized log collection.

Learn feature-specific services when you reach the associated graphics, storage, security, replication or cluster lesson. Starting all services is not a useful baseline configuration.

## 5. Checking services on your host

### Browser method

Open **Host Client > Host > Manage > Services**. Record the service's display name, internal key where shown, current state and startup policy. Exact labels can vary by build.

| Item | Meaning |
|---|---|
| Running / Stopped | Current state of the service. |
| Start | Start the service now. |
| Stop | Stop the service now; associated access can be interrupted. |
| Restart | Stop and start the service; associated sessions can disconnect. |
| Startup policy | Determines when the service should start/stop, such as at host startup or according to use. |

### Read-only CLI checks

Run these **inside the ESXi shell**, not directly at a Windows PowerShell prompt:

```sh
vmware -vl
/etc/init.d/hostd status
/etc/init.d/SSH status
/etc/init.d/ntpd status
```

For a vCenter-agent check where the script is present:

```sh
/etc/init.d/vpxa status
```

If a script is absent, check the Services UI and build-specific documentation. Do not create a missing script or assume the host is corrupt. Service script names and UI service keys do not always match.

### Windows connectivity checks

For your desktop ESXi 8 host, when it is running:

```powershell
Test-NetConnection 10.0.0.11 -Port 443
Test-NetConnection 10.0.0.11 -Port 22
ssh desktop-esxi8
```

TCP 443 checks web-management reachability; TCP 22 checks SSH reachability. Neither verifies NTP over UDP 123. Ping uses ICMP and can fail while TCP access works. Use `exit` to leave the SSH session; it does not power off the host.

### Service inventory worksheet

| Host / build | Service name/key | Present? | Running state | Startup policy | Required feature | Evidence/date |
|---|---|---|---|---|---|---|
| Fill from your host | Fill from Services UI | Yes / No | Running / Stopped | Record exact policy | Explain the need | Record result |

**Evidence boundary:** No live service inventory was collected from your hosts while creating this document. Version-specific claims are separated from items that require verification.

## 6. References

Primary sources consulted for the preceding combined table, checked 16 October 2026:

- **[R1] Broadcom KB 405167 - vpxa / hostd / vpxd relationship:** https://knowledge.broadcom.com/external/article/405167/diagnostics-for-vmware-cloud-foundation.html
- **[R2] Broadcom management-agent guidance:** https://knowledge.broadcom.com/external/article/320280/
- **[R3] Broadcom KB 318545 - ESX/ESXi timekeeping best practices:** https://knowledge.broadcom.com/external/article/318545/esx-and-esxi-host-time-keeping-best-prac.html
- **[R4] Broadcom KB 318047 - Xorg and NVIDIA vGPU dependencies:** https://knowledge.broadcom.com/external/article/318047/xorg-service-is-no-longer-required-for-n.html
- **[R5] Broadcom KB 417364 - Infravisor / vCLS context:** https://knowledge.broadcom.com/external/article/417364/esxi-host-is-unable-to-enter-maintenance.html
- **[R6] Broadcom KB 454750 - CIM providers and removal in ESX 9:** https://knowledge.broadcom.com/external/article/454750
- **[R7] Broadcom KB 313159 - CIM/SLP deprecation in ESXi 8 U3:** https://knowledge.broadcom.com/external/article/313159/common-information-model-cim-and-service.html
- **[R8] Broadcom KB 313701 - DPD/VLTD recovery context:** https://knowledge.broadcom.com/external/article/313701/highfrequency-snapshot-fails-with-error.html

The feature-specific service descriptions are a learning reference, not a replacement for the exact release and feature deployment documentation.
