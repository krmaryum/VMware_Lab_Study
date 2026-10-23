# Phase 06 — 18 Realistic Scenario Questions With Model Responses
**Study: Wednesday | Practice each aloud (45–90 sec).**

## General method
**Scope → Confirm symptoms → Isolate failure stage → Test one hypothesis → Gather logs → Fix per runbook → Verify → Document.**

1. **Server won't power on.** Verify asset/power feeds and BMC reachability; inspect PSU and hardware alerts; coordinate approved physical inspection. Avoid repeated forced power cycles.
2. **BMC unreachable.** Check correct IP and management network, access source, cable/port/VLAN and BMC power; involve networking/remote hands with evidence.
3. **POST fails after memory error.** Review POST/BMC SEL, identify affected DIMM/bank and serial, open hardware case, request approved reseat/replacement, rerun diagnostics.
4. **Disk missing before imaging.** Check controller mode, firmware inventory and physical inventory; confirm NVMe/SATA visibility and cable/bay mapping; escalate hardware if needed.
5. **Wrong RAID mode.** Stop imaging, compare ticket/runbook and existing data risk; change only with authorization since reconfiguration can destroy data; validate result.
6. **PXE says “media test failure”.** Determine link and NIC boot settings; check physical switch port/VLAN and DHCP. Don't assume image server is the first cause.
7. **DHCP address but PXE file missing.** Verify correct UEFI boot target/architecture, DHCP boot-file info or proxy DHCP, filename/path, TFTP/HTTP reachability and logs.
8. **Imaging times out at download.** Check image server status, network path, ACL, service logs, bandwidth and server-specific driver compatibility.
9. **OS boots back to PXE.** Validate disk detection, install completion, bootloader/EFI and boot order. Capture installer and console errors.
10. **Host reachable by BMC but not SSH.** Compare management and host networks. Use console to inspect OS, address, route, SSH daemon, firewall and logs.
11. **Host has wrong IP.** Check MAC/port/VLAN, DHCP reservations, static config, image metadata and inventory; avoid changing prod network blindly.
12. **Puppet fails after OS deployment.** Inspect agent/automation error, DNS/time/certificate issues, package mirrors and the specific failed resource; rerun only when safe.
13. **SSH fails after provisioning.** Check console, `systemctl status sshd` or distro equivalent, listening socket, firewall rules, keys, auth logs and route.
14. **Host shows disk errors.** Correlate `dmesg`, SMART/vendor tools and controller/BMC logs; assess risk, preserve evidence, involve storage/hardware.
15. **GPU not detected.** Compare `lspci`, BMC inventory, PCIe power, firmware/driver installation and `nvidia-smi` (where applicable); separate missing driver from hardware fault.
16. **Many servers fail in one rack.** Search for shared switch port/VLAN, DHCP pool, power or image-service issue. Identify pattern and coordinate owning teams.
17. **Server passes imaging but fails validation.** Keep it out of production, identify failing test, collect logs, remediate approved change and rerun entire required validation.
18. **Inventory wrong for switch port.** Stop assumptions, reconcile physical/logic mappings with network and remote hands, update authoritative system and ticket before release.

## Answer framework practice
For every item write: *what I know* / *what I would check next* / *which team I would involve* / *what verifies a fix*.

## Three questions to ask interviewer
1. “Which onboarding step causes the most delays today—BMC access, PXE/imaging, or validation?”
2. “What does your team use to track asset identity, hardware inventory and readiness gates?”
3. “How is a successful first 60–90 days measured for this role?”
