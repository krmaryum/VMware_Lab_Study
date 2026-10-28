# User and Group Management to Active Directory Domain Services

English study notes | Class companion | 27 October 2026

These notes combine your typed class points with the supplied 22-page AD DS lecture. They follow the learning order: identities → local accounts → directory services → protocols → login → structure → administration. Examples are illustrative unless explicitly described as earlier lab records. No AD deployment or configuration change is claimed.

## Contents

1. Users, groups and permissions
2. SAM and workgroup accounts
3. AD DS, domain and Domain Controller
4. LDAP and Linux directory integration
5. VMware SSO
6. Novell and the history notes
7. Windows Server and the AD service family
8. Topology, workgroup and domain
9. Azure AD, Entra ID and hybrid identity
10. UPN and DOMAIN\username
11. AD objects and directory structure
12. Kerberos login and resource access
13. Database partitions, SYSVOL and replication
14. FSMO roles
15. Schema version and functional levels
16. Readiness, tools, revision and future lab

Read sections 1–5 first. Take a break, then continue with the naming and history sections. Use the original PDF alongside the topic map, rather than treating every slide shorthand as a literal definition.

---

## 1. Users, groups and permissions

A **user account** represents an identity. It may belong to a person or be used for a service. A **security group** collects identities so administrators can assign permissions consistently.

Example: Ali and Aisha work in Accounts. Add their accounts to an Accounts security group and assign the appropriate folder permission to that group. When another colleague joins, group membership can provide the intended access without editing the folder for every person.

| Concept | Question | Example |
|---|---|---|
| Identity management | Who has an account and what is its lifecycle? | Create Ali, later disable his account |
| Authentication | Who are you? | Validate Ali's sign-in |
| Authorization | What may you do? | Permit reading Accounts; deny writing Payroll |

Group membership alone does not create access: the relevant permission or application role must refer to that group. Some groups distribute messages rather than grant security access. User/group management also involves account naming, membership reviews, password policies, disabling unused accounts and keeping administrative privileges appropriate.

A successful login does not guarantee access to every folder. The resource server and its access controls participate in authorization.

---

## 2. SAM and workgroup accounts

**SAM = Security Accounts Manager.** On ordinary Windows workstations and member servers, SAM manages local account information. In a workgroup, each machine manages its own local identities.

```text
DELL\Ali
DESKTOP\Ali
```

These are separate accounts, even if their usernames/passwords match. A password change on Dell does not automatically update the desktop. A local account can be used to authenticate to an allowed share on the computer that owns that account; a workgroup does not prevent networking.

SAM is not exclusive to workgroup computers: an ordinary domain member can retain local accounts alongside domain-account sign-in. Domain controllers use AD DS for ordinary domain accounts; do not apply the workstation local-user model directly to a DC.

| Workgroup misconception | Correct understanding |
|---|---|
| Same workgroup name means access | Credentials, network connectivity and permissions still matter |
| Same username means same identity | The account authority/computer matters |
| Workgroup means no communication | File sharing and other network services can still work |

---

## 3. AD DS, domain and Domain Controller

**AD DS = Active Directory Domain Services.** It is Microsoft's directory service for domain identities and related directory information. A directory stores objects and their attributes in an organized hierarchy.

An **AD domain** is an administrative and replication unit within a forest. It contains users, computers and other objects. A **Domain Controller (DC)** runs AD DS and holds directory data. Installing Windows Server alone does not make it a DC: role installation and DC promotion are distinct steps.

Example: `VCPLAB\Ali` can represent one domain account usable on permitted domain-joined computers. Sign-in rights and resource permissions still apply.

| Term | Think of it as |
|---|---|
| Windows Server | Operating system |
| AD DS | Directory service/role |
| Domain | Directory organization and identity scope |
| DC | Server providing the directory services |
| NTDS.dit | AD DS database file on a DC |

Multiple writable DCs replicate changes. A domain is not automatically a single-server design. A member computer's local account remains distinct from a same-named domain account.

---

## 4. LDAP and Linux directory integration

**LDAP = Lightweight Directory Access Protocol. It is a protocol, not a database product.** A directory server stores information; LDAP defines how clients access directory entries, subject to authentication and access controls.

| Category | Example |
|---|---|
| Protocol | LDAP |
| Directory implementation | OpenLDAP; AD DS also supports LDAP |
| Local Linux account information | `/etc/passwd`, `/etc/shadow`, `/etc/group` |
| Directory client integration | May use components such as SSSD, depending on design |

Linux does not require LDAP for every login. It can use local accounts or a configured central identity service. LDAP is not exclusive to Linux. A directory entry might contain a user's name, mail attribute and group-related information; that does not mean the directory stores that user's actual email messages.

### The “389” in your notes

| Protocol/service | Usual port | Meaning |
|---|---|---|
| LDAP | TCP 389 | Directory access; StartTLS can protect an appropriately configured connection |
| LDAPS | TCP 636 | LDAP with TLS from connection establishment |
| Kerberos | TCP/UDP 88 | Domain authentication |
| DNS | TCP/UDP 53 | Domain-service discovery and name resolution |

AD uses additional ports and services; this is a learning table, not a complete firewall rule set. Port 389 is not the single “AD DS port.” LDAP encryption is not automatic merely because the protocol is LDAP.

---

## 5. VMware SSO

**SSO = Single Sign-On.** It allows an authenticated identity to access related services without repeatedly entering credentials, subject to the system's scope, session rules and permissions.

In this VMware lesson, the likely context is **vCenter Single Sign-On**. A common default SSO administrator identity is:

```text
administrator@vsphere.local
```

Compare that with the `root` account used for a direct standalone ESXi login. The host account and the vCenter SSO identity are different account contexts. vCenter SSO can work with configured identity sources; a valid identity still needs the correct vCenter permissions.

SSO is an authentication experience/mechanism, not a universal synonym for a directory database. Your earlier passwordless SSH alias is also different: it uses SSH client configuration and key authentication, not vCenter SSO.

---

## 6. Novell and the history notes

Your original line was: `Novell - OS | Database x.25`.

- Novell was a company; NetWare was its network operating system.
- NDS means Novell Directory Services; the directory technology later became known as eDirectory.
- X.25 belongs to networking protocols, not the name of a directory database.
- X.500 concerns directory standards, so it may be the intended lecture reference. **Confirm this with the teacher; do not silently change the original note.**

Historical products help explain why organizations wanted central identity and directory services. You do not need to deploy NetWare or X.25 to learn the current AD DS lab.

---

## 7. Windows Server and the AD service family

Your notes mix three categories: operating-system versions, account/directory services, and file systems. Keep them separate.

| Class line | Explanation |
|---|---|
| NT — SAM / FAT | SAM concerns accounts; FAT concerns disk files. NT also supported NTFS and historical NT domains existed before AD. |
| 2000 — DNS / AD / NTFS | Windows 2000 Server introduced Active Directory. DNS and NTFS already existed; DNS is important for AD discovery. |
| 2003 | Continued the AD platform; application directory partitions are part of this generation. |
| 2003 R2 / SP2 | R2 is a release variant; SP2 is a service pack. They are not interchangeable names. |
| 2008 — AD DS | The AD service family uses distinct role names, including AD DS and AD LDS. |
| 2012 / 2016 / 2019 / 2022 / 2025 | Later server releases. This shorthand omits some R2 releases and is not a complete chronology. |

**FAT = File Allocation Table. NTFS = New Technology File System.** Neither is the account directory. Files and folder data live on a file system; directory entries can identify users/resources without holding all resource contents.

### AD DS ≠ AD FS ≠ AD RMS ≠ AD LDS

| Role | Full name | Main purpose |
|---|---|---|
| AD DS | Active Directory Domain Services | Traditional domains and directory identities |
| AD FS | Active Directory Federation Services | Federated application sign-in through trust |
| AD RMS | Active Directory Rights Management Services | Protect usage rights for supported content |
| AD LDS | Active Directory Lightweight Directory Services | Application directory without creating a full AD DS domain |

AD LDS was previously called ADAM. Do not read `ADDS = ADFS | ADRMS | ADLDS` as an equality or claim they were all introduced in 2003 R2/SP2. An AD domain trust and application federation also serve different purposes.

---

## 8. Topology, workgroup and domain

**Topology** describes arrangement and connections. Physical topology examines links/devices; logical network topology examines how communication is organized over those links.

| Pattern | Arrangement |
|---|---|
| Bus | Shared backbone |
| Star | Devices attach to a central device |
| Ring | Connections form a loop |
| Mesh | Multiple interconnections; full mesh connects every pair |
| Hybrid | Combination of patterns |

A cable failure's effect depends on the design. A failed leaf connection in a simple star usually affects that attached device; a failed central switch can affect many devices. Multiple links alone do not prove redundancy.

Workgroup/domain is a classification of identity and management organization. It is not the same classification as bus/star/mesh. Both a workgroup and a domain can operate over the same physical star network. AD logical structure is another context: domains, OUs, trees and forests. AD sites model network connectivity for directory operations.

---

## 9. Azure AD, Entra ID and hybrid identity

**Azure Active Directory was renamed Microsoft Entra ID.** Windows Server AD DS was not renamed Entra ID.

| Service | Main context |
|---|---|
| AD DS | Traditional Windows domains, DCs, Kerberos and Group Policy |
| Microsoft Entra ID | Cloud identity, application sign-in and access management |

Entra ID is not simply a traditional DC hosted in the cloud. Organizations can connect on-premises and cloud identity through a planned hybrid design; synchronization does not make every capability or policy identical. Microsoft Entra Domain Services is a separate managed-domain offering and should not be confused with Entra ID itself.

For now, learn the traditional AD DS lab first. Cloud integration is a later topic, not an installation prerequisite for an isolated domain lab.

---

## 10. UPN and DOMAIN\username

Your examples can identify the same domain account, if configured accordingly:

```text
vergank@vcplab.com
VCPLAB\vergank
```

| Format | Name | Parts |
|---|---|---|
| `vergank@vcplab.com` | User Principal Name (UPN) | User name + `@` + UPN suffix |
| `VCPLAB\vergank` | Down-level logon name | NetBIOS domain + `\` + account logon name |

The down-level user component corresponds to the legacy `sAMAccountName` attribute. That attribute's name does not mean the domain account is a workstation-local SAM account.

UPN looks like email, but it does not prove a mailbox exists. An administrator can configure an alternate UPN suffix, so the suffix need not always equal the AD DNS domain name. The NetBIOS domain name is a separate field.

Compare `DELL\vergank`: that selects a local identity on a computer named DELL, whereas `VCPLAB\vergank` selects the domain identity when VCPLAB is the domain's NetBIOS name.

---

## 11. AD objects and directory structure

An **object** is a directory entry; its **attributes** describe it. A user object may have a name, UPN and account status. Publishing a shared folder in AD stores a reference, not the actual folder contents. A printer object does not store all printed documents.

| Structure | Purpose |
|---|---|
| OU: Organizational Unit | Organize objects, delegate administration and link GPOs |
| Domain | Directory identity and replication unit within a forest |
| Tree | Domains sharing a contiguous DNS namespace |
| Forest | One or more domains sharing schema/configuration and forest relationships |
| Site | Models well-connected subnets for service location and replication |
| DC | Server providing directory services |

An OU is not a security group. Putting Ali in an Accounts OU does not itself grant folder permissions. A forest is the fundamental AD security boundary; an OU is not equivalent isolation.

**GPO = Group Policy Object**, not GPU. Group Policy can configure applicable user/computer settings and some supported software deployment scenarios. Links, filtering, inheritance and processing determine its scope. Policies do not automatically apply to every object simply because AD exists.

Domain trusts can permit authentication across domains; resource permissions are still required. The purpose is not unconditional access to another domain's resources.

---

## 12. Kerberos login and resource access

Kerberos is the primary authentication mechanism for normal AD domain scenarios. DNS helps the client locate a DC; accurate time is important for Kerberos.

### Simplified Ali-to-file-server scenario

1. The client uses AD-aware DNS records to locate a DC.
2. Kerberos Authentication Service processing allows the client to obtain a Ticket Granting Ticket (TGT), after the required authentication exchange.
3. When Ali accesses a service, the client uses the TGT to request the appropriate service ticket from the Ticket Granting Service.
4. The client presents the service ticket to the file service.
5. The service validates the authentication material and evaluates the requested operation against permissions and Ali's security context.

AS and TGS are functions of the Key Distribution Center (KDC), normally on the DC; they are not necessarily separate physical servers. The normal resource server does not require its own NTDS.dit database. The password is not simply sent to every resource server in this flow.

![Original teacher Kerberos diagram, page 7](images/teacher-kerberos.png)

**Read the original diagram with the clarifications above.** Its “Real Server” means the target resource/service. The database shown under that resource should not be interpreted as a requirement for a normal file server.

A previously cached Windows login can sometimes work when a DC is unavailable. That is not evidence that live domain authentication, fresh tickets or all network resources are available.

---

## 13. Database partitions, SYSVOL and replication

The default database file is normally `C:\Windows\NTDS\NTDS.dit`. Database logs are normally in the NTDS directory unless configured elsewhere. `C:\Windows\SYSVOL` contains domain policy/script file content. SYSVOL is not simply inside NTDS.dit.

The lecture's “four partitions” is best understood as **four partition categories**, not exactly four physical pieces in every deployment.

| Category | Main content | Typical replication scope |
|---|---|---|
| Schema | Allowed object classes and attributes | Forest-wide |
| Configuration | Sites, services and forest configuration | Forest-wide |
| Domain | Users, groups, computers and domain data | DCs holding that domain partition |
| Application | Application-specific directory data, including AD-integrated DNS | Selected replica scope |

There can be multiple application partitions, such as DomainDnsZones and ForestDnsZones. Global Catalog servers hold partial information from other domains, so “domain information never replicates outside its domain” needs that qualification.

**KCC = Knowledge Consistency Checker.** It calculates replication topology and connection objects; it is not the whole replication engine. Modern SYSVOL replication uses DFS Replication, distinct from database replication.

Most ordinary changes can originate on any writable DC and replicate. Two writable DCs are different from a standard DNS primary/secondary pair. An AD-integrated DNS zone's replication follows its configured AD scope.

Replication improves availability; it is not backup. An unwanted change can also replicate. Inter-site replication is configured for network needs and schedules; it is not inherently faster than intra-site replication.

---

## 14. FSMO roles

**FSMO = Flexible Single Master Operations.** AD supports multi-master updates for many operations, but designated DCs coordinate certain special operations.

| Scope | Role | Responsibility |
|---|---|---|
| One per forest | Schema Master | Coordinates schema updates |
| One per forest | Domain Naming Master | Coordinates adding/removing directory domains and related naming operations |
| One per domain | RID Master | Allocates RID pools to DCs for unique security identifiers |
| One per domain | PDC Emulator | Important password-change coordination, account-lockout functions and time hierarchy duties |
| One per domain | Infrastructure Master | Maintains relevant cross-domain object references; significance depends on design |

Five roles do not require five servers. The first DC in a new single-domain forest normally holds all five initially. A forest with two domains has two forest-wide role instances plus three per domain: eight instances in total.

The PDC emulator is not the only DC that authenticates users or accepts password changes. Group Policy processing takes place on clients; administrative tools commonly prefer the PDC for editing consistency. The forest-root PDC has a special time-source role in the default hierarchy.

Schema objects are not casually deleted like ordinary accounts; schema changes require controlled planning. Infrastructure Master placement has Global Catalog and forest-design qualifications; avoid a blanket placement rule.

**Transfer** is a planned role move with the current holder available. **Seizure** is a recovery action when the holder cannot return normally. Neither is a beginner troubleshooting shortcut; no transfer/seizure is performed here.

---

## 15. Schema version and functional levels

**Schema** defines object classes and attributes. **Schema version** identifies a schema revision. A server's OS version, schema version, domain functional level and forest functional level are related but different values.

| Windows Server generation in lecture | Schema version |
|---|---:|
| 2000 | 13 |
| 2003 / SP1 / SP2 | 30 |
| 2003 R2 | 31 |
| 2008 | 44 |
| 2008 R2 | 47 |
| 2012 | 56 |
| 2012 R2 | 69 |
| 2016 | 87 |
| 2019 and 2022 | 88 |
| 2025 | 91 |

A schema update does not mean every DC was upgraded to that OS. Functional levels govern AD capabilities and supported DC versions; they are not a count of users or the client Windows edition. Windows Server 2019/2022 did not introduce separate domain/forest functional levels named after those releases.

Do not infer functional levels from the schema number or raise them just because a newer ISO is available. Check current DC compatibility and the planned design first. Schema extension, functional-level changes and backup/recovery are later administration topics.

---

## 16. Readiness, tools, revision and future lab

### Understand the teacher's installation checklist

| Lecture item | What it means for the later lab |
|---|---|
| Windows Server and admin rights | Appropriate supported guest OS and permissions |
| Static IP `192.168.0.111/24` | Teacher example; not a confirmed free address in your lab |
| Unique domain name | Choose a suitable namespace deliberately before promotion |
| DNS server | AD-aware service discovery and suitable client resolver configuration |
| DSRM password | Separate Directory Services Restore Mode recovery credential; keep private |
| Forest/domain functional levels | Compatibility choices based on the DC design |
| 250 MB disk space | Old database/log context; not sufficient for a modern complete server VM |

Verify resources, subnet overlap, time, naming, installation media and recovery plans before configuration. The actual modern Windows Server/VM requirements must be checked for the chosen edition. Domain rename, DC rename and demotion are controlled operations, not casual undo buttons.

### Read-only Windows observation

```powershell
Get-CimInstance Win32_ComputerSystem |
    Select-Object Name, PartOfDomain, Domain
whoami
whoami /groups
```

`PartOfDomain` checks traditional Windows domain membership. `whoami` shows the current security context. Neither a DNS suffix nor a Microsoft-account sign-in proves AD DS membership. Cloud identity states require separate inspection.

After a domain actually exists, suitable tools/permissions may allow these additional checks:

| Tool | Purpose |
|---|---|
| Active Directory Users and Computers | Users, groups, computer objects and OUs |
| DNS Manager | AD service discovery records and zones |
| Group Policy Management | GPO links, settings and scope |
| `klist` | Inspect the current Kerberos ticket cache |
| `gpresult /r` | Inspect applied policy results |
| `dcdiag` | DC diagnostics |
| `repadmin /replsummary` | Replication summary |

These are not claims that AD tools are installed on your current host. Do not use ADSI Edit or `ntdsutil` as unexplained repair steps.

### Self-check: answer before reading the key

1. What do SAM, LDAP, SSO and UPN stand for?
2. Are `DELL\Ali` and `VCPLAB\Ali` automatically the same account?
3. Is LDAP a database, and is it Linux-only?
4. Does TCP/389 cover every AD requirement?
5. Does `vergank@vcplab.com` prove a mailbox exists?
6. Can workgroup and domain use the same star network?
7. Does a normal file server need NTDS.dit for Kerberos access?
8. Are AS and TGS necessarily two machines?
9. Does an OU grant folder permissions?
10. Does a domain trust automatically grant file access?
11. Are there exactly four directory partitions in every AD deployment?
12. Is KCC the entire replication engine?
13. Do five FSMO roles require five DCs?
14. Is schema 91 the same thing as functional level 2025?
15. Does replication replace backup?

### Answer key

1. Security Accounts Manager; Lightweight Directory Access Protocol; Single Sign-On; User Principal Name.
2. No: local and domain account authorities differ.
3. It is a directory access protocol supported by multiple platforms.
4. No: AD uses several services and ports.
5. No: sign-in name and mailbox are different concepts.
6. Yes: topology and identity organization are different dimensions.
7. No.
8. No: they are KDC functions.
9. No: resource permissions/security groups are separate.
10. No: authorization still applies.
11. No: four categories; application partitions can be multiple.
12. No: it calculates replication topology.
13. No: one DC can hold several roles.
14. No: these measure different things.
15. No: unwanted changes can replicate too.

### Later practical learning order

Inventory and plan → prepare a separate server/client lab → install AD DS/DNS and promote → verify discovery → join the member → create user/group/OU → test a harmless GPO → verify allowed/denied folder access → optional second DC and replication → supported backup planning.

Keep the existing standard DNS primary/secondary/stub exercise documented separately. Actual machine names/IPs, client edition, resources and evidence will be confirmed when the practical begins. **No installation was performed for these study notes.**

[Return to README](README.md) | [Sources and lecture map](SOURCES_AND_LECTURE_MAP.md)
