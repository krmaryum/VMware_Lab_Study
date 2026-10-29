# Networking Foundations for the VMware Home Lab

Start here before the ESXi networking, DNS and AD DS practical chapters.

## Study order

1. [Complete networking study notes](Networking-Complete-Roadmap-Study-Notes.md): basics, devices, OSI/TCP-IP, IP addressing, subnetting, ports, DNS, TCP/UDP, SSH/SCP and troubleshooting.
2. [Windows, Linux and ESXi platform differences](PLATFORM_COMMANDS.md): select commands for the machine you are actually using.
3. [Connect the concepts to your VMware lab](VMWARE_LAB_CONNECTIONS.md): VMnet, vSwitch, vmnic, VMkernel and port groups.
4. [Practice and evidence worksheet](PRACTICE_AND_EVIDENCE.md): record observations before making changes.
5. [Roman Urdu reading guide](READING_GUIDE_ROMAN_URDU.md): short navigation and key distinctions; the full original theory guide is in English.

## Status

| Item | Status |
|---|---|
| General networking theory | Included |
| Platform command guide | Included |
| VMware concept mapping | Included |
| Practical exercises | Ready to attempt; outputs not yet recorded |
| New network configuration changes | None performed by this bundle |

## Original reference

[Original Networking PDF](reference/Networking_Original.pdf) is preserved unchanged. The Markdown edition includes additional clarifications and companion guides that are not in that PDF.

## Add to your repository

Copy this whole `00_Networking_Foundations` folder into the root of `VMware_Lab_Study`. Keep the companion files together. If this folder already exists, compare and merge the contents rather than deleting existing material. If `00` is already assigned to another topic, choose an unused number and adjust the main index link.

Add this entry to the repository's main README:

- [Networking Foundations](00_Networking_Foundations/README.md)

- [Protocol — Brief Study Note (English)](Protocol_Brief_Study_Note_English.md)
- [Protocol — Brief Study Note (Roman Urdu)](Protocol_Brief_Study_Note_Roman_Urdu.md)


ESXi-specific configuration remains in your existing ESXi networking section. This folder provides foundational reading and cross-platform observation, not a replacement for your screenshot-based practical chapters.
