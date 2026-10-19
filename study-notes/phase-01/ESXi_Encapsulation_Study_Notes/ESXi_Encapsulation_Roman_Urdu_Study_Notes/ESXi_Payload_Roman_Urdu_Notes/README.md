# VMware ESXi Networking — Payload Study Notes (Roman Urdu)

**Topic:** Payload, Header, Trailer, Encapsulation, Frame, Packet, Segment aur MTU  
**Language:** Roman Urdu (technical terminology English)  
**Lab context:** ESXi `desktop-esxi9`; `vSwitch0` with `SERVERS` port group; storage `vSwitch1` / `STORAGE` / `vmk1` / MTU 1500. VM examples neeche illustrative hain, existing VMs ka claim nahi.

## 1. Payload kya hota hai?

**Payload** woh data hota hai jo kisi protocol data unit ke *andar carry* hota hai. Aam tor par us specific protocol ka header aur trailer payload ka hissa nahi hote.

**Simple example:** Agar aap Linux VM se web server ko `GET /index.html HTTP/1.1` request bhejte hain, to HTTP request bytes TCP segment ka application payload ban sakte hain. TCP segment, IP packet ka payload banta hai. Phir poora IP packet, Ethernet frame ka payload banta hai.

> **Important:** Payload ka meaning layer ke hisaab se change hota hai. “Actual user data” kehna basic explanation hai, lekin IP payload mein TCP header bhi hota hai; Ethernet payload mein IP aur TCP headers dono hote hain.

## 2. Header, Payload, Trailer

| Part | Meaning | Example |
|---|---|---|
| Header | Data ke aage protocol ki control information | Ethernet source/destination MAC, EtherType |
| Payload | Us protocol ke andar carry hone wali cheez | Ethernet ke andar IP packet |
| Trailer | Data ke end par additional information | Ethernet FCS (error detection) |

```text
+----------------------+----------------------------+--------------+
| Ethernet Header      | Ethernet Payload           | FCS Trailer  |
| 14 bytes (untagged)  | Typically IP packet        | 4 bytes      |
+----------------------+----------------------------+--------------+
```

**Roman Urdu:** Ethernet header frame ko batata hai ke sender/receiver MAC addresses kya hain. Payload mein IP packet hota hai. FCS frame errors detect karne mein madad karta hai.

## 3. Har Layer par Payload ka meaning

| OSI Layer | PDU | Header/Trailer | Is layer ka payload |
|---|---|---|---|
| L4 Transport | TCP segment | TCP header | Application data bytes |
| L3 Network | IPv4 packet | IPv4 header | TCP segment (ya doosra L4 payload) |
| L2 Data Link | Ethernet frame | Ethernet header + FCS | IPv4 packet (ya doosra EtherType payload) |
| L1 Physical | Bit/signal stream | Physical encoding/framing | Transmitted frame bits |

```text
Application Data = [ HTTP request bytes ]

TCP Segment      = [ TCP Header ][ HTTP request bytes ]
                                    └ TCP payload ┘

IP Packet        = [ IP Header ][ TCP Header ][ HTTP request bytes ]
                               └────── IP payload ────────────────┘

Ethernet Frame   = [ ETH Header ][ IP Header ][ TCP Header ][ HTTP bytes ][ FCS ]
                                └────── Ethernet payload ──────────────┘
```

Note: Real systems mein TLS, VLAN tags, TCP options aur doosray protocol headers bhi aa sakte hain. Above diagram simplified hai.

## 4. Encapsulation aur Decapsulation

**Sender side (encapsulation):** Application data → TCP segment → IP packet → Ethernet frame → physical bits.

**Receiver side (decapsulation):** Physical bits → Ethernet frame → IP packet → TCP segment → application data.

**Roman Urdu:** Sender par har layer apni control information add karti hai. Receiver par corresponding layers headers/trailers ko interpret karke andar ka payload next layer ko deti hain.

## 5. MTU 1500 ka Payload se relationship

Aapke `STORAGE` vSwitch ka MTU 1500 hai. Ethernet par **MTU 1500** typically maximum IP packet size ko refer karta hai, **poore Ethernet frame ko nahi**.

### Example: Standard IPv4/TCP, koi options nahi

| Component | Size |
|---|---:|
| Application data = TCP payload | 1460 bytes |
| TCP header | 20 bytes |
| IPv4 header | 20 bytes |
| **Total IPv4 packet = Ethernet payload** | **1500 bytes** |
| Ethernet header (untagged) | 14 bytes |
| Ethernet FCS | 4 bytes |
| **Ethernet frame (excluding preamble/SFD)** | **1518 bytes** |

**Calculation:** `1500 - 20 (IPv4 header) - 20 (TCP header) = 1460 bytes TCP payload`.

Agar Ethernet frame par 802.1Q VLAN tag ho, to extra **4 bytes** header mein add hote hain, aur frame 1522 bytes ho sakta hai. TCP options, IPv6, tunnels ya encryption se effective application payload size badal sakta hai.

**MSS**: TCP Maximum Segment Size aam IPv4/TCP no-options MTU-1500 case mein 1460 hota hai. MSS aur MTU same cheez nahi.

## 6. VMware ESXi home-lab example

Aapke `desktop-esxi9` par `vSwitch0` ke andar `SERVERS` VM port group hai. Agar future mein do VMs isi host aur isi VLAN par connect hoti hain:

```text
Linux VM1 (example: 10.0.0.101)
       |
       |  Ethernet frame (IP packet payload ke andar)
       v
SERVERS Port Group  --->  vSwitch0  --->  SERVERS Port Group
                                              |
                                              v
                                  Linux VM2 (10.0.0.102)
```

Aisi same-host, same-VLAN traffic normally internal vSwitch par forward ho sakti hai; `vmnic0` se physical network mein jana zaroori nahi. Agar destination external host ho, traffic selected physical uplink tak ja sakti hai.

**Storage example:** `vmk1` (`172.16.0.102/16`) aur `STORAGE` port group `vSwitch1` par configured hain, MTU 1500 ke saath. Storage protocol ke payload ki actual size transport aur protocol headers par depend karti hai; storage server connectivity abhi independently verify karni hogi.

## 7. Packet Capture — Payload ko observe kaise karein?

Linux VM mein, agar `tcpdump` installed hai, ek authorized lab network par:

```bash
sudo tcpdump -ni eth0 -c 5
```

**Kyun:** Interface `eth0` par 5 packets ki basic headers/summary capture karna. **`-n`** names resolve nahi karta; **`-i eth0`** interface select karta hai; **`-c 5`** 5 packets ke baad stop karta hai. VM mein interface ka naam `ens160`, `ens33`, etc. bhi ho sakta hai—`ip -br link` se check karein.

```bash
sudo tcpdump -ni eth0 -s 0 -X -c 3
```

**Kyun:** Packet bytes ka hex aur ASCII representation dekhna. **`-s 0`** full packet capture snap length request karta hai; **`-X`** hex + ASCII output deta hai. Encrypted HTTPS ka application payload readable text mein nahi dikhega. Capture mein passwords/tokens aa sakte hain—public GitHub par raw captures publish na karein.

**Example only, real output nahi:**

```text
IP 10.0.0.101.53000 > 10.0.0.102.80: Flags [P.], length 18
```

- `10.0.0.101.53000`: example source IP and source port
- `10.0.0.102.80`: destination IP and destination TCP port
- `Flags [P.]`: TCP Push + ACK flags (tcpdump notation)
- `length 18`: tcpdump ke is TCP summary mein TCP application data ki length, **poore Ethernet frame ki length nahi**

Note: `tcpdump` output capture link layer aur command flags ke hisaab se different ho sakta hai.

## 8. Common Confusions

1. **Payload hamesha application message nahi hota.** IP payload ke andar TCP header hota hai.
2. **MTU ≠ Ethernet frame total size.** Ethernet header/trailer alag overhead hain.
3. **Frame ≠ Packet ≠ Segment.** Layer-specific PDUs hain.
4. **Payload ≠ Bandwidth.** Payload data hai; bandwidth transmission capacity/rate hai.
5. **Encryption:** HTTPS mein payload encrypted ho sakta hai, magar packet/frame forwarding phir bhi hoti hai.
6. **VLAN tag:** Ethernet header mein 4 extra bytes add kar sakta hai; VLAN ID port group configuration se associated hota hai.

## 9. Interview Questions & Answers

**Q1. Payload ki definition?**  
A: Kisi protocol unit ke andar carry hone wala data, us protocol ke apne header/trailer ke baghair.

**Q2. TCP payload aur IP payload mein farq?**  
A: TCP payload application bytes hain; IPv4 payload typically poora TCP segment hai, jis mein TCP header bhi hota hai.

**Q3. Ethernet frame ka payload kya hota hai?**  
A: Common IPv4 example mein entire IP packet.

**Q4. MTU 1500 par TCP MSS 1460 kyun?**  
A: Basic IPv4 aur TCP headers 20+20 bytes lete hain; `1500-40=1460`.

**Q5. Ethernet frame 1518 bytes kaise?**  
A: Standard untagged frame mein 14-byte Ethernet header + 1500-byte payload + 4-byte FCS.

**Q6. Kya ESXi vSwitch HTTP payload process karta hai?**  
A: Normal Layer 2 switching mein nahi; Ethernet forwarding ke liye relevant MAC/VLAN/port metadata use karta hai.

**Q7. Same vSwitch par VMs ko physical uplink chahiye?**  
A: Same-host, same-VLAN VM-to-VM switching ke liye uplink zaroori nahi. External network ke liye uplink chahiye.

**Q8. Payload dekhne ke liye kya tool use hoga?**  
A: Linux mein tcpdump ya Wireshark; encryption aur privacy restrictions dhyan mein rakhein.

## 10. Self-Practice

- [ ] Data → Segment → Packet → Frame → Bits apni notebook mein draw karein.
- [ ] TCP vs IP vs Ethernet payload ki definitions apne alfaaz mein likhein.
- [ ] MTU 1500 se MSS 1460 calculate karein.
- [ ] VLAN tag ke saath Ethernet frame size 1522 verify karein.
- [ ] Future Linux VM par `ip -br link` aur `tcpdump` se practice karein.
- [ ] Explain karein ke same-host VMs ka frame `vmnic0` se bahar kyun nahi jana padta.

---

**Study note:** Yeh networking fundamentals ki standalone explanation hai jo aapki ESXi lab configuration se relate karti hai. Diagrams aur sample packet output illustrative hain; actual packet captures nahi.
