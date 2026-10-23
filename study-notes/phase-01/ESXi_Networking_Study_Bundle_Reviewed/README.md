# ESXi networking — structured theory and lab evidence

English and Roman Urdu • Reviewed edition

This bundle follows the same layout as the DNS notes: concept and purpose, connection to the actual lab, detailed recorded steps, screenshots, interpretation and verification. Open one language for each chapter. The core lab is **desktop-esxi9**, although the teacher’s networking handouts also discuss ESXi 8. Version-specific UI labels can differ.

## Read in this order / Is tarteeb se parhein

Virtual switches → Port groups → Adapters/uplinks → VMkernel/storage network → VLANs/traffic flow → Teaming → Shaping → Verification.

For each chapter, first read the concept, then compare the screenshot with the explanation. The recorded STORAGE/SERVERS lab and proposed isolated exercises are deliberately labelled. Existing procedures remain available for repeat practice; an expected result is not a captured output.

ZIP poora extract karein. Markdown preview ya GitHub mein notes kholein aur screenshots/teacher-page folders apni jagah par rakhein. Sirf Notepad mein images ke paths dikhte hain. English aur Roman Urdu aik hi learning route hain; dono aik saath parhna zaroori nahin.

| Topic | English notes | Roman Urdu notes |
|---|---|---|
| 01 Virtual Switches | [English](01_Virtual_Switches/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](01_Virtual_Switches/STUDY_NOTES_ROMAN_URDU.md) |
| 02 Port Groups | [English](02_Port_Groups/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](02_Port_Groups/STUDY_NOTES_ROMAN_URDU.md) |
| 03 Adapters and Uplinks | [English](03_Adapters_and_Uplinks/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](03_Adapters_and_Uplinks/STUDY_NOTES_ROMAN_URDU.md) |
| 04 VMkernel and Storage Network | [English](04_VMkernel_and_Storage_Network/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](04_VMkernel_and_Storage_Network/STUDY_NOTES_ROMAN_URDU.md) |
| 05 VLANs and Traffic Flow | [English](05_VLANs_and_Traffic_Flow/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](05_VLANs_and_Traffic_Flow/STUDY_NOTES_ROMAN_URDU.md) |
| 06 NIC Teaming and Failover | [English](06_NIC_Teaming_and_Failover/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](06_NIC_Teaming_and_Failover/STUDY_NOTES_ROMAN_URDU.md) |
| 07 Traffic Shaping | [English](07_Traffic_Shaping/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](07_Traffic_Shaping/STUDY_NOTES_ROMAN_URDU.md) |
| 08 Verification and Troubleshooting | [English](08_Verification_and_Troubleshooting/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](08_Verification_and_Troubleshooting/STUDY_NOTES_ROMAN_URDU.md) |

## Lab chronology — different checkpoints, not one live inventory

| Stage | Captured observation | What it establishes |
|---|---|---|
| Initial ESXi9 topology | vSwitch0, Management Network/vmk0 10.0.0.10, VM Network, vmnic0 | Original switch topology |
| Temporary second uplink | vmnic1 added Active with vmnic0, then removed | Membership changed; automatic failover untested |
| Storage setup | vSwitch1, STORAGE, vmk1 172.16.0.102/16, vmnic1 at that stage | Host storage-network objects configured; no datastore test |
| VM group | SERVERS on vSwitch0, None (0), zero VMs | Group created; guest connectivity untested |
| Later adapter inventory | vmnic0/1 on vSwitch0; vmnic2/3 on vSwitch1; vmnic4–7 unassigned | Later assignments differ; intervening reassignment steps not captured in Stage 1 |
| Later management screen | vmk0 10.0.0.10/24, gateway 10.0.0.1, DNS 75.75.75.75 / 75.75.76.76 | Configured values at that point, not live tests today |

The earlier notes repeatedly used the first screenshot as a baseline. This edition preserves that historical stage while showing the later inventory separately. The later adapter inventory does not confirm every other setting remained unchanged. No running ESXi system was accessed or modified during this review.

## Recorded results versus future work

| Topic | Recorded | Pending or unproven |
|---|---|---|
| Switch and port groups | Original topology, STORAGE and SERVERS creation | Guest connectivity after assignment |
| VMkernel | vmk0 management and vmk1 storage addresses | Storage endpoint ping, NFS/iSCSI session or datastore |
| Uplinks | Temporary dual-active plus later inventory | Controlled automatic failover and measured distribution |
| VLAN | None (0) shown for SERVERS/STORAGE | Tagged VLAN isolation or end-to-end isolation |
| Traffic shaping | Repeat procedure supplied | Applied policy and baseline/shaped/restored measurements |
| Previous ESXi8 connection | Earlier source records .11 tests separately | Not equivalent to a new .10 ESXi9 measurement |

## Optional isolated practice

The follow-up source proposes **vSwitch-LabNet**, PG-Lab-VMkernel, PG-Lab-VMs and 172.16.50.0/24. These are not the recorded STORAGE/SERVERS objects. Start with [preparation](09_Optional_Isolated_Practice_Source/00_Preparation_and_Network_Plan.md) before Chapters 06–07. It includes outer Workstation MAC mapping, endpoint setup and free-adapter checks.

A major planning issue: the recorded storage interface uses **172.16.0.102/16**, whose connected subnet includes **172.16.50.0/24**. The proposed exercise must therefore not be copied unchanged while that /16 interface is present. Confirm routes and choose an unused, non-overlapping subnet consistently for the exercise, or redesign the isolated lab deliberately. No replacement subnet is asserted as free without inspection.

Do not change management uplinks/VLAN/IP remotely without working local console recovery. The isolated practice is intended to avoid those changes. Two virtual uplinks on one physical desktop do not provide independent physical-hardware redundancy.

## Evidence and included material

- The 17 Stage 1 lab screenshots are retained; teacher PDF page images remain separately labelled.
- Later management and adapter screenshots are included with their own checkpoint explanations.
- Original class PDFs are preserved under class_reference. The optional practice source retains its own reference copies so its original links work.
- Each of the eight main topics has separate English and Roman Urdu notes. Updated individual chapter ZIPs are under separate_bundles.
- [Review notes](REVIEW_NOTES.md) explain the changes, limits and validation checks.

## Suggested next milestone

First collect a current read-only inventory of Workstation mappings, switches, adapters, VMkernel addresses and routes. Reconcile that inventory with the historical screenshots. Then choose a non-overlapping isolated exercise network. Capture real connectivity and failover results before attempting throughput shaping. Keep vMotion and storage-protocol/datastore configuration as separate later labs.

## Official references

- [Broadcom: Configure a standard vSwitch](https://knowledge.broadcom.com/external/article/326175/configuring-standard-vswitch-vss-or-virt.html)
- [Broadcom: Standard port-group VLAN configuration](https://knowledge.broadcom.com/external/article/311057/configuring-vlan-on-a-vswitch-standard-p.html)
- [Broadcom: NIC teaming](https://knowledge.broadcom.com/external/article?legacyId=1004088)
- [Broadcom: IP hash](https://knowledge.broadcom.com/external/article/321396/understanding-ip-hash-load-balancing.html)
- [Broadcom: Shaping example](https://knowledge.broadcom.com/external/article/429833/limiting-bandwidth-for-vsphere-replicati.html)
- [Broadcom API: Shaping units](https://developer.broadcom.com/xapis/vsphere-web-services-api/latest/vim.host.NetworkPolicy.TrafficShapingPolicy.html)

Class materials are preserved as the teaching source. Official references explain mechanisms; neither substitutes for actual test evidence on the home lab.
