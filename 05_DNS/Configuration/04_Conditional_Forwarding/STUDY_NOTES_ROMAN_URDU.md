# Conditional forwarding — KRNC.org (Roman Urdu)

## Bunyadi tasawwur

Conditional forwarder khaas domain ki queries selected DNS IP ko bhejta hai. Is se connected private ya partner network ki zone copy kiye baghair names resolve ho sakte hain. KRNC.org ka saved rule queries 172.16.0.18 ko bhejta hai; jawab ke liye target reachable aur requested naam resolve karne ke qabil hona chahiye.


## Is configuration ko samjhein

Rule ka sawal hai: “Is namespace ki query kis DNS server ko bhejni hai?” `KRNC.org` ke liye saved destination `172.16.0.18` hai. Rule banane se DNS01 us domain ka authoritative server nahin banta aur na us domain ke records ki zone copy milti hai.

Yeh stub ki authority-discovery information se alag hai. Forwarding destination explicitly configured hoti hai, is liye address badlay to rule manage karna hota hai. Yeh default gateway bhi nahin: forwarder application level par DNS sawal bhejta hai, lekin network ko target IP tak raasta phir bhi chahiye.

Current VMnet2 host-only subnet `192.168.0.0/24` hai aur gateway blank recorded hai. Doosri subnet ka rule save karna connectivity nahin banata. Meaningful test ke liye real target DNS, existing test record, working route aur permitted DNS traffic chahiye.

**Recorded result:** corrected rule save hua. Is ke zariye successful resolution demonstrate nahin hui. Future test mein target ko direct query aur DNS01 ke zariye wohi query compare karke actual results capture hon. Sirf TCP port test valid DNS jawab ka saboot nahin.

## Lab mein is ka kirdar

Conditional forwarder ek khaas domain ki queries muqarrar DNS IP ko bhejta hai. Zone ki copy nahin leta. Ye connected offices, partner networks aur hybrid networks mein kaam aata hai. General forwarder doosri eligible queries handle karta hai; conditional rule khaas namespace match karta hai.

## Hamara saved rule

DNS01 192.168.0.121 par KRNC.org → 172.16.0.18 set hua. Matlab app.KRNC.org ki query is IP par jayegi; app sirf example hai, verified record nahin. VCPLAB.Com ki queries local zone se handle hoti hain.

## Steps

1. DNS01 par DNS Manager kholein.
2. Conditional Forwarders par right-click → New Conditional Forwarder.
3. DNS Domain mein KRNC.org dein.
4. IP row mein 172.16.0.18 likhein aur Enter karein.
5. Workgroup lab mein AD storage unchecked/unavailable rahega.
6. OK karein. KRNC.org select karke target IP check karein.

Pehli do tasaveer server Properties aur general Forwarders tab ki jagah dikhati hain. Domain-specific rule ke liye alag Conditional Forwarders node use karein. Is feature ki historical introduction date/UI change is lab mein verify nahin ki.

## Typing ghalti

040114 mein 172.16.018 likha tha jo name samjha gaya aur No such host is known aaya. 040430 mein sahi 172.16.0.18 saved hai. Ghalat entry repeat na karein.

## Jab asal target available ho to test

1. Confirm karein .18 asal DNS server hai aur DNS01 se reachable hai.
2. 192.168.0.0/24 host-only network se 172.16.0.18 tak route apne aap nahin banta. Connectivity/routing aur DNS UDP/TCP 53 chahiye.
3. Teacher se KRNC.org ka existing record lein.
4. Pehle target ko seedha, phir DNS01 ke zariye query karein.

```powershell
# known-host ko asal existing record se replace karein.
nslookup known-host.krnc.org. 172.16.0.18
nslookup known-host.krnc.org. 192.168.0.121
```

Abhi rule saved hai, resolution verified nahin. Saved rule ya server ka naam dikhna forwarding ki kamyabi ka saboot nahin. Sirf queries forward karne ke liye zone transfer permission nahin chahiye. Circular forwarding se bachein.


## Screenshots ka silsila

Upar diye gaye steps follow karein. Ye tasaveer capture kiye gaye stages dikhati hain. Jo wizard pages share nahin huay un ki exact settings confirm nahin hain.

## 035645 — Server Properties entry

![Server Properties entry](screenshots/035645.png)

## 035838 — General Forwarders tab location

![General Forwarders tab location](screenshots/035838.png)

## 040114 — Incorrect address entry produces errors

![Incorrect address entry produces errors](screenshots/040114.png)

## 040430 — Correct KRNC.org rule saved

![Correct KRNC.org rule saved](screenshots/040430.png)
