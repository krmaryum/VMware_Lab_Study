# Chapter 01 — Virtual Switches

> **Lab baseline (confirmed from screenshot):** `desktop-esxi9`, `vSwitch0`, `vmnic0` (reported 10000 Full), Management Network `vmk0` / `10.0.0.10`, VM Network with zero VMs, VLAN shown as `--`. This is a different host from `desktop-esxi8` / `10.0.0.11`. Do not assume that uninspected settings are configured as described in the examples.

> **Safety:** Viewing pages and using read-only commands is safe. Changing management uplinks, port groups, VLANs, or vmk0 IP may cut off remote access. Have ESXi console access and a rollback plan before changes.

## Learning objectives
Understand the physical switch vs ESXi standard virtual switch, how ports forward Layer-2 frames, what an uplink does, and how your own vSwitch0 is wired.

![Actual ESXi9 vSwitch0 screenshot](screenshots/01-esxi9-vswitch0.png)

## The concept
A **physical Ethernet switch** connects devices on a LAN using hardware ports. A **standard virtual switch (vSS)** is a software switching facility inside one ESXi host. VM vNICs and VMkernel interfaces attach through port groups, and an optional physical uplink (`vmnic`) connects the vSwitch to an external network. Traffic between VMs on the **same host and same VLAN/port group network** can be switched locally without leaving the host, subject to security/network policies.

```text
ESXi host desktop-esxi9
  VM guest vNICs --> [VM Network port group] ----+
  Management vmk0 --> [Management Network PG] --+--> [vSwitch0] --> [vmnic0 uplink] --> external LAN
```

### In your screenshot
- **vSwitch0**: the standard virtual switch.
- **Management Network**: contains one VMkernel port `vmk0`, displayed as `10.0.0.10`.
- **VM Network**: a separate virtual-machine port group; zero VMs currently connected.
- **vmnic0**: attached physical adapter (in a nested ESXi lab, ESXi may see a *virtualized* hardware NIC provided by VMware Workstation).
- **10000 Full**: ESXi reports 10,000 Mb/s full duplex *at this NIC link*. It is **not a measured internet throughput**.

## vSS vs vDS
| Feature | Standard vSwitch (vSS) | Distributed Switch (vDS) |
|---|---|---|
| Management | Individual ESXi host | Centrally via vCenter |
| Lab requirement | Standalone ESXi enough | vCenter and appropriate license/support |
| Consistency across hosts | Configure separately | Common distributed configuration |

## Lab — Observe without changing
1. Open `https://desktop-esxi9/ui` and sign in.
2. Choose **Networking → Virtual switches**; expand `vSwitch0`.
3. Record port groups, VMkernel ports, physical uplinks, link speed, and VLAN display.
4. Click **View settings** (read-only); capture screenshot and note MTU, security, and teaming policy *if displayed*.

## What does output mean?
- **vSwitch0** identifies the switch object, not an IP endpoint.
- **vmk0: 10.0.0.10** is the ESXi management VMkernel IP, not an IP on the vSwitch itself.
- **vmnic0 10000 Full** indicates a negotiated/reported link property; it does not guarantee actual data rate.

## Roman Urdu
Physical switch asal hardware device hai. ESXi ka `vSwitch0` software-based switch hai. `VM Network` mein VMs ki virtual NICs lagti hain, aur `Management Network` mein `vmk0` laga hai. `vmnic0` uplink ke through vSwitch external LAN tak ja sakta hai. Agar ESXi Workstation mein nested hai to vmnic0 asal PC ka physical NIC nahi, guest ESXi ko diya gaya virtual adapter bhi ho sakta hai.

## Knowledge check
1. Does a vSwitch need a physical uplink for two same-host VMs to talk on the same network?
2. Is `10.0.0.10` the IP of `vSwitch0` or `vmk0`?
3. Why might reported 10 Gbps not equal actual speed?
