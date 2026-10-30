# DNS Zones — Primary, Secondary aur Stub

Roman Urdu study notes • Concept → misaal → behavior → verification

Bhai, yeh guide aapki classroom DNS01/DNS02/DNS03 misaal se teen zone roles samjhati hai. Yeh concept aur repeat-practice guide hai, nayi live inspection nahin. Wohi English poster neeche aur separate PNG mein shamil hai.

![DNS zone types ka English comparison poster](images/DNS_Zone_Types_Poster_English.png)

**Poster ko kaise parhein:** kitaab mein `www`, `mail`, `db` aur directory mein multiple NS entries samjhane ki misaal hain. In se yeh sabit nahin hota ke woh records ya tamam NS entries aapki lab mein configured hain. In notes ki classroom host examples DNS01, DNS02 aur DNS03 hain. Poster standard file-backed model summarize karta hai; AD-integrated replication ka farq aagay diya hai.

---

## 1. Pehle server, zone aur record alag samjhein

- **DNS server** woh service/machine hai jo DNS sawalon ka jawab deti hai.
- **Zone** DNS namespace ka administratively managed hissa hai. 
- **Record** us zone ki aik individual entry hai.

Records office ki misaal lein: server service dene wala office, zone managed register aur record us register ki entry hai. Misaal samajhne ke liye hai; har zone simple file mein hona lazmi nahin. Storage design ke mutabiq hoti hai.

| Cheez | Classroom misaal |
|---|---|
| Server | DNS01, IP 192.168.0.121 |
| Forward zone | VCPLAB.Com |
| Host A record | dns02.vcplab.com → 192.168.0.122 |
| CNAME alias | dns.vcplab.com → dns01.vcplab.com |
| Reverse zone | 0.168.192.in-addr.arpa |
| PTR record | 192.168.0.122 → dns02.vcplab.com |

Domain name aur zone related hain, lekin unka scope hamesha bilkul aik nahin. Child namespace apni zone mein delegate ho to parent us ke tamam ordinary records nahin rakhta. Is lesson mein aik classroom forward zone par focus rakhein.

---

## 2. Primary — records edit karne ki jagah

Standard file-backed design mein primary ke paas full writable zone data hota hai. Administrator records yahan maintain karta hai. Classroom example mein DNS01 par VCPLAB.Com ki primary zone hai.

Maan lein administrator aik illustrative `printer.vcplab.com` A record banata hai. Write primary par hoti hai. Phir zone se us record ki query ka jawab mil sakta hai. Printer sirf misaal hai; lab mein printer configure honay ka dawa nahin.

**Misaal:** asal register jise authorized clerk update kar sakta hai.

“Writable” zone ka role hai; har client ko badalne ki ijazat nahin. Permissions aur dynamic-update policy phir bhi control karti hain ke changes kaun kar sakta hai. Primary ka matlab physical machine lazmi hona ya clients ka hamesha pehle isi ko query karna nahin.

**Revision:** DNS01 par DNS02 ka A record banana naam ki mapping hai. Is se DNS02 par secondary zone nahin banti.

---

## 3. Secondary — poori read-only copy

DNS02 classroom zone ki full read-only copy rakhta hai. Existing records ka jawab apni copy se de sakta hai; har user query par DNS01 se nahin poochta. Is se authoritative jawab ki doosri jagah milti hai.

Updates approved **zone transfers** se aati hain. SOA serial changes pehchanne mein madad karta hai. Notify check ka paigham de sakta hai aur refresh/retry settings update checks control karti hain. Transfer permission aur working network path alag requirements hain. Valid design mein doosra secondary bhi transfer source ho sakta hai.

**Misaal:** updated photocopy. Locally parhi ja sakti hai, lekin changes writable source par maintain hotay hain.

Primary band ho to secondary valid copy se promotion ke baghair jawab de sakta hai. Refresh expiry conditions se zyada fail rahe to hamesha ke liye serve nahin kar sakta. Zone expiry aur cached record ka TTL alag timers hain.

Secondary availability ke liye useful hai magar backups ka badal nahin: accidental deletion bhi transfer ho sakti hai. Aik physical host ki do DNS VMs usi host ki failure par depend karti hain.

**Revision:** Standard secondary ko administratively primary banana alag recovery action hai; automatic failover nahin.

---

## 4. Stub — authority ki information, poora register nahin

DNS03 ka stub authoritative servers ki limited maloomat rakhta hai. Is mein SOA, NS aur zaroori address/glue records hotay hain. Multiple entries ho sakti hain; “teen categories” ka matlab sirf teen records nahin. Full zone ke tamam hosts, aliases aur PTR is mein nahin hotay.

**Misaal:** contact directory jo batati hai poora register kahan dekha ja sakta hai.

Stub use karne wala resolver missing jawab ke liye reachable authority dhoond sakta hai. Munasib cached answer bhi use ho sakta hai. Authority primary ya secondary ho sakti hai; universal rule “sirf primary” nahin. Metadata refresh ki configured master list aur NS authority list related magar alag concepts hain.

Is liye stub secondary jaisi independent full-copy behavior nahin deta. Usable cache na ho aur required authorities unreachable hon to stub missing host record khud nahin bana sakta. Har query ko tez karne ki guarantee ya full DR backup nahin.

**Classroom ki had:** recorded stub mein DNS01 ki authority information thi. Sirf DNS02 secondary maujood honay se us ka automatically use hona assume na karein.

---

## 5. Aik hi sawal teenon se poochhein

Sawal: “dns02.vcplab.com ka IPv4 address kya hai?”

| Destination | Correct jawab kaise mil sakta hai? |
|---|---|
| DNS01, primary | Writable authoritative zone ka A record parhta hai |
| DNS02, secondary | Apni full read-only authoritative copy ka A record parhta hai |
| DNS03, stub/resolver | Authority discovery aur resolution, ya usable cached data se; full host list stub mein nahin |

Teenon `.122` de sakte hain, lekin matlab yeh nahin ke teenon same data store karte hain. Client ko milne wala answer aur server ka storage/role alag sawal hain.

| Muqabla | Primary | Secondary | Stub |
|---|---|---|---|
| Full ordinary zone records | Haan | Haan | Nahin |
| Full zone records ki direct editing | Writable source | Nahin | Host records edit karne wali copy nahin |
| Yahan main purpose | Zone data maintain | Full replicated data serve | Authorities discover |
| Register ki misaal | Asal | Updated photocopy | Contact directory |

**Forward/reverse doosri classification hai:** lookup ki direction batati hai. Primary/secondary/stub zone ka role hai. Reverse zone ki standard primary DNS01 aur secondary DNS02 par ho sakti hai. Forward setup se reverse setup automatic nahin hota.

---

## 6. Roles zone ke hain; AD integration ka model alag hai

Aik machine aik zone ki primary aur doosri zone ki secondary ya stub rakh sakti hai. Same zone ki conflicting primary aur secondary/stub copies alag local Windows DNS zone objects ke taur par aik server par nahin rakhti.

Upar register wali explanation aapki workgroup/file-backed lab ki hai. AD-integrated design mein suitable writable domain-controller DNS replicas updates accept aur AD replication use kar sakti hain. Doosri writable AD-integrated copy ko automatically standard read-only secondary na kahein. Read-only domain controller alag design hai.

Client ki “preferred DNS” aur “alternate DNS” settings resolvers choose karti hain; yeh server par primary/secondary zone declare nahin kartin. NXDOMAIN jaisa valid negative jawab aam tor par har alternate resolver ko try karne ki request nahin.

---

## 7. Repeat-practice verification

Aisi machine se run hon jis ka isolated DNS lab tak raasta verified ho. Yeh dobara karne ke checks hain, abhi execute kiye huay results nahin.

```powershell
# Aik hi record, mukhtalif DNS query destinations
nslookup -type=A dns02.vcplab.com. 192.168.0.121
nslookup -type=A dns02.vcplab.com. 192.168.0.122
nslookup -type=A dns02.vcplab.com. 192.168.0.123

# Full copies ka version metadata
nslookup -type=SOA vcplab.com. 192.168.0.121
nslookup -type=SOA vcplab.com. 192.168.0.122

# Secondary se reverse lookup
nslookup 192.168.0.122 192.168.0.122
```

Aakhri argument query receive karne wala DNS server hai, expected answer nahin. DNS02 ke A ka correct answer `.122` hai. Successful query exact upstream packet path, DNSSEC validation ya application ports ka proof nahin.

| Check | Kya evidence record ho? | Had |
|---|---|---|
| Primary lookup | Actual command aur answer | Sirf us waqt ki query |
| Secondary lookup | Responding server .122 aur correct answer | Primary-outage test nahin |
| Stub lookup | Responding server .123 aur answer | Full copy ya uncached path ka proof nahin |
| New-record replication | Controlled edit aur secondary par record | Purana record match hona kaafi nahin |
| Primary outage | Controlled outage aur secondary responses | Valid copy aur connectivity phir bhi chahiye |

Sirf notes parhne ke liye existing records change ya DNS shutdown nahin hota. Controlled change/outage tests planned lab mein recovery steps aur actual results ke saath hon.

---

## 8. Aam ghaltiyan aur revision

| Ghalat fehmi | Sahi matlab |
|---|---|
| Secondary har lookup primary se poochta hai | Apni full valid copy se jawab deta hai |
| Stub chhota full backup hai | Limited authority information rakhta hai |
| Do successful answers failover prove karte hain | Controlled outage test chahiye |
| Same answer ka matlab same stored records | Resolver behavior storage ka farq chhupa sakta hai |
| Primary physical hona lazmi hai | Zone role aur physical/virtual hosting alag hain |
| DNS02 ka A add karna DNS02 configure karta hai | Sirf us zone mein naam ki mapping banti hai |
| Zone banana AD join hai | Zone creation aur domain joining alag hain |

**Revision sawal jawab**

1. Standard model mein direct editable copy? **Primary.**
2. Tamam ordinary records ki read-only copy? **Secondary.**
3. Full host list ke baghair authorities dhoondne wala? **Stub.**
4. Primary unavailable ho to secondary existing data answer kar sakta hai? **Haan, jab copy valid aur server reachable ho.**
5. Primary/secondary machine ki permanent pehchan hai? **Nahin, role zone-specific hai.**
6. Poster ke saaray illustrated NS addresses lab mein honay ka proof hain? **Nahin, woh illustrative entries hain.**

## Sources aur study ki had

Lab names/addresses aur comparison examples supplied study context se hain. Poster educational illustration hai, configuration evidence nahin. Is bundle ke liye live machine change ya test nahin hui.

Core zone-role definitions ka official reference: [Microsoft — DNS zones](https://learn.microsoft.com/en-us/windows-server/networking/dns/zone-types).

Mazeed protocol background: [RFC 1034 — Domain names: concepts and facilities](https://www.rfc-editor.org/rfc/rfc1034), khaas taur par authoritative data, transfers aur cached resolver data ka farq.
