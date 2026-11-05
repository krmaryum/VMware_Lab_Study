# Active Directory Domain Services (AD DS) - Complete Book-Based Study Notes

**Language:** English  
**Source:** Uploaded lecture PDF, `09 Active Directory Domain Service (ADDS)(3).pdf`, 22 pages.  
**Scope:** Every agenda topic, substantive bullet, table entry and technical diagram is addressed below. Repeated chapter-divider slides are mapped rather than reproduced. This is an expanded explanation, not a verbatim transcription or a completed lab report.

**Labels used:**
- **Book topic:** Material covered by the lecture.
- **Clarification:** A correction or qualification needed to avoid a misleading interpretation.
- **Additional explanation:** Supporting knowledge and examples beyond the lecture.

## Contents

1. [Page-by-page coverage](#1-page-by-page-coverage)
2. [AD DS foundations](#2-ad-ds-foundations)
3. [Database, partitions and paths](#3-database-partitions-and-paths)
4. [Kerberos authentication](#4-kerberos-authentication)
5. [Directory objects](#5-directory-objects)
6. [Purposes of AD DS](#6-purposes-of-ad-ds)
7. [Features and limitations](#7-features-and-limitations)
8. [Logical and physical structure](#8-logical-and-physical-structure)
9. [All five FSMO roles](#9-all-five-fsmo-roles)
10. [Schema versions](#10-schema-versions)
11. [Installation requirements](#11-installation-requirements)
12. [An integrated example](#12-an-integrated-example)
13. [Read-only checks for a future lab](#13-read-only-checks-for-a-future-lab)
14. [Correction register](#14-correction-register)
15. [Revision questions and answers](#15-revision-questions-and-answers)
16. [Glossary](#16-glossary)
17. [Repository placement and learning status](#17-repository-placement-and-learning-status)
18. [References](#18-references)

## 1. Page-by-page coverage

Page numbers refer to PDF page position, starting at 1.

| PDF page(s) | Source content | Covered here |
| --- | --- | --- |
| 1 | Agenda: AD DS, database/partitions, authentication, objects, purposes, features, structure, FSMO, schema versions, installation | Sections 2-11 |
| 2 | AD DS chapter divider and management-console illustration | Section 2; console tools explained in section 13 |
| 3 | Definition, introduction in 2000, KCC, eight functions | Section 2 |
| 4 | Database/partitions divider | Section 3 |
| 5 | NTDS.DIT; four partition categories; default paths; ADSI Edit | Section 3 |
| 6 | Authentication divider | Section 4 |
| 7 | Kerberos, DNS/AD/client diagram, AS/TGS/resource-server diagram | Section 4, including diagram corrections |
| 8 | Objects divider | Section 5 |
| 9 | Definition, attributes, all nine listed object categories | Section 5 |
| 10 | Purpose divider | Section 6 |
| 11 | All nine purposes | Section 6 |
| 12 | Features divider | Section 7 |
| 13 | All seven features | Section 7 |
| 14 | Structure divider | Section 8 |
| 15 | Domain, tree, forest, OU; site and DC | Section 8 |
| 16 | FSMO divider and decorative shapes | Section 9; shapes contain no additional technical instructions |
| 17 | Forest-wide FSMO roles | Section 9 |
| 18 | Domain-wide FSMO roles and transfer/seizure note | Section 9 |
| 19 | Schema-version divider | Section 10 |
| 20 | Schema definition and entire historical version table; heading mistakenly says FSMO | Section 10 |
| 21 | Installation-requirements divider | Section 11 |
| 22 | All prerequisites, example IP, 250 MB/200 MB/50 MB figures | Section 11, with historical-sizing clarification |

Repeated Windows Server labels, decorative artwork and repeated console images introduce the chapters; they do not add separate procedures. This illustrated edition embeds original lecture slides and newly drawn educational diagrams. Original slides retain the source wording; consult the corrections before relying on them. The new diagrams are conceptual illustrations, not screenshots of a completed lab.

## 2. AD DS foundations

![AD DS overview](images/01_ADDS_Overview.png)

*New educational diagram; see IMAGES_AND_SOURCES.md for provenance.*

**Book topic - pages 2-3.**

### 2.1 What is AD DS?

**Active Directory Domain Services is Microsoft's directory service for organizing [identities](md/ADDS_Identities.md) and [resources](md/ADDS_Resources.md) in a [domain environment](md/ADDS_Domain_Environment.md).**


A directory is an organized collection of [object](md/ADDS_Objects_Attributes_and_Relationships_English.md) and their [attributes](md/ADDS_Objects_Attributes_and_Relationships_English.md). AD DS stores identities such as users, computers and groups, together with [relationships](md/ADDS_Objects_Attributes_and_Relationships_English.md) and security information. Administrators use it to manage access and resources centrally.

A **Domain Controller (DC)** is a Windows Server that has been promoted to run AD DS. Installing the AD DS role alone does not finish promotion.

**Example:** Khalid has a domain account. He can sign in to permitted domain computers without administrators creating a separate local account on each computer.

<img src="images/Windows-Server-AD-DS.png" width="400">

### 2.2 History and hierarchy

The lecture dates Active Directory's introduction to **Windows 2000**, which is correct. Modern AD DS develops from that directory platform.

AD DS is hierarchical because a [forest](md/ADDS_Forest.md) contains domains, and [organizational units](md/ADDS_Forest.md) (OUs) can be created within those domains to organize objects such as users, computers, and groups.

<img src="images/Active-Directory-history-hierarchy.png" width="400">


### 2.3 Every function listed in the book

| Book function | Explanation | Example or qualification |
| --- | --- | --- |
| User and group management | Create and manage identities and group memberships | Put Khalid in a Finance access group |
| Authentication and authorization | Establish identity and supply identity/group information used in access decisions | DC authenticates; the file server checks access permissions |
| Centralized management | Administer many objects through common tools | Active Directory Users and Computers |
| Domain services | Maintain domain identity information and serve logon/directory requests | A DC handles domain authentication |
| Trusts | Permit authentication paths between domains/forests | A trusted-domain user can be considered for access |
| Group Policy | Manage supported user/computer settings through GPOs | Lock a workstation after the configured inactivity period |
| DNS integration | Locate DCs and services using DNS records | Clients query service records to discover a DC |
| Replication | Copy relevant directory changes among DCs | A new user becomes available on other replicas |

**Clarification - authorization:** AD DS is not the component that directly opens every folder. It provides identities and security information. A resource server or application evaluates its permissions and other rules. Successful authentication does not guarantee access.

**Clarification - trusts:** A trust does not automatically grant permission to every resource. Trust direction, authentication restrictions and resource permissions still matter.

**Clarification - Group Policy:** GPOs can be linked to sites, domains and OUs. Scope, inheritance and filtering determine which settings apply. Not every GPO applies to every computer.

### 2.4 What does the KCC do?
KCC stands for Knowledge Consistency Checker. It is a built-in component of AD DS that creates and maintains the replication topology. This topology defines which domain controllers (DCs) obtain directory changes from which other DCs.

The KCC creates connection objects as needed. Each connection object identifies the source DC from which the destination DC pulls changes. For example, if DC02 has an inbound replication connection from DC01, then DC02 pulls changes from DC01. The KCC creates the connection; AD DS replication transfers the changes.

Multi-master replication: Many routine directory updates can be made on any writable DC. For example, if you change a user’s department on DC01, that change can replicate to the other DCs. A Read-Only Domain Controller (RODC) holds a read-only copy of the directory and does not originate directory updates.

Replication is not always immediate. DNS or network connectivity problems can interrupt replication. Between sites, the site-link schedule also determines when replication can occur.

Replication improves availability, but it does not replace backups. If a user account is accidentally deleted, that deletion can also replicate to other DCs.

<img src="images/Windows-Server-KCC-replication.png" width="400">


## 3. Database, partitions and paths

![Database and partition categories](images/02_Database_Partitions.png)

*New educational diagram; see IMAGES_AND_SOURCES.md for provenance.*

**Book topic - pages 4-5.**

### 3.1 NTDS.DIT

The AD DS database file is **NTDS.DIT**. The lecture expands NTDS as “New Technology Directory Service” and DIT as “Directory Information Tree.” Treat this as terminology; remember the exact filename.

The database uses Microsoft's Extensible Storage Engine (ESE). It holds directory information, including protected credential-related data. It is security-sensitive and is not a normal document to copy or edit casually.

### 3.2 Four partition categories, not always exactly four partitions

A **directory partition**, also called a naming context, is a logical unit of directory data with its own replication scope.

The lecture lists four categories:

| Category | Main contents | Normal replication scope |
| --- | --- | --- |
| Schema | Definitions of object classes and attributes | All DCs in the [forest](md/ADDS_Forest) |
| Configuration | Forest configuration, sites, subnets, site links and service/topology information | All DCs in the forest |
| Domain | Objects belonging to a particular domain | DCs for that domain; Global Catalog qualification below |
| Application | Application-specific directory data, such as AD-integrated DNS data | Selected/enlisted DC replicas according to its scope |

**Clarification:** A DC can host multiple application partitions. Therefore, “four types” does not mean the database always contains exactly four individual partitions.

### 3.3 Schema partition - the directory's rules

The schema defines:
- Which object classes can exist, such as user or computer.
- Which attributes those objects can have.
- Required/optional attributes and relevant structural rules.

The lecture calls it a template and says it displays the database in form format. A better explanation is that it defines the allowed structure; it is not a graphical form-rendering feature.

There is one shared schema per forest. It is extensible under controlled conditions, rather than freely changeable by every administrator. Extending it affects the forest.

### 3.4 Configuration partition - how the forest is organized

This includes the forest's directory configuration: sites, subnet mappings, site links, server/DC topology objects and references to naming contexts.

**Example:** Mapping a Chicago client subnet to a Chicago site helps AD choose appropriate services and replication arrangements.

### 3.5 Domain partition - domain objects

This includes the domain's users, groups, computer accounts, OUs and related objects.

The lecture says it “never replicates with other domains.” That needs qualification:
- A full domain replica normally goes to DCs in that domain.
- A **Global Catalog (GC)** can hold partial, read-only replicas of other domains' domain partitions: all their objects with a selected subset of attributes.
- A writable GC also keeps its normal full local-domain replica.
- This does not merge all domains into one writable domain database.

The domain partition is one part of AD data; schema and configuration information are also AD database contents.

### 3.6 Application partitions

The lecture notes their introduction with Windows Server 2003 and their use with AD-integrated applications such as DNS.

Common DNS examples are **DomainDnsZones** and **ForestDnsZones**. Their replication scopes differ. Custom application partitions are also possible.

They can be created and removed through appropriate supported procedures. Removing a partition can remove useful application data; it is not routine cleanup.

### 3.7 Default paths and what they contain

| Item from the lecture | Typical default location | Meaning |
| --- | --- | --- |
| Database | `C:\Windows\NTDS\ntds.dit` | Directory database |
| AD transaction logs | `C:\Windows\NTDS\` | ESE transaction logs used for database consistency/recovery |
| SYSVOL | `C:\Windows\SYSVOL\` | Replicated file content such as policy templates and scripts |
| ADSI Edit | Management tool, opened with `adsiedit.msc` | Views/edits directory objects and naming contexts |

**Clarification:** ADSI Edit is not a database file or installation path. Logs and SYSVOL are not partitions inside NTDS.DIT.

A GPO has:
1. A directory component in AD.
2. A file component in SYSVOL.

Modern SYSVOL replication uses DFS Replication (DFSR), separate from AD database replication. Paths can be customized during promotion. Do not hand-edit database files or make experimental directory changes through ADSI Edit.

## 4. Kerberos authentication

![Corrected Kerberos ticket flow](images/03_Kerberos_Corrected.png)

*New educational diagram; see IMAGES_AND_SOURCES.md for provenance.*

**Book topic - pages 6-7.**

### 4.1 What Kerberos does

Kerberos is AD DS's primary domain authentication mechanism. It uses tickets to let a client prove its identity to services without sending the user's password to each service.

The lecture's diagram lists an Authentication Server, Ticket Granting Server and “Real Server.”

**Clarification:** AS and TGS are logical functions of the **Key Distribution Center (KDC)** on a DC. They do not require two separate Windows servers. “Real Server” is better called the **target/resource/application server**.

### 4.2 Correct roles

| Component | Role |
| --- | --- |
| Client | Acts for the user or other principal |
| DNS | Helps discover domain controllers and services |
| KDC: Authentication Service (AS) | Handles initial ticket-granting-ticket exchange |
| KDC: Ticket-Granting Service (TGS) | Issues tickets for particular services |
| Target service | Receives the service ticket and authenticates the client |
| Resource/application authorization | Decides which operations the authenticated identity may perform |

### 4.3 Step-by-step password-based example

1. Khalid signs in on a domain client.
2. The client discovers an appropriate DC, using DNS and domain-location information.
3. The client performs an AS exchange with the KDC. In typical password-based pre-authentication it proves knowledge of a key derived from the credentials; it does not send a plain-text password to the DC.
4. The KDC issues a **Ticket-Granting Ticket (TGT)** and associated client session information.
5. To access a file service, the client requests a service ticket using its TGT. The request identifies the service using a **Service Principal Name (SPN)**.
6. The KDC issues the appropriate **service ticket**.
7. The client presents the ticket to the file service.
8. The service validates the authentication information.
9. The file server evaluates share/NTFS permissions and other applicable rules.
10. Access succeeds only if the relevant authorization checks permit it.

This is a beginner model, not a full description of every Kerberos message. Smart cards, Windows Hello and other mechanisms can change the initial credential exchange.

### 4.4 TGT versus service ticket

| Ticket | Used for |
| --- | --- |
| TGT | Requesting service tickets from the KDC |
| Service ticket | Authenticating to a particular service |

A ticket is not unlimited permission. Its validity, service identity and authorization context matter.

### 4.5 Correcting the source diagram

The source places NTDS.DIT/domain-partition labels below both the authentication server and the resource server.

A normal member file/application server **does not need a local NTDS.DIT database**. It can validate a Kerberos ticket using the service's key. AD stores the relevant domain account information on DCs.

The diagram also shows DNS, AD, a switch and multiple clients. These illustrate discovery and network connectivity; DNS is not a gateway carrying every subsequent login packet.

### 4.6 Dependencies and exceptions

**Additional explanation:**
- Correct DNS configuration helps clients find DCs and services.
- Time must be sufficiently synchronized for Kerberos validation.
- SPNs must identify the intended service correctly.
- Firewalls must permit the necessary protocols.
- Some Windows scenarios use NTLM or other mechanisms. Do not assume every successful Windows login proves Kerberos was used.
- Cached interactive logon may allow sign-in while a DC is unavailable; it does not prove access to all live domain services.
- Single sign-on reduces repeated prompts but does not eliminate authorization checks.

## 5. Directory objects

![OU versus security group versus GPO](images/04_OU_Group_GPO.png)

*New educational diagram; see IMAGES_AND_SOURCES.md for provenance.*

**Book topic - pages 8-9.**

An **object** is an identifiable directory entry. An **attribute** describes it.

Example user attributes can include display name, sign-in name and department. An object has an identity and a location in the directory.

The source lists all of the following:

| Source category | Explanation and important distinction |
| --- | --- |
| Users | User identities used for sign-in and access control |
| Groups | Collections used for security permissions or distribution purposes |
| Computers | Computer accounts representing joined machines; separate from their human users |
| Domain controllers | DC computer accounts plus associated server/NTDS Settings and related directory objects |
| Organizational Units | Containers inside a domain used for organization, GPO scope and delegation |
| Shared folders | Published directory entries can describe network shares; the actual files remain on storage/file servers |
| Printers | Published print-queue objects can help users discover printers; the physical printer is not stored in AD |
| Sites | Configuration objects representing network locality |
| Group Policy | GPO directory objects plus associated SYSVOL content; not entirely stored in NTDS.DIT |

### 5.1 OU versus group

- **OU:** organize objects, delegate administration and scope linked GPOs.
- **Security group:** collect identities for permission assignment.
- A user can belong to many groups while occupying one parent container/OU at a time.
- Moving a user to an OU does not automatically grant access to a file share.
- A container named Users is not necessarily an OU.

### 5.2 Names used in AD

**Additional explanation:**

| Name | Example | Meaning |
| --- | --- | --- |
| UPN | `khalid@ad.example.com` | User Principal Name; a sign-in name, not proof of a mailbox |
| Down-level logon | `LAB\khalid` | NetBIOS domain name plus account name |
| Distinguished Name | `CN=Khalid,OU=Finance,DC=ad,DC=example,DC=com` | Directory location/name |
| SID | Domain SID plus an object's RID | Security identity used in Windows access control |

The “DC=” parts of a DN mean **domain component**, not domain-controller server.

## 6. Purposes of AD DS

**Book topic - pages 10-11. All nine purposes are covered.**

| Purpose | What it means | Example |
| --- | --- | --- |
| Centralized authentication and authorization support | Common identities and group information | Domain sign-in followed by file-server permission checks |
| User and resource management | Maintain directory entries and relationships | Disable a departing employee's account |
| Security and access control | Use identities, permissions and policies | Restrict a Finance share to approved groups |
| Scalability and delegation | Grow the directory while distributing limited administration | Let help desk reset passwords for one OU |
| Directory-based service for resources | Discover information through the directory | Find a published printer or application directory entry |
| Trust relationships and federation | Connect identity environments through appropriate mechanisms | Cross-domain authentication or a separate federation service |
| Replication for high availability | Maintain directory replicas | Another DC can answer suitable requests during an outage |
| Cloud and hybrid support | Integrate with cloud identity/services using additional components | Synchronize selected identities to Microsoft Entra ID |
| Directory services for applications | Let applications query/use directory identities and information | An LDAP-aware business application |

**Clarification - federation:** AD DS trusts and federation are not synonyms. AD FS is a separate role/service. Federation generally uses trust and tokens/assertions across identity systems.

**Clarification - cloud:** Microsoft Entra ID is not simply a cloud-hosted writable replica of NTDS.DIT. Hybrid integration requires design and appropriate tools.

**Clarification - delegation:** Give only the permissions required for the task. Delegating password resets in Finance does not mean granting Domain Admin rights.

## 7. Features and limitations

**Book topic - pages 12-13. All seven listed features are explained.**

### 7.1 Administration using Group Policy

GPOs centralize supported settings, scripts and some software deployment mechanisms. Windows Installer package deployment is one example; GPO is not a universal replacement for application-management systems.

Computer/user clients process applicable policy. A GPO's location, filters and precedence affect the result.

### 7.2 Renaming a DC or domain

The lecture lists both as possible features. They are separate operations with different prerequisites and application limitations.

A DC rename is not equivalent to renaming an ordinary standalone PC without checking dependencies. A domain rename is a specialized project and may be unsupported by applications in the environment. Plan and validate; do not treat it as routine name cleanup.

### 7.3 Integrated security with Kerberos

Kerberos supports domain authentication and single sign-on. Permissions, secure configuration and other controls are still needed; the presence of Kerberos does not make the entire environment automatically secure.

### 7.4 Installing or uninstalling AD DS

Promoting a server creates a DC; supported demotion removes that role as a DC. Demotion planning includes DNS, replication, role holders and whether other DCs remain.

Removing the last DC can remove the domain. Deleting a DC VM is not the same as supported demotion.

### 7.5 Cross-forest trust

A forest trust can provide an authentication path between forests when prerequisites and policy permit it. Trust is not a merger, database replication relationship or automatic file permission.

### 7.6 Site-to-site replication

The lecture calls site-to-site replication faster.

**Clarification:** Intersite replication is designed around link cost, scheduling and bandwidth considerations; it is not inherently faster than intrasite replication. Within a well-connected site, change notification generally supports quicker convergence. Actual timing depends on configuration and connectivity.

### 7.7 Extensible schema

New classes and attributes can support applications.

**Clarification:** Schema extensions are forest-wide changes. Schema class/attribute definitions are not ordinary objects that you can freely delete afterward. Supported definitions can sometimes be marked defunct, subject to restrictions. Test, plan permissions and backups, and use supported application/vendor procedures.

## 8. Logical and physical structure

![Logical and physical structure](images/05_Logical_Physical.png)

*New educational diagram; see IMAGES_AND_SOURCES.md for provenance.*

**Book topic - pages 14-15.**

### 8.1 Logical components

| Component | Meaning | Example |
| --- | --- | --- |
| Domain | Directory partition and administrative/replication unit within a forest | `ad.example.com` |
| Tree | One or more domains with a contiguous DNS namespace | `example.com` and `hr.example.com` |
| Forest | One or more domains sharing schema, configuration and forest-wide relationships | A forest can contain multiple trees |
| OU | Organizational/delegation/GPO container within a domain | Finance OU |

A forest can contain a single domain. Different trees in one forest can use different DNS namespaces.

**Additional explanation:** The forest is the central AD security boundary. A domain is an administrative/replication boundary, but it is not complete security isolation from highly privileged administrators elsewhere in the same forest.

### 8.2 DNS namespace

A namespace organizes names hierarchically. In `dc01.ad.example.com`:
- `com` is the top-level domain.
- `example.com` is a domain.
- `ad.example.com` is a subdomain.
- `dc01` is the host label in this example.

A DNS domain name does not by itself prove an AD domain exists. DNS and AD are related but different systems.

### 8.3 Physical components

| Component | Meaning |
| --- | --- |
| Site | Group of IP subnets considered well connected for AD locality/topology purposes |
| Subnet | Network prefix mapped to a site |
| Site link | Logical relationship used to model replication connectivity/cost/schedule |
| DC | Server hosting directory replicas and domain services |

A site often corresponds to a location, but it is not simply a city label. Accurate subnet mapping matters. One domain can span sites; one site can contain DCs from multiple domains.

**Additional explanation:** Logical structure answers “How are identities organized?” Physical structure answers “Where are services and network connections located?”

## 9. All five FSMO roles

![FSMO roles and scopes](images/06_FSMO_Roles.png)

*New educational diagram; see IMAGES_AND_SOURCES.md for provenance.*

**Book topic - pages 16-18.**

**FSMO = Flexible Single Master Operations.**

AD DS is generally multi-master, but specific operations use one designated role holder to avoid conflicts. A single DC can hold multiple FSMO roles.

| Scope | Role | Main responsibility |
| --- | --- | --- |
| Forest | Schema Master | Originating changes to the forest schema |
| Forest | Domain Naming Master | Control changes to domain/naming-context namespace |
| Domain | RID Master | Allocate RID pools to DCs |
| Domain | PDC Emulator | Password/lockout coordination, time hierarchy and preferred administrative functions |
| Domain | Infrastructure Master | Maintain cross-domain object references where applicable |

Two roles per forest plus three per domain means **2 + (3 x number of domains)** role instances. A three-domain forest has 11 instances, not necessarily 11 separate servers.

### 9.1 Schema Master

One per forest. Schema updates originate on its DC and then replicate.

The source includes deleting schema classes/attributes. That wording is misleading: normal deletion of classSchema/attributeSchema definitions is not allowed. Adding/modifying definitions and supported defunct handling are different operations.

### 9.2 Domain Naming Master

One per forest. It coordinates changes such as adding/removing domains and application naming contexts and keeps namespace references consistent.

The lecture mentions domain rename. Role availability is only one part of any supported rename workflow; the role alone does not make a rename safe or supported.

### 9.3 RID Master

One per domain. It allocates pools of Relative Identifiers to writable DCs. A DC uses its available pool when creating security principals.

**SID concept:** Domain SID + RID gives the principal's domain SID identity.

The RID Master does not have to personally issue a fresh RID for every user creation request. If it is unavailable, DCs may continue creating principals while their usable pools last.

### 9.4 PDC Emulator

One per domain. Relevant functions include:
- Preferential receipt of password updates.
- Helping resolve failed-password authentication attempts when another DC may have older information.
- Account-lockout processing.
- A special place in Windows domain time synchronization.
- Preferred administrative targeting for tools such as Group Policy management.
- Historical compatibility with Windows NT-style PDC expectations.

**Clarifications:**
- Other writable DCs can accept ordinary password changes.
- The PDC is not the only DC that authenticates users.
- Clients process Group Policy; the PDC does not personally apply every client's settings.
- The forest-root PDC occupies the top of the normal domain time hierarchy; configure an appropriate reliable upstream time source. Every machine does not need to query it directly.
- NT 4.0 compatibility explains history, not a recommendation to deploy obsolete clients.

### 9.5 Infrastructure Master

One per domain. It maintains references to objects in other domains, such as cross-domain group membership references after changes.

Its practical workload depends on the forest design, GC placement and features such as AD Recycle Bin. It is not the role that replicates all objects or updates every user's attributes.

### 9.6 Transfer versus seizure

The lecture mentions ntdsutil and GUI tools.

| Operation | Appropriate situation |
| --- | --- |
| Transfer | Current role holder is available; planned move |
| Seizure | Role holder cannot be recovered in the required situation; recovery procedure |

GUI tools differ by role: AD Users and Computers for domain roles, AD Domains and Trusts for Domain Naming Master, and the Schema snap-in for Schema Master. PowerShell and ntdsutil are other supported tools.

Seizure is not routine failover for a short outage. Follow recovery guidance before bringing an old role holder back. This document intentionally provides no role-seizure command.

Role loss does not necessarily stop all logins immediately. Impact depends on the role, cached/pool information, topology and outage duration.

## 10. Schema versions

**Book topic - pages 19-20.**

The page-20 heading says FSMO roles, but its contents concern schema versions.

The schema version is recorded in the schema naming context's **objectVersion** attribute. It identifies a schema level, not the OS of every DC.

### 10.1 Entire source table, normalized

| Windows Server release in the source | Schema objectVersion |
| --- | --- |
| Windows Server 2025 | 91 |
| Windows Server 2022 | 88 |
| Windows Server 2019 | 88 |
| Windows Server 2016 | 87 |
| Windows Server 2012 R2 | 69 |
| Windows Server 2012 | 56 |
| Windows Server 2008 R2 | 47 |
| Windows Server 2008 RTM | 44 |
| Windows Server 2003 R2 | 31 |
| Windows Server 2003 RTM, SP1 and SP2 | 30 |
| Windows 2000 Server | 13 |

These are historical mappings, not a recommendation to deploy unsupported versions.

### 10.2 Three different version concepts

| Concept | Describes |
| --- | --- |
| DC operating-system version | Windows release installed on a particular DC |
| Schema version | Forest-wide class/attribute schema level |
| Domain/forest functional level | AD capabilities and permitted DC OS combinations |

A newer schema can coexist with older supported DCs where the compatibility rules permit it. “Schema 91” does not mean every DC is Windows Server 2025.

Windows Server 2019 and 2022 use the Windows Server 2016 functional level as their newest level. Windows Server 2025 introduces a 2025 functional level. Choose levels from the actual DC compatibility matrix, not the schema table.

## 11. Installation requirements

![AD DS preparation checklist](images/07_Preparation.png)

*New educational diagram; see IMAGES_AND_SOURCES.md for provenance.*

**Book topic - pages 21-22. Every listed prerequisite is addressed.**

| Source prerequisite | Explanation |
| --- | --- |
| Windows Server OS | Use a supported Windows Server release for the DC role |
| Administrator rights | Required credentials depend on new forest, new domain or additional DC |
| Static IP, example 192.168.0.111/24 | Provide stable addressing and correct network configuration |
| Unique domain name | Plan a suitable DNS namespace and avoid naming conflicts |
| DNS server | Ensure AD-capable name/service discovery; DNS can be installed during promotion |
| DSRM password | Set a protected Directory Services Restore Mode recovery password |
| Forest functional level | Select a level compatible with all relevant DCs and the design |
| Domain functional level | Select a compatible domain capability level |
| 250 MB minimum: 200 MB database + 50 MB logs | Historical figure; not a valid total size for a modern Windows Server DC |

### 11.1 OS and hardware sizing

**Clarification:** The 250 MB figure is not enough for a modern server installation or a realistic DC. Account for the OS, updates, paging, directory database, transaction logs, SYSVOL, monitoring and growth. Current Windows Server documentation gives an OS storage minimum measured in gigabytes; minimum installation requirements are not production recommendations.

Use current requirements for the exact OS and deployment option. On a small learning host, start with a modest isolated lab and avoid running more VMs than the host can support. No universal CPU/RAM/disk allocation fits every AD deployment.

### 11.2 Permissions

Creating a new forest requires appropriate local administrative credentials. Adding a domain or DC has different domain/forest permission requirements, and schema preparation can require additional privileges.

Do not equate local Administrator, Domain Admin and Enterprise Admin. They have different scope.

### 11.3 Addressing

The source address `192.168.0.111/24` means IP `192.168.0.111`, subnet mask `255.255.255.0`.

It is an example, not an instruction to use that address on your network. Plan IP, prefix, gateway where required, DNS server settings and subnet reachability.

### 11.4 DNS and naming

Clients and DCs must use DNS services that can resolve the AD namespace and service records. Public DNS alone cannot normally resolve private AD records. External resolution can be provided through appropriately configured forwarding.

For a new lab, use a namespace you control or an appropriate reserved example namespace in documentation. The earlier learner lab used VCPLAB.com; this note does not establish ownership of that public domain.

### 11.5 DSRM

Directory Services Restore Mode is a recovery mode for AD DS maintenance/recovery. Its password is distinct from normal domain-account use. Record it securely; do not put passwords in a public repository or command history.

### 11.6 Functional levels

Choose based on DC compatibility and required features. Domain functional level cannot be below forest functional level.

For Windows Server 2025 DCs in an existing domain, Microsoft documents at least the Windows Server 2016 functional level requirement. Raising to the 2025 level requires compatible DCs throughout the affected scope. Plan and verify rather than guessing.

### 11.7 Additional preparation checklist

**Additional explanation:**
1. Confirm server identity, hostname and intended role.
2. Verify network, DNS and time.
3. Plan whether this is a new forest, child domain or additional DC.
4. Plan database/log/SYSVOL storage on supported volumes.
5. Apply updates and required security configuration.
6. Keep firewall protection enabled with required role rules.
7. Plan another DC and independent failure domains when availability matters.
8. Plan supported system-state/forest recovery backups and test recovery.
9. Keep the lab isolated from an existing production domain.
10. Capture configuration evidence before and after promotion.

The lecture supplies prerequisites rather than a complete installation walkthrough. Do not mistake these notes for evidence that promotion has already been performed.

## 12. An integrated example

**Additional explanation - hypothetical, not a completed environment.**

An organization uses `ad.example.com`, DC01 and DC02, a Finance OU, Khalid's user account and a file server FS01.

| Step | Component responsible |
| --- | --- |
| Client discovers a DC | DNS and domain discovery |
| Khalid proves identity and obtains tickets | Client and DC's KDC |
| Khalid requests Finance files | Client and FS01 |
| Access permission is evaluated | FS01 using identities/groups and access rules |
| Help desk resets a Finance password | Delegated permissions on the appropriate objects |
| Password/user changes propagate | AD replication |
| Desktop lock setting applies | Client Group Policy processing |
| Policy files replicate | SYSVOL/DFSR |
| A forest schema update is authorized/performed | Appropriate permissions and Schema Master |

If DC01 fails, DC02 can provide suitable domain services when DNS, replication and connectivity are healthy. FS01 remains responsible for its files. Losing FS01 is not solved merely by adding another DC.

## 13. Read-only checks for a future lab

**Additional explanation.** These do not install AD DS. Run only in a suitable AD environment with the tools and read permissions available. No outputs below are claimed as observed from your servers.

### 13.1 Useful consoles

| Tool | Purpose |
| --- | --- |
| `dsa.msc` | AD Users and Computers |
| `dssite.msc` | AD Sites and Services |
| `domain.msc` | AD Domains and Trusts |
| `gpmc.msc` | Group Policy Management |
| `adsiedit.msc` | Advanced directory inspection; do not edit casually |
| DNS Manager | DNS zones and records |

### 13.2 Inspect domain, forest and DCs

~~~powershell
Import-Module ActiveDirectory

Get-ADDomain |
    Select-Object DNSRoot, DomainMode, PDCEmulator, RIDMaster, InfrastructureMaster

Get-ADForest |
    Select-Object RootDomain, ForestMode, SchemaMaster, DomainNamingMaster

Get-ADDomainController -Filter * |
    Select-Object HostName, Site, IsGlobalCatalog, IsReadOnly
~~~

### 13.3 Inspect schema version

~~~powershell
$rootDse = Get-ADRootDSE
Get-ADObject -Identity $rootDse.schemaNamingContext -Properties objectVersion |
    Select-Object DistinguishedName, objectVersion
~~~

This reads schema metadata. It does not extend the schema.

### 13.4 Inspect role holders and replication

~~~powershell
netdom query fsmo
repadmin /replsummary
repadmin /showrepl
~~~

Availability of netdom/repadmin depends on installed management tools. Review failures with topology, DNS and events; do not immediately force changes.

### 13.5 Observe current Kerberos tickets

~~~powershell
klist
~~~

Output is session-specific. Ticket visibility depends on how you signed in and which services you accessed. Do not publish tickets, secrets or unnecessary identity details.

## 14. Correction register

| Source wording or diagram | Correct interpretation |
| --- | --- |
| KCC is the replication service | KCC builds/maintains topology; AD replication transfers directory changes |
| Four partitions | Four categories; actual partition count can exceed four |
| Schema displays the database as forms | Schema defines object/attribute structure |
| Domain data never crosses domains | Full domain replication is domain-scoped; GC carries partial other-domain replicas |
| Resource server has NTDS.DIT | Normal member resource servers do not need it |
| AS and TGS as separate servers | Logical KDC functions; separate machines are not required |
| AD determines every resource access | Resource/application checks enforce permissions using identity information |
| Site-to-site replication is faster | Not inherently; costs, schedules and bandwidth affect intersite replication |
| Schema Master deletes definitions | Normal schema-definition deletion is disallowed; supported defunct handling differs |
| PDC is password-change master | Other writable DCs accept changes; PDC has special coordination responsibilities |
| PDC processes all GPO updates | Clients process policy; PDC is a preferred administrative target |
| Schema version equals server OS | Schema, OS version and functional levels are separate |
| 250 MB sufficient for installation | Historical sizing, not modern full DC/OS requirements |
| ADSI Edit listed alongside paths | It is a management tool, not a database path |

These corrections preserve the lecture's topics while improving technical precision.

## 15. Revision questions and answers

**1. What is AD DS?**  
A directory service organizing identities/resources and supporting domain authentication and administration.

**2. What is a DC?**  
A server promoted to host AD DS and provide domain services.

**3. Is installing the role enough?**  
No. Promotion/configuration must also complete.

**4. When was Active Directory introduced?**  
With Windows 2000.

**5. What does KCC do?**  
Build and maintain replication topology.

**6. Where is the directory database?**  
By default, in `C:\Windows\NTDS\ntds.dit`.

**7. What are the four partition categories?**  
Schema, configuration, domain and application.

**8. Does that mean exactly four partitions?**  
No. Multiple application partitions and GC replicas complicate the actual count.

**9. What does schema define?**  
Allowed object classes, attributes and related structural rules.

**10. What does configuration contain?**  
Forest-wide configuration including sites, subnets and replication topology information.

**11. What does a GC add?**  
Partial replicas of other domains' domain objects for forest-wide lookup and related functions.

**12. Is SYSVOL inside NTDS.DIT?**  
No. It is replicated filesystem content.

**13. Are AS and TGS necessarily separate servers?**  
No. They are KDC functions on DCs.

**14. Does a file server normally keep NTDS.DIT?**  
No. A member file server is not a DC.

**15. Does a Kerberos ticket grant every permission?**  
No. The service still authorizes operations.

**16. How do OU and group differ?**  
OU supports organization/delegation/policy scope; security groups support permission assignment.

**17. Does a published share object contain the actual documents?**  
No. Files remain on the file server/storage.

**18. Does a trust automatically grant access?**  
No. Authentication paths and resource permissions are distinct.

**19. Is AD FS the same as AD DS?**  
No. Federation is a separate service/concept.

**20. How many FSMO roles exist in one domain forest?**  
Five instances: two forest-wide and three domain-wide.

**21. What does RID Master allocate?**  
RID pools used by DCs when creating security principals.

**22. Is the PDC the only writable DC?**  
No. Ordinary writable DCs support multi-master updates.

**23. Transfer or seize for a planned move?**  
Transfer while the existing role holder is available.

**24. Which schema versions correspond to 2019/2022 and 2025?**  
88 and 91 respectively.

**25. Does schema 91 prove all DCs are 2025?**  
No. Check DC OS and functional-level compatibility separately.

**26. Is 250 MB a modern DC disk allocation?**  
No. Plan the OS and all workload/storage requirements.

**27. What is DSRM used for?**  
Directory-services recovery/maintenance scenarios.

**28. Why is DNS important?**  
Clients need to locate DCs and services through appropriate DNS records.

**29. Is replication a backup?**  
No. Undesired changes can replicate.

**30. Why have two DCs?**  
To reduce dependency on one server, provided DNS, replication, connectivity and failure-domain design also work.

## 16. Glossary

| Term | Meaning |
| --- | --- |
| AD DS | Active Directory Domain Services |
| DC | Domain Controller; in a DN, DC instead means domain component |
| NTDS.DIT | AD directory database file |
| ESE | Extensible Storage Engine |
| Naming context | Directory partition |
| KCC | Knowledge Consistency Checker |
| KDC | Key Distribution Center |
| AS | Authentication Service |
| TGS | Ticket-Granting Service |
| TGT | Ticket-Granting Ticket |
| SPN | Service Principal Name |
| GC | Global Catalog |
| OU | Organizational Unit |
| GPO | Group Policy Object |
| SYSVOL | Shared replicated directory containing policy/script files |
| DFSR | Distributed File System Replication |
| FSMO | Flexible Single Master Operations |
| RID | Relative Identifier |
| SID | Security Identifier |
| PDC Emulator | Primary Domain Controller Emulator role |
| DSRM | Directory Services Restore Mode |
| UPN | User Principal Name |
| LDAP | Lightweight Directory Access Protocol; not the database filename |
| AD FS | Active Directory Federation Services |
| RODC | Read-Only Domain Controller |

## 17. References

The uploaded lecture is the primary source for coverage and topic order. The following Microsoft references support the added explanations and corrections. Documentation was checked during preparation; consult current requirements before a deployment.

1. [AD replication concepts](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/replication/active-directory-replication-concepts) - KCC, sites and GC.
2. [Naming contexts and directory partitions](https://learn.microsoft.com/en-us/windows/win32/ad/naming-contexts-and-partitions) - partition concepts.
3. [Kerberos authentication overview](https://learn.microsoft.com/en-au/windows-server/security/kerberos/kerberos-authentication-overview) - KDC and tickets.
4. [Kerberos protocol exchanges](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-kile/b4af186e-b2ff-43f9-b18e-eedb366abf13) - AS/TGS/application exchanges.
5. [FSMO roles](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-fsmo-roles) - role scopes and responsibilities.
6. [FSMO details and qualifications](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/fsmo-roles) - cross-domain reference and role behavior.
7. [Active Directory schema specification](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/5859bab0-f545-4db6-80c6-9798b484b3d8) - schema objects and defunct handling.
8. [Find schema version](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/find-active-directory-schema) - objectVersion mappings.
9. [Install AD DS](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-) - deployment options and prerequisites.
10. [AD DS functional levels](https://learn.microsoft.com/windows-server/identity/ad-ds/active-directory-functional-levels) - DC/version compatibility.
11. [Raise functional levels](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/raise-domain-forest-functional-levels) - preparation and compatibility.
12. [Windows Server hardware requirements](https://learn.microsoft.com/en-us/windows-server/get-started/hardware-requirements) - modern OS sizing context.



## Appendix - Original lecture visuals

These are high-resolution renders of the supplied lecture pages, not new Microsoft screenshots. They preserve source errors discussed in the correction register, especially on pages 7, 13, 17, 18 and 22.

### PDF page 3 - ADDS Foundations

![Lecture page 3](images/source_pages/Page_03_ADDS_Foundations.png)

### PDF page 5 - Database and Partitions

![Lecture page 5](images/source_pages/Page_05_Database_and_Partitions.png)

### PDF page 7 - Authentication

![Lecture page 7](images/source_pages/Page_07_Authentication.png)

### PDF page 9 - Objects

![Lecture page 9](images/source_pages/Page_09_Objects.png)

### PDF page 11 - Purposes

![Lecture page 11](images/source_pages/Page_11_Purposes.png)

### PDF page 13 - Features

![Lecture page 13](images/source_pages/Page_13_Features.png)

### PDF page 15 - Structure

![Lecture page 15](images/source_pages/Page_15_Structure.png)

### PDF page 17 - FSMO Forest

![Lecture page 17](images/source_pages/Page_17_FSMO_Forest.png)

### PDF page 18 - FSMO Domain

![Lecture page 18](images/source_pages/Page_18_FSMO_Domain.png)

### PDF page 20 - Schema Versions

![Lecture page 20](images/source_pages/Page_20_Schema_Versions.png)

### PDF page 22 - Installation Requirements

![Lecture page 22](images/source_pages/Page_22_Installation_Requirements.png)

[Image provenance / Tasaveer ki tafseel](IMAGES_AND_SOURCES.md) | [README](README.md)
