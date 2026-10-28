# AD DS — Fundamental Concepts and Revision

English | Complete translation of the merged and structured Roman Urdu edition

This guide brings previously studied concepts together in a smooth reading order. Repeated explanations have been consolidated, while the unique points from both source files have been retained. The examples explain concepts; they are not evidence of a completed lab. Use this guide for revision alongside the detailed AD DS reference bundle.

## Contents

1. [What is a directory?](#1-what-is-a-directory)
2. [AD DS, Domain and Domain Controller](#2-ad-ds-domain-and-domain-controller)
3. [Domain and Workgroup](#3-domain-and-workgroup)
4. [Authentication, Authorization and Access](#4-authentication-authorization-and-access)
5. [How does DNS help locate a DC?](#5-how-does-dns-help-locate-a-dc)
6. [Kerberos, KDC, TGT and Service Ticket](#6-kerberos-kdc-tgt-and-service-ticket)
7. [Objects and Attributes](#7-objects-and-attributes)
8. [Security Groups and Permissions](#8-security-groups-and-permissions)
9. [OU versus Security Group](#9-ou-versus-security-group)
10. [Group Policy, Policy and Permission](#10-group-policy-policy-and-permission)
11. [Centralized Management and Access Control](#11-centralized-management-and-access-control)
12. [Replication and Multiple DCs](#12-replication-and-multiple-dcs)
13. [AD Sites](#13-ad-sites)
14. [Scalability and Delegation](#14-scalability-and-delegation)
15. [Combined Revision and Learning Checkpoint](#15-combined-revision-and-learning-checkpoint)

## Our consistent example

We use the same example throughout the guide:

| Item | Name or purpose in the example |
| --- | --- |
| User | Khalid |
| Domain computers | PC1 and PC2 |
| Domain Controllers | DC1 and DC2 |
| Security group | Finance-Read |
| OU | Finance OU |
| Resource | Finance folder on a file server |
| Another department | Sales |

The Finance-Read group, Finance OU and Finance folder are three different things. Similar names do not mean they are the same object or automatically connected.

## 1. What is a directory?

A directory is an organized collection of information. A telephone directory contains names and numbers; a network directory can contain information about users, computers, groups and other resources.

A book's index is a useful analogy. However, a directory does more than identify locations: it also holds details about entries. A user's name, logon name and department are examples of such information.

A reference to a shared folder in a directory does not mean that all the folder's files are stored in the directory database.

## 2. AD DS, Domain and Domain Controller

**AD DS = Active Directory Domain Services.** It is Microsoft's directory service, which helps centrally store and manage information about users, computers, groups and related network resources.

A **domain** is a logical scope for managing users, computers and resources. Domain accounts are centrally managed.

A **Domain Controller (DC)** is a server that runs AD DS and holds a copy of the directory database. It provides functions such as domain authentication and directory queries. A domain can contain more than one DC.

Example: an administrator creates Khalid's domain account. A DC holds information about that account and participates in verifying domain sign-in. Read/write access to the Finance folder is determined by the resource's permissions; the file server also checks these.

| Term | Simple identification |
| --- | --- |
| AD DS | Directory service |
| Domain | Logical management scope for identities and resources |
| DC | Server providing directory services |
| Administrator | Authorized person who manages accounts and settings |

## 3. Domain and Workgroup

| Domain | Workgroup |
| --- | --- |
| Domain accounts are centrally managed | Each computer's local accounts are managed separately |
| DCs provide domain directory services | There is no central account system equivalent to an AD domain |
| One domain account can sign in to permitted domain computers | Each machine checks credentials against its applicable local account authority |

If Khalid has a local account on both PC1 and PC2, those are different accounts even when the names match. Changing one local password does not automatically change the other.

A domain account does not need to be created as a separate local account on every domain computer. However, having a domain account does not automatically permit sign-in to every server: logon rights and other restrictions apply.

A workgroup does not mean network communication is disabled. File sharing is possible with suitable credentials and permissions. Ordinary domain-joined computers can also retain local accounts.

## 4. Authentication, Authorization and Access

| Term | Question | Khalid's example |
| --- | --- | --- |
| Authentication | Who are you? | Verify Khalid's identity |
| Authorization | What are you allowed to do? | Allow Read on the Finance folder |
| Access | Can the resource be used according to the applicable permission? | Khalid opens and reads a file |

An **incorrect password**, in our simple example, is an authentication issue. **Successful sign-in followed by Access Denied on a folder**, after the correct identity has been established, points toward authorization.

If Khalid has Read permission, he can read the permitted content; that Read grant alone does not allow editing. Actual access can also be affected by other applicable permissions and controls.

Remember: **verifying identity does not grant permission to perform every action.** In real troubleshooting, do not assume the final cause from the error label alone; examine the relevant evidence.

## 5. How does DNS help locate a DC?

DNS helps resolve names through records. In AD, an important additional purpose is helping clients locate DCs and domain services.

Simple flow:

1. Khalid signs in to the domain on PC1.
2. PC1 uses DNS to locate a suitable DC; SRV records are important in AD service discovery.
3. PC1 communicates with the DC for authentication.
4. Later, when the Finance folder is opened, the resource's permissions are checked.

DNS does not verify passwords or grant folder permissions. Locating a DC and successfully authenticating are separate checks too.

An AD client's DNS configuration must therefore support resolution of the domain's records. Receiving an answer from public internet DNS alone does not prove that private domain services can be discovered.

## 6. Kerberos, KDC, TGT and Service Ticket

Kerberos is the primary authentication protocol in normal AD domain scenarios. A **protocol** is a defined set of rules for communication.

The **KDC — Key Distribution Center** service runs on a DC. It handles the Kerberos authentication and ticket process.

### Khalid's journey from sign-in to the Finance folder

1. Khalid signs in to the domain, and the computer contacts the KDC.
2. When the required authentication exchange succeeds, the computer receives a **TGT — Ticket Granting Ticket**.
3. Khalid wants to use the Finance file service. The computer uses the TGT to request a **service ticket** for that service.
4. The computer presents the service ticket to the file server.
5. The file service validates the authentication material and decides access according to Khalid's security context and permissions.

| Item | Purpose |
| --- | --- |
| KDC | Service handling Kerberos authentication and tickets |
| TGT | Ticket used to obtain service tickets |
| Service ticket | Ticket for a particular service |

This flow uses tickets rather than sending a password to every resource server. Receiving a ticket does not grant permission to every file.

Tickets have a validity period. Under suitable conditions, the system renews them or obtains new tickets; the user does not necessarily have to enter a password again every time. DNS, network connectivity and accurate time are important to the process.

Memory aid: **Kerberos establishes identity; permissions determine authorization.**

## 7. Objects and Attributes

An **object** is a distinct directory entry representing something. **Attributes** are the information describing that entry.

| Example | Object type |
| --- | --- |
| Khalid's account | User object |
| PC1's domain account | Computer object |
| Finance-Read | Security group object |
| Finance OU | Organizational Unit object |

Khalid's name, username and department are examples of attributes. An OU is itself an object and can contain other permitted objects.

A resource's directory entry and the actual resource are different. Information about a printer in the directory does not mean the physical printer is inside the directory.

## 8. Security Groups and Permissions

A security group makes access management for multiple accounts more manageable.

Our example:

1. The administrator creates the Finance-Read security group.
2. Khalid and the relevant Finance employees are added to it.
3. The administrator assigns the group Read permission on the Finance folder.
4. Khalid can obtain the applicable Read access through the group.

**Creating a group does not grant permission automatically.** The relevant permission must be assigned on the resource. This reduces the effort of assigning permission separately to every user.

Read means reading; Write means writing or changing content. Both share permissions and NTFS permissions can be relevant to Windows file sharing. Other memberships, direct grants and applicable denies can also affect effective access.

A user can belong to several groups at the same time. Adding Khalid to the Sales group does not automatically remove his Finance-Read membership; the administrator must review old membership against the intended access.

## 9. OU versus Security Group

An **OU — Organizational Unit** is a logical container within a domain; a folder is a useful analogy. It can contain users, computers, groups and nested OUs. Not every object must be inside an OU; other containers also exist.

An OU's main purposes are:

- Organizing objects.
- Providing a scope for Group Policy affecting applicable users/computers.
- Delegating limited administrative authority.

| Item | Main purpose | Our example |
| --- | --- | --- |
| Security group | Manage members' resource permissions | Finance-Read gets Read on the folder |
| OU | Organization, GPO scope and delegation | Organize Finance users/computers |

Placing Khalid in the **Finance OU** does not automatically give him access to the **Finance folder**. The relevant security group and permissions can provide that access.

The Finance OU does not have one shared password. Its users have their own accounts and passwords.

## 10. Group Policy, Policy and Permission

**GPO = Group Policy Object.** It contains user and computer configuration settings. An administrator can link a GPO to an OU; settings are processed according to their applicable scope.

Example: a suitable setting to lock the screen after five minutes of inactivity. Central configuration reduces the effort of visiting each computer and setting it separately.

| Concept | Meaning | Example |
| --- | --- | --- |
| Policy | Settings and rules | Screen lock after inactivity |
| Permission | Allowed operation on a resource | Read on the Finance folder |

Not every GPO setting applies to every object in an OU. User settings concern users and computer settings concern computers; placement and processing context matter. Security groups can be used in GPO filtering, but user/computer settings do not apply directly to groups.

**Password-policy clarification:** the normal domain-user password policy is managed at domain level. Linking a password-length GPO to the Finance OU is not the normal way to create a separate password policy for Finance domain users. Fine-grained password policies are a separate advanced mechanism for particular users/groups; their practical configuration is outside this guide.

## 11. Centralized Management and Access Control

### Centralized Management

Managing users and computers through a central system. Khalid's domain account is created and managed centrally; a separate local account is not required for him on every member computer.

### User and Resource Management

Accounts and memberships organize appropriate resource access. Our example is granting Finance-Read permission to read the folder and giving Khalid membership.

### Security and Access Control

Provide access according to need, then review/remove it when the need ends. If Khalid moves from Finance to Sales, the administrator should review old Finance memberships and the appropriate Sales memberships.

Removing him from Finance-Read is intended to remove access obtained through that membership, but other groups or direct permissions may still provide access. Existing sessions, security tokens and tickets can prevent the change from appearing immediately everywhere.

Effective access must therefore be verified after membership changes; editing the group list alone is not complete verification.

## 12. Replication and Multiple DCs

**Replication** carries directory changes between DCs.

Example: Khalid's domain account is created on DC1. Replication allows DC2 in the same domain to obtain that account information. This is not an example of copying Khalid's entire account into a separate independent domain.

Do not assume replication completes instantly. If the account has not yet reached DC2, authentication through that DC can encounter a problem.

### Why have more than one DC?

For availability and redundancy. If DC1 is unavailable, a suitable available DC2 can provide authentication, provided the network, DNS, required directory information and other necessary services are working.

| Item | Benefit or limitation |
| --- | --- |
| Multiple DCs | Another service copy can be available when one DC fails |
| Replication | Directory changes reach the copies |
| Backup | Separate protection for recovery; replication does not replace it |

**Replication is not a substitute for backup.** An accidental deletion or unwanted change can replicate too. Supported backup and recovery planning are required separately.

## 13. AD Sites

An **AD Site** is a logical grouping related to network connectivity and associated with configured IP subnets. Sites help clients locate a suitable DC and help organize replication.

Two offices or buildings may be different sites, but every building does not necessarily need a separate AD site. The design follows connectivity and network requirements.

Do not equate a site with a domain. A domain can span multiple sites; a site can also contain DCs from multiple domains. At beginner level, remember: a domain organizes identities, while a site represents network connectivity in the directory.

## 14. Scalability and Delegation

### Scalability

Increasing a system's capacity to meet growing requirements. If there are 10 computers today and 100 tomorrow, AD DS can manage additional objects; design, capacity and connectivity must still be assessed.

Additional DCs can be deployed when required. Reducing capacity is also part of planning; removing a DC requires a suitable demotion and verification procedure. Deleting the VM alone is not proper DC retirement.

### Delegation

Giving an authorized person or team limited authority for a specific task.

Example: delegate authority to a help-desk operator to reset passwords for the relevant users in the Finance OU. Making that operator an administrator of the entire domain is unnecessary. Sensitive/protected accounts can have additional restrictions.

Delegation and folder permissions are different. Password-reset authority does not grant Read access to Finance files.

## 15. Combined Revision and Learning Checkpoint

### Quick revision table

| Term | Simple meaning |
| --- | --- |
| Directory | Organized collection of entries and their information |
| AD DS | Central directory service |
| Domain | Logical management scope for identities/resources |
| DC | Server running AD DS |
| Authentication | Verifying identity |
| Authorization | Deciding the permitted operation |
| Access | Using a resource according to the applicable permission |
| DNS | Resolves names/records; also supports DC discovery in AD |
| KDC | Service handling Kerberos authentication and tickets |
| TGT | Ticket used to obtain service tickets |
| Service ticket | Ticket for a particular service |
| Object | Directory entry |
| Attribute | Information describing the entry |
| Security group | Permission management through membership |
| OU | Container for objects, GPO scope and delegation |
| Policy | Settings and rules |
| Permission | Allowed operation on a resource |
| Replication | Carrying directory changes to copies |
| Site | Subnet/connectivity grouping in AD |
| Scalability | Increasing capacity according to requirements |
| Delegation | Limited administrative authority for a specific task |

### Self-check questions

Answer in your own words before reading the key.

1. Does a directory only identify a resource's location, or does it also store attributes?
2. What is the difference between AD DS, a domain and a DC?
3. In a workgroup, does the same username on PC1 and PC2 mean the same account?
4. In our simple example, which stage is affected by an incorrect password?
5. What does denied folder access after successful sign-in suggest?
6. How does DNS help in the sign-in flow?
7. What do KDC and TGT stand for?
8. What is the difference between a TGT and a service ticket?
9. Must a user enter a password again for every service access?
10. Khalid's account is an object; what might department be?
11. Does creating a security group immediately grant folder permission?
12. Will placing Khalid in the Finance OU give him the Finance folder?
13. Can a user belong to multiple groups?
14. Give separate examples of a policy and a permission.
15. Does every GPO setting apply to every object in an OU?
16. Does a password GPO linked only to the Finance OU create a separate password policy for Finance domain users?
17. Is an old session guaranteed to update immediately after membership is removed?
18. What is carried by replication, and is it a backup?
19. When can DC2 provide authentication if DC1 fails?
20. Is every building a separate AD site?
21. What does scalability mean in simple terms?
22. Must someone become a Domain Admin to receive delegated password-reset authority?
23. Does the Finance OU have one common password?
24. Does authority to reset passwords grant access to the Finance folder?

### Answers and short explanations

1. It also stores attributes; a directory is more than a list of pointers.
2. AD DS is the service; a domain is a logical management scope; a DC is the server providing the service and a directory copy.
3. No. The accounts belong to their respective local account authorities.
4. Authentication: identity verification has not succeeded.
5. In this simplified case, check authorization; sign-in and resource permission are different.
6. It helps the client locate suitable DCs/domain services.
7. Key Distribution Center; Ticket Granting Ticket.
8. A TGT is used to request a service ticket; the service ticket is presented to a particular service.
9. No. The system can operate according to valid tickets/sessions and renewal conditions.
10. Department can be an attribute of the user object.
11. No. The relevant resource permission must be assigned to the group.
12. No. OU placement does not grant folder permission.
13. Yes. Adding Sales membership does not automatically remove Finance membership.
14. A screen-lock policy; Read permission on the Finance folder.
15. No. User/computer settings, scope and filtering matter.
16. No. Domain password policy and fine-grained password policies have their own rules.
17. No. Tokens, sessions and tickets can mean a delay or refresh is required.
18. Directory changes are carried; replication is not backup.
19. When DC2 is suitable/available and DNS, networking and the required information/services are working.
20. No. Subnet and connectivity design matter.
21. Increasing system capacity according to requirements.
22. No. Specific authority can be delegated within an appropriate OU scope.
23. No. Users have their own accounts and passwords.
24. No. Administrative delegation and file-resource permissions are different.

### End-to-end recap

Khalid signs in on PC1 → DNS helps locate a suitable DC → the Kerberos exchange provides a TGT → a service ticket is obtained for the file service → the file server checks the relevant permissions → the applicable Finance-Read grant allows Khalid to read the permitted file.

The Finance OU helps organize administration and policy. Replication carries account changes to another DC. An additional DC can improve availability. Delegation gives the help desk authority for a particular task, rather than access to every resource.

### Learning checkpoint — where should the next lesson start?

The two original files record different stopping points:

| Original record | Next topic recorded at that time |
| --- | --- |
| Fundamental notes | AD DS Database and Partitions |
| Revision notes | Directory-Based Service for Network Resources, after Scalability/Delegation in AD DS Purpose |

This merge does not assume you have completed either remaining topic. The exact current stopping point still needs confirmation. The database file is named **NTDS.dit**; a detailed chapter about partitions has not been added to this revision guide.

First identify the current class/page checkpoint, then continue from there. The original stopping notes are preserved in the [coverage map](MERGE_MAP.md) and archive.

**Documentation status:** merged study guide ready; no claim of practical evidence or a completed AD installation.

[Return to README](README.md) | [Source coverage and changes](MERGE_MAP.md)
