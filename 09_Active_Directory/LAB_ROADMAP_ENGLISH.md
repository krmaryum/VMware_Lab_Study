# AD DS practical lab — roadmap for later

**Status: planned. No AD installation, promotion or domain join has been performed by this bundle.**

## 1. Purpose and boundaries

Build a small domain, join a member computer, create users/groups/OUs, and test a harmless policy and file permission. Learn the evidence behind each result. Preserve your existing DNS01/DNS02/DNS03 practice environment; do not silently convert its zones or reuse its machines.

Your Dell and HP desktop each have approximately 16 GB RAM. Start with two guests and avoid running the complete ESXi/DNS lab at the same time. The Surface Snapdragon laptop can be used for management; it is not the selected x64 VMware Workstation host for this plan.

## 2. Proposed machines — confirm before creating

| Name | Purpose | Starting allocation, subject to review |
|---|---|---|
| ADDC01 | New Windows Server 2025, AD DS and DNS | 2 vCPU, 4 GB RAM, 80 GB disk |
| CLIENT01 | Windows 11 Pro/Enterprise domain member | 2 vCPU, 4 GB RAM, 80 GB disk; verify TPM/Secure Boot and OS requirements |
| ADDC02 | Optional second DC, a later phase | 2 vCPU, 4 GB RAM, 80 GB disk; run only when resources permit |

These are suggested lab allocations, not universal production sizing or official minimum requirements. Windows Home cannot join a traditional AD domain. If an appropriate Windows client image is unavailable, discuss a separate Windows Server member VM instead. Do not replace a current DNS server without a deliberate migration plan.

## 3. Plan names and networking first

- Illustrative domain: `ad.lab.example`; illustrative NetBIOS name: `ADLAB`. Confirm both before promotion. These are lab examples, not an existing environment.
- Start with one isolated VMware virtual network shared by the two guests. External DNS forwarding and internet access can be added deliberately later.
- Inventory current virtual networks and routes before choosing a subnet. Previously recorded networks include home `10.0.0.0/24`, DNS VMnet2 `192.168.0.0/24`, storage `172.16.0.0/16`, and an earlier VMware network `192.168.23.0/24`; current values must be rechecked.
- Fill in the table below only after checking for overlap. The teacher’s `192.168.0.111` is an example, not a verified free address.
- AD members must use the lab AD DNS service. Do not configure a public DNS resolver as an alternate on the domain client. A gateway is required only for destinations outside the subnet; an isolated first phase may have none.

| Item | Confirmed value |
|---|---|
| VMware network / mode | Pending |
| Subnet / prefix | Pending |
| ADDC01 static IP / DNS | Pending |
| CLIENT01 IP / DNS | Pending |
| Gateway, if needed | Pending |
| Domain / NetBIOS name | Pending |

## 4. Phases and acceptance evidence

| Phase | What we will do later | Evidence of success |
|---|---|---|
| 1. Inventory | Confirm host resources, OS editions, existing VMs and networks | Approved machine and address plan |
| 2. Prepare | Create guests; set names, addresses and accurate time | OS/network output; connectivity results |
| 3. Install and promote | Install AD DS and DNS; create a new forest with a separate DSRM secret | Domain and DC visible; promotion completes |
| 4. Validate DNS | Inspect AD zones and SRV records | DC locator DNS query returns ADDC01 |
| 5. Join client | Point client DNS to AD DNS, join domain and restart | Domain membership and domain-user login confirmed |
| 6. Organize | Create test OU, user and security groups | Objects appear in correct location |
| 7. Policy | Link a harmless test policy to a test OU | `gpresult` shows expected applied GPO |
| 8. Permissions | Grant access through a group; test allowed and denied users | Both positive and negative access results |
| 9. Optional redundancy | Add ADDC02 and examine DNS/AD replication | Replication health and controlled login/DNS tests |
| 10. Recovery preparation | Plan supported backup and restoration | Backup scope, recovery goals and recovery procedure recorded |

Adding the role is not the same as promoting a server to a DC. AD-integrated writable DNS on two writable DCs differs from the standard primary/secondary/stub DNS exercise you already studied. A second DC improves availability but does not replace backup. A VM snapshot is not the complete AD recovery plan.

## 5. Evidence commands — read only

Inventory now, if needed:

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object Manufacturer, Model, TotalPhysicalMemory
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version, OSArchitecture
Get-NetIPConfiguration
Get-NetRoute -AddressFamily IPv4
```

Only after deployment, on a suitable machine with the necessary tools and permissions:

```powershell
Resolve-DnsName -Type SRV _ldap._tcp.dc._msdcs.ad.lab.example
whoami
whoami /groups
gpresult /r
```

Domain names above must match the domain actually created. Later, `dcdiag` and `repadmin /replsummary` will help check DC health and replication. No FSMO seizure, schema modification or broad security-policy changes are part of this beginner lab.

## 6. Record each practical session

Use: objective → initial state → command/action → actual output or screenshot → interpretation → validation → remaining work. Record planned and completed work separately. Keep passwords, DSRM secrets and private keys out of notes and GitHub.

All practical evidence is currently **pending**. Return to [README](README.md).
