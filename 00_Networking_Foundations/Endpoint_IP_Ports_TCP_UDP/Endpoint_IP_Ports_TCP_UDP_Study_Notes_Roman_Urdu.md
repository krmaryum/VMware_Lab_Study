# Endpoint, IP Address, Port aur TCP/UDP
## Roman Urdu — Structured Study Notes

## 1. Is lesson ka maqsad

Is lesson ke baad aap samajh sakein ge:

- Endpoint kya hai aur “logical endpoint” ka kya matlab hai.
- IP address, transport protocol aur port number ka alag kaam kya hai.
- TCP port 80 aur UDP port 80 kyun alag hain.
- Client aur server ke endpoints mil kar communication kaise banate hain.
- Aap ke Windows-to-ESXi SSH lab mein yeh concepts kaise apply hote hain.

Yeh theory notes hain. Neechay diye gaye temporary port aur connection outputs illustrative hain; aap ki machine se capture nahin kiye gaye.

## 2. Endpoint ka seedha matlab

**Endpoint communication ka ek sira ya point hai jahan data bheja ya receive kiya jata hai.**

“Endpoint” lafz mukhtalif contexts mein istemal hota hai. Security mein laptop ko endpoint keh sakte hain; API mein kisi operation ke URL ko endpoint keh sakte hain. Is lesson mein hum **TCP/UDP network communication endpoint** ki baat kar rahe hain.

TCP/UDP communication mein ek endpoint ki pehchan samajhne ke liye yeh teen cheezein dekhein:

**Transport protocol + IP address + port number**

Misal:

**TCP + 10.0.0.11 + 22**

Matlab: is IP par TCP port 22 wala communication point.

## 3. Logical endpoint kya hai?

**Logical** ka matlab yahan software ke zariye banaya aur manage kiya gaya point hai.

Network port koi physical suraakh nahin hota. Aap Ethernet cable ko TCP port 22 mein nahin lagate. Cable physical network adapter mein lagti hai; TCP/UDP port operating system ke networking software mein hota hai.

| Term | Kya hai? |
| --- | --- |
| Physical Ethernet port | Hardware connection jahan network cable lag sakti hai |
| TCP/UDP port | Software mein numbered communication point |
| Logical endpoint | Software ke zariye pehchana gaya communication sira |

## 4. IP address ka kaam — kahan jana hai?

IP address network par kisi interface/destination tak data pohanchane mein istemal hota hai.

Beginner level par isay “kis machine tak jana hai?” samajh sakte hain. Zyada durust taur par ek machine ke multiple interfaces aur multiple IP addresses ho sakte hain.

Aap ke lab ki misal:

| System | IP address |
| --- | --- |
| Windows desktop | 10.0.0.213 |
| Desktop ESXi 8 — pehle share kiya gaya lab address | 10.0.0.11 |

Yeh addresses aap ke pehle share kiye gaye environment se hain. Apne mojooda lab mein IP dobara verify karein.

Sirf IP batane se yeh clear nahin hota ke us destination par SSH chahiye ya HTTPS.

## 5. Port number ka kaam — kis communication point par jana hai?

Ek machine par multiple network services chal sakti hain. Port numbers operating system ko traffic sahi socket/application tak pohanchane mein madad dete hain.

| Service | Aam transport | Default server port | Kaam |
| --- | --- | --- | --- |
| SSH | TCP | 22 | Remote command-line access |
| HTTP | TCP | 80 | Web requests |
| HTTPS over HTTP/1.1 ya HTTP/2 | TCP | 443 | Secure web communication |
| HTTPS over HTTP/3 | QUIC over UDP | 443 | Secure web communication ka doosra transport model |
| DNS | UDP aur TCP | 53 | DNS queries aur doosray DNS operations |

**Default port convention hai, majboori nahin.** Service doosray port par configure ho sakti hai. Sirf port 22 nazar aane se yeh qatayi sabit nahin hota ke service SSH hi hai.

Port numbers ki range **0 se 65535** hai. Port 0 reserved hai; aam service connection ke liye isay destination port ke taur par istemal nahin kiya jata.

## 6. Protocol ka kaam — kis tareeqay se communication hogi?

Yahan protocol se murad transport protocol, yani TCP ya UDP hai.

| Pehlu | TCP | UDP |
| --- | --- | --- |
| Bunyadi model | Connection-oriented byte stream | Datagram-based communication |
| Shuruat | Aam tor par connection handshake | TCP jaisa connection handshake nahin |
| Delivery aur order | Ordered delivery, acknowledgments aur retransmission mechanisms | Khud delivery, order ya retransmission guarantee nahin deta |
| Data ki hadbandi | Application ko byte stream milti hai | Datagram boundaries barqarar rehti hain |
| Misal | SSH | Bohat si aam DNS queries |

TCP bhi network failure ki surat mein hamesha successful delivery ki guarantee nahin de sakta; connection fail ho sakta hai.

UDP ke upar application ya doosra protocol reliability add kar sakta hai. Misal ke taur par QUIC UDP ke upar transport features deta hai. Is liye “UDP hamesha unreliable application hoti hai” kehna durust nahin.

## 7. TCP port 80 aur UDP port 80 alag kyun hain?

Operating system TCP aur UDP ke ports ko alag manage karta hai. Dono ka number same ho sakta hai, lekin protocol alag hone ki wajah se communication points alag hain.

| Protocol | IP | Port | Pehchan |
| --- | --- | --- | --- |
| TCP | 192.168.1.20 | 80 | TCP endpoint |
| UDP | 192.168.1.20 | 80 | Alag UDP endpoint |

Agar application TCP port 80 par listen kar rahi hai, to UDP port 80 par bheja gaya data us TCP listener ko deliver nahin hoga.

Ek machine par TCP 80 aur UDP 80 dono istemal ho sakte hain, agar un ke liye munasib sockets/services configure hon. Ek hi application bhi dono protocols istemal kar sakti hai.

**Number same hona, endpoint same honay ke barabar nahin.**

## 8. Building wali misal

| Networking cheez | Building ki misal |
| --- | --- |
| IP address | Building ka address |
| TCP ya UDP | Do alag wings |
| Port number | Wing ke andar numbered darwaza |
| Application/service | Us darwazay ke peechay kaam karne wala department |

TCP wing mein room 80 aur UDP wing mein room 80 alag rooms hain.

Yeh samjhane ki misal hai. Asal networking mein koi physical TCP wing ya UDP darwaza nahin hota.

## 9. Listening ka matlab

**Service ka listening hona** ka matlab hai ke woh kisi configured address aur port par aane wali communication receive karne ke liye tayyar hai.

TCP server listening socket incoming connection requests ka intezar karta hai. UDP mein application socket bind karke datagrams receive kar sakti hai; is ka TCP jaisa listening handshake nahin hota.

Misal: ESXi ki SSH service TCP port 22 par available hai.

Lekin sirf service chalna kaafi nahin. Client tak route, firewall permission aur sahi address bhi chahiye.

**Service running, port reachable aur login successful — teen alag checks hain.**

## 10. Aap ke SSH lab ki step-by-step misal

Windows par:

~~~powershell
ssh root@10.0.0.11
~~~

SSH client default taur par yeh use karta hai:

| Hissa | Value | Matlab |
| --- | --- | --- |
| Application protocol | SSH | Remote login ka tareeqa |
| Transport protocol | TCP | Neechay data transport karne ka protocol |
| Destination IP | 10.0.0.11 | ESXi host ka lab address |
| Destination port | 22 | Default SSH server port |
| Username | root | Login account |

**root IP ya port ka hissa nahin.** Yeh authentication ke liye account name hai.

Agar SSH doosray port, misal ke taur par 2222 par configured ho:

~~~powershell
ssh -p 2222 root@10.0.0.11
~~~

Yeh command server ka port change nahin karti. Sirf client ko batati hai ke kis destination port par connect karna hai.

## 11. Client ka bhi apna endpoint hota hai

Maan lein Windows desktop 10.0.0.213 se connection banta hai. Operating system temporary local port 52345 choose karta hai.

| Side | Protocol | IP | Port |
| --- | --- | --- | --- |
| Windows SSH client | TCP | 10.0.0.213 | 52345 |
| ESXi SSH server | TCP | 10.0.0.11 | 22 |

52345 sirf illustrative temporary port hai. Har connection mein yahi port hona zaroori nahin.

### Request ki direction

| Field | Value |
| --- | --- |
| Source endpoint | TCP, 10.0.0.213:52345 |
| Destination endpoint | TCP, 10.0.0.11:22 |

### Response ki direction

| Field | Value |
| --- | --- |
| Source endpoint | TCP, 10.0.0.11:22 |
| Destination endpoint | TCP, 10.0.0.213:52345 |

Source aur destination packet ki direction ke saath badal jate hain. Client/server roles isi wajah se aapas mein swap nahin hote.

## 12. Temporary ya ephemeral port kya hai?

Client ko apna local communication point chahiye. Operating system aam tor par is ke liye temporary, yani **ephemeral**, port choose karta hai.

- Server aam tor par configured service port par requests receive karta hai.
- Client aam tor par temporary source port use karta hai.
- Port allocation ki exact range operating system aur configuration par depend karti hai.
- Application zaroorat par apna source port bind bhi kar sakti hai.

Server ka reply client ke source port par wapas aata hai. Har response ko client ke port 22 par bhejna zaroori nahin.

## 13. Do SSH sessions alag kaise pehchane jate hain?

Ek Windows desktop se ek hi ESXi host par do sessions kholein:

| Session | Client endpoint | Server endpoint |
| --- | --- | --- |
| Session A | TCP, 10.0.0.213:52345 | TCP, 10.0.0.11:22 |
| Session B | TCP, 10.0.0.213:52346 | TCP, 10.0.0.11:22 |

Server IP aur port same hain; client ports alag hain. Is se operating systems sessions ko alag pehchan sakte hain.

Ek TCP connection ko us ke do endpoints, yani source IP/port aur destination IP/port, se pehchana jata hai. Protocol ko bhi shamil karein to isay aksar **5-tuple** kehte hain:

1. Source IP
2. Source port
3. Destination IP
4. Destination port
5. Transport protocol

Ek endpoint aur poora connection ek hi cheez nahin. Connection ke do endpoints hote hain.

## 14. Colon wali notation ka matlab

~~~text
10.0.0.11:22
~~~

Is mein colon IP aur port ko alag karta hai. Yeh shorthand apne aap TCP ya UDP nahin batati. Protocol context se maloom hota hai ya alag likhna hota hai.

~~~text
TCP 10.0.0.11:22
~~~

IPv6 address ke saath ambiguity se bachne ke liye brackets istemal hote hain:

~~~text
[2001:db8::11]:443
~~~

Yeh documentation ke liye example IPv6 address hai, aap ke lab ka address nahin.

## 15. Common confusions

| Ghalat fehmi | Durust samajh |
| --- | --- |
| Port physical cable socket hai | TCP/UDP port logical software point hai |
| Port number akela endpoint hai | Protocol, IP aur port ka context chahiye |
| TCP 80 aur UDP 80 ek hain | Dono alag communication points hain |
| Port 22 ka matlab lazman SSH hai | Yeh default convention hai; service verify karni hoti hai |
| Port open hai to login zaroor hoga | Authentication aur authorization abhi bhi fail ho sakti hain |
| Ping fail hai to SSH bhi fail hai | ICMP aur TCP alag hain; SSH phir bhi reachable ho sakta hai |
| Client bhi hamesha port 22 use karta hai | Client aam tor par temporary local port use karta hai |
| Ek service port sirf ek client ke liye hai | Ek listening server bohat se alag TCP connections handle kar sakta hai |
| HTTP application protocol aur TCP ek hi hain | HTTP/SSH application protocols hain; TCP transport protocol hai |

## 16. Lab mein evidence kaise samjhein?

Aap ke pehle lab mein Test-NetConnection port 22 par successful hua tha aur phir SSH login bhi hua tha. Is se do alag baatein maloom hui thin:

1. TCP port 22 tak connection ban saka.
2. SSH authentication ke baad session khul gaya.

Conceptually, future investigation mein yeh layers alag dekhein:

| Check | Sawal |
| --- | --- |
| Address | Kya destination IP sahi hai? |
| Transport aur port | Kya TCP/UDP aur port sahi hain? |
| Reachability | Kya route aur firewall communication allow karte hain? |
| Service | Kya expected application respond kar rahi hai? |
| Authentication | Kya account/key/password qabool ho raha hai? |
| Authorization | Login ke baad kis kaam ki ijazat hai? |

Yeh checklist completed naya practical lab nahin; troubleshooting ko organize karne ka tareeqa hai.

## 17. Revision sawalat aur jawab

**1. Endpoint kya hai?**  
Communication ka ek sira jahan data bheja ya receive kiya jata hai.

**2. Logical endpoint kyun kehte hain?**  
Kyunke yeh software networking ka point hai, physical cable socket nahin.

**3. IP ka kya kaam hai?**  
Network destination/interface tak data pohanchane mein madad deta hai.

**4. Port ka kya kaam hai?**  
Transport protocol ke andar sahi socket/application tak traffic pohanchane mein madad deta hai.

**5. TCP port 80 aur UDP port 80 kyun alag hain?**  
Kyunke TCP aur UDP alag transport protocols aur alag port spaces rakhte hain.

**6. SSH ka default server endpoint kaise likhein?**  
Misal: TCP, 10.0.0.11:22.

**7. Client ka temporary port kyun chahiye?**  
Client ko apna communication point chahiye taa ke response sahi socket/session tak wapas aaye.

**8. Kya har SSH client ka source port 22 hota hai?**  
Nahin. Aam tor par ephemeral port hota hai.

**9. Kya endpoint aur connection ek hain?**  
Nahin. Ek TCP connection ke do endpoints hote hain.

**10. Kya port reachable honay se password sahi sabit hota hai?**  
Nahin. TCP connectivity aur authentication alag checks hain.

**11. Kya 10.0.0.11:22 likhne se protocol maloom ho jata hai?**  
Nahin. Protocol ko context ya alag label se samajhna hota hai.

**12. Kya UDP use karne wali application apni reliability add kar sakti hai?**  
Ji haan. UDP khud yeh guarantee nahin deta, lekin upar ka protocol/application mechanisms add kar sakta hai.

## 18. Khud explain karne ki practice

Apne alfaaz mein yeh example samjhayen:

**TCP, 10.0.0.213:52345 aur TCP, 10.0.0.11:22**

Jawab mein batayein:

- Client kaunsa hai?
- Server kaunsa hai?
- Temporary port kaunsa hai?
- Default SSH server port kaunsa hai?
- Response kis destination par wapas jayega?

**Expected understanding:** Windows client 10.0.0.213 ka example source port 52345 hai. ESXi server 10.0.0.11 ka SSH port 22 hai. Response TCP par 10.0.0.213:52345 ko wapas jata hai.

## 19. Ek nazar mein yaad rakhne ka formula

| Sawal | Jawab |
| --- | --- |
| Kahan jana hai? | IP address |
| Kis transport se? | TCP ya UDP |
| Kis communication point par? | Port number |
| Kis account se login? | Username |
| Communication ke kitne siray? | Client endpoint aur server endpoint |

**Endpoint: ek sira. Connection: dono siron ke darmiyan communication.**

## 20. Repo mein suggested jagah

Is file ko yahan rakh sakte hain:

`VMware_Lab_Study/00_Networking_Foundations/`

Us folder ke README mein yeh link add karein:

~~~markdown
- [Endpoint, IP, Ports aur TCP/UDP — Roman Urdu](Endpoint_IP_Ports_TCP_UDP_Study_Notes_Roman_Urdu.md)
~~~

