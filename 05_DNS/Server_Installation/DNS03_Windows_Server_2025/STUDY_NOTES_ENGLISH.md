# DNS03 — Windows Server 2025: installation transition and DNS readiness

## Purpose of this chapter

DNS03 initially used Windows 11. That operating system provided a client desktop and network tests, but it was not the Windows Server DNS-role platform needed for the server lab. DNS03 was subsequently rebuilt with Windows Server 2025. This chapter supplies the missing place in the installation section and connects that rebuild to the later, documented DNS configuration.

The supplied organized archive did not contain a separate Server 2025 setup walkthrough for DNS03. Accordingly, this is a **retrospective installation/readiness checkpoint**, not a claim that every installer page was captured. The existing Windows 11 record remains historical and its VM resources, ISO and VM file path are not automatically assigned to the replacement server.

## Recorded replacement-server identity

| Item | Recorded value / evidence limit |
|---|---|
| Guest hostname | DNS03 |
| Operating system | Windows Server 2025 Standard, Desktop Experience, recorded in the suffix notes |
| Membership | WORKGROUP; suffix configuration is not an AD join |
| Network | VMnet2 host-only, 192.168.0.0/24 in the configuration record |
| IPv4 / mask | 192.168.0.123 / 255.255.255.0 |
| Gateway | Blank in the isolated lab |
| Preferred / alternate DNS | 192.168.0.123 / 192.168.0.121 in DNS03 suffix evidence |
| Primary DNS suffix | VCPLAB.Com |
| Full computer name | DNS03.VCPLAB.Com |
| Later DNS role | DNS03 hosts the forward/reverse stub zones documented in the configuration chapter |
| Exact replacement ISO path and VM storage path | Not established by the available replacement-server captures |
| Replacement VM RAM, CPU and disk allocation | Not independently established here; do not reuse the Windows 11 values as proof |

## Installation transition in context

1. The earlier Windows 11 build was the first DNS03 attempt. It is retained under Archive/Historical_VM_Setups/DNS03_Windows_11.
2. The replacement used Windows Server 2025 with the graphical Desktop Experience. Server Manager is available in the later capture.
3. The guest identity and isolated static-network settings were established for DNS03. The later ipconfig evidence verifies the resulting configuration, rather than every intermediate dialog.
4. The DNS Server role was available by the later stub-zone exercise. The original add-role wizard sequence is not supplied here, so the subsequent working configuration is the evidence of that later role state.
5. Primary suffix and later DNS operation were verified in their own configuration exercises. These are linked below rather than replacing the detailed steps already present.

## Checkpoint 1 — Server Manager and server identity

The existing DNS03 suffix workflow begins in Server Manager → Local Server. This is later server-readiness evidence; it is not an installer screenshot.

![DNS03 Server Manager, copied from suffix lab](screenshots/01_server_manager.png)

The computer properties capture belongs to that same DNS03 suffix workflow. The source notes identify the operating system as Windows Server 2025 Standard (Desktop Experience).

![DNS03 computer properties from suffix lab](screenshots/02_system_properties.png)

## Checkpoint 2 — Naming and network result

The recorded suffix was applied through Computer Name → Change → More and followed by restart. The final command was:

```powershell
ipconfig /all
```

The result records DNS03, VCPLAB.Com, static .123/24 and the configured DNS addresses. The primary suffix forms the naming context; it does not join Active Directory, create a DNS zone or prove every record was registered.

![DNS03 ipconfig after suffix restart](screenshots/03_ipconfig_after_suffix.png)

The complete [DNS Suffix procedure](../../Configuration/06_DNS_Suffix/STUDY_NOTES_ENGLISH.md) preserves all seven captured stages. This checkpoint does not claim that all firewall profiles, Windows activation, VMware Tools or the exact replacement VM hardware allocation were verified.

## Checkpoint 3 — DNS-role readiness and later stub configuration

The later configuration record shows VCPLAB.Com as a stub on DNS03, holding limited authority information rather than a complete secondary copy.

![DNS03 forward stub records](screenshots/04_forward_stub_records.png)

Direct forward and reverse tests through .123 were recorded:

```powershell
nslookup dns02.vcplab.com. 192.168.0.123
nslookup 192.168.0.122 192.168.0.123
```

![Recorded DNS03 lookup results](screenshots/05_stub_lookup_results.png)

These successful results establish later DNS service operation through DNS03. They do not prove primary-outage recovery or the exact upstream packet path. The full [Stub DNS chapter](../../Configuration/03_Stub_DNS/STUDY_NOTES_ENGLISH.md) retains the detailed configuration and its limitations.

## Reusable inventory for a repeat installation

The following commands are a future verification checklist, not newly captured outputs. Inside the replacement Windows Server, an elevated PowerShell session can record identity, OS, addressing and role state:

```powershell
hostname
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture
ipconfig /all
Get-WindowsFeature DNS
Get-Service DNS -ErrorAction SilentlyContinue
```

`Installed` for the role and a running service answer different questions from an actual successful query. For a full repeat build, capture the replacement ISO/edition, VM path and hardware, installer disk selection, first successful Server Manager session, rename/restart, adapter settings and add-role result. Do not reuse Windows 11 screenshots to illustrate Server 2025 installer choices.

## Status summary

- Completed in the later record: Server 2025 DNS03 identity, suffix/network checkpoint, stub configuration and direct lookups.
- Missing as a separate historical walkthrough: replacement Windows Setup pages and DNS role-installation wizard pages.
- Not claimed: AD membership, full DNSSEC validation, primary-outage recovery or live retesting during documentation.
