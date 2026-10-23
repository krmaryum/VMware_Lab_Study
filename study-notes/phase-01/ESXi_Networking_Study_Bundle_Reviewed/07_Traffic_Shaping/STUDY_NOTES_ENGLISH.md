# 06B - Traffic Shaping on a Standard Switch

> **Addressing correction:** Existing STORAGE was recorded as 172.16.0.102/16. The proposed 172.16.50.0/24 falls inside that /16, so it overlaps the existing connected network. Treat all 172.16.50.x values below as an unexecuted source example. Before implementation choose a verified unused, non-overlapping subnet and consistently replace every endpoint/interface value. Do not change the existing management or storage addresses merely to follow this example.


> **Status: proposed repeat exercise.** The original source provides the steps below; the new lab objects and performance/failover results have not been evidenced as completed. Complete the preparation in `../09_Optional_Isolated_Practice_Source/00_Preparation_and_Network_Plan.md` first. Keep this separate from the recorded STORAGE/SERVERS stages.


## Study notes

Traffic shaping controls the sending rate. On a vSphere Standard Switch it controls outbound traffic from the attached virtual adapter (for example, VM-A transmitting toward the network). Do not interpret this as an inbound/download limiter for the guest. A port-group policy is applied per attached virtual adapter, not as a single shared budget for the entire group.


Class reference: Network Part-II, page 9.

| Field | Meaning | Lab value if UI shows Kbit/s and KB |
|---|---|---|
| Average bandwidth | Sustained target rate over time | 10000 Kbit/s = 10 Mbit/s |
| Peak bandwidth | Upper burst rate | 20000 Kbit/s = 20 Mbit/s |
| Burst size | Credit/budget of burst data | 1024 KB |

Burst size is an amount of data, not remaining bandwidth and not a rate. Read the UI unit labels before entering values. APIs may express rates in bits/s and size in bytes; do not copy UI numbers unchanged into an API.


## Prerequisites

VM-A .20 is inside ESXi on PG-Lab-VMs. Endpoint .30 is a separate VM directly in Workstation on LAB-ESXI-NET. Both should already ping successfully. iperf3 must be installed on both; use the OS package repository when internet access is available, then test over the isolated lab interface.

Ubuntu/Debian guest example:

```bash
sudo apt update
sudo apt install iperf3
```

Rocky/RHEL-family guest example (requires a repository providing iperf3):

```bash
sudo dnf install iperf3
```

Allow TCP 5201 in the endpoint guest firewall, scoped to the lab interface/source if possible. Do not disable the entire firewall. On a firewalld guest, identify the zone of the lab interface before adding 5201/tcp to that zone. Keep results on the test subnet; a second NAT interface is only for package installation.

## Task A - Record baseline

1. Check that traffic shaping is disabled on vSwitch-LabNet and PG-Lab-VMs, or record and temporarily remove only lab policies that would affect the test.
2. On endpoint .30 run:

```bash
iperf3 -s -B 172.16.50.30
```

3. On VM-A run:

```bash
iperf3 -c 172.16.50.30 -B 172.16.50.20 -t 30
```

4. Record the sender/receiver rate. The baseline should exceed 10 Mbit/s to demonstrate the proposed cap. If it does not, resolve the bottleneck or choose a lower supported shaping limit.

## Task B - Enable shaping only for the test VM port group

1. Open Networking -> Port groups -> PG-Lab-VMs -> Edit settings.
2. Expand Traffic shaping.
3. Enable Override / Override switch settings if shown.
4. Set Status / Enabled to Enabled.
5. Enter Average 10000 Kbit/s, Peak 20000 Kbit/s, Burst size 1024 KB, matching actual displayed units.
6. Save and reopen to verify persistence.
7. Leave management and PG-Lab-VMkernel shaping unchanged.

If your UI exposes shaping at switch level instead, the learning path is Virtual switches -> vSwitch-LabNet -> Edit settings -> Traffic shaping. That affects inheriting lab groups; record the wider scope. Prefer the VM group override for this exercise.

## Task C - Measure shaped transmission

On VM-A run:

```bash
iperf3 -c 172.16.50.30 -B 172.16.50.20 -t 60 -i 1
```

Observe early burst behavior and the longer sustained rate. Expect roughly 10 Mbit/s sustained when shaping is the limiting factor, with protocol overhead and implementation effects. Do not expect an exact number or guarantee that every first interval reaches 20 Mbit/s.

Optional reverse-direction test:

```bash
iperf3 -c 172.16.50.30 -B 172.16.50.20 -t 30 -R
```

This sends bulk data from endpoint to VM-A. With only VM-A's outbound policy configured, it need not show the same bulk-data cap. TCP acknowledgments and other bottlenecks still affect measured results.

## Task D - Restore and compare

1. Disable the PG-Lab-VMs shaping override or restore its prior policy. If the parent has shaping enabled, inheriting it will not disable shaping; inspect both levels.
2. Run the original 30-second test again.
3. Verify the measured throughput recovers toward baseline.
4. Stop the endpoint server with Ctrl+C and remove any temporary firewall exception created solely for this test.

| Test | Measured Mbit/s | Notes |
|---|---|---|
| Baseline, VM-A sends | Fill after testing | No shaping |
| Shaped, VM-A sends | Fill after testing | Average 10, peak 20 |
| Reverse, optional | Fill after testing | Different direction |
| Restored baseline | Fill after testing | Policy restored |

## Success criteria

Save the policy screenshot and actual performance outputs. Ping proves reachability, not bandwidth. Shaping caps consumption; it does not reserve bandwidth, speed up a slow path or guarantee a minimum rate.
