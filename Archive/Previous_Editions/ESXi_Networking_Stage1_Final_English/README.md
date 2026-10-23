# VMware ESXi 8 Networking — Stage 1 Final Study Notes (English)

**Edition:** English translation and consolidated expansion of the Stage 1 Roman Urdu study bundle. Theory is grounded in the instructor's five-page `06 ESXi Network Part-I.pdf`; the practical stages use the 17 original screenshots from `desktop-esxi9`. Technical terms and commands remain in English.

## Chapters

1. [Virtual Switches and vSS](01-Virtual-Switches.md) — teacher pages 2–3 and original vSwitch0
2. [Port Groups and SERVERS practical](02-Port-Groups.md) — VM port group vs VMkernel; 4-step walkthrough
3. [Physical NICs, Uplinks, and NIC Teaming](03-Physical-Adapters-and-Uplinks.md) — temporary dual-uplink configuration and removal
4. [VMkernel and Dedicated STORAGE Networking](04-VMkernel-Adapters.md) — vSwitch1, vmk1, static IPv4, MTU
5. [VLANs and Packet Flow](05-VLANs-and-Traffic-Flow.md) — teacher page 5 and boundaries of what was verified
6. [Troubleshooting and Verification](06-Troubleshooting-and-Practical-Lab.md) — why commands are run, how to interpret outputs, and next lab

## Reference material

- [Original teacher handout PDF](06_ESXi_Network_Part-I_Teacher_Source.pdf)
- Rendered teacher slides in `teacher_pdf_pages/` (5 pages)
- All 17 original lab screenshots in `screenshots/`

## Final observed lab configuration

| Area | Captured observation |
|---|---|
| Host accessed | `desktop-esxi9` (separate from previous `desktop-esxi8` troubleshooting lab) |
| Management | `vSwitch0` / `Management Network` / `vmk0: 10.0.0.10` / `vmnic0` |
| VM networks | `VM Network`, `SERVERS` on `vSwitch0`; both show 0 VMs in screenshots |
| SERVERS VLAN | `None (0)` |
| Storage | `vSwitch1` / `STORAGE` / `vmk1: 172.16.0.102/16` / `vmnic1` |
| Storage MTU | `1500` set in wizard |
| Teaming exercise | `vmnic0` and `vmnic1` temporarily Active together; `vmnic1` later removed from `vSwitch0` |

**Not proven:** live failover, load balancing distribution, VMware Workstation network modes, storage-server connectivity, guest VM networking, or VLAN isolation. Neither port-group labels nor separate IP subnets alone prove network separation.

## How to use these notes

Open README.md in VS Code, follow the chapters in sequence, and use each screenshot as evidence. Commands in code blocks are either read-only diagnostic commands or clearly marked examples. **Do not change the management uplink, management VLAN, or vmk0 IP without console access and a recovery plan.**
