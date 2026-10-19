# Chapter 05 — VLANs and Traffic Flow

> **Lab baseline (confirmed from screenshot):** `desktop-esxi9`, `vSwitch0`, `vmnic0` (reported 10000 Full), Management Network `vmk0` / `10.0.0.10`, VM Network with zero VMs, VLAN shown as `--`. This is a different host from `desktop-esxi8` / `10.0.0.11`. Do not assume that uninspected settings are configured as described in the examples.

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
| Layer-2 switching | Which Ethernet port should receive this frame? | vSwitch forwards based on MAC learning |
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

## Roman Urdu
Same VLAN par same ESXi ke VMs aksar vSwitch ke andar hi communicate kar sakti hain. Doosre network ke liye router/default gateway chahiye hota hai. DNS sirf naam se IP dhoondta hai; switching traffic ko MAC ki bunyaad par forward karti hai.

## Questions
1. Does `tracert` show every vSwitch and physical switch?
2. When is routing needed?
3. Can same-host VMs communicate without an uplink?
