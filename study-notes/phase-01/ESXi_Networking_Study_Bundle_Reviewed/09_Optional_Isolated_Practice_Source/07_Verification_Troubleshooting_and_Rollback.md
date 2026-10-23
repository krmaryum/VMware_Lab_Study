# 07 - Verification, Troubleshooting and Rollback

> **Addressing correction:** Existing STORAGE was recorded as 172.16.0.102/16. The proposed 172.16.50.0/24 falls inside that /16, so it overlaps the existing connected network. Treat all 172.16.50.x values below as an unexecuted source example. Before implementation choose a verified unused, non-overlapping subnet and consistently replace every endpoint/interface value. Do not change the existing management or storage addresses merely to follow this example.


## Read-only ESXi checks

Run through local shell or temporarily enabled SSH. These commands inspect configuration; they do not create the lab.

```sh
esxcli network nic list
esxcli network vswitch standard list
esxcli network vswitch standard portgroup list
esxcli network ip interface list
esxcli network ip interface ipv4 get
esxcli network vswitch standard policy failover get -v vSwitch-LabNet
esxcli network vswitch standard portgroup policy failover get -p PG-Lab-VMs
esxcli network vswitch standard portgroup policy shaping get -p PG-Lab-VMs
```

If a subcommand differs on your release, use its --help and confirm in the Host Client rather than changing configuration to match this text.

## Evidence checklist

- [ ] Baseline management screenshot, vmk0 10.0.0.10.
- [ ] vmnic4/vmnic5 MAC-to-Workstation mapping.
- [ ] New switch name and MTU.
- [ ] First uplink attached and connected.
- [ ] VMkernel group and actual vmk number/IP.
- [ ] VM group and guest adapter assignment.
- [ ] External VM-A and VMkernel ping outputs.
- [ ] Two-uplink switch summary.
- [ ] Active/standby screenshot and external failover results.
- [ ] Active/active screenshot and restored links.
- [ ] Shaping settings plus baseline/shaped/restored iperf3 measurements.

These are tasks to capture, not claims that the lab already passed.

## Troubleshooting table

| Symptom | Likely checks |
|---|---|
| Cannot add vmnic | Another switch owns it; confirm assignment |
| Physical adapter down | Workstation connection, MAC mapping, outer segment |
| Host-local ping succeeds, external fails | Outer mapping, peer firewall, nested MAC forwarding |
| All lab pings fail | Guest IP/mask, VM port group, VLAN mismatch, vmk status |
| Failover fails | Second adapter on different network, port-group override, standby link down |
| No loss during same-host test | Test may not use uplink; use external peer |
| Shaping no effect | Wrong group/direction, inheritance, baseline already below cap |
| Reverse also slow | Outer bottleneck, endpoint shaping, CPU, TCP behavior |
| No networks in observed range | Not assigned-IP evidence; use vmk IP settings and ping |
| No internet on lab guest | Expected on isolated no-gateway subnet |
| Two uplinks, one busy | Normal for one virtual port under originating-port policy |

Roman Urdu: Problem ko layer-wise check karein: link, outer mapping, VLAN, IP/mask, firewall, phir application. Har failure par gateway ya management IP change karna sahi approach nahi.

## Recovery

If management access is lost unexpectedly, use the local Workstation console. Review the actual changes and restore the recorded management assignments; do not reset all ESXi networking as a first response. This bundle intentionally creates separate lab objects.

## Cleanup in dependency order

1. Restore the disconnected Workstation lab adapters.
2. Stop iperf3 and remove temporary endpoint firewall rules.
3. Restore shaping and teaming to the recorded pre-test lab settings.
4. Move the lab guest vNIC to its original appropriate port group, or remove its dedicated lab vNIC. Record/restore the guest IP separately.
5. Delete only the new lab VMkernel adapter by its actual number. Confirm it is not vmk0 or the pre-existing vmk1.
6. Delete PG-Lab-VMkernel and PG-Lab-VMs once unused.
7. Delete vSwitch-LabNet, releasing its uplinks.
8. Restore original Workstation network mapping if desired; delete the temporary segment only when unused.
9. Stop SSH if you enabled it temporarily. Verify 10.0.0.10 management access.

Roman Urdu: Pehle dependent adapters/VM attachments hataein, phir port groups aur aakhir mein switch. Existing vSwitch1 ya vmk1 ko lab ka hissa samajh kar delete na karein.
