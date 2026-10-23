# Chapter 04 — VMkernel Adapters

> **Lab baseline (confirmed from screenshot):** `desktop-esxi9`, `vSwitch0`, `vmnic0` (reported 10000 Full), Management Network `vmk0` / `10.0.0.10`, VM Network with zero VMs, VLAN shown as `--`. This is a different host from `desktop-esxi8` / `10.0.0.11`. Do not assume that uninspected settings are configured as described in the examples.

> **Safety:** Viewing pages and using read-only commands is safe. Changing management uplinks, port groups, VLANs, or vmk0 IP may cut off remote access. Have ESXi console access and a rollback plan before changes.

## Learning objectives
Understand `vmk` interfaces, host management traffic, addressing and the default gateway.

## What is a VMkernel adapter?
A **VMkernel network adapter** is an IP-enabled network interface used by the ESXi host itself for services such as management, vMotion, certain storage networks, and other VMkernel services when configured. It is **not** a virtual NIC inside a guest VM.

**Confirmed here:** `vmk0` belongs to the `Management Network` port group and shows `10.0.0.10` on `desktop-esxi9`. **Not yet confirmed:** subnet mask, gateway, other vmks or enabled services.

```text
Windows browser --> LAN --> uplink vmnic0 --> vSwitch0
                           --> Management Network --> vmk0 (10.0.0.10)
```

## GUI lab (read-only)
1. Networking → **VMkernel adapters**.
2. Select `vmk0`; document IP, subnet/prefix, TCP/IP stack, and enabled services.
3. If viewable, note host default gateway and DNS settings in host networking configuration.
4. Capture screenshots, redact sensitive infrastructure data before publication.

## Optional ESXi commands
```sh
esxcli network ip interface list
esxcli network ip interface ipv4 get
esxcli network ip route ipv4 list
esxcli network ip dns server list
```
**Why:** list interfaces, IPv4 addresses, routes and DNS servers respectively. **How to interpret:** `vmk0` is a host interface; `10.0.0.10` is its IP; a default route `0.0.0.0/0` sends off-subnet traffic via a gateway (example only, verify actual output). No output has yet been collected for this host.

## Roman Urdu
`vmk0` ESXi host ka khud ka network interface hota hai. Browser se ESXi manage karna isi management IP ki wajah se mumkin hota hai. IP ya VLAN bina console access ke change na karein kyunke aap ka connection toot sakta hai.

## Questions
1. How does a VMkernel adapter differ from a guest vNIC?
2. What service can use vmk0?
3. Why is the gateway important for off-subnet management?
