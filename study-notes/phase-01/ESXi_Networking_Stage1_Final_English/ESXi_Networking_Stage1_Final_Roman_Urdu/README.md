# VMware ESXi 8 Networking — Stage 1 Final Study Notes (Roman Urdu)

**Scope:** Teacher ki `06 ESXi Network Part-I.pdf` ki theory + aapke `desktop-esxi9` practical lab ke screenshots. Yeh chapter-wise Roman Urdu bundle hai; technical commands as-is English mein hain.

## Chapters

1. [Virtual Switches](01-Virtual-Switches.md) — Teacher pages 2–3, vSwitch0
2. [Port Groups](02-Port-Groups.md) — SERVERS setup with step screenshots
3. [Physical Adapters & NIC Teaming](03-Physical-Adapters-and-Uplinks.md) — vmnic1 add/remove
4. [VMkernel Adapters & Storage](04-VMkernel-Adapters.md) — vSwitch1, vmk1, 172.16.0.102, MTU 1500
5. [VLANs & Traffic Flow](05-VLANs-and-Traffic-Flow.md) — Teacher page 5, actual stage boundaries
6. [Troubleshooting & Next Labs](06-Troubleshooting-and-Practical-Lab.md) — command meanings, verification checklist

## Teacher original source

- [Teacher slides — PDF](06_ESXi_Network_Part-I_Teacher_Source.pdf) (5 pages; diagrams also exported to `teacher_pdf_pages/`)

## Actual screenshot gallery — Stage 1

- [Chapter 01: initial vSwitch0](screenshots/01-original-vswitch0.png)
- [Chapter 03: unclaimed vmnic1](screenshots/02-add-vmnic1-selected.png)
- [Chapter 03: new vmnic1 active pending](screenshots/03-vmnic1-pending-active.png)
- [Chapter 03: vmnic1 saved](screenshots/04-vmnic1-active-saved.png)
- [Chapter 03: dual uplinks](screenshots/05-vswitch0-dual-uplinks.png)
- [Chapter 03: removed vmnic1](screenshots/06-vswitch0-single-uplink.png)
- [Chapter 04: storage connection type](screenshots/07-storage-select-type.png)
- [Chapter 04: selected vmnic1](screenshots/08-storage-free-adapter.png)
- [Chapter 04: vmnic1 moved active](screenshots/09-storage-new-uplink.png)
- [Chapter 04: STORAGE MTU](screenshots/10-storage-port-properties.png)
- [Chapter 04: vmk1 static IP](screenshots/11-storage-ipv4.png)
- [Chapter 04: vSwitch1 created](screenshots/12-storage-switch-created.png)
- [Chapter 04: storage topology](screenshots/13-storage-vmk1-topology.png)
- [Chapter 02: select VM port group](screenshots/14-servers-select-portgroup.png)
- [Chapter 02: select vSwitch0](screenshots/15-servers-target-vswitch0.png)
- [Chapter 02: SERVERS label](screenshots/16-servers-label.png)
- [Chapter 02: SERVERS topology](screenshots/17-servers-created.png)

## Verified configuration at end of this stage

| Component | Observation |
|---|---|
| UI / lab host | `desktop-esxi9`, management `10.0.0.10` |
| `vSwitch0` | `Management Network` (`vmk0`), `VM Network`, `SERVERS`; uplink `vmnic0` |
| `vSwitch1` | `STORAGE`, `vmk1 = 172.16.0.102/16`, uplink `vmnic1` |
| STORAGE MTU | 1500 in configuration screenshot |
| SERVERS VLAN | None (0) |
| SERVERS VM count | 0 |
| NIC Teaming | Temporarily two active uplinks on vSwitch0; later vmnic1 removed and assigned to vSwitch1; actual failover not tested |

**Limitations:** Storage server connectivity, Workstation virtual NIC modes, VLAN segregation and actual failover remain unverified. `vmnic` naming in nested ESXi represents guest-visible adapters. Original `desktop-esxi8` hostname troubleshooting is a separate lab and was not merged with this host.

**Safety:** Before management network changes ensure ESXi console access; do not change `vmk0`/management uplink or VLAN blindly.
