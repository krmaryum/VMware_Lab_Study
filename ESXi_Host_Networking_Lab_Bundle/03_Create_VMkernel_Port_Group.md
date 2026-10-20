# 03 - How to Create a VMkernel Port Group and Adapter

## Study notes

The port group carries policy; the VMkernel adapter (vmkN) provides the ESXi host's network interface and IP. They are separate objects. Host functions include management, IP storage and vMotion. Enabling a service is different from merely creating the adapter. vMotion is a function using a VMkernel interface, not another name for VMkernel.

Roman Urdu: Port group policy deta hai, VMkernel adapter host ko IP deta hai. vmk0 management ke liye pehle se hai. Naya vmk banane se vMotion ya storage datastore automatically configure nahi hota.

Class reference: Network Part-I, page 4; class lab diagram, page 1.

## Task A - Add the host port group

1. Open Networking -> Port groups.
2. Click Add port group.
3. Name: PG-Lab-VMkernel.
4. Virtual switch: vSwitch-LabNet.
5. VLAN ID: 0.
6. Save.

## Task B - Add the VMkernel adapter

1. Open Networking -> VMkernel adapters.
2. Click Add VMkernel adapter, or Add networking -> VMkernel adapter if that is the route shown.
3. Choose existing PG-Lab-VMkernel. If the wizard also supports creating a port group, do not create a duplicate; choose the existing one.
4. Select IPv4 and Static configuration.
5. Enter IP address 172.16.50.10.
6. Enter subnet mask 255.255.255.0.
7. MTU: 1500. TCP/IP stack: Default.
8. Leave additional service checkboxes unselected for this general IP-storage connectivity exercise. NFS/iSCSI does not require a generic storage service checkbox.
9. Preserve the existing host default gateway 10.0.0.1; do not replace it with an invented storage gateway.
10. Save and record the assigned vmk number. Because vmk1 exists, it may be vmk2, but use the actual value.
11. Open the new adapter -> IP Settings and verify the address.

Roman Urdu: Is lab mein naya vmk 172.16.50.10 hoga. Pehle wala vmk0 10.0.0.10 waise hi rehna chahiye. Same subnet ke test ke liye naya gateway zaroori nahi.

## Task C - Configure the external endpoint

1. Open the separate Linux endpoint VM directly in Workstation.
2. Attach its adapter to LAB-ESXI-NET.
3. In its network settings set IPv4 manual: 172.16.50.30, mask 255.255.255.0 (/24).
4. For this isolated interface leave gateway and DNS blank. Keep any separate management/NAT interface clearly identified.
5. Apply settings, then verify:

```bash
ip -br address
ip route
ping -c 4 172.16.50.10
```

## Task D - Test from ESXi

If ESXi Shell/SSH is available, run this with the actual new vmk number:

```sh
# Replace vmk2 if your assigned number differs.
vmkping -I vmk2 172.16.50.30
```

If SSH is disabled, enable it temporarily through Host -> Manage -> Services -> TSM-SSH -> Start, or use the local console's troubleshooting options. Stop temporary SSH access after testing if it was previously disabled.

## Expected result

The new vmk has 172.16.50.10/24; ping works to the external peer; vmk0 and browser management remain available. Capture IP Settings and both ping outputs. A successful ping proves basic IP connectivity, not a mounted NFS datastore or an iSCSI session.

## Troubleshooting

If ping fails, check peer firewall, MAC mapping, same Workstation segment, IP mask and VLAN 0. A peer attached to VMnet8 while the uplink is on a LAN segment will not share the intended Layer 2 path.
