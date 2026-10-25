# AD DS practical lab — baad mein karne ka roadmap

**Status: planned. Is bundle se AD install, DC promotion ya domain join nahin hua.**

## 1. Maqsad aur scope

Aik chhota domain banayein ge, member computer join karein ge, phir users, groups, OUs, harmless GPO aur file permissions test karein ge. Har result ka evidence samjhein ge. Aapka DNS01/DNS02/DNS03 wala existing lab alag rahega; uski machines ya zones ko baghair plan convert nahin karein ge.

Dell aur HP desktop mein taqreeban 16 GB RAM hai. Pehle do guests se shuru karein ge; tamam ESXi aur DNS VMs saath chalane se memory kam ho sakti hai. Snapdragon Surface management ke liye use ho sakta hai; is plan ka x64 Workstation host nahin hai.

## 2. Proposed machines — pehle confirm karein ge

| Naam | Kaam | Shuruati suggested resources |
|---|---|---|
| ADDC01 | Naya Windows Server 2025, AD DS aur DNS | 2 vCPU, 4 GB RAM, 80 GB disk |
| CLIENT01 | Windows 11 Pro/Enterprise domain member | 2 vCPU, 4 GB RAM, 80 GB disk; TPM/Secure Boot aur OS requirements check hon gi |
| ADDC02 | Baad mein optional doosra DC | 2 vCPU, 4 GB RAM, 80 GB disk; available resources dekh kar |

Yeh lab suggestions hain, official minimum ya production sizing nahin. Windows Home traditional AD domain join nahin karta. Suitable client ISO na ho to alag Windows Server member VM discuss kar sakte hain. Existing DNS server ko baghair migration plan replace na karein.

## 3. Naam aur network ka plan

- Example domain `ad.lab.example` aur NetBIOS naam `ADLAB` hai. Promotion se pehle final naam confirm hoga; yeh aapka existing domain nahin.
- Pehle dono guests aik isolated VMware network par hon ge. Internet access aur DNS forwarding baad mein deliberate plan se add hon ge.
- Subnet chunne se pehle current routes aur VMware networks check hon ge. Pehle record hue networks: home `10.0.0.0/24`, DNS VMnet2 `192.168.0.0/24`, storage `172.16.0.0/16`, aur aik purana VMware network `192.168.23.0/24`. Current values dobara verify hon gi.
- Teacher ka `192.168.0.111` example hai; free IP ka proof nahin.
- Domain members AD DNS use karein ge. Client mein public DNS ko alternate na lagayein. Gateway doosre subnet tak jane ke liye hota hai; isolated pehle phase mein zaroori nahin ho sakta.

| Cheez | Confirmed value |
|---|---|
| VMware network / mode | Abhi pending |
| Subnet / prefix | Abhi pending |
| ADDC01 static IP / DNS | Abhi pending |
| CLIENT01 IP / DNS | Abhi pending |
| Gateway, agar chahiye | Abhi pending |
| Domain / NetBIOS naam | Abhi pending |

## 4. Phases aur evidence

| Phase | Baad mein kya karein ge | Kamyabi ka evidence |
|---|---|---|
| 1. Inventory | Resources, OS editions, VMs aur networks confirm | Final machine aur IP plan |
| 2. Preparation | Guests, names, static network aur time | OS/network output aur connectivity |
| 3. Installation/promotion | AD DS aur DNS role, phir naya forest | DC/domain nazar aaye, promotion complete |
| 4. DNS verification | AD zones aur SRV records check | DC locator query ADDC01 return kare |
| 5. Domain join | Client DNS set, domain join aur restart | Membership aur domain-user login |
| 6. Organization | Test OU, user aur security groups | Objects sahi jagah par |
| 7. GPO | Test OU par harmless policy | `gpresult` mein expected GPO |
| 8. Permissions | Group ke zariye access | Allowed aur denied dono users ka test |
| 9. Optional redundancy | ADDC02 add, replication check | Health output aur controlled tests |
| 10. Recovery plan | Supported backup aur recovery planning | Backup scope aur recovery procedure |

Role install karna aur DC promote karna alag steps hain. Do writable DCs par AD-integrated DNS, standard primary/secondary/stub DNS exercise se mukhtalif hai. Doosra DC backup ka replacement nahin. Sirf VM snapshot complete AD recovery plan nahin hota.

## 5. Read-only evidence commands

Zaroorat ho to ab inventory ke liye:

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object Manufacturer, Model, TotalPhysicalMemory
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version, OSArchitecture
Get-NetIPConfiguration
Get-NetRoute -AddressFamily IPv4
```

Sirf deployment ke baad, suitable tools aur permissions wali machine par:

```powershell
Resolve-DnsName -Type SRV _ldap._tcp.dc._msdcs.ad.lab.example
whoami
whoami /groups
gpresult /r
```

Query ka domain actual banaye hue domain se match hona chahiye. Baad mein `dcdiag` aur `repadmin /replsummary` se DC health aur replication samjhein ge. Beginner lab mein FSMO seizure, schema changes ya broad security-policy changes nahin hon ge.

## 6. Har practical ka record

Maqsad → pehle ki state → command/action → actual output/screenshot → matlab → verification → baqi kaam. Planned aur completed alag likhein. Password, DSRM secret aur private keys notes/GitHub mein na rakhein.

Tamam practical evidence abhi **pending** hai. [README](README.md) par wapas jayein.
