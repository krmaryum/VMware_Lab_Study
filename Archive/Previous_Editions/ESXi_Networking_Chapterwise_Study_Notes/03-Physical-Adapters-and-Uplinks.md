# Chapter 03 — Physical Adapters and Uplinks

> **Lab baseline (confirmed from screenshot):** `desktop-esxi9`, `vSwitch0`, `vmnic0` (reported 10000 Full), Management Network `vmk0` / `10.0.0.10`, VM Network with zero VMs, VLAN shown as `--`. This is a different host from `desktop-esxi8` / `10.0.0.11`. Do not assume that uninspected settings are configured as described in the examples.

> **Safety:** Viewing pages and using read-only commands is safe. Changing management uplinks, port groups, VLANs, or vmk0 IP may cut off remote access. Have ESXi console access and a rollback plan before changes.

## Learning objectives
Understand `vmnic`, link state, link speed/duplex, uplinks and redundancy, especially nested networking.

## Core terms
- **vmnic0:** adapter interface name ESXi assigned; virtualized inside nested ESXi is possible.
- **Uplink:** a vmnic assigned to a vSwitch to reach networks beyond the host.
- **Link status:** whether the interface believes a link is connected.
- **Speed / duplex:** reported link characteristics, not benchmark results.

Your screenshot shows `vmnic0 10000 Full` as the uplink for `vSwitch0`. The screenshot **does not prove** there are multiple active uplinks or redundancy.

## Nested ESXi note
```text
Guest VM --> ESXi9 vSwitch0 --> ESXi9 vmnic0 (virtual NIC)
  --> VMware Workstation virtual networking --> Desktop host NIC --> LAN
```
Workstation network mode (Bridged/NAT/Host-only), any nested promiscuous/MAC behavior, and external VLAN design affect connectivity. Your screenshot alone does not tell us the Workstation mode.

## Read-only GUI lab
1. Networking → **Physical adapters**.
2. Record device name, status, speed, duplex, driver and MAC (omit MAC from public GitHub notes if desired).
3. Return to **Virtual switches**, identify the assigned uplinks.
4. Compare the host's connected devices with your diagram.

## Optional ESXi shell commands (read-only; only if shell access is already enabled)
```sh
esxcli network nic list
esxcli network nic get -n vmnic0
```
**Why:** `list` enumerates adapters and state; `get` describes one adapter. Interpret `Link Status`, `Speed`, `Duplex`, `Driver`, `MAC Address`—do not mistake link speed for measured throughput. Command fields vary by ESXi build.

## Roman Urdu
`vmnic0` ESXi ka uplink adapter hai. Agar ESXi VMware Workstation ke andar chal raha ho to yeh guest OS ko diya gaya virtual adapter ho sakta hai. 10000 Full ka matlab reported 10 Gbps full-duplex link, internet speed guarantee nahi.

## Questions
1. How is a vmnic different from a VM's vNIC?
2. Why might one uplink be a single point of failure?
3. What would you verify before setting up NIC teaming?
