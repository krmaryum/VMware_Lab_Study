# VMware Lab Study

A topic-wise study collection with detailed lab notes, screenshots and revision material in English and Roman Urdu.

## Suggested Study Sequence

[Networking foundations](00_Networking_Foundations/README.md) → [virtualization foundations](01_Foundations/README.md) → [lab setup](02_Lab_Setup/README.md) → [ESXi installation](03_ESXi_Installation/README.md) → [ESXi networking](04_ESXi_Networking/README.md) → [DNS](05_DNS/README.md) → [storage](06_ESXi_Storage/README.md) → [vCenter and VM management](07_vCenter_and_VM_Management/README.md). Use troubleshooting whenever a relevant problem arises.

## Study and Lab Status

Status describes the documented scope, not every possible task in the subject. **Completed** means the named record or configuration is available; **In progress** means recorded work exists with specific verification still pending; **Planned** means no completed practical chapter is claimed.

| Section | Status | Documented checkpoint / remaining work |
|---|---|---|
| [00. Networking Foundations](00_Networking_Foundations/README.md) | Completed — study notes available; practice pending | Networking theory, platform commands, VMware concept mapping and evidence worksheet available; practical results not yet recorded |
| [01. Foundations](01_Foundations/README.md) | Completed — notes available | Encapsulation, payload and revision material documented; further learning continues |
| [02. Lab Setup](02_Lab_Setup/README.md) | Completed — recorded scope | File transfer and passwordless SSH procedures/results available |
| [03. ESXi Installation](03_ESXi_Installation/README.md) | Completed — recorded scope | Desktop installation sequence and evidence available |
| [04. ESXi Networking](04_ESXi_Networking/README.md) | In progress | Switch/port-group/VMkernel configuration recorded; controlled failover, guest connectivity and shaping measurements pending |
| [05. DNS Services](05_DNS/README.md) | In progress | Primary, secondary, stub and DNS03 suffix verified; conditional-forwarder resolution and trust-anchor/DNSSEC validation pending |
| [06. ESXi Storage](06_ESXi_Storage/README.md) | Planned — full practical lab | Existing VMFS checkpoint/reference material available; complete protocol/datastore lab pending |
| [07. vCenter and VM Management](07_vCenter_and_VM_Management/README.md) | Planned | Reserved for deployment and administration labs |
| [08. Troubleshooting](08_Troubleshooting/README.md) | Completed — existing case records | Historical connection/incident cases documented; new cases can be added |

**DNS03 Server 2025:** a [dedicated installation and readiness checkpoint](05_DNS/Server_Installation/DNS03_Windows_Server_2025/README.md) is now included. It distinguishes verified later Server 2025 evidence from the archived Windows 11 build and identifies missing installation-stage captures.

## [00. Networking Foundations](00_Networking_Foundations/README.md)

Networking basics, OSI/TCP-IP, addressing, subnetting, protocols, platform command differences and VMware lab connections.

[Open topic guide](00_Networking_Foundations/README.md)

## [01. Virtualization Foundations](01_Foundations/README.md)

ESXi encapsulation, payload study material and revision questions.

[Open topic guide](<01_Foundations/README.md>)

## [02. Lab Setup](02_Lab_Setup/README.md)

Remote access, file transfer and passwordless SSH reference material.

[Open topic guide](<02_Lab_Setup/README.md>)

## [03. ESXi Installation](03_ESXi_Installation/README.md)

The recorded desktop installation sequence, screenshots and diagnostic outputs.

[Open topic guide](<03_ESXi_Installation/README.md>)

## [04. ESXi Networking](04_ESXi_Networking/README.md)

Reviewed networking chapters with English and Roman Urdu notes and lab evidence.

[Open topic guide](<04_ESXi_Networking/README.md>)

## [05. DNS Services](05_DNS/README.md)

Foundations, installation records, DNS suffix, primary, secondary, stub, conditional forwarding and DNSSEC.

[Open topic guide](<05_DNS/README.md>)

## [06. ESXi Storage](06_ESXi_Storage/README.md)

Storage reference material and the next planned practical chapter.

[Open topic guide](<06_ESXi_Storage/README.md>)

## [07. vCenter and VM Management](07_vCenter_and_VM_Management/README.md)

A reserved section for future vCenter and VM administration labs.

[Open topic guide](<07_vCenter_and_VM_Management/README.md>)

## [08. Troubleshooting](08_Troubleshooting/README.md)

Historical connection labs and incident records, organized by case.

[Open topic guide](<08_Troubleshooting/README.md>)

## [Additional Resources](Additional_Resources/README.md)

Class PDFs, visual revision material, interview preparation and optional practice exercises.

[Browse Additional Resources](Additional_Resources/README.md)

## [Archive](Archive/README.md)

Previous editions, original chapter ZIPs and historical setups remain available for reference. The main study folders provide the current reading route.

[Browse Archive](Archive/README.md)

## Using the Collection

On GitHub, open this README and select any linked section in the status table or topic headings. For an offline copy, extract the full collection and open this README in a Markdown viewer. Keep the folder structure intact so relative links work. Each guide groups resources by subject with short, readable link labels. Detailed chapter text and screenshot explanations are retained.

Storage and vCenter sections include planned work rather than completed implementation claims. DNSSEC validation and KRNC.org resolution remain unverified in the DNS record. Chapter-specific notes state the evidence limits.

[File Move Map](FILE_MOVE_MAP.csv) | [Earlier Link Review](LINK_REVIEW.md)
