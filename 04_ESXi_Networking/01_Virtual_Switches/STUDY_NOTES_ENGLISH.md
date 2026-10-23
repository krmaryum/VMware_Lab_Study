# Chapter 01 — Virtual Switches

## Concept and lab connection

A standard switch provides Layer-2 connectivity inside one ESXi host. The VM’s vNIC, a port group, the switch and an uplink each have different jobs. The switch is not the management IP endpoint: `vmk0` owns `10.0.0.10` in this lab. Two compatible same-host VM connections can exchange traffic locally; an external destination needs the appropriate uplink path.

The practical sequence first inspected `vSwitch0`, then temporarily added a second uplink, moved it to the dedicated storage switch and created `SERVERS`. These are recorded configuration changes, not a measured performance result. A standard switch is managed per host; a distributed switch is a different, centrally managed design.

**Checkpoint:** Giving a switch a name does not give it an IP address. The host’s VMkernel interface or a guest’s own interface needs addressing.

> **Safety:** Viewing pages and using read-only commands is safe. Changing management uplinks, port groups, VLANs, or vmk0 IP may cut off remote access. Have ESXi console access and a rollback plan before changes.

## Learning objectives
Understand the physical switch vs ESXi standard virtual switch, how ports forward Layer-2 frames, what an uplink does, and how your own vSwitch0 is wired.

![Actual ESXi9 vSwitch0 screenshot](screenshots/01-original-vswitch0.png)

## The concept
A **physical Ethernet switch** connects devices on a LAN using hardware ports. A **standard virtual switch (vSS)** is a software switching facility inside one ESXi host. VM vNICs and VMkernel interfaces attach through port groups, and an optional physical uplink (`vmnic`) connects the vSwitch to an external network. Traffic between VMs on the **same host and same VLAN/port group network** can be switched locally without leaving the host, subject to security/network policies.

The conceptual path is: endpoint interface → port group → standard switch → suitable uplink when the destination is external. The screenshots establish the captured objects; the outer Workstation mapping requires separate verification.

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


## Knowledge check
1. Does a vSwitch need a physical uplink for two same-host VMs to talk on the same network?
2. Is `10.0.0.10` the IP of `vSwitch0` or `vmk0`?
3. Why might reported 10 Gbps not equal actual speed?


---
## Teacher's PDF: vSphere Standard Switch (pages 2–3)
The teacher's diagram on page 3 shows **two ESXi hosts**, each with its own standard switch, VM and VMkernel-facing port groups, and pNIC uplinks to a physical switch. This is an architecture example, not evidence that both hosts in our lab have all those services configured.

![Teacher's two-host networking diagram](teacher_pdf_pages/teacher-page-03.png)

**Why uplinks matter:** A VM and another VM on the same ESXi host and compatible Layer-2 network can exchange traffic through the local vSwitch even with no external uplink. Traffic destined for the external LAN requires a suitable uplink and compatible upstream connectivity.

### Your observed topology, before additional configuration
![Initial vSwitch0: management and VM Network](screenshots/01-original-vswitch0.png)

At that time the ESXi9 Host Client showed `Management Network` with `vmk0: 10.0.0.10`, `VM Network` with zero connected VMs, and `vmnic0` as uplink. Later screenshots show additional changes; do not treat the initial screenshot as the final state.
