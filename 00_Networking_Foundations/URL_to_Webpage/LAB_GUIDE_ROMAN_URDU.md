# URL Se Webpage Tak — Planned Observation Lab

[Folder guide](README.md) | [Theory notes](URL_to_Webpage_Study_Notes_Roman_Urdu.md)

## Maqsad aur zarooriyat

Windows se name resolution, TCP connection aur browser requests observe karein. Internet connection, Windows PowerShell aur browser darkar hain. DNS server, firewall ya ESXi settings badalne ki zaroorat nahin. Agar doosray shell mein networking cmdlet dastiyab na ho to Windows PowerShell istemal karein.

**Status: planned.** Neechay output ki wazahat hai; yeh aap ki machine se capture kiya hua result nahin.

## 1. DNS query karein

```powershell
Resolve-DnsName -Name www.example.com -Type A -DnsOnly
Resolve-DnsName -Name www.example.com -Type AAAA -DnsOnly
```

Jahan dikhaye jayein, name, record type, addresses aur TTL note karein. Jawab mein aliases aa sakte hain aur waqt ke saath result badal sakta hai. AAAA answer na milna apne aap mein website kharab honay ka saboot nahin. Yeh commands configured resolver se query karti hain; har upstream DNS hop nahin dikhati aur browser ke DNS behaviour ko bilkul waise reproduce nahin karti.

## 2. TCP port 443 test karein

```powershell
Test-NetConnection -ComputerName www.example.com -Port 443
```

`RemoteAddress`, `InterfaceAlias`, `SourceAddress` aur `TcpTestSucceeded` note karein.

`True` ka matlab yeh TCP connection ban saka. Is se TLS validation, successful HTTP response, database health ya HTTP/3 connectivity sabit nahin hoti. ICMP block honay par ping fail aur TCP test successful ho sakta hai.

## 3. Browser observe karein

1. F12 ya Ctrl+Shift+I se browser Developer Tools kholein.
2. **Network** panel select karein.
3. Panel khula rakh kar `https://www.example.com/` kholein ya reload karein.
4. Document request select karein. URL, method, HTTP status, response headers aur dastiyab timing fields note karein.
5. Agar **Protocol** column dastiyab ho to enable karke protocol note karein.
6. Apni pasand ke zyada resources walay page par document, CSS, script, image aur Fetch/XHR requests ka muqabla karein. Example page par resources kam ho sakte hain.

Browser pehle se bana connection dobara istemal kar sakta hai, encrypted DNS use kar sakta hai ya local cache se jawab de sakta hai. Is liye timing fields missing ya bohat chhoti ho sakti hain. Cache se jawab milna bhi valid observation hai. HTTP/3 mein UDP par QUIC hota hai; upar wali TCP command us rastay ko test nahin karti.

## 4. Evidence checklist

| Evidence | Aap ka observation |
| --- | --- |
| Date, Windows machine aur browser | Abhi bharna hai |
| DNS answer aur record type | Abhi bharna hai |
| TCP port 443 result | Abhi bharna hai |
| Browser request URL aur HTTP status | Abhi bharna hai |
| Protocol aur nazar aane wali timing | Abhi bharna hai |
| Apne alfaaz mein explanation | Abhi bharna hai |

Lab karte waqt screenshots `evidence` subfolder mein save karein. Public evidence mein cookies, authorization headers aur doosray session secrets shamil na karein. Developer Tools se yeh sabit nahin hota ke application ne database query ki thi, aur tamam private backend components bhi nazar nahin aate.

## 5. Evidence ke mutabiq troubleshooting

| Observation | Agli investigation |
| --- | --- |
| DNS query fail hai | Configured DNS, connectivity aur name ka wajood check karein |
| DNS kaam karta hai; TCP fail hai | Routing, filtering, listener availability ya destination investigate karein |
| TCP kaam karta hai; browser fail hai | Asal browser error, certificate, HTTP status aur proxy behaviour dekhein |
| HTML load hota hai; kuch content fail hai | Failed resource ka hostname, response aur request details dekhein |

Apne asal observations record karne ke baad hi lab ko completed mark karein. Commands ki documentation ke liye [sources](SOURCES.md) dekhein.
