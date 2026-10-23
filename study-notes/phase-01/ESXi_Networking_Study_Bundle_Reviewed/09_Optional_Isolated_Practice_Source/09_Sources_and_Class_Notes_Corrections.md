# 09 - Sources and Clarifications

> **Addressing correction:** Existing STORAGE was recorded as 172.16.0.102/16. The proposed 172.16.50.0/24 falls inside that /16, so it overlaps the existing connected network. Treat all 172.16.50.x values below as an unexecuted source example. Before implementation choose a verified unused, non-overlapping subnet and consistently replace every endpoint/interface value. Do not change the existing management or storage addresses merely to follow this example.


## Attached class PDFs used

| File | Pages used | Contribution |
|---|---|---|
| 06 ESXi Network Part-I.pdf | 2-5 | Standard switches, pNICs, groups and VLANs |
| 06 ESXi Network Part-II.pdf | 3-7, 9 | Teaming, failover states and shaping |
| 06 VMware ESXi Network Lab.pdf | 1 | Teacher network topology |

Original copies are under class_reference. The other attached class PDFs were inventoried; they concern setup, introductions, storage, AD, DNS and other subjects outside the six requested networking topics.

## Screenshot provenance

- user_physical_adapters.png: your desktop-esxi9 physical-adapter view uploaded as image(20261020-010501).png.
- user_vmk0.png: your actual management IPv4 settings uploaded as image(20261020-010900).png.
- teacher_observed_ranges.png: teacher screenshot uploaded as image(20261020-010433).png.
- class_network_topology.png: rendered page 1 of the attached class network lab PDF.

These images are references from the supplied material. They are not screenshots of the new practice switch or successful tests. Earlier screenshots outside the available files have not been reconstructed or represented as included.

## Corrections to read alongside the class slides

| Slide wording / possible interpretation | Clarification |
|---|---|
| Originating port called physical-layer load balancing | Selection is based on virtual switch port ID; avoid teaching it as an OSI-layer algorithm |
| IP hash described as first IP failing then second coming up | IP hash selects an uplink using source/destination addresses; it is not sequential IP failover |
| Explicit failover described as application layer / DRS | It prioritizes eligible uplinks and does not perform application-aware load balancing |
| Unused described as not used at corporate level | Unused is a per-policy exclusion and can be intentional in enterprise configurations |
| All failures detected within a fixed 3-10 seconds | Detection/recovery varies; link-state and upstream failures are different cases |
| Burst bandwidth described as remaining bandwidth | Burst size is a data budget, not a rate or a remaining-bandwidth counter |
| Average for ping, peak for HTTP/AD | These rate settings are not application-specific categories |
| Every port group automatically communicates | VLAN, subnet, security and topology determine actual connectivity |

Roman Urdu: Teacher ki slides learning foundation hain. Upar technical meanings clarify kiye gaye hain taa-ke interview ya real lab mein ghalat mental model na bane.

## Official supporting references

Accessed 20 October 2026 UTC. UI labels may differ between the class ESXi 8 material and your ESXi 9 Host Client.

- [Broadcom: How to configure NIC teaming](https://knowledge.broadcom.com/external/article?legacyId=1004088) - uplink membership, policies and configuration.
- [Broadcom: Understanding IP hash load balancing](https://knowledge.broadcom.com/external/article/321396/understanding-ip-hash-load-balancing.html) - policy behavior.
- [Broadcom: Bandwidth limitation through traffic shaping](https://knowledge.broadcom.com/external/article/429833/limiting-bandwidth-for-vsphere-replicati.html) - UI rate and burst units.
- [Broadcom API: HostNetworkTrafficShapingPolicy](https://developer.broadcom.com/xapis/virtual-infrastructure-json-api/latest/data-structures/HostNetworkTrafficShapingPolicy/) - API units differ from UI units.
- [Broadcom: Observed IP ranges on healthy uplinks](https://knowledge.broadcom.com/external/article/433047/observed-ip-ranges-shows-no-networks-for.html) - observed range is not configured host IP.
- [Broadcom: New-VMHostNetworkAdapter](https://developer.broadcom.com/powercli/latest/vmware.vimautomation.core/commands/new-vmhostnetworkadapter/) - VMkernel/port group/switch association.

## Scope

This bundle teaches standard-switch host networking. It does not configure NFS/iSCSI targets, vMotion migration, distributed switches, LACP, routing or production VLAN infrastructure. Those require separate labs and prerequisites. The example 172.16.50.x network is newly proposed, not a claim about your existing configuration.
