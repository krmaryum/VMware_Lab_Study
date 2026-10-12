# Phase 05 — DOA, GPU, Remote Hands, Inventory & Handoff
**Priority: High | Study: Tuesday**

## 1. DOA means “dead on arrival”
Newly delivered hardware cannot reach required baseline (e.g. no power, no POST, bad RAM, dead controller/NIC, faulty PSU). Don't label it DOA based solely on an OS or network configuration error.

## 2. Failure triage pattern
**Identify → Isolate → Gather evidence → Coordinate → Re-test → Document.**
- Confirm asset ID, location, stage and symptom.
- Check BMC health/event log and error messages.
- Compare expected hardware inventory with discovered parts.
- Determine if issue is likely physical, firmware, network, provisioning or OS.
- If hardware intervention is required, provide remote hands the precise rack/U, asset tag, FRU/serial and approved steps.
- For RMA candidates, collect vendor-required diagnostics and preserve chain of documentation.

## 3. GPU server checks
- Expected accelerator quantity and models present.
- PCIe detection and any BMC/firmware-reported errors.
- Approved GPU driver/firmware compatibility.
- Temperature/power health and workload-specific test if required.
- **Command only with installed NVIDIA drivers:**
```bash
lspci | grep -Ei 'vga|3d|nvidia|amd'
nvidia-smi
```
A failed `nvidia-smi` could mean a missing driver or permissions, not necessarily dead GPU hardware.

## 4. Inventory accuracy
Record the facts required by runbook: ticket/asset ID, datacenter, rack/U, switch+port, BMC identity/address (safely), hostname, serials, disks, NICs, GPU population, firmware baseline, provisioning state, test result, owner, timestamps, blockers. **Never put passwords, API tokens or keys into a spreadsheet or ticket.**

## 5. Handoff example
```text
Asset: LAB-SRV-042 (example only)
Location: DC-A / Rack R12 / U18
Hardware: 2 CPUs, 256 GiB RAM, 4 NVMe, 2 NICs (per expected inventory)
BMC: reachable; sensors healthy; event log reviewed
Firmware/BIOS: matches approved baseline
Network/PXE: approved VLAN and deployment completed
OS / config: Ubuntu version per ticket; configuration job passed
Validation: SSH, disks, NIC, logs, GPU check (if present) passed
Blockers: none
Evidence: ticket refs, logs, screenshots
State: Ready for review / production approval per runbook
```

## 6. Interview questions
**Q: Multiple servers fail in the same rack?** Look for shared cause: power feed, switch/VLAN, DHCP/PXE services or firmware batch before treating every server as an independent failure.

**Q: Remote hands says cable fixed but link remains down?** Reconfirm exact asset/port pairing, switch administrative state, NIC status and logs; coordinate a controlled test and record results.

**Q: You're behind schedule with 20 machines?** Prioritize by blocker and SLA, work a repeatable checklist, group common failures, escalate dependencies early, and never skip validation just to increase a completion count.

## 7. Practice
- [ ] Explain DOA vs software provisioning failure.
- [ ] Draft a clear escalation with rack/U, symptom, evidence, owner and next step.
- [ ] Explain why inventory quality matters for future outages.

**Roman Urdu:** Remote hands data center mein physically cable, disk, PSU ya doosra component check karne wali team hoti hai. Aap clear rack/asset details aur issue ka proof dein. Production handoff se pehle tracking update karein.
