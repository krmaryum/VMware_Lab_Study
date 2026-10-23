# Quick Revision — Vultr Server Onboarding Specialist
**Review in the final 20 minutes (not as a replacement for the phases).**

## The golden flow
**Rack/power → BMC → BIOS/firmware → storage → network/DHCP/PXE → OS → Puppet → validation → inventory → release.**

## Fast definitions
| Term | One-sentence meaning |
|---|---|
| BMC | Independent hardware management controller |
| IPMI | Standardized hardware management interface family |
| iDRAC | Dell's remote management implementation |
| Out-of-band | Management access independent of host OS availability |
| BIOS/UEFI | Firmware that initializes hardware and launches boot process |
| RAID 1 | Mirrors data across disks for redundancy |
| JBOD | Makes disks available individually rather than as one RAID array |
| VLAN | Logical Layer-2 segment |
| DHCP | Dynamic network configuration and possible boot hints |
| PXE | Network boot mechanism used for automated provisioning |
| iPXE | Enhanced network-boot environment with HTTP and scripting support |
| Puppet | Declarative configuration management/catalog application |
| DOA | Hardware unusable at intake; confirm cause first |
| RMA | Return merchandise authorization/vendor replacement workflow |

## Command memory
```bash
ip -br a                 # IP addresses
ip route                 # Routes
ip -br link              # Interface/link state
lsblk -f                 # Disks/filesystems
df -hT                   # Filesystem space
free -h                  # Memory
systemctl --failed       # Failed units
journalctl -p err -b     # Boot errors
lspci                    # PCI devices
hostnamectl              # Host identity
```

## Interview sentence starters
- “First, I identify which stage failed.”
- “I verify against the approved runbook before changing hardware or storage settings.”
- “The BMC management path is separate from normal host connectivity.”
- “I capture the exact asset, timestamp, console error and logs before escalation.”
- “I consider onboarding complete only after validation and inventory are both updated.”
- “I haven't used that in production yet; here's the closest relevant experience and how I'd approach it.”

## Never do this
- Don't claim production iDRAC/Puppet experience you don't have.
- Don't reset, reinstall or reconfigure RAID without approval.
- Don't publish credentials in tickets or spreadsheets.
- Don't call a server production-ready because only the installer succeeded.
- Don't troubleshoot everything at once; identify the failure stage.

## Core sources
https://www.dice.com/job-detail/307c4e94-d04b-40d8-99c0-5191f0f98947
https://docs.vultr.com/products/compute/instances/bare-metal/provisioning
