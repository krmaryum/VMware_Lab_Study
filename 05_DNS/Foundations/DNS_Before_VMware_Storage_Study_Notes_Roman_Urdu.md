# DNS Before VMware Storage - Study Notes (Roman Urdu)

**Prepared:** 17 October 2026  
**Purpose:** VMware networking ke baad aur storage lecture se pehle DNS ke basic concepts samajhna.  
**Scope:** Concept explanations aur read-only Windows checks; DNS server installation ya running lab configuration changes is guide mein perform nahi ki gayin.

## Index

1. [DNS kya hai](#1-dns-kya-hai)
2. [Aapke VMware lab ka example](#2-aapke-vmware-lab-ka-example)
3. [Hostname, domain aur FQDN](#3-hostname-domain-aur-fqdn)
4. [DNS server aur gateway ka farq](#4-dns-server-aur-gateway-ka-farq)
5. [Forward aur reverse lookup](#5-forward-aur-reverse-lookup)
6. [Important DNS records](#6-important-dns-records)
7. [DNS zone](#7-dns-zone)
8. [Query, cache aur TTL](#8-query-cache-aur-ttl)
9. [Public aur private DNS](#9-public-aur-private-dns)
10. [VMware aur storage mein DNS](#10-vmware-aur-storage-mein-dns)
11. [SSH alias, hosts file aur DNS](#11-ssh-alias-hosts-file-aur-dns)
12. [Windows par practical checks](#12-windows-par-practical-checks)
13. [Troubleshooting example](#13-troubleshooting-example)
14. [Quick revision](#14-quick-revision)

## 1. DNS kya hai

**DNS = Domain Name System**

Iska main kaam naam se IP address maloom karna hai. Aap insaan ke liye yaad rehne wala naam use karte hain:

```text
host01.lab.example
```

DNS us naam ka IP bata sakta hai:

```text
10.0.0.10
```

Phir computer us IP se connection banata hai.

**DNS connection khud nahi banata; connection ke liye address dhoondhne mein madad karta hai.** DNS mein doosri tarah ki information bhi hoti hai; name-to-IP mapping iska introductory use case hai.

## 2. Aapke VMware lab ka example

Desktop lab ki pehle verified mapping:

| ESXi host | IP address |
|---|---|
| HOST01 - ESXi 9 | 10.0.0.10 |
| HOST02 - ESXi 8 | 10.0.0.11 |

Yeh previous lab evidence hai; is guide ke liye hosts ko live re-test nahi kiya gaya.

Aap browser mein IP use karte hain:

```text
https://10.0.0.10
```

DNS configure ho to naam se access kar sakte hain:

```text
https://host01.lab.example
```

`lab.example` sirf learning example hai, aapke lab mein configured domain hone ka claim nahi.

**Sirf ESXi ka hostname HOST01 rakhne se DNS record automatically create nahi hota.** Naam aur IP ka record relevant DNS mein available hona chahiye. DNS name resolve hone ke baad bhi browser certificate ka name/trust check separately ho sakta hai.

## 3. Hostname, domain aur FQDN

Example:

```text
host01.lab.example
```

| Hissa | Matlab |
|---|---|
| host01 | Hostname |
| lab.example | Domain |
| host01.lab.example | FQDN - Fully Qualified Domain Name |

**FQDN computer ka complete DNS name hai.**

Sirf `host01` likhne par computer configured DNS suffix/search settings use kar sakta hai. Complete FQDN likhne se intended naam zyada clear hota hai. DNS tools mein terminal dot, jaise `host01.lab.example.`, absolute DNS name indicate kar sakta hai.

## 4. DNS server aur gateway ka farq

DNS server woh system hai jo DNS queries ke answers deta hai.

```text
Question: host01.lab.example ka IP kya hai?
Answer:   10.0.0.10
```

Windows computer ko pata hona chahiye ke kis DNS server se poochna hai. DNS server address network settings mein manually configure ho sakta hai ya DHCP se mil sakta hai.

| Setting | Kaam |
|---|---|
| IP address | Aapke device ka network address |
| Subnet mask | Batata hai kaunsa address local subnet mein hai |
| Default gateway | Doosre networks tak traffic bhejne ka raasta |
| DNS server | Naam resolve karne ke liye query ka destination |

Router DNS forwarding bhi kar sakta hai, lekin **gateway aur DNS logically alag functions hain**. Dono ka same address hona possible hai, compulsory nahi.

## 5. Forward aur reverse lookup

### Forward lookup: Name to IP

```text
host01.lab.example -> 10.0.0.10
```

Sawal: Is naam ka IP kya hai?

### Reverse lookup: IP to name

```text
10.0.0.10 -> host01.lab.example
```

Sawal: Is IP ka DNS name kya hai?

**Forward record banane se reverse record har system mein automatically nahi banta.** Dono ko separately configure/verify karna pad sakta hai. Reverse aur forward results ko consistent rakhna administration/troubleshooting mein useful hai.

## 6. Important DNS records

Pehle in four records ko samjhein:

| Record | Kaam | Example |
|---|---|---|
| A | Name ko IPv4 se map karta hai | host01.lab.example -> 10.0.0.10 |
| AAAA | Name ko IPv6 se map karta hai | Hostname -> IPv6 address |
| PTR | Reverse lookup mein IP ko name se relate karta hai | 10.0.0.10 -> host01.lab.example |
| CNAME | Ek DNS name ko doosre DNS name ka alias banata hai | esxi.lab.example -> host01.lab.example |

**CNAME directly IP address ko point nahi karta; doosre DNS name ko point karta hai.**

Baad mein yeh records bhi aayenge:

| Record | Purpose |
|---|---|
| NS | Zone ke authoritative DNS servers identify karta hai |
| SOA | Zone ki administrative information rakhta hai |
| MX | Email server routing ke liye |
| TXT | Text-based information aur verification ke liye |

## 7. DNS zone

**Zone DNS namespace ka woh hissa hai jiske records ek administrative unit ke taur par manage hote hain.**

Simple lab example:

```text
Zone: lab.example

host01    A    10.0.0.10
host02    A    10.0.0.11
```

Teacher jab Forward Lookup Zone aur Reverse Lookup Zone kahe:

| Zone | Purpose |
|---|---|
| Forward lookup zone | Name se IP resolve karna |
| Reverse lookup zone | IP se name resolve karna |

Domain aur zone related concepts hain; advanced delegation designs mein unki boundaries different ho sakti hain. Beginner lab mein pehle zone ke records aur lookup direction samjhein.

## 8. Query, cache aur TTL

Browser mein hostname enter karne par conceptual sequence:

1. Computer local name-resolution information/cache check karta hai.
2. Zaroorat par configured DNS resolver ko query bhejta hai.
3. Resolver apne records/cache se answer deta hai ya appropriate DNS servers se answer hasil karta hai.
4. Computer returned IP par connection try karta hai.

Exact lookup behavior application aur OS configuration ke mutabiq farq ho sakta hai.

**Har query Internet tak nahi jati.** Local lab record local DNS server directly answer kar sakta hai.

### Cache aur TTL

DNS answers kuch waqt cache hote hain.

**TTL = Time To Live:** record ko cache mein kitni der reuse kiya ja sakta hai.

Is liye record change ke baad kuch clients temporarily purana answer use kar sakte hain. Cache mein stored answer aur DNS server ke current authoritative record ko alag samjhein.

## 9. Public aur private DNS

| Type | Example use |
|---|---|
| Public DNS | Public websites ke names resolve karna |
| Private/internal DNS | Company ya home lab ke internal systems resolve karna |

Agar local DNS mein yeh banaya hai:

```text
host01.lab.example -> 10.0.0.10
```

Public DNS server ko private record ka automatically pata nahi chalega.

**Client ko aisa DNS resolver use karna hoga jo internal zone resolve kar sake.** Internal DNS public names ke liye appropriate forwarding/recursive resolution bhi provide kar sakta hai; uski configuration separate topic hai.

## 10. VMware aur storage mein DNS

VMware infrastructure mein ESXi hosts, vCenter aur doosre components ko consistent names se identify karna useful hai.

Storage lecture mein NAS/NFS server jaisa naam mil sakta hai:

```text
nas01.lab.example
```

Agar hostname use ho aur DNS fail kare, storage server ka address resolve nahi hoga.

**DNS resolve hona storage working hone ka complete proof nahi.** Routing, required ports, permissions aur storage service bhi sahi honi chahiye.

| Check | Kya verify hota hai? |
|---|---|
| DNS lookup | Naam ka address resolve ho raha hai |
| Network/service connectivity | Required destination/service reachable hai |
| Storage authentication/permissions | Client ko access allowed hai |
| Mount/datastore operation | Actual storage operation kaam kar rahi hai |

## 11. SSH alias, hosts file aur DNS

Aapka existing alias:

```powershell
ssh desktop-esxi9
```

SSH config example:

```sshconfig
Host desktop-esxi9
    HostName 10.0.0.10
    User root
```

Yeh SSH client ka local nickname hai.

| Method | Kahan kaam karta hai? |
|---|---|
| SSH config alias | Matching SSH client configuration mein |
| Windows hosts-file entry | Us machine ki applicable name resolution mein |
| DNS record | Relevant DNS system use karne wale clients ke liye |

Is liye `ssh desktop-esxi9` chalne se browser mein `https://desktop-esxi9` chalna zaroori nahi. SSH alias ko `nslookup` se resolve karna required nahi jab uska HostName direct IP hai.

## 12. Windows par practical checks

Yeh read-only checks hain; settings change nahi hongi. Windows PowerShell mein run karein, ESXi shell mein nahi.

### Configured DNS servers

```powershell
Get-DnsClientServerAddress -AddressFamily IPv4
```

Different adapters ki entries aa sakti hain; active/relevant adapter identify karein.

### Public name lookup

```powershell
nslookup example.com
```

Output mein aam tor par query answer karne wala DNS server aur requested name ke returned addresses dikhte hain.

### Lab name lookup

**Record configure hone ke baad:**

```powershell
nslookup host01.lab.example
```

Abhi record nahi bana to failure expected ho sakta hai. Example hostname ko configured fact na samjhein.

`nslookup` DNS ko directly query karta hai; SSH alias ko test nahi karta. Hosts-file based resolution aur direct DNS query ka result different ho sakta hai.

### Optional reverse lookup

```powershell
nslookup 10.0.0.10
```

PTR record configured aur relevant reverse DNS reachable ho to name mil sakta hai. Forward record ki presence reverse result guarantee nahi.

## 13. Troubleshooting example

Maan lein:

```text
https://10.0.0.10             -> Works
https://host01.lab.example   -> Fails
```

Pehla check name resolution:

```powershell
nslookup host01.lab.example
```

| Result | Possible next check |
|---|---|
| Name not found | Record aur requested spelling |
| DNS request timed out | Configured DNS server aur reachability |
| Wrong IP returned | Record aur cache |
| Correct IP returned | HTTPS connectivity, certificate aur application |

Naam fail hone par foran ESXi reinstall ya network reset na karein. Problem ko separate layers mein check karein: name resolution, connectivity, phir service/application.

## 14. Quick revision

| Sawal | Answer |
|---|---|
| DNS ka full form? | Domain Name System |
| DNS ka main introductory use? | Naam se IP maloom karna |
| A record? | Name to IPv4 |
| AAAA record? | Name to IPv6 |
| PTR record? | Reverse lookup |
| CNAME record? | DNS name ka alias doosre DNS name ki taraf |
| FQDN? | Fully Qualified Domain Name |
| TTL? | Time To Live; cached DNS answer ki reuse duration |
| Gateway aur DNS same function? | Nahi |
| ESXi hostname set se DNS automatically banega? | Zaroori nahi |
| SSH alias DNS record hai? | Nahi |
| DNS success se storage success prove hota hai? | Nahi |

Teacher ke lecture se pehle five points clear rakhein:

1. DNS naam resolve karta hai.
2. A record forward IPv4 lookup ke liye hai.
3. PTR reverse lookup ke liye hai.
4. Client ko correct DNS server chahiye.
5. Hostname set karna aur DNS record banana separate steps hain.

**Next lesson:** Instructor ke design ke mutabiq DNS server/zone configure karna, A/PTR records banana aur Windows/ESXi se resolution verify karna. Is guide mein koi domain/server pehle se configured assume nahi kiya gaya.
