# VMware Workstation & Nested ESXi Troubleshooting Lab

Practical study notes based on a classmate’s nested ESXi case, plus a checklist of common related issues.

## Start here

1. [Chapter 01 — CPU virtualization (English)](01_CPU_Virtualization/README_EN.md)
2. [Chapter 01 — CPU virtualization (Roman Urdu)](01_CPU_Virtualization/README_ROMAN_URDU.md)
3. [English case study](02_Nested_Virtualization/README_EN.md)
4. [Roman Urdu case study](02_Nested_Virtualization/README_ROMAN_URDU.md)
5. [Common troubleshooting topics](TROUBLESHOOTING_INDEX.md)

> Lab note: A VM that runs a normal guest OS does **not** ordinarily require the VMware Workstation **Virtualize Intel VT-x/EPT or AMD-V/RVI** guest setting. A VM that runs a hypervisor such as ESXi, Hyper-V, or KVM generally does. Host firmware virtualization is a separate prerequisite.

> Evidence: The classmate reported that enabling nested virtualization resolved the error. The screenshot does not independently establish the exact text of the original error.

## Evidence images

- `assets/nested_virtualization_unchecked.jpg` — Workstation processor settings, nested virtualization unchecked.
- Classmate chat screenshot excluded from the shareable bundle because it displays personal contact information.
- `assets/esxi_boot_modules.jpg` — ESXi boot modules loading; not by itself an error.

## Safety

Do not disable Windows security protections such as VBS or Memory Integrity without evaluating security impact. Shut down a VM before changing its processor virtualization settings. Back up VM files before invasive repairs.
