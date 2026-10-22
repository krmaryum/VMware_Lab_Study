# DNS01 — Primary DNS Server Configuration

Step-by-step lab record • 21 October 2026

Is bundle mein primary zone, reverse zone, A/PTR, CNAME aur verification hain. DNS02 secondary aur DNS03 stub ke liye alag notes honge.

## Lab settings

| Server | IP | Planned zone role |
| --- | --- | --- |
| DNS01 | 192.168.0.121 | Primary — configured |
| DNS02 | 192.168.0.122 | Secondary — verified |
| DNS03 | 192.168.0.123 | Stub — verified |

Network: VMnet2 host-only, 192.168.0.0/24; gateway blank. Zone: VCPLAB.Com. DNS01 remains a workgroup server in this workflow; DNS suffix configuration is separate from domain joining.

## Zaroori alfaaz

| Term | Matlab |
| --- | --- |
| Primary zone | Writable authoritative copy |
| A | Naam se IPv4 address |
| PTR | IP se DNS naam |
| CNAME | Alias se asal DNS naam |
| NS | Zone ka authoritative name server |
| SOA | Authority, serial aur refresh ki information |
| Zone transfer | Zone data doosray DNS server ko copy karna |

## 1. DNS Manager kholen

Server Manager → Tools → DNS kholen. Ya Win + R dabayein, `dnsmgmt.msc` likhein aur Enter karein. DNS01 → Forward Lookup Zones kholen. Shuru mein zone list khaali thi.

![Your lab screenshot — 235732](screenshots/20261021_235732.png)

![Your lab screenshot — 235818](screenshots/20261021_235818.png)

![Your lab screenshot — 235924](screenshots/20261021_235924.png)

![Your lab screenshot — 000139](screenshots/20261022_000139.png)

![Your lab screenshot — 000227](screenshots/20261022_000227.png)

## 2. Standard primary forward zone banayein

Forward Lookup Zones par right-click → New Zone → Next karein. Primary zone select karein. Active Directory wala checkbox is lab mein available nahi. Zone name **VCPLAB.Com** likhein. Nayi file **VCPLAB.Com.dns** rakhein. Isolated classroom lab mein teacher ki tarah **Allow both nonsecure and secure dynamic updates** select karein. Is se baghair authentication updates aa sakti hain; yeh production ki secure default setting nahi. Summary check karke Finish karein.

![Your lab screenshot — 000313](screenshots/20261022_000313.png)

![Your lab screenshot — 000427](screenshots/20261022_000427.png)

![Your lab screenshot — 000558](screenshots/20261022_000558.png)

![Your lab screenshot — 000717](screenshots/20261022_000717.png)

![Your lab screenshot — 001437](screenshots/20261022_001437.png)

![Your lab screenshot — 001628](screenshots/20261022_001628.png)

![Your lab screenshot — 001759](screenshots/20261022_001759.png)

![Your lab screenshot — 001827](screenshots/20261022_001827.png)

## 3. Zone aur zone file check karein

VCPLAB.Com select karein. SOA, NS aur dns01 ka A record 192.168.0.121 nazar aata hai. Zone Standard Primary aur Running hai. Win + E se File Explorer khol kar C:\Windows\System32\dns mein file dekhein. VCPLAB.Com.dns zone banne ke baad nazar aati hai; pehle nahi thi. Is lab mein records DNS Manager se manage karein.

![Your lab screenshot — 001250](screenshots/20261022_001250.png)

![Your lab screenshot — 002213](screenshots/20261022_002213.png)

![Your lab screenshot — 002248](screenshots/20261022_002248.png)

![Your lab screenshot — 002357](screenshots/20261022_002357.png)

## 4. IPv4 reverse zone aur DNS01 PTR banayein

Agli 12 tasveerain **teacher reference screenshots** hain, aap ki machine ki captures nahi. Aap ne bataya ke DNS01 par inhein follow kiya. Reverse Lookup Zones → New Zone → Next → Primary zone → IPv4 select karein. Network ID **192.168.0** likhein. Zone **0.168.192.in-addr.arpa** aur file **0.168.192.in-addr.arpa.dns** hogi. Classroom dynamic-update option select karke Finish karein. Forward zone mein dns01 A record ki Properties kholen, Update associated pointer (PTR) record check karke Apply / OK karein. Reverse zone refresh karein. Apne lab mein 192.168.0.121 ka target dns01.vcplab.com hoga. Teacher ke hostnames ki jagah apna DNS01 naam use karein.

![Teacher reference — 005525](screenshots/20261022_005525.png)

![Teacher reference — 005550](screenshots/20261022_005550.png)

![Teacher reference — 005612](screenshots/20261022_005612.png)

![Teacher reference — 005647](screenshots/20261022_005647.png)

![Teacher reference — 005716](screenshots/20261022_005716.png)

![Teacher reference — 005739](screenshots/20261022_005739.png)

![Teacher reference — 005803](screenshots/20261022_005803.png)

![Teacher reference — 005828](screenshots/20261022_005828.png)

![Teacher reference — 005934](screenshots/20261022_005934.png)

![Teacher reference — 005959](screenshots/20261022_005959.png)

![Teacher reference — 010030](screenshots/20261022_010030.png)

![Teacher reference — 010111](screenshots/20261022_010111.png)

## 5. DNS01 verify karein aur pehle errors samjhein

Command `nslookup dns01.vcplab.com. 192.168.0.121` chalayein. Pehle test mein Non-existent domain aaya. Final dot ke saath aglay test mein sahi IP aayi lekin timeout aur Unknown bhi tha. PTR ke baad aap ki screenshot mein server name aur jawab dono sahi hain aur timeout nahi. PTR na hone se Unknown aa sakta hai, magar har timeout ya NXDOMAIN ki wajah sirf PTR nahi samajhni. Local query ke liye Internet zaroori nahi. Aakhri dot naam ko absolute banata hai.

![Your lab screenshot — 002845](screenshots/20261022_002845.png)

![Your lab screenshot — 003245](screenshots/20261022_003245.png)

![Your lab screenshot — 005233](screenshots/20261022_005233.png)

## 6. DNS01 par DNS02 aur DNS03 host records add karein

DNS01 → Forward Lookup Zones → VCPLAB.Com par right-click → New Host (A or AAAA) karein. DNS02 aur 192.168.0.122 likhein. PTR checkbox check karke Add Host → OK karein. DNS03 ke liye 192.168.0.123 use karein aur Done karein. Aik screenshot mein galti se **19.168.0.123** tha; final verified list mein **192.168.0.123** sahi hai. Yeh records add karna doosri machine par secondary ya stub zone banana nahi hai.

![Your lab screenshot — 014419](screenshots/20261022_014419.png)

![Your lab screenshot — 014753](screenshots/20261022_014753.png)

![Your lab screenshot — 015026](screenshots/20261022_015026.png)

![Your lab screenshot — 015119](screenshots/20261022_015119.png)

![Your lab screenshot — 015231](screenshots/20261022_015231.png)

![Your lab screenshot — 015400](screenshots/20261022_015400.png)

## 7. Refresh aur duplicate PTR entries ki safai

PTR missing samajhne se pehle reverse zone refresh karein. Pehle DNS02 nazar nahi aaya; refresh ke baad DNS02 aur DNS03 ki do do entries thin. Har IP ki aik sahi PTR rakh kar extra entry delete ki gayi. Final screenshot mein .121, .122 aur .123 ki aik aik PTR hai. DNS mein capital aur small letters ka farq nahi. Sahi PTR pehle se ho to dobara add na karein.

![Your lab screenshot — 015452](screenshots/20261022_015452.png)

![Your lab screenshot — 015727](screenshots/20261022_015727.png)

![Your lab screenshot — 015808](screenshots/20261022_015808.png)

![Your lab screenshot — 020008](screenshots/20261022_020008.png)

![Your lab screenshot — 020147](screenshots/20261022_020147.png)

## 8. DNS CNAME alias banayein

VCPLAB.Com par right-click → New Alias (CNAME) karein. Alias **DNS** likhein. Browse → DNS01 → Forward Lookup Zones → VCPLAB.Com → dns01 select → OK karein, phir record create karne ke liye OK karein. Alias **dns.vcplab.com → dns01.vcplab.com** hai. CNAME mein target ka DNS naam hota hai; IP target ke A record se milti hai.

![Your lab screenshot — 020409](screenshots/20261022_020409.png)

![Your lab screenshot — 020537](screenshots/20261022_020537.png)

![Your lab screenshot — 020935](screenshots/20261022_020935.png)

![Your lab screenshot — 021027](screenshots/20261022_021027.png)

![Your lab screenshot — 021128](screenshots/20261022_021128.png)

![Your lab screenshot — 021222](screenshots/20261022_021222.png)

## 9. Alias aur host records verify karein

**nslookup dns.vcplab.com. 192.168.0.121** chalayein. Final output mein Name dns01.vcplab.com, Address 192.168.0.121 aur Aliases dns.vcplab.com hai. Pehle dns01 ya sirf vcplab.com query hua tha, is liye alias test nahi hua tha. DNS01 ke zariye DNS02 ki forward aur reverse queries bhi sahi hain. Is se DNS01 ke records verify hote hain, DNS02 ki secondary service nahi.

![Your lab screenshot — 021437](screenshots/20261022_021437.png)

![Your lab screenshot — 021642](screenshots/20261022_021642.png)

![Your lab screenshot — 021951](screenshots/20261022_021951.png)

![Your lab screenshot — 022455](screenshots/20261022_022455.png)

## 10. Continue with secondary DNS

The earlier incomplete transfer setup is superseded by the completed procedure in ../02_Secondary_DNS. DNS02 and DNS03 lookup verification succeeded later in the lab. DNS01 was subsequently signed and unsigned for the DNSSEC exercise; its final forward zone is unsigned. See Chapter 05.

## Repeat these verification commands

```powershell
nslookup dns01.vcplab.com. 192.168.0.121
nslookup dns02.vcplab.com. 192.168.0.121
nslookup dns03.vcplab.com. 192.168.0.121
nslookup 192.168.0.121 192.168.0.121
nslookup 192.168.0.122 192.168.0.121
nslookup 192.168.0.123 192.168.0.121
nslookup dns.vcplab.com. 192.168.0.121
```

Expected A answers: .121 / .122 / .123 respectively. Expected PTR targets: DNS01 / DNS02 / DNS03 in VCPLAB.Com. Expected alias target: DNS01, .121. These commands form a repeat checklist; screenshots do not document every command above being run.


## Current lab checkpoint / Maujooda halat

DNS01 primary, DNS02 secondary, and DNS03 stub all returned successful lab lookups. KRNC.org conditional forwarding is configured but not resolution-tested. VCPLAB.Com was signed, then unsigned; no trust-anchor import or DNSSEC validation test was shown.
