# 06A - NIC Teaming and Failover

> **Addressing correction:** Existing STORAGE was recorded as 172.16.0.102/16. The proposed 172.16.50.0/24 falls inside that /16, so it overlaps the existing connected network. Treat all 172.16.50.x values below as an unexecuted source example. Before implementation choose a verified unused, non-overlapping subnet and consistently replace every endpoint/interface value. Do not change the existing management or storage addresses merely to follow this example.


> **Status: proposed repeat exercise.** The original source provides the steps below; the new lab objects and performance/failover results have not been evidenced as completed. Complete the preparation in `../09_Optional_Isolated_Practice_Source/00_Preparation_and_Network_Plan.md` first. Keep this separate from the recorded STORAGE/SERVERS stages.


## Study notes

NIC teaming uses multiple uplinks on a switch. Failover policies control which paths may carry traffic. Under the default originating virtual port policy, one virtual port normally uses one selected uplink at a time; adding two links is not a promise that one transfer doubles in speed.


Class reference: Network Part-II, pages 3-7. See the included source clarification notes for corrections to the slide wording.

## Task A - Configure active/standby

1. Open vSwitch-LabNet -> Edit settings -> Teaming and failover.
2. Load balancing: Route based on originating virtual port ID.
3. Network failure detection: Link status only.
4. Notify switches: Yes.
5. Failback: Yes for this exercise.
6. Move vmnic4 into Active adapters.
7. Move vmnic5 into Standby adapters.
8. Save.
9. Inspect both lab port groups. Their teaming policy should inherit the switch policy; remove unintended lab-group overrides so this task tests the switch setting.

| State | Meaning |
|---|---|
| Active | Eligible to carry normal traffic |
| Standby | Available when active paths fail |
| Unused | Not eligible under this policy, including failover |


## Task B - Test a real external failover path

1. From the external Workstation endpoint start a continuous ping to VM-A:

```bash
ping 172.16.50.20
```

2. Also confirm that endpoint reaches 172.16.50.10.
3. In the ESXi VM's Workstation settings, use the previously recorded MAC mapping to locate vmnic4's adapter.
4. Clear Connected for only that lab adapter. Do not remove the adapter device and do not disconnect management adapters.
5. Watch the ping. Brief loss may occur; traffic should recover through vmnic5 when the link-down is detected and the backup path works.
6. Check ESXi Physical adapters: vmnic4 should show down.
7. Re-enable Connected for the same adapter.
8. Confirm both paths are up and ping remains successful.
9. Stop ping with Ctrl+C and capture before/during/after results.

A ping between VM-A and the vmk on the same ESXi switch can stay inside the host and is not sufficient evidence of uplink failover. Use the external endpoint.


## Task C - Configure active/active

1. Return to Teaming and failover.
2. Keep the originating virtual port policy.
3. Move vmnic5 from Standby to Active so both lab uplinks are Active.
4. Save and verify both lab port groups inherit the policy.
5. Repeat the external ping test, disconnecting only one lab uplink at a time and restoring it before another test.
6. For observing distribution, use multiple guest vNICs/VMs and adapter counters. One ping or one transfer may remain on one uplink.

## Port-group override exercise

On PG-Lab-VMkernel only, you may temporarily enable the teaming override and set vmnic5 Active, vmnic4 Standby. Verify the VM group still inherits the switch policy. Restore inheritance afterward. Overrides apply to that group, not every group on the switch.

## Policy choices

Use originating virtual port for this lab. IP hash needs coordinated upstream static aggregation on a standard-switch design and is not a simple first-IP/second-IP failover mode. Explicit failover order prioritizes eligible uplinks; it is not application-aware DRS. LACP is not supported on a standard switch. Physical NIC load balancing belongs to distributed switches.

## Pass criteria

Both adapters are assigned correctly, active/standby failover works through the external endpoint, both adapters are restored, and the final chosen policy is recorded. A fixed failover time is not guaranteed; measure your observed result.
