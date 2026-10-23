# DNS: A Step-by-Step Learning Guide

English edition • 20 October 2026

Read this guide in order. It follows one imaginary organization from one DNS server to a more resilient design. Each lesson answers the question raised by the previous lesson. Screenshots are your teacher’s lecture evidence, not proof that the lab configuration has been completed.

Our running example uses the illustrative namespace `lab.example`. It is not a recommendation to rename your current servers. Your teacher’s exact addressing plan appears in Lesson 7.

**Learning route:** DNS basics → primary and secondary → failure dependencies → AD-integrated DNS → stub zones → remote sites and recovery → your lab.

You can stop after any lesson. Answer its checkpoint before continuing. The English and Roman Urdu files explain the same material; you only need to follow one.

---

## Lesson 1 — Why do we need DNS?

**Goal:** Understand a name, an IP address, a record and a zone.

Suppose a server has IP address `192.0.2.20`. Remembering `files.lab.example` is easier than remembering the address. DNS provides the records that let a client find the address associated with that name. This example address is documentation-only.

An **A record** associates a name with an IPv4 address. An **AAAA record** associates a name with an IPv6 address. A **zone** is an administratively managed portion of DNS containing records. Our example zone is `lab.example`; one of its records is `files.lab.example`.

A forward lookup asks, “What address belongs to this name?” A reverse lookup asks, “What name is associated with this address?” Reverse DNS uses separate zones and PTR records. Creating a forward record does not automatically prove that a matching reverse record exists.

DNS is a directory, not the connection itself. Correct name resolution does not prove that a firewall allows HTTPS or SSH, or that a user is authorized to sign in.

### A little history, without losing the main idea

Early networks used shared HOSTS files to map names to addresses. As networks grew, distributing one file became difficult. DNS introduced a distributed, hierarchical naming system. **BIND** is DNS server software; it is not a different name-resolution protocol. **WINS** is a legacy service for NetBIOS name resolution, not a replacement for modern DNS. Your Windows `hosts` file is still a local mapping mechanism and is not the DNS server’s zone database.

### Start with one server

Our organization creates a standard primary zone on DNS-A. An administrator adds the file-server record there. DNS-A is authoritative for that zone: it holds the zone data it is responsible for answering.

**Checkpoint:** DNS resolves the file-server name, but TCP port 443 is blocked. Is DNS necessarily broken?

**Answer:** No. Name resolution and service connectivity are different checks.

**Next question:** What happens if DNS-A stops working?

---

## Lesson 2 — Add a secondary so one failure does not stop DNS

**Goal:** Understand a standard primary/secondary design before learning AD.

DNS-A holds the writable standard primary zone. We add DNS-B with a secondary copy of `lab.example`. DNS-B receives a full read-only copy through an authorized zone transfer. Users can query DNS-B even when DNS-A is unavailable, provided that the secondary copy remains valid and DNS-B is reachable.

| Action | DNS-A: standard primary | DNS-B: secondary |
|---|---|---|
| Administrator changes this zone’s records | Yes | Not directly |
| Answers queries for records in its zone copy | Yes | Yes |
| Receives the zone through standard transfer | Source in this example | Recipient in this example |

The role belongs to **a zone**, not permanently to a machine. One server can host a primary zone for one namespace and a secondary zone for another. An authorized secondary can also be a transfer source in an appropriate design.

### Follow one change

1. Add `printer.lab.example` on DNS-A.
2. The zone’s SOA serial identifies a newer zone version.
3. DNS-B checks for changes and receives an update through zone transfer.
4. Verify that querying DNS-B directly returns the new record.
5. Only then test what happens when DNS-A becomes unavailable.

The secondary does not ask DNS-A for every user query. It answers from its own copy. SOA refresh and retry values control update checks; expiry limits how long it can keep serving a copy without successful refresh. A record’s cache TTL is a different timer. A secondary is therefore not an unlimited offline copy.

### Client settings matter

Clients must be able to discover or be configured to use suitable DNS resolvers. A server called “secondary” is not automatically the client’s alternate DNS setting. Also, a valid negative answer such as NXDOMAIN is not normally a reason to retry every other configured DNS server. Keep the resolvers consistent for your internal namespace.

DNS normally needs both UDP and TCP port 53. Standard full zone transfers use TCP. Permit transfers only to approved servers, not every network client.

If the primary is lost permanently, an administrator can convert a suitable standard secondary zone to a primary. This is not automatic, and the returning old primary must be handled carefully to avoid inconsistent writable copies. Back up the zone and record the change.

**Checkpoint:** When DNS-A fails, must DNS-B be promoted before it can answer existing records?

**Answer:** No. A reachable secondary can already answer from its valid copy. Promotion concerns where future changes are written.

**Next question:** Are two servers sufficient if both depend on the same ESXi host?

---

## Lesson 3 — Count independent failures, not just servers

**Goal:** Understand why your teacher discussed physical servers and ESXi VMs.

Two DNS VMs on one ESXi host protect against some guest failures. They do not protect against that host losing power. Putting them on different hosts improves isolation, but a shared switch, datastore or power supply can still affect both.

Your teacher’s physical-plus-virtual example separates some platform dependencies:

![Teacher’s small-organization AD and DNS sketch](images/01_small_org_ad_dns.png)

Read the sketch as a physical AD/DNS server and another AD/DNS instance on ESXi, connected through network infrastructure. The exact labels are classroom shorthand. The storage symbol alone does not establish that a VM must use an external database.

A **datastore** stores VM files. A guest’s filesystem stores its operating system and application data. AD DS has a directory database, commonly NTDS.dit, in the domain controller’s configured storage. A VM may use local or shared storage; virtualization does not inherently require an external database.

A physical DNS server is one valid choice, not a universal requirement for small organizations. Virtual DNS and domain controllers are also valid designs. Ask which host, disk, switch, power source and site each server depends on. Keep backups too: accidental deletion can replicate to redundant copies.

**Checkpoint:** Will two DNS VMs on the same host survive that host being switched off?

**Answer:** No. They share that host as a failure dependency.

**Next question:** The sketch also says AD. Does that change the primary/secondary model?

---

## Lesson 4 — AD-integrated DNS is a different replication model

**Goal:** Separate identity management from DNS and compare the two zone models.

Your class first studies enterprise standalone DNS without AD. The later small-organization lesson adds AD. These are teaching scenarios, not rules saying enterprise must separate AD/DNS or small organizations must combine them.

**AD DS** manages domain identities such as users, groups and computers. It supports authentication and authorization as part of IAM. DNS helps clients locate services, including domain controllers. DNS does not itself manage user permissions.

**GPO** means Group Policy Object. It can apply suitable settings to users and computers within its linked and filtered scope. The lecture’s “GPU” was likely intended to be GPO; a GPU is graphics hardware. Group Policy Software Installation can deploy suitable MSI packages when requirements such as package access, permissions and policy scope are satisfied. It is not a universal installer for every application.

### Compare only after understanding Lesson 2

| Question | Standard primary/secondary zone | AD-integrated zone |
|---|---|---|
| Where is zone data stored? | Standard zone files | AD DS |
| How does data replicate? | DNS zone transfers | AD replication |
| Where are writes accepted? | Standard primary | Appropriate writable DC/DNS replicas |
| Are all other copies automatically secondary? | A secondary is explicitly configured | No |

![Comparison of zone replication models](images/06_zone_models.png)

Do not automatically call the second writable AD/DNS server “read-only secondary.” An RODC is a distinct design. AD replication scope, permissions and update configuration matter.

![Teacher’s terminology reminder](images/02_teacher_zone_reminders.png)

Read this classroom slide in layers: **forward/reverse** describes lookup direction; **primary/secondary/stub** describes the zone’s role; **AD-integrated** describes storage and replication. These are different questions. The word “SubZone” on the slide should not make you treat every zone type as a delegated child zone.

**Checkpoint:** Do two writable AD-integrated DNS copies necessarily use standard primary-to-secondary transfer?

**Answer:** No. Their AD-integrated data is replicated through AD.

**Next question:** What if we need to find another namespace’s DNS servers without copying all its records?

---

## Lesson 5 — A stub is a signpost, not a backup copy

**Goal:** Understand why a stub cannot replace the secondary from Lesson 2.

Our organization now needs to resolve names in another namespace, `branch.example`. Instead of holding every host record for it, DNS-A holds a stub zone containing information about its authoritative DNS servers.

A stub contains SOA, NS and necessary address/glue records, which may include A and AAAA. Think of categories of records, not exactly three individual entries. It does not hold the complete list of branch host records.

### Walk through one query

1. A client asks DNS-A for `printer.branch.example`.
2. DNS-A uses the stub’s authority information to locate an authoritative server for `branch.example`.
3. DNS-A queries a reachable authority and obtains the answer.
4. DNS-A returns the result to the client and may cache it according to DNS rules.

![How a stub helps find an authoritative server](images/07_stub_query.png)

The authority might be a primary or secondary for that zone. It is not always “the primary.” If every authority is unreachable and no usable cached answer exists, the stub does not magically supply the missing host record. Nor is “stub fails, redirect to primary” a general DNS rule.

A stub can help maintain authority discovery. It does not guarantee faster queries and is not a full disaster recovery copy. A server can host a stub for one zone and other zone types for other namespaces; “stub server forever” is not a permanent hardware identity.

**Checkpoint:** Which holds the complete read-only zone: a secondary or a stub?

**Answer:** A secondary. A stub holds limited authority information.

**Next question:** Where should a full copy live if an entire site fails?

---

## Lesson 6 — Remote DNS helps; complete disaster recovery needs more

**Goal:** Distinguish DNS availability from application recovery.

DNS-A and DNS-B at the same data center can both be lost during a site outage. DNS-C at another location can reduce that shared risk. Hosting a DNS VM in a cloud is one possible approach, but private routing, security, supported transfers and operations still need design. A managed cloud DNS service is not automatically equivalent to a Windows DNS VM.

![Teacher’s enterprise and cloud sketch](images/03_enterprise_stub_cloud.png)

The sketch illustrates primary, stub and cloud-secondary ideas. Repeated ESXi labels are classroom shorthand. It does not prove which real hosts, links or cloud services have been configured.

![Teacher’s site discussion with primary, secondary and stub annotations](images/04_enterprise_site_sketch.png)

### What happens during a data center outage?

| What remains available? | Likely result |
|---|---|
| Remote DNS and a working path to it | Clients on that path may still resolve names |
| Remote DNS but local clients lose their only network path | Those clients may still be unable to query it |
| DNS responds but the application’s only server is in the failed site | Names resolve, but the application is unavailable |
| Remote DNS, application replicas, data and connectivity | A recovery design can restore a broader service |

“Outside world works, internal communication breaks” is one possible topology outcome, not an automatic property of cloud DNS. It depends on which services, records and network paths survive.

A **DR site** is a disaster recovery location. VMware **DRS** means Distributed Resource Scheduler, a different feature. Also spell out **data center** and **domain controller** because both are often abbreviated DC.

### Explain the teacher’s “3–4 hours back” example

Suppose the outage is at 16:00. The newest usable restored data is from 12:00. Up to four hours of changes may be missing. That relates to the recovery point and data-loss tolerance, **RPO**. If service returns at 18:00, recovery took two hours; **RTO** is the target for that recovery duration. The actual outcome and the target are not necessarily the same.

This does not mean changing the Windows clock backward. It means recovering an earlier state of data. Test backups and recovery procedures as well as keeping redundant DNS copies.

**Checkpoint:** Does a cloud secondary alone restore the file server after the entire main site is lost?

**Answer:** No. You also need recoverable workloads, data and usable network connectivity.

**Next question:** How do these ideas map to your actual machines?

---

## Lesson 7 — Your lab: known facts first, planned changes second

**Goal:** Avoid mixing the existing installation with a new lecture plan.

### A. Last evidenced installation checkpoint

These facts come from your supplied Windows Server installation notes. They are a historical checkpoint, not a live inspection today.

| Item | Evidenced value |
|---|---|
| VM display name / Windows hostname | DNS-Win2025 / DNS01 |
| OS | Windows Server 2025 Standard, Desktop Experience |
| Domain state | WORKGROUP; AD deployment not confirmed |
| VM resources | 4 GB RAM, 2 vCPUs, 80 GB split NVMe virtual disk |
| VM path | `D:\VMware-Labs\DNS\DNS-Win2025` |
| Network | NAT VMnet8, `192.168.23.0/24` |
| Server address | `192.168.23.10/24` |
| Gateway | `192.168.23.2` |
| Temporary preferred DNS | `192.168.23.2`; alternate blank |
| DHCP | Disabled on the server; recorded pool `.128–.254` |
| Successful checks | Gateway ping and external name lookup |
| DNS Server role | Not installed at that checkpoint |

An external lookup succeeding at this point shows that the configured resolver worked. It does not prove your own DNS role or zone exists. `Server: Unknown` in nslookup alone does not prove DNS is broken; the resolver’s reverse name may be unavailable.

### B. Teacher’s later three-server plan

![Exact names and addresses from the teacher’s plan](images/05_teacher_three_server_plan.png)

| Class label | Hostname | IP address |
|---|---|---|
| Server01 | ASNZSRVIN01 | 192.168.0.121 |
| Server02 | ASNZSRVIN02 | 192.168.0.122 |
| Server03 | ASNZSRVIN03 | 192.168.0.123 |

The image does not specify prefix length, gateway, VMnet, resolver settings or DNS role assignments. Do not assume `/24`, rename DNS01, or move it to these addresses just to match this table. Verify the intended virtual network and addressing first. A server number does not automatically mean primary, secondary or stub.

The class reminder mentions `VCPLAB.Com`; that is lecture text, not proof that you control the public domain. Our `lab.example` examples are deliberately illustrative.

### C. Follow this order in the next practical lab

1. **Inventory:** record actual hostname, IP/prefix, gateway, virtual network and installed roles for each server.
2. **Choose the class topology:** keep the existing network or build the new one deliberately. Record the decision and confirmed role mapping.
3. **Primary:** install DNS and create a standard primary forward zone for the standalone lesson.
4. **Records:** add a test A record; query that specific server directly. Add and test reverse DNS separately when required.
5. **Secondary:** configure authorized transfers and verify the zone copy on the second server.
6. **Failure test:** query the secondary directly with the primary temporarily unavailable. Record evidence and restore the primary.
7. **Stub exercise:** use a second namespace so its discovery purpose is visible. Do not silently replace the secondary with a stub.
8. **Later AD lesson:** study AD-integrated replication as its own exercise rather than mixing two models during troubleshooting.

These are planned stages, not commands already executed. We will capture exact GUI steps, outputs and results as the lab is performed.

### Safe inventory commands inside each Windows Server

Run the role checks in an elevated PowerShell session. They inspect configuration; they do not install roles or change addresses.

```powershell
hostname
Get-NetIPConfiguration
Get-DnsClientServerAddress -AddressFamily IPv4
Get-WindowsFeature DNS,AD-Domain-Services
Get-Service DNS -ErrorAction SilentlyContinue
```

If the DNS service is absent, check whether the role is installed. Do not invent a successful service output. To test a record once the lab zone exists, replace both placeholders:

```powershell
Resolve-DnsName -Name '<test-name-in-your-zone>' -Server '<DNS-server-IP>' -Type A
```

**Checkpoint:** Is `192.168.0.121` confirmed as your current working server address?

**Answer:** No. It is a teacher plan. Your supplied installation checkpoint used `192.168.23.10`.

**You are ready to continue when:** you can explain primary versus secondary, secondary versus stub, AD replication versus zone transfer, and the difference between an evidenced configuration and a planned one.

---

## Final revision — explain the design in your own words

A primary is where changes are made in our standard-zone example. A secondary keeps a full read-only copy and can answer while that copy is valid. An AD-integrated writable copy follows AD’s replication model. A stub helps find authorities but does not keep the full host list. A remote copy helps only where clients can reach it, and full disaster recovery requires the applications and their data too.

Use this evidence table during the practical session:

| Stage | Expected result | Actual evidence | Status |
|---|---|---|---|
| Inventory | Addresses and roles recorded | To capture | Pending |
| Primary | Test name resolves directly | To capture | Pending |
| Secondary | Same test name resolves on secondary | To capture | Pending |
| Update | New record reaches secondary | To capture | Pending |
| Controlled failure | Valid secondary answers | To capture | Pending |
| Recovery | Primary restored and data consistent | To capture | Pending |
| Stub | Other namespace resolves through reachable authority | To capture | Pending |

### Further reading

These official references support the explanations; they are optional after the lessons.

- [Microsoft: DNS zone types](https://learn.microsoft.com/en-us/windows-server/networking/dns/zone-types)
- [Microsoft: Manage DNS zones](https://learn.microsoft.com/en-us/windows-server/networking/dns/manage-dns-zones)
- [Microsoft: Virtualized domain controllers](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v)
- [Microsoft: Group Policy overview](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-policy/group-policy-overview)
- [Microsoft: Deploy software using Group Policy](https://learn.microsoft.com/en-us/troubleshoot/windows-server/group-policy/use-group-policy-to-install-software)
- [RFC 1034: DNS concepts](https://www.rfc-editor.org/rfc/rfc1034)
- [AWS: Disaster recovery planning](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/plan-for-disaster-recovery-dr.html)

Original class pictures are preserved as supplied. Comparison and query illustrations are explanatory additions. Previous installation screenshots belong in the separate installation notes so this learning route stays focused.
