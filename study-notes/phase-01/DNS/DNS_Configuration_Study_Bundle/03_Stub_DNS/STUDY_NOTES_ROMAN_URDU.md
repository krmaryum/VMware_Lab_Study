# DNS03 — Stub zone (Roman Urdu)

## Bunyadi tasawwur

Stub zone mein SOA, NS aur authoritative server ke zaroori address records hotay hain. Is ka maqsad full copy rakhe baghair asal DNS servers tak pohanchna hai. DNS03 master se ye limited maloomat refresh karta aur VCPLAB.Com ki queries ke jawab ke liye relevant authoritative server dhoondhta hai.


## Lab values

| Server | Address | Role |
| --- | --- | --- |
| DNS01 | 192.168.0.121 | Standard primary |
| DNS02 | 192.168.0.122 | Secondary |
| DNS03 | 192.168.0.123 | Stub / resolving server |

VMnet2 host-only: 192.168.0.0/24. Forward zone: VCPLAB.Com. Reverse zone: 0.168.192.in-addr.arpa. Internet is unnecessary for these local lookups. A DNS suffix does not join a computer to an AD domain. This lab uses file-backed zones, not AD-integrated zones.

## Kya aur kyun

Stub zone SOA, NS aur zaroori address records rakhta hai taa ke authoritative server ka pata chal sake. Har A, CNAME ya PTR copy nahin hota. Server ki maloomat refresh hoti rehti hai. Ye full secondary replica ka badal nahin.

## 1. Forward stub banayein

1. DNS03 → DNS Manager → Forward Lookup Zones → New Zone.
2. Next → Stub zone select karein. Is workgroup lab mein AD storage available nahin.
3. VCPLAB.Com naam dein.
4. Nayi file VCPLAB.Com.dns select karein.
5. Master 192.168.0.121 add karke validation OK dekhein.
6. Next → Stub / Forward summary → Finish → Refresh.
7. SOA, dns01.vcplab.com ka NS aur dns01 ka A .121 dekhein. DNS02/DNS03 A aur DNS alias ka na hona yahan normal hai.

## 2. Reverse stub banayein

1. Reverse Lookup Zones → New Zone → Stub zone.
2. IPv4 → Network ID 192.168.0 dein; agar zone name mangay to 0.168.192.in-addr.arpa dein.
3. Nayi file accept karein; master 192.168.0.121 dein.
4. Finish → Refresh. Final screenshot mein SOA aur NS hain; reverse wizard capture nahin hua.
5. Stub mein mukammal PTR list ki umeed na karein.

## 3. DNS03 se lookup test

```powershell
nslookup dns02.vcplab.com. 192.168.0.123
nslookup 192.168.0.122 192.168.0.123
```

034759 mein dono successful hain. Jawab DNS03 .123 ne diya, result DNS02 aur .122 hai. Non-authoritative answer recursive jawab ke mutabiq hai; DNS03 full zone nahin rakhta. Is screenshot se exact packet path ya cache ka use confirm nahin hota.

## Troubleshooting

Master .121, NS aur us ka IP, network, DNS service aur recursion check karein. DNS01 ko seedha query karke compare karein. Stub ko authoritative server tak pohanch chahiye; secondary apni full copy rakhta hai.


## Screenshots ka silsila

Upar diye gaye steps follow karein. Ye tasaveer capture kiye gaye stages dikhati hain. Jo wizard pages share nahin huay un ki exact settings confirm nahin hain.

## 033557 — DNS03 New Zone

![DNS03 New Zone](screenshots/033557.png)

## 033643 — Wizard welcome

![Wizard welcome](screenshots/033643.png)

## 033722 — Stub selected

![Stub selected](screenshots/033722.png)

## 033806 — VCPLAB.Com name

![VCPLAB.Com name](screenshots/033806.png)

## 033935 — New stub file

![New stub file](screenshots/033935.png)

## 034036 — DNS01 master validates OK

![DNS01 master validates OK](screenshots/034036.png)

## 034058 — Forward stub summary

![Forward stub summary](screenshots/034058.png)

## 034304 — Forward stub records

![Forward stub records](screenshots/034304.png)

## 034336 — Reverse stub final records

![Reverse stub final records](screenshots/034336.png)

## 034759 — DNS03 forward and reverse tests pass

![DNS03 forward and reverse tests pass](screenshots/034759.png)
