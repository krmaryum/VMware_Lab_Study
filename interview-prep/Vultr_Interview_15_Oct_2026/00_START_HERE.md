# Vultr Server Onboarding Specialist — Interview Study Pack
**Interview:** Thursday, October 15, 2026  
**Goal:** Explain the end-to-end onboarding of a newly racked CPU/GPU server clearly and troubleshoot each stage.  
**How to use:** Study files in numeric order. Speak answers aloud. Mark each checklist only after you can explain it without reading.

> Scope: Built around publicly listed Server Onboarding Specialist responsibilities. These are preparation materials, not internal Vultr procedures. Do not imply you used a tool in production if you have only studied it.

## Four-day schedule
| Date | Focus | Files | Target |
|---|---|---|---|
| Sun Oct 11 | Onboarding overview, BMC, BIOS, storage | Phase 01, 02 | Explain boot-to-handoff path |
| Mon Oct 12 | Networking, DHCP, PXE, imaging | Phase 03 | Troubleshoot a failed PXE boot |
| Tue Oct 13 | Linux validation, Puppet, hardware, inventory | Phase 04, 05 | Explain validation & escalation |
| Wed Oct 14 | Scenarios, behavioral, mock interview | Phase 06, 07, quick sheet | 30-minute mock aloud |
| Thu Oct 15 | Light review only | Quick sheet | Clear, calm delivery |

## Learning objectives
- Describe rack handoff → BMC → BIOS/storage → network/PXE → OS → config management → validation → inventory → production release.
- Distinguish **BMC management network** from the **host production network**.
- State what evidence you would collect before escalating to remote hands, network, provisioning, or hardware teams.
- Give a concise, truthful answer for tools you have not directly used.

## End-to-end onboarding map
```text
Physical rack / power / cabling / asset assignment
  ↓
BMC/IPMI: remote health, console, power, network identity
  ↓
BIOS/UEFI + firmware + CPU/RAM/storage/NIC verification
  ↓
Storage mode: approved RAID / HBA / JBOD choice
  ↓
Switch/VLAN + DHCP/PXE (or approved imaging workflow)
  ↓
OS image: partition, install, reboot from disk
  ↓
Configuration management (e.g., Puppet): baseline state
  ↓
Functional verification: SSH, networking, disk, logs, GPUs
  ↓
Fleet inventory / ticket evidence / handoff / readiness
```

## Confidence checklist
- [ ] I can explain each box in <60 seconds.
- [ ] I can distinguish connectivity, imaging, and configuration failures.
- [ ] I can name first three commands/checks for Linux, networking, and BMC incidents.
- [ ] I can give one real example of methodical troubleshooting.
- [ ] I can tell the difference between exposure and hands-on experience.

## Reliable references
- Job listing: https://www.dice.com/job-detail/307c4e94-d04b-40d8-99c0-5191f0f98947
- Vultr bare-metal provisioning: https://docs.vultr.com/products/compute/instances/bare-metal/provisioning
- Vultr bare-metal CLI: https://docs.vultr.com/reference/vultr-cli/bare-metal
- Vultr Terraform bare-metal resource: https://docs.vultr.com/reference/terraform/resources/bare_metal_server
