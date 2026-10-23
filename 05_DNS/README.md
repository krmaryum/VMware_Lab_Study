# DNS Services

Study notes, server installation records and configuration labs, organized by topic. Each configuration topic has separate English and Roman Urdu notes.

## Suggested study sequence

DNS foundations → server installation → DNS suffix → primary DNS → secondary DNS → stub DNS → conditional forwarding → Trust Points and DNSSEC.

The topic numbers below match the existing folders. DNS Suffix can be read first even though its folder is numbered 06.

## DNS Configuration Topics

### 01. Primary DNS

Writable authoritative zones, host records, reverse lookups and aliases.

[English](<Configuration/01_Primary_DNS/STUDY_NOTES_ENGLISH.md>) | [Roman Urdu](<Configuration/01_Primary_DNS/STUDY_NOTES_ROMAN_URDU.md>)

### 02. Secondary DNS

Read-only zone copies, transfer permissions and replication verification.

[English](<Configuration/02_Secondary_DNS/STUDY_NOTES_ENGLISH.md>) | [Roman Urdu](<Configuration/02_Secondary_DNS/STUDY_NOTES_ROMAN_URDU.md>)

### 03. Stub DNS

Records that identify authoritative DNS servers and support name resolution.

[English](<Configuration/03_Stub_DNS/STUDY_NOTES_ENGLISH.md>) | [Roman Urdu](<Configuration/03_Stub_DNS/STUDY_NOTES_ROMAN_URDU.md>)

### 04. Conditional Forwarding

Rules that send queries for a specific domain to selected DNS servers.

[English](<Configuration/04_Conditional_Forwarding/STUDY_NOTES_ENGLISH.md>) | [Roman Urdu](<Configuration/04_Conditional_Forwarding/STUDY_NOTES_ROMAN_URDU.md>)

### 05. Trust Points and DNSSEC

Digital signatures, trusted anchors, zone signing and unsigning.

[English](<Configuration/05_Trust_Points_DNSSEC/STUDY_NOTES_ENGLISH.md>) | [Roman Urdu](<Configuration/05_Trust_Points_DNSSEC/STUDY_NOTES_ROMAN_URDU.md>)

### 06. DNS Suffix

Primary DNS suffix configuration and verification of the full computer name.

[English](<Configuration/06_DNS_Suffix/STUDY_NOTES_ENGLISH.md>) | [Roman Urdu](<Configuration/06_DNS_Suffix/STUDY_NOTES_ROMAN_URDU.md>)

## Recorded Lab Status

| Topic | Recorded result |
| --- | --- |
| Primary DNS | Host, reverse and alias resolution verified in the configuration notes |
| Secondary DNS | Forward and reverse queries against DNS02 passed |
| Stub DNS | Forward and reverse queries through DNS03 passed |
| Conditional forwarding | KRNC.org rule saved; target reachability and resolution unverified |
| Trust Points and DNSSEC | Zone signed and then unsigned; trust-anchor import and validation not demonstrated |
| DNS suffix | DNS03 suffix verified after restart; DNS02 suffix not independently verified |

## Foundations and Supporting Concepts

- [DNS Before VMware Storage — English](<Foundations/DNS_Before_VMware_Storage_Study_Notes_English.md>)
- [DNS Before VMware Storage — Roman Urdu](<Foundations/DNS_Before_VMware_Storage_Study_Notes_Roman_Urdu.md>)
- [Subnet Masks — English](<Foundations/Subnet_Mask_Study_Notes_English.md>)
- [DNS History and Foundations — English](<Server_Installation/DNS01_Windows_Server_2025/DNS_History_and_Foundations/DNS_Study_Notes_English.md>)
- [DNS History and Foundations — Roman Urdu](<Server_Installation/DNS01_Windows_Server_2025/DNS_History_and_Foundations/DNS_Study_Notes_Roman_Urdu.md>)
- [DNS History and Foundations — Overview](<Server_Installation/DNS01_Windows_Server_2025/DNS_History_and_Foundations/README.md>)

## Server Installation and Earlier Lab Notes

These resources preserve the installation sequence and earlier study material. Use the configuration topics above for the consolidated DNS configuration record.

### DNS01 — Windows Server 2025

- [Installation Part 1 — Overview](<Server_Installation/DNS01_Windows_Server_2025/DNS_History_and_Foundations/Installation_Part1/Windows_Server_2025_DNS_Lab_Part1_English/README.md>)
- [Installation Part 1 — English Notes](<Server_Installation/DNS01_Windows_Server_2025/DNS_History_and_Foundations/Installation_Part1/Windows_Server_2025_DNS_Lab_Part1_English/STUDY_NOTES_ENGLISH.md>)
- [Earlier Step-by-Step Notes — English](<Server_Installation/DNS01_Windows_Server_2025/DNS_Step_by_Step/DNS_ENGLISH.md>)
- [Earlier Step-by-Step Notes — Roman Urdu](<Server_Installation/DNS01_Windows_Server_2025/DNS_Step_by_Step/DNS_ROMAN_URDU.md>)
- [Earlier Step-by-Step Notes — Overview](<Server_Installation/DNS01_Windows_Server_2025/DNS_Step_by_Step/README.md>)
- [Quick Reference](<Server_Installation/DNS01_Windows_Server_2025/QUICK_REFERENCE.md>)
- [Installation Overview](<Server_Installation/DNS01_Windows_Server_2025/README.md>)
- [Installation Notes — English](<Server_Installation/DNS01_Windows_Server_2025/STUDY_NOTES_ENGLISH.md>)
- [Installation Notes — English and Roman Urdu](<Server_Installation/DNS01_Windows_Server_2025/STUDY_NOTES_EN_ROMAN_URDU.md>)

### DNS02 — Windows Server 2016

- [Installation Overview](<Server_Installation/DNS02_Windows_Server_2016/README.md>)
- [Screenshot Index](<Server_Installation/DNS02_Windows_Server_2016/SCREENSHOT_INDEX.md>)
- [Installation Notes — English](<Server_Installation/DNS02_Windows_Server_2016/STUDY_NOTES_ENGLISH.md>)
- [Installation Notes — Roman Urdu](<Server_Installation/DNS02_Windows_Server_2016/STUDY_NOTES_ROMAN_URDU.md>)

## Configuration Overview and Review

- [Configuration Bundle Overview](<Configuration/README.md>)
- [Configuration Review Notes](<Configuration/REVIEW_NOTES.md>)

## Using These Notes

Select a topic, read its explanation, follow the detailed lab record and review the screenshots and verification results. Quick references support revision after the main notes.

Keep this file named `START_HERE.md` inside `05_DNS`. The links use the existing relative folder paths. Related screenshots and notes should remain together.
