# nslookup (Name Server Lookup) — DNS troubleshooting ke study notes

## Maqsad

`nslookup` DNS server se DNS ki maloomat poochta hai. Naam ka ghalat IP aaye, aik DNS server doosray se mukhtalif jawab de ya reverse lookup missing ho to yeh tool madad karta hai. Mukhtalif record types query ho sakti hain. Yeh zone configure, record create ya connectivity repair nahin karta.

Examples aapki private classroom zone `VCPLAB.Com` par hain. Yeh repeat-practice commands hain, naye live test results nahin. Neeche sample output sirf samjhane ke liye hai. Commands chalane wali machine ka chosen DNS server tak working network path chahiye; Surface automatically Workstation host-only network ka member nahin hota.

## 1. Server aur sawal pehchanein

| Server | Recorded lab IP | Role |
|---|---|---|
| DNS01 | 192.168.0.121 | Standard primary |
| DNS02 | 192.168.0.122 | Secondary |
| DNS03 | 192.168.0.123 | Stub / resolving server |

Recorded DNS network VMnet2 host-only, `192.168.0.0/24` hai. Is lab ko follow karte waqt puranay NAT network ki values is mein mix na karein.

```powershell
nslookup dns02.vcplab.com. 192.168.0.121
```

| Hissa | Matlab |
|---|---|
| nslookup | DNS se sawal karne ka tool |
| dns02.vcplab.com. | Woh naam jis ke baare mein poochna hai |
| 192.168.0.121 | Woh DNS server jis se jawab lena hai |

Aakhri dot naam ko absolute banata hai; suffix expansion par depend nahin karna parta. Command ka matlab hai: “DNS01 se poochho DNS02 ka address kya hai.” DNS02 ka naam poochne ka matlab yeh nahin ke DNS02 ne jawab diya.

## 2. Output ka sahi matlab

**Yeh expected output ki misaal hai, naya captured result nahin:**

```text
Server:  dns01.vcplab.com
Address: 192.168.0.121

Name:    dns02.vcplab.com
Address: 192.168.0.122
```

Upar Server/Address block jawab dene walay DNS server ka hai. Neechay Name/Address block sawal ka jawab hai. `.121` destination server aur `.122` returned record hai. Dono ko aik na samjhein.

Doosra address na diya jaye:

```powershell
nslookup dns02.vcplab.com.
```

To tool computer ki DNS configuration se default DNS server use karta hai. Lab servers compare karte waqt IP explicitly dene se test clear aur repeatable rehta hai.

## 3. Forward aur reverse lookups

Forward lookup naam se address dhoondta hai. IPv4 specifically chahiye to A type dein:

```powershell
nslookup -type=A dns02.vcplab.com. 192.168.0.121
```

Reverse lookup address se associated naam poochta hai; us ka PTR alag reverse zone mein hota hai:

```powershell
nslookup 192.168.0.122 192.168.0.121
```

Is lab mein expected mapping `.122` se `dns02.vcplab.com` hai. Forward aur reverse data alag hain. A record sahi honay se PTR automatically verified nahin hota. Har normal forward lookup ke liye PTR lazmi nahin.

## 4. Primary, secondary aur stub ko compare karein

Aik hi sawal teen servers ko:

```powershell
nslookup -type=A dns02.vcplab.com. 192.168.0.121
nslookup -type=A dns02.vcplab.com. 192.168.0.122
nslookup -type=A dns02.vcplab.com. 192.168.0.123
```

| Query destination | Correct answer ka matlab |
|---|---|
| DNS01 .121 | Primary record ka jawab de raha hai |
| DNS02 .122 | Secondary apni full zone copy se jawab de sakta hai |
| DNS03 .123 | Stub/resolving server ke zariye resolution chalti hai; full zone copy ka saboot nahin |

Aik successful query latest edit ki replication, automatic failover ya exact upstream path sabit nahin karti. Cached ya recursive answer ho sakta hai. Replication test mein known naya change aur relevant copies ki queries chahiye. Outage test controlled conditions aur actual results ke saath alag hota hai.

## 5. Record type ke mutabiq sawal

| Type | Sawal | Lab mein istemal |
|---|---|---|
| A | Naam ka IPv4 kya hai? | DNS01/DNS02/DNS03 host records |
| AAAA | IPv6 kya hai? | Jab actual record ho |
| PTR | IP ka associated naam kya hai? | Reverse zone |
| CNAME | Alias kis canonical naam ko point karta hai? | dns.vcplab.com → dns01.vcplab.com |
| NS | Zone ke authoritative servers kaun listed hain? | Authority information |
| SOA | Zone serial aur authority/timers kya hain? | Versions aur refresh information compare karna |
| MX | Mail exchangers kaun hain? | Is lab record mein configured nahin |
| TXT | Text record kya hai? | Jab actual record maujood ho |

```powershell
nslookup -type=CNAME dns.vcplab.com. 192.168.0.121
nslookup -type=NS vcplab.com. 192.168.0.121
nslookup -type=SOA vcplab.com. 192.168.0.121
nslookup -type=SOA vcplab.com. 192.168.0.122
```

Alias ka target naam hota hai, IP nahin. Target ka A record IPv4 deta hai. SOA serial equal hona useful comparison hai, har record ka complete audit nahin. MX ya AAAA na hona automatically server fault nahin.

## 6. Interactive mode

Lines aik aik karke enter hoti hain. `nslookup` ke baad tool ka apna prompt aata hai; baqi lines PowerShell commands nahin:

```text
nslookup
server 192.168.0.121
set type=A
dns02.vcplab.com.
set type=SOA
vcplab.com.
exit
```

`server` session ka query destination badalta hai. `set type` sawal ka record type badalta hai. `exit` shell par wapas lata hai. Query type badalne se server ke DNS records change nahin hotay.

## 7. Message samjhein, phir setting change karein

| Message | Matlab aur agla check |
|---|---|
| Server: Unknown | Selected DNS server ka reverse display name resolve nahin hua; asal answer alag parhein |
| NXDOMAIN / Non-existent domain | Queried naam na honay ka negative jawab; spelling, suffix, zone aur selected server check hon |
| No records | Naam ho sakta hai magar requested record type missing ho |
| DNS request timed out | Waqt par jawab nahin mila; network, firewall, service, listening address aur load check hon |
| Non-authoritative answer | Jawab dene wala is answer ka authoritative source nahin; recursive/cached answer phir bhi sahi ho sakta hai |
| SERVFAIL / Server failed | Server processing complete nahin kar saka; upstream, zone health aur relevant validation check hon |
| REFUSED / Query refused | Server policy ne request refuse ki; permitted clients/recursion check hon |

Aik session mein pehle timeout aur baad mein answer dono aa sakte hain. Missing PTR Unknown display explain kar sakta hai, har timeout ki wajah nahin. Valid NXDOMAIN aur koi jawab na aana alag cheezein hain. NXDOMAIN par aam tor par har alternate DNS server ko try nahin kiya jata.

## 8. Practical troubleshooting ki tarteeb

1. Test computer identify aur network record: `hostname`, `ipconfig /all`.
2. Known complete naam ko primary IP ke against explicitly query.
3. No response, negative response aur wrong returned data ko alag samjhein.
4. Wohi naam secondary aur stub server se compare.
5. PTR ya server display name ka masla ho to reverse data alag test.
6. Server par relevant zone, record, DNS service aur permitted traffic inspect. DNS UDP aur TCP port 53 dono use karta hai.
7. Actual command, output aur sirf us se sabit conclusion save.

Optional TCP path check:

```powershell
Test-NetConnection 192.168.0.121 -Port 53
```

Yeh sirf TCP connectivity hai; UDP DNS ya correct DNS answer ka proof nahin. Ping ICMP test hai, DNS nahin. Successful lookup se RDP/SSH/HTTPS chalna prove nahin hota.

`nslookup` seedha DNS query karta hai; Windows hosts-file entry ya SSH config alias ka test nahin. Normal applications ka name-resolution path mukhtalif ho sakta hai. `ipconfig /flushdns` Windows client resolver cache clear karta hai; missing server record ya stale server zone ki general repair nahin.

## 9. Practice aur evidence sheet

| Task | Command / check | Expected result | Actual result |
|---|---|---|---|
| Primary A | dns02 ko .121 se query | .122 | Practice mein record karein |
| Reverse | .122 ko .121 se query | DNS02 full name | Practice mein record karein |
| Secondary | dns01 ko .122 se query | .121 | Practice mein record karein |
| Stub | dns02 ko .123 se query | .122 | Practice mein record karein |
| Alias | CNAME dns.vcplab.com ko .121 se query | dns01.vcplab.com | Practice mein record karein |
| Zone version | .121/.122 ke SOA compare | Unexpected difference investigate ho | Practice mein record karein |

Aisi machine se run hon jis ka lab tak route verified ho. Yahan naye successful outputs claim nahin. KRNC.org forwarding aur DNSSEC validation alag exercises hain; ordinary local lookup unke tests complete nahin karti.

## Revision sawal jawab

1. Jawab dene wala server output mein kahan hai? **Upar Server/Address block mein.**
2. dns02 ko .121 ke through query karne se DNS02 ki service test hoti hai? **Nahin; destination DNS01 hai.**
3. Server: Unknown hamesha lookup failure hai? **Nahin; answer block alag dekhein.**
4. Forward record se reverse automatically prove hota hai? **Nahin.**
5. DNS03 ka jawab full zone copy ka saboot hai? **Nahin; stub/resolver authority ya cache se jawab la sakta hai.**
6. TCP 53 success correct record ki guarantee hai? **Nahin.**

## References

- [Microsoft nslookup command reference](https://learn.microsoft.com/windows-server/administration/windows-commands/nslookup)
- [Microsoft nslookup settings](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup-set)
- [Microsoft query types](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup-set-type)

Addresses aur roles supplied lab context se hain. Commands repeat checklist aur sample output illustrative hai.
