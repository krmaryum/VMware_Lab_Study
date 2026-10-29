# Subnet Mask — Study Notes

## Contents

1. [Subnet mask kya hai?](#1-subnet-mask-kya-hai)
2. [Aapke desktop ki example](#2-aapke-desktop-ki-example)
3. [Local aur remote destinations](#3-local-aur-remote-destinations)
4. [Computer faisla kaise karta hai](#4-computer-faisla-kaise-karta-hai)
5. [Subnet mask, gateway aur DNS ka farq](#5-subnet-mask-gateway-aur-dns-ka-farq)
6. [Windows commands](#6-windows-commands)
7. [Aham points aur revision](#7-aham-points-aur-revision)

## 1. Subnet mask kya hai?

**Subnet mask IPv4 address ke network aur host portions ki pehchan karta hai.** Aapka computer apna IP address aur subnet mask istemal karke yeh tay karta hai ke destination uske local subnet ka hissa hai ya nahin.

Subnet, IP network ka aik logical hissa hota hai. Host aik device ya network interface hota hai jiske paas IP address ho.

Mask akela kisi network ki pehchan nahin karta: isay IP address ke saath istemal karna zaroori hai.

## 2. Aapke desktop ki example

Yeh values aapki pehle share ki hui home-lab information se li gayi hain; yeh koi naya live check nahin hai.

| Setting | Value | Matlab |
| --- | --- | --- |
| Desktop IP address | `10.0.0.213` | Desktop ke Ethernet interface ka address |
| Subnet mask | `255.255.255.0` | Pehle 24 bits network ki pehchan karte hain |
| CIDR notation | `10.0.0.213/24` | `/24` isi mask ko zahir karne ka doosra tareeqa hai |
| Network address | `10.0.0.0` | Is subnet ki pehchan karta hai |
| Aam usable host range | `10.0.0.1`–`10.0.0.254` | Is subnet mein interfaces ke liye dastiyab addresses |
| Broadcast address | `10.0.0.255` | Is subnet ke liye broadcast destination |
| Default gateway | `10.0.0.1` | Jab koi zyada specific route match na ho to istemal hone wala router |

**Is khaas mask** ke saath, pehle teen numbers network (`10.0.0`) ki pehchan karte hain aur aakhri number host (`213`) ki pehchan karta hai.

Is `/24` subnet mein kul 256 addresses hain. Aam riwayat ke mutabiq, network aur broadcast addresses reserved hote hain, jis se 254 usable host addresses bach jate hain. Kuch addresses pehle se assign ho chuke ya DHCP mein reserved ho sakte hain.

“Pehle teen numbers” wala rule har subnet mask par apply na karein. Misal ke taur par, `/16` aur `/25` subnet ki boundaries mukhtalif banate hain.

## 3. Local aur remote destinations

Aapke desktop `10.0.0.213/24` se:

| Destination | Kya same local subnet hai? | Mutawaqqa rasta |
| --- | --- | --- |
| `10.0.0.8` — Surface laptop | Ji haan | Local network par direct delivery |
| `10.0.0.140` — Dell laptop | Ji haan | Local network par direct delivery |
| `10.0.0.11` — desktop ESXi 8 lab | Ji haan | Local delivery, basharte ke virtual networking usay is LAN se connect karti ho |
| `192.168.109.101` — Dell ESXi 9 lab | Nahin | Munasib route chahiye; mumkin hai home router is virtual subnet ka rasta na janta ho |
| `8.8.8.8` — public DNS server | Nahin | Aam tor par default gateway ko bheja jata hai |

“Direct delivery” ke raste mein Ethernet switch ya Wi-Fi access point phir bhi ho sakta hai. Is ka matlab hai ke us local subnet par devices ke darmiyan aam tor par router ki Demand nahin hoti.

Sirf default gateway hone se remote destination automatically reachable nahin ho jati. Gateway aur destination network ke liye munasib routes chahiye, jin mein wapsi ka rasta bhi shamil hai. VMware host-only network doosre physical computer se automatically reachable nahin hota.

## 4. Computer faisla kaise karta hai

Aik interface aur uske connected subnet ki seedhi example mein:

1. Computer apna IP address aur subnet mask combine karke network address nikalta hai.
2. Woh destination IP address ko bhi isi mask ke saath combine karta hai.
3. Agar network addresses match karte hain, to destination us connected subnet par hai.
4. Local IPv4 delivery ke liye computer aam tor par destination ka MAC address maloom karne ke liye ARP istemal karta hai.
5. Remote destination ke liye woh route select karta hai aur aam tor par us ki bajaye next-hop router ka MAC address istemal karta hai.

Is calculation mein bitwise **AND** operation istemal hota hai:

```text
Desktop:     10.0.0.213 AND 255.255.255.0 = 10.0.0.0
Surface:     10.0.0.8   AND 255.255.255.0 = 10.0.0.0
Dell ESXi:   192.168.109.101 AND 255.255.255.0 = 192.168.109.0
```

Desktop aur Surface ke results match karte hain. Dell ESXi ka result match nahin karta.

Asal operation mein Windows apni routing table dekhta hai aur sab se zyada specific matching route select karta hai. Is liye VPNs, multiple interfaces aur additional routes traffic ka rasta badal sakte hain.

## 5. Subnet mask, gateway aur DNS ka farq

| Component | Bunyadi kaam | Example |
| --- | --- | --- |
| IP address | IP network par interface ki pehchan karta hai | `10.0.0.213` |
| Subnet mask | Network aur host portions ki pehchan karta hai | `255.255.255.0` |
| Default gateway | Jab koi zyada specific route match na ho to next hop deta hai | `10.0.0.1` |
| DNS server | DNS queries ke jawab deta hai, jaise hostname ka IP address | Aapka configured DNS resolver |

DNS address dhoondne mein madad karta hai. Routing tay karti hai ke traffic us address tak kaise pohnchega. DNS lookup successful hone se connectivity ki guarantee nahin milti.

Do local computers DNS ke baghair IP address se communicate kar sakte hain. Unke connected subnet par communication ke liye aam tor par default gateway ki Demand nahin hoti.

## 6. Windows commands

Yeh read-only commands PowerShell mein chalayein.

### Addresses, masks aur gateways dekhein

```powershell
ipconfig
```

Us adapter ke neeche dekhein jo aap waqai istemal kar rahe hain: Ethernet ya Wi-Fi. VMware, WSL aur VPN adapters ke alag subnets ho sakte hain.

### Interface configuration dekhein

```powershell
Get-NetIPConfiguration
```

### IPv4 addresses aur prefix lengths dekhein

```powershell
Get-NetIPAddress -AddressFamily IPv4 |
    Select-Object InterfaceAlias, IPAddress, PrefixLength
```

Prefix length `24`, `255.255.255.0` ke barabar hoti hai.

### IPv4 routing table check karein

```powershell
route print -4
```

Default route `0.0.0.0` aur mask `0.0.0.0` ke saath nazar aata hai. Is se zyada specific matching routes ko is par tarjeeh milti hai.

### Doosre device par service test karein

```powershell
Test-NetConnection 10.0.0.11 -Port 22
```

`TcpTestSucceeded : True` ka matlab hai ke port 22 par TCP connection kamyab hua. Yeh sabit nahin karta ke aapka SSH username, password ya key bhi kamyabi se authenticate ho jayega.

### ICMP reachability test karein

```powershell
ping 10.0.0.11
```

Ping timeout se yeh sabit nahin hota ke device offline hai. Firewall, SSH ya HTTPS ko allow karte hue ICMP block kar sakta hai. Yeh aapki pehli SCP preparation ke dauran hua tha: ping timeout ho raha tha, lekin TCP port 22 kaam kar raha tha.

## 7. Aham points aur revision

- Same subnet par hone se connectivity ki guarantee nahin milti. Firewalls, Wi-Fi isolation, VLAN separation aur virtual-network settings traffic block kar sakti hain.
- Milte-julte dikhne wale IP addresses ka yeh matlab zaroori nahin ke dono devices same subnet mein hain. Unke masks check karein.
- Devices ki subnet settings unke intended network ke liye consistent honi chahiye; mismatched masks se confusing ya one-way connectivity ho sakti hai.
- Kisi unreachable address ko local dikhane ke liye sirf mask change na karein. Underlying network aur routing ko bhi connection support karna chahiye.
- Subnet mask IP addresses assign nahin karta, names translate nahin karta aur firewall ports open nahin karta.

### Quick questions

**Subnet mask computer ko kya batata hai?**

Kaun se bits network ki pehchan karte hain aur kaun se host ki. Is se yeh tay karne mein madad milti hai ke destination connected subnet par hai ya nahin.

**Kya `10.0.0.213/24` aur `10.0.0.8/24` same subnet mein hain?**

Ji haan. Dono `10.0.0.0/24` ka hissa hain.

**Kya `192.168.109.101`, `10.0.0.213/24` ke liye local hai?**

Nahin. Us tak pohnchne ke liye munasib route aur compatible network configuration chahiye.

**Kya `/24` ka matlab 24 devices hain?**

Nahin. Is ka matlab 24 network bits hain. IPv4 `/24` mein 8 host bits bachti hain: kul 256 addresses, jin mein aam tor par 254 usable host addresses hote hain.

### Aik jumlay mein definition

> **Subnet mask:** IPv4 address ke network aur host portions ki pehchan karta hai aur computer ko yeh tay karne mein madad deta hai ke destination local subnet par hai ya uske liye routing chahiye.
