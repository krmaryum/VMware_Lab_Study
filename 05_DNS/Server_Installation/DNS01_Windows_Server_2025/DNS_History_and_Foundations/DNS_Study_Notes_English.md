# DNS — History, Foundations and Your VMware Lab

Date: October 20, 2026 | Theory companion | Lab paused after Windows installation

## Index

1. [Purpose and learning order](#chapter-1)
2. [Why names need resolution](#chapter-2)
3. [History: HOSTS.TXT to distributed DNS](#chapter-3)
4. [Hosts files, LMHOSTS, BIND, WINS and DNS](#chapter-4)
5. [Names, hierarchy and the TLD correction](#chapter-5)
6. [Resolver, DNS server and a query](#chapter-6)
7. [Records and zones](#chapter-7)
8. [Redundancy: enterprise now and AD later](#chapter-8)
9. [Your exact lab checkpoint — no changes](#chapter-9)
10. [Optional read-only observation and troubleshooting](#chapter-10)
11. [Revision questions and next session](#chapter-11)

<a id="chapter-1"></a>

## 1. Purpose and learning order

This is a theory companion to your completed Windows installation notes. Keep theory and installation evidence separate, but linked. Read this guide before the next DNS configuration session. The lab is paused: do not change preferred DNS, install roles, create zones, or deploy cloud resources while reading.

Our sequence follows your class: enterprise standalone DNS **without Active Directory first**; small-organization **AD-integrated DNS later**. These are teaching examples, not rules based on company size. Enterprises can use AD and small organizations can use standalone DNS.

The bundle includes original installation notes and their 50 screenshots, your new class reminder screenshot, and four newly drawn conceptual diagrams. Redrawn designs are illustrations, not proof that those environments were deployed.

<a id="chapter-2"></a>

## 2. Why names need resolution

People remember names such as `dns01.lab.example`; IP networking delivers packets using addresses. Name resolution obtains an address or other identifier associated with a name. DNS is one major mechanism; a hosts file is another local mechanism.

DNS means **Domain Name System**. It is a distributed naming system and protocol family, not one worldwide server and not merely a Windows feature. It can return addresses and other record types.

Example only: `dns01.lab.example` could map to `192.168.23.10`. This record does **not** exist merely because we wrote it here. `lab.example` is a documentation example, not your selected lab domain. DNS does not create routes, allow firewall traffic, authenticate root, or start the destination service.

<a id="chapter-3"></a>

## 3. History: HOSTS.TXT to distributed DNS

Before DNS, the early Internet used a centrally maintained HOSTS.TXT table distributed to hosts. As the network grew, frequent changes and keeping copies synchronized became difficult. RFC 882 explicitly describes that scaling problem. [S1]

| Period | Development | Why it matters |
| --- | --- | --- |
| Before DNS | Central host table | Manual distribution did not scale well |
| November 1983 | Paul Mockapetris published RFC 882; companion RFC 883 described implementation | Early DNS design and protocol specifications |
| 1984 | RFC 920 discussed domain requirements | Deployment and administrative naming organization |
| November 1987 | RFC 1034 and RFC 1035 replaced the earlier core specifications | Foundational DNS concepts and wire/data formats |
| Early 1980s onward | Berkeley developed BIND | Software implementing DNS became available |
| Legacy Windows era | NetBIOS name resolution and WINS | Separate historical naming requirement |
| 2000 | BIND 9 first released; Windows 2000/AD is a separate platform milestone | Neither milestone is the invention of DNS |

This is not a compulsory upgrade chain from hosts to BIND to WINS to DNS. Different mechanisms overlapped and served different purposes. [S1–S5]

![DNS history](images/01_history.png)

<a id="chapter-4"></a>

## 4. Hosts files, LMHOSTS, BIND, WINS and DNS

| Item | What it is | What it resolves / does |
| --- | --- | --- |
| Windows hosts | Local text file | Static hostname-to-address mappings for applications using the relevant system resolver |
| LMHOSTS | Legacy local mapping file | NetBIOS names to IPv4 addresses |
| DNS | Naming system and protocols | Queries for domain-name resource records |
| BIND | Berkeley Internet Name Domain software | Implements DNS server functions |
| WINS | Windows Internet Name Service | Legacy dynamic NetBIOS name registration and resolution |
| Windows DNS Server | Microsoft DNS implementation | Hosts zones and answers DNS queries |
| SSH config Host alias | SSH client configuration | Selects target address, user and identity file for SSH |

**BIND is not a competitor protocol to DNS.** Installing BIND means choosing DNS software. Its origins are Berkeley/Unix; that does not mean DNS is only for Unix. BIND 9 is open-source software maintained by ISC. “Open standard DNS” and “open-source BIND” are different ideas. [S4, S5]

**WINS is not LMHOSTS.** WINS uses a server database and client registration; LMHOSTS is a local file. Microsoft describes WINS as legacy and recommends DNS for new environments. We study WINS for understanding older Windows environments, not as a required part of this new DNS lab. [S6]

Your earlier `ssh dell-esxi9` worked because SSH knew the alias. Chrome and Test-NetConnection needed a Windows name mapping. Adding a hosts entry fixed that local resolution; it did not deploy a DNS server.

<a id="chapter-5"></a>

## 5. Names, hierarchy and the TLD correction

Read the educational name `dns01.lab.example.` from right to left: final dot = DNS root; `example` = reserved example TLD; `lab` = child domain; `dns01` = host label. Its fully qualified domain name (FQDN) includes all labels. Software often displays an FQDN without the final dot.

A hostname can have multiple addresses, and an address can serve multiple names. Renaming Windows does not automatically create a record in an unrelated DNS zone.

Your class screenshot lists “DNS - 2000 - Open Source” and “Top Level Domain - 7”. Treat these as reminders needing qualification:

| Class reminder | Explanation |
| --- | --- |
| DNS - 2000 | DNS predates 2000; 1983 core documents prove it |
| Open Source | DNS is an open standard; software licensing depends on the implementation |
| .com, .edu, .org, .mil, .net, .gov | Familiar historical generic categories; not the complete modern root |
| .ac | Country-code TLD for Ascension Island; not a generic worldwide academic category |
| .ac.uk or .ac.in | Academic naming categories under particular country domains |
| Historical seven generic TLDs | Common list: .com, .edu, .gov, .int, .mil, .net, .org; introduced over time, not all in one original release |
| Modern TLDs | Include country codes and many newer generic domains; no fixed total of seven |

RFC 920 lists early administrative categories; use IANA's root database for modern TLD delegations. The name appearing at the bottom of the class screenshot is teacher material, not automatically a domain you own or should create. [S2, S7]

![Original teacher reminder screenshot](images/teacher_class_reminders.png)

<a id="chapter-6"></a>

## 6. Resolver, DNS server and a query

The client-side resolver asks a configured DNS server. A recursive resolver obtains an answer on the client's behalf; an authoritative server answers from zone data it is responsible for. One DNS software installation can serve both roles when configured that way.

For a public name with no usable cached answer, a resolver may follow referrals from root to TLD to authoritative servers. A configured forwarder offers another path. For a locally hosted authoritative zone, the server can answer directly; root servers are not consulted for every lookup. [S3]

A **recursive query** asks the recipient to obtain a final answer or failure. An **iterative exchange** can return a referral. TTL limits ordinary cache lifetime; caches can keep earlier answers until expiry. A negative answer can also be cached.

![DNS query roles](images/02_lookup.png)

DNS lookup and HTTPS connection are separate actions: first obtain an address; then connect to that address and service port. Your earlier desktop incident demonstrated this: the name resolved correctly while port 443 still failed.

<a id="chapter-7"></a>

## 7. Records and zones

| Record | Meaning | Educational example |
| --- | --- | --- |
| A | Name to IPv4 | dns01.lab.example → 192.168.23.10 |
| AAAA | Name to IPv6 | IPv6 address for a host |
| PTR | Reverse mapping | 192.168.23.10 → dns01.lab.example |
| CNAME | Alias to another DNS name | portal.lab.example → web01.lab.example |
| MX | Mail exchanger | Mail delivery destination for a domain |
| NS | Authoritative name server | Servers responsible for a zone |
| SOA | Zone authority and timing metadata | Serial, refresh, retry and expiry |
| TXT | Text information | Verification or mail-related policies |
| SRV | Service location | Target host, port, priority and weight |

A CNAME is not the same as an SSH alias. MX identifies a mail server name, not simply a mailbox. SRV is not limited to chat. A and PTR records are separate; one need not exist just because the other does. [S8]

A forward zone organizes name-based records. A reverse zone organizes address-to-name PTR records. **Direction** (forward/reverse) and **zone type/storage** are different dimensions.

Standard primary = writable original zone; secondary = read-only transferred copy; stub = limited authority-location records, not all host records. AD-integrated zone data is stored/replicated in AD and can support multiple writable DNS/DC copies. These are not all “sub-zones”. A delegated child domain is a different concept. [S9]

<a id="chapter-8"></a>

## 8. Redundancy: enterprise now and AD later

Teacher enterprise examples: primary on premises; a secondary VM on another ESXi host; optionally a cloud secondary. Use unique names DNS01/DNS02/DNS03 in explanations even where a sketch repeats DNS01. These topologies are conceptual and are not deployed in your current one-server lab.

A standard secondary gets zone data through authorized zone transfers. A primary outage can leave the secondary answering from its valid copy, but after SOA expiry without refresh it cannot continue serving that expired zone. Configure clients with appropriate reachable internal DNS servers. Secondary zone role is not the same as a client's alternate DNS preference. A negative answer is not necessarily a trigger to ask another configured server.

![Standalone DNS redundancy](images/03_redundancy.png)

Failure domains matter: two DNS VMs on one host share host failure; different hosts on one switch share switch failure. Cloud helps only when its service and connectivity remain available. External users may reach cloud DNS while the local site is down, but DNS answering does not restore an application that exists only in the failed site. Cloud DNS needs an appropriate private/public design, routing, and access controls; do not expose internal recursive DNS to the Internet.

Next week's AD example will explain DNS/DC integration and AD replication separately from standard zone transfers. AD is not a prerequisite for installing standalone Windows DNS. [S9, S10]

<a id="chapter-9"></a>

## 9. Your exact lab checkpoint — no changes

The supplied installation bundles (1) and (2) are byte-for-byte identical. One installation copy is included here.

| Item | Recorded state |
| --- | --- |
| VM display name / guest hostname | DNS-Win2025 / DNS01 |
| OS | Windows Server 2025 Standard, Desktop Experience |
| Workgroup | WORKGROUP; no AD deployment recorded |
| Resources | 4 GB RAM, 2 vCPUs, 80 GB disk |
| Network | NAT / VMnet8, 192.168.23.0/24 |
| Static address | 192.168.23.10; DHCP disabled |
| Gateway | 192.168.23.2 |
| Preferred DNS | 192.168.23.2 — temporary VMware resolver setting |
| Evidence | Gateway ping and microsoft.com lookup succeeded |
| DNS Server role / zones | Pending; not installed/configured in Part 1 |

![Current lab checkpoint](images/04_lab.png)

**DNS01 is currently using DNS, not yet providing the planned DNS service.** The machine name alone is not evidence of role installation. Do not point its preferred DNS to itself until the later planned installation/configuration and verification steps.

Installation details remain in the included [Part 1 notes](Installation_Part1/Windows_Server_2025_DNS_Lab_Part1_English/STUDY_NOTES_ENGLISH.md). They document a corrected typo: 192.168.231.10 was wrong; final address is 192.168.23.10. Old screenshots are not all final settings.

<a id="chapter-10"></a>

## 10. Optional read-only observation and troubleshooting

No new lab is required now. If reviewing DNS01, these commands only inspect/test; they do not install a role or create records:

```powershell
hostname
ipconfig /all
Get-DnsClientServerAddress -AddressFamily IPv4
nslookup microsoft.com
nslookup microsoft.com 192.168.23.2
```

Run the explicit .2 query inside this VMnet8 environment; other machines may not have a route to it. Output addresses for public names can change. `Server: Unknown` can mean the resolver address has no resolvable reverse name; it does not by itself mean the requested lookup failed.

To inspect the role from elevated PowerShell **inside Windows Server**, not the physical Windows client:

```powershell
Get-WindowsFeature -Name DNS
```

This is an optional future verification command, not a result we observed in the supplied bundle. Distinguish the DNS Client service from the DNS Server role.

| Result | First interpretation |
| --- | --- |
| Name resolution fails before an address appears | Check spelling, resolver settings, hosts/DNS data |
| Correct IP but TCP 443 fails | Check VM power, routes, adapter attachment, firewall and HTTPS service |
| NXDOMAIN / nonexistent domain | The queried name is reported not to exist; check the right zone/server |
| DNS timeout | No timely answer; check reachability, service and rules |
| Correct DNS answer but browser certificate warning | Check certificate identity/trust separately |

Ordinary DNS uses UDP and TCP port 53; do not assume UDP only. Test-NetConnection -Port 53 checks TCP, not a complete UDP DNS transaction. Prefer an actual DNS query to test DNS behavior. nslookup queries DNS servers and does not validate Windows hosts-file behavior.

<a id="chapter-11"></a>

## 11. Revision questions and next session

1. **DNS versus BIND?** DNS is the system/standard; BIND is DNS software.
2. **WINS versus DNS?** Legacy NetBIOS name registration/resolution versus domain-name record queries.
3. **Did DNS start in 2000?** No; core DNS documents were published in 1983.
4. **Why move beyond HOSTS.TXT?** Central distribution and frequent updates did not scale.
5. **Does .ac mean every academic institution?** No; .ac is a country-code TLD.
6. **Forward versus reverse?** Name-based lookup versus address-to-name lookup.
7. **Primary versus secondary?** Writable standard zone versus read-only transferred copy.
8. **Does a secondary mean all clients automatically fail over?** No; client configuration, reachability and resolver behavior matter.
9. **Does cloud DNS alone recover a data center?** No; applications and their data need recovery too.
10. **Is DNS01 already a DNS server?** Not in the recorded Part 1 state; the role is pending.

Next time: verify current DNS01 settings, inspect DNS role state, then follow the teacher's standalone DNS installation and zone creation sequence. Choose the lab namespace deliberately before creating zones. Add evidence as each step is completed. Secondary servers, cloud and AD remain later phases.

## Sources / Hawalay

Primary references checked October 20, 2026. Source IDs in text map to the links below. The class screenshot is user-provided material; corrections and conceptual diagrams are explanatory additions.

- [S1 — RFC 882, November 1983: original DNS concepts and HOSTS.TXT scaling problem](https://www.rfc-editor.org/rfc/rfc882)
- [S2 — RFC 920, 1984: domain requirements](https://www.rfc-editor.org/rfc/rfc920)
- [S3 — RFC 1034, 1987: DNS concepts](https://www.rfc-editor.org/rfc/rfc1034)
- [S4 — ISC: History of BIND](https://www.isc.org/bindhistory/)
- [S5 — ISC: BIND 9 software](https://www.isc.org/bind/)
- [S6 — Microsoft: WINS overview](https://learn.microsoft.com/en-us/windows-server/networking/technologies/wins/wins-top)
- [S7 — IANA: Root Zone Database](https://www.iana.org/domains/root/db)
- [S8 — RFC 1035: core records and DNS transport](https://www.rfc-editor.org/rfc/rfc1035)
- [S9 — Microsoft: DNS zone types](https://learn.microsoft.com/en-us/windows-server/networking/dns/zone-types)
- [S10 — Microsoft: DNS overview](https://learn.microsoft.com/en-us/windows-server/networking/dns/dns-overview)

SRV is a later extension to the original record set; see [RFC 2782](https://www.rfc-editor.org/rfc/rfc2782). Reserved documentation names: [RFC 2606](https://www.rfc-editor.org/rfc/rfc2606). These supplemental standards are provided for further reading.
