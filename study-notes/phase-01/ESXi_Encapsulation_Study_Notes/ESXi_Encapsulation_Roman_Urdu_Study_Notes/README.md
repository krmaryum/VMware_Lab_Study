# VMware ESXi Networking — Encapsulation, Frames, Packets aur Segments

**Mukammal Study Notes | Roman Urdu | VMware ESXi Home Lab**

> **Scope:** Yeh guide hamari pichhli Encapsulation lesson ki Roman Urdu edition hai. Yeh networking fundamentals aur aapke ESXi lab ko connect karti hai. Is mein diye gaye example VM addresses illustrative hain; hamne abhi live packet-capture test perform nahi kiya.

## Learning Objectives — Is chapter mein kya seekhenge?

Is chapter ke baad aap Data, TCP Segment, UDP Datagram, IP Packet, Ethernet Frame aur Bits ka farq bata sakenge; encapsulation aur decapsulation explain kar sakenge; ESXi vSwitch ka role samajh sakenge; MTU 1500 ke frame sizes calculate kar sakenge; aur Linux/ESXi networking commands ke outputs interpret kar sakenge.

## 1. OSI Model mein Data Units (PDU)

| OSI Layer | Protocol / Function | Data Unit | Zaroori Address / Information |
|---|---|---|---|
| Layer 7 — Application | HTTP, DNS | Data | Application request |
| Layer 6 — Presentation | Encoding, encryption ki representation | Data | Data format |
| Layer 5 — Session | Session coordination (conceptual OSI layer) | Data | Session state |
| Layer 4 — Transport | TCP / UDP | **TCP Segment / UDP Datagram** | Source aur destination ports |
| Layer 3 — Network | IPv4 / IPv6 | **IP Packet** | Source aur destination IP addresses |
| Layer 2 — Data Link | Ethernet | **Ethernet Frame** | Source/destination MAC, EtherType, FCS |
| Layer 1 — Physical | Physical signal | **Bits** | Electrical, optical ya radio signals |

**Roman Urdu explanation:** Jab data sender se receiver tak jata hai, neeche aati hui har layer apni zaroori information add karti hai. TCP use ho to Layer 4 par Segment, Layer 3 par IP Packet, Layer 2 par Ethernet Frame, aur Layer 1 par Bits ki shakal mein transmission hoti hai.

> Payload = Woh data jo hum destination tak bhejna chahte hain.

Payload woh data hai jo kisi network frame, packet ya segment ke andar carry hota hai. Har layer par payload ka matlab us layer ke hisaab se change hota hai.

### Diagram: Sender aur Receiver

```text
SENDER — ENCAPSULATION (top -> bottom)

Application Data
      |
      v
[TCP HEADER | DATA]                               = TCP SEGMENT
      |
      v
[IP HEADER | TCP HEADER | DATA]                   = IP PACKET
      |
      v
[ETH HEADER | IP HEADER | TCP HEADER | DATA | FCS] = ETHERNET FRAME
      |
      v
101100101010...                                 = BITS ON LINK

RECEIVER — DECAPSULATION (bottom -> top)

Bits -> Frame -> Packet -> Segment -> Application Data
```

**Yaad rakhein:** Yeh TCP example hai. UDP use hone par Layer 4 PDU *UDP datagram* hota hai. TLS jaise protocols apni records aur overhead bhi add kar sakte hain.

## 2. Encapsulation — Step by Step Kya Hota Hai?

Farz karein **Linux VM1 (example `10.0.0.101`)** web request bhejti hai **Linux VM2 (example `10.0.0.102`)** ko. Yeh IP addresses aapke real configured VMs ke nahi, sirf example hain.

1. **Application Layer:** VM1 HTTP data banati hai, jaise `GET / HTTP/1.1` aur zaroori headers. Yeh application data hai.
2. **Transport Layer:** TCP source port, destination port (HTTP ke liye misal ke taur par `80`), sequence number, flags aur baqi fields add karta hai. Ab data **TCP Segment** kehlata hai.
3. **Network Layer:** IPv4 source/destination IP, TTL, protocol aur doosri fields add karta hai. Ab **IP Packet** banta hai.
4. **Data Link Layer:** Ethernet source/destination MAC, EtherType aur wire par FCS trailer add hota hai. Ab **Ethernet Frame** banta hai.
5. **Physical Layer:** Frame ke bits link par encode/transmit kiye jate hain. Agar dono VMs aik hi ESXi host ke compatible vSwitch/VLAN par hain, internal virtual forwarding hoti hai aur physical cable zaroori nahi hoti.

**Asaan alfaaz mein:** `Data -> Segment -> Packet -> Frame -> Bits`. Har neeche wali layer apna header (aur Ethernet case mein trailer) add karti hai. Is process ko **Encapsulation** kehte hain.

## 3. Decapsulation — Receiver par kya hota hai?

Receiver par iska ulta process hota hai. Ethernet layer apni information check/process karti hai, IP layer packet handle karti hai, TCP destination port ki madad se data sahi application tak deta hai. TCP zaroorat par multiple segments ko sequence mein reassemble bhi karta hai.

```text
RECEIVER
BITS
  ↓
ETHERNET FRAME      (Layer 2 information process)
  ↓
IP PACKET           (Layer 3 information process)
  ↓
TCP SEGMENT         (Layer 4 information process)
  ↓
APPLICATION DATA    (Application ko actual data)
```

**Roman Urdu:** Sender par headers add hote hain; receiver par layers unko process karti hain aur upar wali layer ko actual payload deti hain. Isi ko **Decapsulation** kehte hain.

## 4. Frame vs Packet vs Segment — Interview Comparison

| Sawal | Frame | Packet | Segment (TCP) |
|---|---|---|---|
| OSI Layer | Layer 2 | Layer 3 | Layer 4 |
| Addressing | MAC address | IP address | Port number |
| Common Device | Switch / ESXi vSwitch | Router / L3 device | End-host TCP stack |
| Andar kya hota hai? | Aksar IP packet | Example mein TCP segment | Application bytes |
| Routed hop par change? | Aam tor par naya L2 header | TTL change hota hai; source/destination aksar same | Aam tor par end-to-end TCP information |

**Technical note:** NAT hone par IP addresses aur ports change ho sakte hain. Router TTL badalta hai. Standard ESXi vSwitch by default routing nahi karta.

**Example:** Frame Ethernet ki local delivery ke liye; packet IP network tak routing ke liye; segment TCP application ke ports aur reliable transport ke liye.

## 5. Ethernet Frame ke andar kya hota hai?

Standard untagged **Ethernet II** frame (preamble/SFD aur inter-frame gap ke baghair):

```text
| Destination MAC — 6 bytes | Source MAC — 6 bytes | EtherType — 2 bytes |
|                      Payload — 46 to 1500 bytes                 |
|                      FCS — 4 bytes                              |
```

- **Destination MAC (6 B):** Layer 2 network par frame kis interface/device ko deliver karna hai.
- **Source MAC (6 B):** Is hop par frame bhejne wale interface ka MAC.
- **EtherType (2 B):** Andar ka protocol identify karta hai, jaise IPv4 `0x0800` ya ARP `0x0806`.
- **Payload (46–1500 B):** Aksar IP packet hota hai. Chhota payload ho to minimum frame size ke liye padding add ho sakti hai.
- **FCS (4 B):** Frame Check Sequence; transmission ke bit errors detect karne mein madad. Aksar NIC capture se pehle FCS remove kar deta hai, is liye `tcpdump` par nahi dikhta.
- **802.1Q VLAN Tag (+4 B):** Tagged Ethernet frame mein 4 extra bytes hotay hain.

## 6. MTU 1500 aur Frame 1518 — Calculation

Aam **IPv4 + TCP** example, jab IPv4/TCP options nahi hain:

| Component | Size |
|---|---:|
| TCP Application Data / MSS | 1460 bytes |
| TCP Header | 20 bytes |
| IPv4 Header | 20 bytes |
| **IP Packet / IP MTU** | **1500 bytes** |
| Ethernet Header (untagged) | 14 bytes |
| FCS | 4 bytes |
| **Complete untagged frame** | **1518 bytes** |
| Optional VLAN tag | +4 bytes |
| **802.1Q tagged frame** | **1522 bytes** |

### Formulas

```text
TCP MSS = MTU - IPv4 Header - TCP Header
        = 1500 - 20 - 20
        = 1460 bytes

Untagged Ethernet Frame = Ethernet Header + IP Packet + FCS
                        = 14 + 1500 + 4
                        = 1518 bytes

Tagged Ethernet Frame = 1518 + 4 = 1522 bytes
```

**Important:** Physical Ethernet link par preamble 7 bytes, start-frame delimiter 1 byte aur inter-frame gap 12 byte-times bhi hotay hain; magar yeh 1518/1522 calculation mein count nahi kiye gaye. IPv6 header aam tor par 40 bytes hota hai; options, tunnels ya encapsulation additional overhead add karte hain.

**Roman Urdu:** MTU 1500 ka matlab poore Ethernet frame ka size 1500 nahi. Yeh aam Ethernet/IP context mein Layer 3 IP packet ki limit hai. Header aur FCS ke sath frame 1518 bytes ho sakta hai; VLAN tag ke sath 1522.

## 7. Aapke VMware ESXi Lab par Apply Karein

Aapke pehle share kiye gaye screenshots se yeh topology observe hui thi. Agar baad mein configuration change hui ho to dobara verify karein.

```text
                   ESXi: desktop-esxi9

 Linux VM1                              Linux VM2
     |                                      |
     +------------ SERVERS Port Group -----+
                          |
                       vSwitch0
                       /     \
        Management Network     vmnic0 (uplink)
        vmk0: 10.0.0.10            |
                               External LAN

        STORAGE VMkernel: vmk1 (172.16.0.102/16)
                          |
                   STORAGE Port Group
                          |
                    vSwitch1 (MTU 1500)
                          |
                        vmnic1
                          |
              Storage network (peer not tested)
```

- **Same ESXi host + same VLAN + same vSwitch:** VM-to-VM frames host ke andar forward ho sakte hain, `vmnic0` se bahar nahi jana parta.
- **External network:** Uplink se traffic nikal sakta hai, jab L2 connectivity aur zaroori routing configured ho.
- **Different IP subnets:** Aam communication ke liye router/L3 routing zaroori hoti hai; vSwitch apne aap router nahi.
- **Isolated vSwitches:** Alag isolated vSwitches ke darmiyan automatic Layer 2 path nahi hota.
- **Storage `vSwitch1` MTU 1500:** Storage ke liye jumbo frames lazmi nahi. MTU 9000 karne se pehle end-to-end verification karein.

**Note:** Ab tak actual VM-to-VM packet capture ya storage peer se end-to-end connectivity test document nahi hua. VM addresses `.101` / `.102` sirf teaching examples hain.

## 8. Practical Commands — KYUN, KAISE, OUTPUT KA KYA MATLAB?

### Exercise A — Linux NIC aur MTU check karein

```bash
ip -br addr
ip link show
```

**Kyun?** Interface ka naam, IP, interface status aur MTU dekhne ke liye.

**Output ka matlab:** `UP` ka matlab administrative state up; `LOWER_UP` aam tor par carrier signal available; `mtu 1500` interface par configured MTU. Interface `ens160`, `eth0` ya koi aur naam ho sakta hai.

### Exercise B — Route aur Next Hop check karein

```bash
ip route
ip route get 10.0.0.102
```

**Kyun?** Check karna hai Linux destination IP ke liye kaunsa route aur interface use karega.

**Output ka matlab:** `dev ens160` egress interface batata hai, `via 10.0.0.1` gateway/next hop ko show karta hai. Actual result aapke network par depend karega.

### Exercise C — Neighbor/ARP Table

```bash
ip neigh show
```

**Kyun?** Pata karna ke on-link IP ya next hop ke liye kaunsa MAC address resolve hua.

**Output ka matlab:** `lladdr` link-layer MAC; `REACHABLE` recent neighbor confirmation, `STALE` purani cached entry, `INCOMPLETE` address resolution abhi complete nahi hui. Jab tak contact na karein har device listed nahi hogi.

### Exercise D — tcpdump se Frame/Packet Observe Karein

```bash
sudo tcpdump -ni <interface-name> -e -c 10 'arp or icmp or tcp'
```

`<interface-name>` ko `ip -br addr` se milne wale actual interface se replace karein.

**Kyun?** ARP, ICMP aur TCP packet traffic observe karne ke liye.

**Flags ka matlab:**

| Flag | Matlab |
|---|---|
| `sudo` | Necessary privileges ke sath command |
| `-n` | DNS/name resolution ke baghair numeric IP/ports |
| `-i` | Capture kis interface se karna hai |
| `-e` | Ethernet MAC/header information show |
| `-c 10` | 10 captured packets ke baad stop |
| `'arp or icmp or tcp'` | Sirf relevant traffic filter |

**Output ka matlab:** TCP mein source/destination IP/port aur flags aa sakte hain; ARP mein IP-to-MAC resolution. FCS aam tor par capture mein nahi hota. Sirf apne ya authorized network par capture karein.

### Exercise E — ESXi Shell se vSwitch aur VMkernel Check

```bash
esxcli network vswitch standard list
esxcli network ip interface list
esxcli network ip interface ipv4 get
```

**Kyun?** Standard switches, uplinks, MTU, VMkernel interfaces aur IPv4 settings verify karna.

**Output ka matlab:** `vSwitch0`, `vSwitch1`, uplink mapping, `vmk0` management aur `vmk1` storage IP check karein. Commands read-only inspection ke liye hain; Shell/SSH permissions chahiye ho sakti hain.

### Exercise F — Storage VMkernel se Ping (jab storage peer available ho)

```bash
vmkping -I vmk1 <storage-peer-IP>
```

**Kyun?** Storage VMkernel interface `vmk1` se peer reachability test.

**Output ka matlab:** Reply aane se basic IP/ICMP reachability ka evidence milta hai, storage service working hone ka proof nahi. ICMP blocked ho sakta hai; ping failure akela complete diagnosis nahi. Abhi yeh test **pending** hai.

## 9. Common Troubleshooting Errors

| Symptom | Mumkin Wajah | Kya Check Karein |
|---|---|---|
| Dono VMs connected lekin ping fail | Wrong VLAN/subnet, firewall, guest NIC, routing | `ip -br addr`, `ip route`, VLAN ID |
| IP works, hostname fail | DNS / hosts-file issue | `nslookup`, `Resolve-DnsName`, hosts file |
| TCP 443 open magar browser fail | TLS/certificate/browser ya application issue | Complete HTTPS URL, HTTP response |
| Same-host VM traffic physical NIC par nazar nahi aa raha | vSwitch internal forwarding | Correct VM/virtual interface par capture |
| MTU 9000 configured, large packets fail | Network ke tamam hops jumbo support nahi karte | vSwitch, vmk, virtual/physical switch, peer MTU |

## 10. Interview Questions with Roman Urdu Answers

**Q1. Encapsulation kya hai?** Data neeche layers mein jata hai aur har layer apna header/trailer add karti hai taake transmission possible ho.

**Q2. Decapsulation kya hai?** Receiver par layers headers ko process karte hue application data tak pohanchti hain.

**Q3. Segment, Packet aur Frame mein farq?** TCP Segment Layer 4; IP Packet Layer 3; Ethernet Frame Layer 2.

**Q4. Frame mein MAC address kyun hota hai?** Local Layer 2 network par sahi destination ki taraf forwarding ke liye.

**Q5. Packet mein IP address kyun hota hai?** Logical addressing aur networks ke darmiyan routing ke liye.

**Q6. Kya same ESXi vSwitch par 2 VMs bina physical uplink communicate kar sakti hain?** Ji haan, jab port group/VLAN aur guest configuration compatible ho.

**Q7. Kya Standard vSwitch different subnets ke darmiyan route karta hai?** Nahi, yeh fundamentally Layer 2 switch hai.

**Q8. MTU 1500 ka matlab frame 1500 bytes hai?** Nahi, full-size IP packet ke sath untagged Ethernet frame 1518 bytes (FCS included, preamble/SFD excluded) ho sakta hai.

**Q9. VLAN tagging kya add karti hai?** Ethernet frame mein 4-byte 802.1Q tag.

**Q10. tcpdump mein FCS kyun nahi dikhta?** NIC ya virtual stack capture se pehle FCS strip kar deta hai.

## 11. Practice Quiz

1. Layer 2 ka PDU kya kehlata hai?
2. Layer 3 mein kaun se addresses hotay hain?
3. MTU 1500, IPv4 header 20B aur TCP header 20B ho to MSS kya hoga?
4. Same host, VLAN aur vSwitch par VMs ka traffic kya zaroor physical uplink se jayega?
5. VLAN tag Ethernet frame mein kitne bytes add karta hai?

**Answers:** 1) Frame; 2) Source aur destination IP; 3) 1460 bytes; 4) Nahi; 5) 4 bytes.

---

## Suggested Next Hands-on Lab

`SERVERS` par 2 isolated test Linux VMs banayein, real lab ke mutabiq safe IPs dein, `ip link` se MAC addresses dekhein, connectivity test karein aur authorized interface par `tcpdump` se ARP/ICMP/TCP capture karein. **Actual outputs aur screenshots** document karein. ESXi management network ki settings bina need change na karein.

**Revision:** Stage 1 fundamentals — October 2026.
