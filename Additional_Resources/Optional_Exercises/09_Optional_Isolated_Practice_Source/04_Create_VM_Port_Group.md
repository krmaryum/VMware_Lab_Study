# 04 - How to Create a Virtual Machine Port Group

> **Addressing correction:** Existing STORAGE was recorded as 172.16.0.102/16. The proposed 172.16.50.0/24 falls inside that /16, so it overlaps the existing connected network. Treat all 172.16.50.x values below as an unexecuted source example. Before implementation choose a verified unused, non-overlapping subnet and consistently replace every endpoint/interface value. Do not change the existing management or storage addresses merely to follow this example.


## Study notes

A VM port group is a connection point for guest vNICs. The guest's IP address is configured inside its operating system or by DHCP, not on the port group. Do not attach a guest to the VMkernel port group; use a separate VM port group.

Roman Urdu: VM port group guest machines ke network cards ko connect karta hai. Guest ka IP uske OS mein set hota hai. Port group ka naam IP address ya DHCP server nahi banata.

## Task A - Create PG-Lab-VMs

1. Open Networking -> Port groups -> Add port group.
2. Name: PG-Lab-VMs.
3. Virtual switch: vSwitch-LabNet.
4. VLAN ID: 0.
5. Save.
6. Verify this group and PG-Lab-VMkernel both belong to vSwitch-LabNet.

Both use VLAN 0, so they share the untagged Layer 2 network in this lab. To create actual segmentation, a later exercise needs distinct VLANs and compatible upstream tagging, or separate isolated switches/networks.

## Task B - Attach a guest VM

1. Select a small existing Linux test VM inside ESXi. If none exists, create a small guest and install Linux before continuing; OS installation is a prerequisite rather than a completed step in this bundle.
2. Open Virtual machines -> VM-A -> Edit settings.
3. Add a Network Adapter if required, or select its dedicated lab adapter.
4. Network / Port group: PG-Lab-VMs.
5. Enable Connected and Connect at power on, where shown.
6. Save and power on the guest.
7. In the guest OS set the lab interface to 172.16.50.20/24, without a gateway for this isolated subnet.
8. Verify:

```bash
ip -br address
ping -c 4 172.16.50.10
ping -c 4 172.16.50.30
```

Roman Urdu: VM-A ka IP .20, ESXi lab vmk ka .10 aur bahar test endpoint ka .30 hai. Pehle host vmk aur phir external endpoint ping karein.

## Expected result

- VM-A's lab adapter shows PG-Lab-VMs.
- Guest IP is .20/24.
- Both pings receive replies, subject to peer ICMP firewall settings.
- Capture port-group properties, VM adapter assignment and guest ping output.

## Nested lab considerations

If .10 responds but .30 does not, check the outer Workstation path. Guest MAC forwarding through a nested hypervisor can require additional configuration depending on the outer virtualization/network setup. Do not blindly enable every security option on ESXi; first distinguish host-local connectivity from the external path. Workstation LAN segments are on one Workstation host, not automatically stretched across separate PCs.

## Revision

Changing a guest's port group changes its network attachment. It does not automatically change a manually configured guest IP. Keep its IP subnet compatible with its new network.
