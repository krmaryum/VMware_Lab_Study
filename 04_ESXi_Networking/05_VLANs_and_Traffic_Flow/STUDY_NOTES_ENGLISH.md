# Chapter 05 — VLANs and Traffic Flow

## Concept and lab connection

VLANs separate Layer-2 segments; subnets describe Layer-3 addressing. Port-group names are administrative labels. A different label or a different IP subnet does not, by itself, prove secure isolation. Upstream switching, outer Workstation mapping and routing determine the actual path.

`SERVERS` and `STORAGE` show None (0) in their captured settings. This is an untagged design for those groups, not a demonstration of tagged VLAN isolation. The initial topology’s `--` display must not be used to infer every other group’s configured VLAN value.

For a name-based connection, first resolve the name; then reach its IP through the network; then connect to the service. These are different stages. A working DNS answer cannot fix a wrong VLAN, and `tracert` does not list every Layer-2 switch.

**Checkpoint:** Same-host switching may never use the uplink. A test intended to prove uplink behavior needs an external endpoint.

> **Safety:** Viewing pages and using read-only commands is safe. Changing management uplinks, port groups, VLANs, or vmk0 IP may cut off remote access. Have ESXi console access and a rollback plan before changes.

## Learning objectives
Trace guest-to-guest, guest-to-LAN and desktop-to-ESXi management traffic; distinguish MAC forwarding, IP routing, and VLAN tagging.

## Three network journeys
**A. Same ESXi, same port group/VLAN:** VM-A frame → its vNIC → port group → vSwitch → VM-B vNIC. Typically no external uplink needed.

**B. VM to external LAN:** VM vNIC → VM Network → vSwitch0 → vmnic0 → Workstation networking (if nested) → external network. Where routing happens depends on destination subnet.

**C. Windows desktop to host management:** Windows client → network → ESXi management uplink → vSwitch0 → Management Network → vmk0 `10.0.0.10` → ESXi HTTPS service.

## Switching vs routing vs DNS
| Mechanism | Question it answers | Example |
|---|---|---|
| DNS / hosts file | What IP corresponds to this name? | `desktop-esxi9` → `10.0.0.10` (mapping to verify locally) |
| Layer-2 switching | Which Ethernet port should receive this frame? | vSwitch forwards using MAC-to-port information |
| Layer-3 routing | How do I reach another IP subnet? | Host/default gateway forwards off-subnet packets |
| VLAN | What logical Layer-2 segment carries this traffic? | VLAN 20, hypothetical |

## Practical scenario (hypothetical)
Management on VLAN 10 and guests on VLAN 20 need a correctly configured port group and compatible upstream switching. Moving management into an unsupported VLAN can strand ESXi.

## Commands from the Windows desktop
```powershell
ping 10.0.0.10
Test-NetConnection 10.0.0.10 -Port 443
tracert 10.0.0.10
```
**Why and how to interpret:** `ping` checks ICMP reply (failure does not always mean offline); `Test-NetConnection` checks the HTTPS TCP port; `tracert` helps explore Layer-3 hops but switches generally do not appear as routed hops. These outputs have **not** been collected yet for ESXi9.


## Questions
1. Does `tracert` show every vSwitch and physical switch?
2. When is routing needed?
3. Can same-host VMs communicate without an uplink?


---
## Teacher's PDF page 5 — VLANs versus this lab
![Teacher VLAN slide](teacher_pdf_pages/teacher-page-05.png)
The teacher explains VLANs as logical traffic segmentation and VLAN tagging through ESXi port groups/uplinks. **That is the concept, not a VLAN isolation test completed in our lab.**

### Current observed paths (conceptual)
The conceptual path is: endpoint interface → port group → standard switch → suitable uplink when the destination is external. The screenshots establish the captured objects; the outer Workstation mapping requires separate verification.
Do not assume `vSwitch1`'s storage network is physically isolated solely because it has a different subnet. The VMware Workstation network modes and external path still need checking.

**Key distinction:** VLAN tagging is Layer 2 segmentation. IP subnetting is Layer 3 addressing. They are often used together, but neither proves end-to-end isolation without testing.
