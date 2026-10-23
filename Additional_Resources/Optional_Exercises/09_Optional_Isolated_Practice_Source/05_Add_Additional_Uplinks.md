# 05 - How to Add Additional Uplinks

> **Addressing correction:** Existing STORAGE was recorded as 172.16.0.102/16. The proposed 172.16.50.0/24 falls inside that /16, so it overlaps the existing connected network. Treat all 172.16.50.x values below as an unexecuted source example. Before implementation choose a verified unused, non-overlapping subnet and consistently replace every endpoint/interface value. Do not change the existing management or storage addresses merely to follow this example.


This chapter extends chapter 02: the first uplink provides one path; the second makes teaming practice possible.

## Task - Add vmnic5 to the same switch

1. Confirm vmnic5 is currently unused and record its MAC.
2. Match that MAC to the ESXi VM's Workstation adapter.
3. Attach it to the same LAB-ESXI-NET segment as vmnic4.
4. Confirm Connected and Connect at power on.
5. In ESXi open Networking -> Virtual switches -> vSwitch-LabNet -> Edit settings.
6. Add uplink -> select vmnic5.
7. Keep vmnic4 attached.
8. Save and inspect the switch summary.
9. Open Teaming and failover and record whether the UI placed the new adapter in Active, Standby or Unused. Do not assume its default position.

Roman Urdu: Doosra adapter add karna aur usay Active banana alag checks hain. Dono uplinks same network/VLAN tak pohanchne chahiye, warna failover ke baad connection toot sakta hai.

## Expected result

| Adapter | Switch | Outer network |
|---|---|---|
| vmnic4 | vSwitch-LabNet | LAB-ESXI-NET |
| vmnic5 | vSwitch-LabNet | LAB-ESXI-NET |

Capture both uplinks in one screenshot, then repeat VM-A -> 172.16.50.30 ping.

## Important distinction

Two virtual uplinks can demonstrate ESXi policy and failover behavior. If both ultimately rely on the same desktop physical NIC, Windows host and router, they do not provide independent hardware redundancy or double physical network speed.

Roman Urdu: Lab mein do virtual wires hain, lekin desktop ya uska physical NIC fail ho to dono paths band ho sakte hain. Real production redundancy ke liye independent physical paths plan kiye jate hain.

## If a third adapter is available

You may add another verified unused adapter to the same network later using the same method. This lab only needs vmnic4 and vmnic5; keep unused candidates unassigned rather than moving management uplinks.
