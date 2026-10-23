# Chapter 02 — Port Groups

> **Lab baseline (confirmed from screenshot):** `desktop-esxi9`, `vSwitch0`, `vmnic0` (reported 10000 Full), Management Network `vmk0` / `10.0.0.10`, VM Network with zero VMs, VLAN shown as `--`. This is a different host from `desktop-esxi8` / `10.0.0.11`. Do not assume that uninspected settings are configured as described in the examples.

> **Safety:** Viewing pages and using read-only commands is safe. Changing management uplinks, port groups, VLANs, or vmk0 IP may cut off remote access. Have ESXi console access and a rollback plan before changes.

## Learning objectives
Explain VM port groups and VMkernel port groups, VLAN IDs, and how a guest selects its logical network.

## What is a port group?
A **port group** defines common connection and policy settings for a set of virtual switch ports. Guest VMs connect via a **virtual-machine port group**. ESXi management and services connect via **VMkernel interfaces** associated with a VMkernel port group.

| Seen in your screenshot | Role | Evidence |
|---|---|---|
| Management Network | VMkernel networking for host services | Has `vmk0`, IP `10.0.0.10` |
| VM Network | Virtual machine attachment network | Displays `Virtual Machines (0)` |

### VLAN explanation
VLANs split a physical or virtual Ethernet domain logically. On a standard switch port group, **VLAN ID 0** generally indicates untagged traffic; **1–4094** configure VLAN tagging (with platform-specific reserved behavior); **4095** is a special guest VLAN trunking mode used only in specific designs. Your screenshot shows `VLAN ID: --`, **not enough evidence to assert a specific configured VLAN ID**. Open Port Groups to inspect the actual field before stating it is 0.

## Examples (not applied to your host)
```text
vSwitch0
├─ Management Network ── vmk0, 10.0.0.10
└─ VM Network         ── VM-A vNIC, VM-B vNIC
```

## Lab — Read-only inspection
1. Networking → **Port groups**.
2. Capture name, virtual switch, VLAN ID, and associated VMkernel/VM details.
3. Select each port group and record policy settings without editing.
4. When later adding a VM, identify which port group its Network Adapter selects.

## Output interpretation
- **Port group name** is an administrator label, not a DNS name.
- **VLAN ID** sets how traffic is tagged or untagged at a port group; the upstream network must be compatible.
- **0 VMs** only means no guest VM is attached; it does not indicate that the network is faulty.

## Roman Urdu
Port group virtual switch par networking rules ka group hai. `Management Network` ESXi ki management connectivity ke liye hai, jabke `VM Network` virtual machines ko attach karne ke liye. VLAN ka matlab logical network separation. Agar VLAN ko galat set kar diya to aap remote management access kho sakte hain.

## Questions
1. What is the difference between `Management Network` and `VM Network`?
2. What can happen if a management VLAN is changed incorrectly?
3. Why should we not infer VLAN ID from a `--` display?
