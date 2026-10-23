# 00 - Preparation and Network Plan

## Your verified starting point

Your screenshots show desktop-esxi9, vmk0 10.0.0.10/24, gateway 10.0.0.1, DNS 75.75.75.75 and 75.75.76.76. The physical-adapter screenshot shows vmnic0/vmnic1 on vSwitch0, vmnic2/vmnic3 on vSwitch1, and vmnic4-vmnic7 unassigned at that moment. vmk1 already exists. Current assignments must be checked before changing anything.

Roman Urdu: Purane screenshot mein yeh configuration thi. Abhi ki screen dobara check karein. Management ke vmnic0/vmnic1, vSwitch0 aur vmk0 ko is practice mein change nahi karna.

![Actual uplink starting state](assets/user_physical_adapters.png)
![Actual management IPv4 configuration](assets/user_vmk0.png)

## Separate exercise values

| Object | Proposed value |
|---|---|
| Standard switch | vSwitch-LabNet |
| First uplink | vmnic4, only if unused now |
| Second uplink | vmnic5, only if unused now |
| VMkernel port group | PG-Lab-VMkernel |
| VMkernel IPv4 | 172.16.50.10/24 |
| Guest VM port group | PG-Lab-VMs |
| Nested guest VM-A | 172.16.50.20/24 |
| External test endpoint | 172.16.50.30/24 |
| VLAN ID on both groups | 0 (untagged) |
| MTU | 1500 throughout |
| Gateway for isolated test | None on test guests; preserve ESXi's existing default gateway |

These are proposed values, not IPs copied from your teacher. Verify 172.16.50.0/24 is unused and non-overlapping. If it is already used, choose another unused subnet and consistently replace all example addresses.

Roman Urdu: Naye IPs practice ke liye hain. Teacher ka 192.168.0.x copy karna zaroori nahi. Dono port groups VLAN 0 par honge, is liye woh isolated from each other nahi hain. Alag names security separation nahi dete.

## Required test environment

- ESXi Host Client access and the local Workstation console for the ESXi VM.
- Two currently unused ESXi adapters; one works for chapters 1-4, two are required for chapter 5 onward.
- One small Linux guest inside ESXi for VM port-group and shaping tests.
- One separate Linux test endpoint directly in Workstation, on the same lab network. Use existing lightweight guests where possible; your desktop RAM is limited.
- iperf3 on both Linux test machines for the bandwidth lab. Install only in the guests, not ESXi.

If nested guests cannot boot on your hardware, you can complete switch/VMkernel configuration and host ping tests, but mark VM and shaping performance tests as pending.

## Task 0.1 - Record configuration

1. Open https://10.0.0.10/ui/ and sign in.
2. Capture Networking -> Virtual switches, Physical adapters, Port groups and VMkernel adapters.
3. Confirm vmnic4 and vmnic5 show no switch assignment.
4. Write down their full MAC addresses, link status, and current Workstation networks.
5. Keep the ESXi VM console open in Workstation.

## Task 0.2 - Map Workstation adapters by MAC

1. In Workstation select the ESXi VM -> Settings.
2. Select each candidate Network Adapter and inspect Advanced -> MAC Address, where available.
3. Match its MAC to ESXi vmnic4 or vmnic5. Do not assume Network Adapter 5 always means vmnic4.
4. Use an isolated network named LAB-ESXI-NET. A Workstation LAN Segment is suitable for VMs on the same Workstation host.
5. Connect both mapped ESXi adapters and the external test endpoint to that same segment.
6. Enable Connected and Connect at power on. If LAN segment creation needs the VM powered off, shut ESXi down cleanly first, change the settings and boot it again.

Roman Urdu: Dono uplinks ek hi logical network tak pohanchne chahiye. Do unrelated NAT ya host-only networks ko ek team mein na milayein. Surface is isolated segment ka member automatically nahi hai; management browser apne 10.0.0.x network par rahega.

| ESXi adapter | Full MAC | Workstation adapter | Workstation network |
|---|---|---|---|
| vmnic4 | Record actual | Record actual | LAB-ESXI-NET |
| vmnic5 | Record actual | Record actual | LAB-ESXI-NET |

## Expected result

You have verified unused uplinks, a non-overlapping subnet, and a separate test network. The management page at 10.0.0.10 still opens.
