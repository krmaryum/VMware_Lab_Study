# Phase 02 — BMC, IPMI, BIOS/UEFI, Firmware, RAID/JBOD
**Priority: Critical | Study: Sunday**

## 1. BMC / IPMI / iDRAC
A **BMC** is a dedicated embedded management controller. **IPMI** is a management interface/protocol family; **iDRAC** is Dell's implementation. Comparable interfaces include HPE iLO and various vendor BMC UIs. BMC lets authorized operators inspect hardware, remotely power cycle, and use a console even if the OS does not boot.

**Out-of-band vs in-band:** BMC typically uses a separate management path and credentials; SSH relies on the host OS/network being alive. Management access must be tightly controlled.

### BMC check sequence
1. Confirm expected BMC IP and management VLAN, access policy, and credentials **without storing secrets in tickets**.
2. Confirm BMC responds using approved network tests and UI/API.
3. Check power state, SEL/event logs, fan, thermal, PSU, RAM and disk alerts.
4. Use remote console/virtual media if workflow authorizes it.
5. Record model/serial/BMC identity and result.

### Example commands (only if installed/authorized)
```bash
# In Linux, local IPMI interface may require ipmitool and working BMC drivers
sudo ipmitool mc info
sudo ipmitool sensor list
sudo ipmitool sel list
# Avoid remote plaintext password flags and unauthorized power commands.
```

## 2. BIOS vs UEFI
BIOS/UEFI initializes hardware and chooses the boot path. Modern UEFI supports configurable boot entries and secure boot policies. Common onboarding checks: boot mode, PXE boot ordering, virtualization setting if required, CPU profiles, storage-controller mode, and hardware clock.

## 3. Firmware
Firmware is low-level software in components (BMC, system BIOS, NIC, RAID controller, drives). Check **approved** versions and vendor compatibility. Updates should follow change control; flashing the wrong image or interrupting power risks an outage.

## 4. RAID and JBOD
| Mode | What it means | Tradeoff |
|---|---|---|
| RAID 0 | Data striped across disks | Performance/capacity, no redundancy |
| RAID 1 | Mirrored disks | Can survive one disk failure in a two-disk mirror; reduced usable capacity |
| RAID 5 | Striping with single distributed parity | Can tolerate one disk failure; write penalty/rebuild risk |
| RAID 10 | Striped mirrors | Performance + redundancy; fewer usable bytes |
| JBOD / HBA passthrough | Individual disks passed to OS | Useful for software-defined storage, no automatic mirroring |

**Important:** RAID is not a backup. Storage mode must follow the target workload/runbook; do not assume RAID 1 is always best. Vultr's public provisioning options include RAID 1 and no-RAID choices.

## 5. What to do when BMC is unreachable
- Verify correct IP/asset, management network path and access permissions.
- Check from approved jump host; confirm ping (if allowed), HTTPS and route behavior.
- Ask network team to confirm switch-port state/VLAN; ask remote hands to verify cable, PSU, BMC state if warranted.
- Gather timestamps and evidence before any approved BMC reset; avoid blind power cycling.

## 6. Scenario Q&A
**Q: Remote console opens but OS won't boot. What next?** Inspect POST messages, disk and boot device detection, firmware error logs, boot mode and UEFI order, then locate failure boundary (hardware, bootloader, OS). Escalate based on evidence.

**Q: Two disks found instead of four?** Compare inventory/controller/BMC and OS, inspect controller mode and drive bays, record missing serials, involve remote hands or hardware team before imaging.

**Q: You have never used iDRAC in production?** Say: “I understand the purpose of out-of-band management and the health, console and power functions. My direct hands-on exposure has been [truthful scope], and I can follow the team's approved runbook to validate the BMC and escalate faults.”

## 7. Practice
- [ ] Explain BMC vs SSH and IPMI vs iDRAC.
- [ ] Explain RAID 1 vs JBOD in 30 seconds.
- [ ] State safe steps for a firmware change.
- [ ] Troubleshoot unreachable BMC with evidence and escalation.

**Roman Urdu:** BMC se hum OS chal raha ho ya nahin, hardware health aur remote console check kar sakte hain. RAID redundancy de sakta hai; JBOD mein disks separately nazar aati hain. Har setting company ke runbook ke mutabiq honi chahiye.
