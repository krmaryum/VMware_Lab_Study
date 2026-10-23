# DNS Before VMware Storage - Study Notes (English)

**Prepared:** 17 October 2026  
**Purpose:** Understand basic DNS concepts after VMware networking and before the storage lecture.  
**Scope:** Concept explanations and read-only Windows checks; no DNS server installation or running lab configuration changes were performed for this guide.

## Index

1. [What is DNS](#1-what-is-dns)
2. [Your VMware lab example](#2-your-vmware-lab-example)
3. [Hostname, domain and FQDN](#3-hostname-domain-and-fqdn)
4. [DNS server versus gateway](#4-dns-server-versus-gateway)
5. [Forward and reverse lookup](#5-forward-and-reverse-lookup)
6. [Important DNS records](#6-important-dns-records)
7. [DNS zone](#7-dns-zone)
8. [Queries, cache and TTL](#8-queries-cache-and-ttl)
9. [Public and private DNS](#9-public-and-private-dns)
10. [DNS in VMware and storage](#10-dns-in-vmware-and-storage)
11. [SSH aliases, hosts files and DNS](#11-ssh-aliases-hosts-files-and-dns)
12. [Practical Windows checks](#12-practical-windows-checks)
13. [Troubleshooting example](#13-troubleshooting-example)
14. [Quick revision](#14-quick-revision)

## 1. What is DNS

**DNS = Domain Name System**

Its main introductory purpose is to find an IP address from a name. You use a name that is easier for a person to remember:

```text
host01.lab.example
```

DNS can return the IP address associated with that name:

```text
10.0.0.10
```

The computer then connects to that IP address.

**DNS does not establish the connection itself; it helps find the address needed for the connection.** DNS also holds other kinds of information; name-to-IP mapping is its introductory use case.

## 2. Your VMware lab example

Previously verified desktop lab mapping:

| ESXi host | IP address |
|---|---|
| HOST01 - ESXi 9 | 10.0.0.10 |
| HOST02 - ESXi 8 | 10.0.0.11 |

This is previous lab evidence; the hosts were not live-tested again for this guide.

You currently use an IP address in the browser:

```text
https://10.0.0.10
```

With DNS configured, you can use a name:

```text
https://host01.lab.example
```

`lab.example` is only a learning example, not a claim that this domain is configured in your lab.

**Setting the ESXi hostname to HOST01 does not automatically create a DNS record.** The name-to-IP record must be available in the relevant DNS system. Even after the name resolves, the browser may separately check the certificate's name and trust.

## 3. Hostname, domain and FQDN

Example:

```text
host01.lab.example
```

| Part | Meaning |
|---|---|
| host01 | Hostname |
| lab.example | Domain |
| host01.lab.example | FQDN - Fully Qualified Domain Name |

**An FQDN is the computer's complete DNS name.**

When you enter only `host01`, the computer may use its configured DNS suffix/search settings. Using the complete FQDN makes the intended name clearer. In DNS tools, a trailing dot, such as `host01.lab.example.`, can indicate an absolute DNS name.

## 4. DNS server versus gateway

A DNS server is a system that answers DNS queries.

```text
Question: What is the IP address of host01.lab.example?
Answer:   10.0.0.10
```

The Windows computer must know which DNS server to ask. The DNS server address can be configured manually in network settings or supplied through DHCP.

| Setting | Function |
|---|---|
| IP address | Your device's network address |
| Subnet mask | Identifies which addresses belong to the local subnet |
| Default gateway | The route used to reach other networks when no more specific route applies |
| DNS server | The destination for name-resolution queries |

A router can also provide DNS forwarding, but **gateway and DNS are logically different functions**. They can have the same address, but this is not compulsory.

## 5. Forward and reverse lookup

### Forward lookup: Name to IP

```text
host01.lab.example -> 10.0.0.10
```

Question: What is the IP address of this name?

### Reverse lookup: IP to name

```text
10.0.0.10 -> host01.lab.example
```

Question: What is the DNS name associated with this IP?

**Creating a forward record does not automatically create a reverse record in every system.** You may need to configure and verify both separately. Keeping forward and reverse results consistent is useful for administration and troubleshooting.

## 6. Important DNS records

Start with these four records:

| Record | Function | Example |
|---|---|---|
| A | Maps a name to an IPv4 address | host01.lab.example -> 10.0.0.10 |
| AAAA | Maps a name to an IPv6 address | Hostname -> IPv6 address |
| PTR | Associates an IP with a name for reverse lookup | 10.0.0.10 -> host01.lab.example |
| CNAME | Makes one DNS name an alias of another DNS name | esxi.lab.example -> host01.lab.example |

**A CNAME does not point directly to an IP address; it points to another DNS name.**

You will also encounter these records later:

| Record | Purpose |
|---|---|
| NS | Identifies authoritative DNS servers for a zone |
| SOA | Holds administrative information about a zone |
| MX | Supports email server routing |
| TXT | Holds text-based information, including verification data |

## 7. DNS zone

**A zone is a portion of the DNS namespace whose records are managed as an administrative unit.**

Simple lab example:

```text
Zone: lab.example

host01    A    10.0.0.10
host02    A    10.0.0.11
```

When the teacher mentions Forward Lookup Zone and Reverse Lookup Zone:

| Zone | Purpose |
|---|---|
| Forward lookup zone | Resolve names to IP addresses |
| Reverse lookup zone | Resolve IP addresses to names |

Domains and zones are related concepts; their boundaries can differ in advanced delegation designs. For a beginner lab, first understand zone records and lookup direction.

## 8. Queries, cache and TTL

The conceptual sequence when you enter a hostname in a browser:

1. The computer checks local name-resolution information/cache.
2. If needed, it sends a query to the configured DNS resolver.
3. The resolver answers from its records/cache or obtains an answer from the appropriate DNS servers.
4. The computer attempts to connect to the returned IP address.

Exact lookup behavior can vary with application and OS configuration.

**Not every query goes to the Internet.** A local DNS server can answer a local lab record directly.

### Cache and TTL

DNS answers are cached for a period of time.

**TTL = Time To Live:** how long a DNS record can be reused from cache.

After a record changes, some clients may temporarily use an older answer. Distinguish a cached answer from the DNS server's current authoritative record.

## 9. Public and private DNS

| Type | Example use |
|---|---|
| Public DNS | Resolve public website names |
| Private/internal DNS | Resolve internal company or home-lab systems |

If you create this record in local DNS:

```text
host01.lab.example -> 10.0.0.10
```

A public DNS server does not automatically learn that private record.

**The client must use a DNS resolver that can resolve the internal zone.** Internal DNS can also provide appropriate forwarding/recursive resolution for public names; that configuration is a separate topic.

## 10. DNS in VMware and storage

Consistent names are useful for identifying ESXi hosts, vCenter and other VMware infrastructure components.

During the storage lecture, you may encounter a NAS/NFS server name such as:

```text
nas01.lab.example
```

If a hostname is used and DNS fails, the storage server's address cannot be resolved through that name.

**Successful DNS resolution does not prove that storage is working.** Routing, required ports, permissions and the storage service must also be correct.

| Check | What does it verify? |
|---|---|
| DNS lookup | The name resolves to an address |
| Network/service connectivity | The required destination/service is reachable |
| Storage authentication/permissions | The client is allowed access |
| Mount/datastore operation | The actual storage operation works |

## 11. SSH aliases, hosts files and DNS

Your existing alias:

```powershell
ssh desktop-esxi9
```

SSH configuration example:

```sshconfig
Host desktop-esxi9
    HostName 10.0.0.10
    User root
```

This is a local nickname used by the SSH client.

| Method | Where does it work? |
|---|---|
| SSH configuration alias | In the matching SSH client configuration |
| Windows hosts-file entry | In applicable name resolution on that machine |
| DNS record | For clients using the relevant DNS system |

Therefore, `ssh desktop-esxi9` working does not mean that `https://desktop-esxi9` must work in a browser. The SSH alias does not need to resolve through `nslookup` when its HostName is a direct IP address.

## 12. Practical Windows checks

These are read-only checks; they do not change settings. Run them in Windows PowerShell, not inside the ESXi shell.

### Configured DNS servers

```powershell
Get-DnsClientServerAddress -AddressFamily IPv4
```

Entries for different adapters may appear; identify the active/relevant adapter.

### Public name lookup

```powershell
nslookup example.com
```

The output normally shows the DNS server answering the query and the addresses returned for the requested name.

### Lab name lookup

**After the record is configured:**

```powershell
nslookup host01.lab.example
```

If the record has not been created yet, failure may be expected. Do not treat the example hostname as an existing configuration.

`nslookup` queries DNS directly; it does not test your SSH alias. Hosts-file-based resolution and a direct DNS query may give different results.

### Optional reverse lookup

```powershell
nslookup 10.0.0.10
```

If a PTR record is configured and the relevant reverse DNS is reachable, a name may be returned. A forward record does not guarantee a reverse result.

## 13. Troubleshooting example

Suppose:

```text
https://10.0.0.10             -> Works
https://host01.lab.example   -> Fails
```

Start by checking name resolution:

```powershell
nslookup host01.lab.example
```

| Result | Possible next check |
|---|---|
| Name not found | The record and requested spelling |
| DNS request timed out | Configured DNS server and reachability |
| Wrong IP returned | The record and cache |
| Correct IP returned | HTTPS connectivity, certificate and application |

Do not immediately reinstall ESXi or reset networking because a name fails. Troubleshoot separate layers: name resolution, connectivity, then the service/application.

## 14. Quick revision

| Question | Answer |
|---|---|
| What does DNS stand for? | Domain Name System |
| What is its main introductory use? | Find an IP address from a name |
| A record? | Name to IPv4 |
| AAAA record? | Name to IPv6 |
| PTR record? | Reverse lookup |
| CNAME record? | A DNS name alias pointing to another DNS name |
| FQDN? | Fully Qualified Domain Name |
| TTL? | Time To Live; the reuse duration of a cached DNS answer |
| Are gateway and DNS the same function? | No |
| Does setting an ESXi hostname automatically create DNS? | Not necessarily |
| Is an SSH alias a DNS record? | No |
| Does DNS success prove storage success? | No |

Keep these five points clear before the teacher's lecture:

1. DNS resolves names.
2. An A record supports forward IPv4 lookup.
3. A PTR record supports reverse lookup.
4. The client needs the correct DNS server.
5. Setting a hostname and creating a DNS record are separate steps.

**Next lesson:** Configure the DNS server/zone according to the instructor's design, create A/PTR records and verify resolution from Windows/ESXi. This guide does not assume that a domain/server has already been configured.
