# VMware ESXi Networking — Payload Study Notes (English)

**Topic:** Payload, Header, Trailer, Encapsulation, Frame, Packet, Segment, and MTU  
**Language:** English  
**Lab context:** ESXi host `desktop-esxi9`; `vSwitch0` with the `SERVERS` port group; storage `vSwitch1` / `STORAGE` / `vmk1` / MTU 1500. VM addresses in examples are illustrative, not confirmed existing VMs.

## 1. What is a Payload?

A **payload** is the data carried *inside* a given protocol data unit (PDU). It generally excludes the headers and trailers that belong to **that particular protocol**.

**Example:** When a Linux VM sends an HTTP request such as `GET /index.html HTTP/1.1`, those HTTP request bytes may become the **application payload** of a TCP segment. The entire TCP segment becomes the payload of an IP packet. The entire IP packet then becomes the payload of an Ethernet frame.

> **Important:** The meaning of payload depends on the protocol layer. Saying “payload is the actual user data” is a useful starting point, but the IP payload also contains a TCP header, and the Ethernet payload contains both IP and TCP headers.

## 2. Header vs Payload vs Trailer

| Part | Meaning | Example |
|---|---|---|
| Header | Control information preceding the data | Ethernet source/destination MAC addresses, EtherType |
| Payload | Data carried within that particular protocol | An IP packet inside an Ethernet frame |
| Trailer | Information appended after the data | Ethernet FCS used for error detection |

```text
+----------------------+----------------------------+--------------+
| Ethernet Header      | Ethernet Payload           | FCS Trailer  |
| 14 bytes (untagged)  | Typically an IP packet     | 4 bytes      |
+----------------------+----------------------------+--------------+
```

**Explanation:** The Ethernet header identifies source and destination MAC addresses and the carried protocol. The payload commonly contains an IP packet. The Frame Check Sequence (FCS) helps detect frame corruption.

## 3. Payload at Each Layer

| OSI Layer | Protocol Data Unit | Header / Trailer | Payload at This Layer |
|---|---|---|---|
| L4 Transport | TCP segment | TCP header | Application data bytes |
| L3 Network | IPv4 packet | IPv4 header | TCP segment (or other transport content) |
| L2 Data Link | Ethernet frame | Ethernet header + FCS | IPv4 packet (or another EtherType payload) |
| L1 Physical | Bit/signal stream | Physical encoding and framing | Bits representing the transmitted frame |

```text
Application Data = [ HTTP request bytes ]

TCP Segment      = [ TCP Header ][ HTTP request bytes ]
                                    └ TCP payload ┘

IP Packet        = [ IP Header ][ TCP Header ][ HTTP request bytes ]
                               └──────── IP payload ──────────────┘

Ethernet Frame   = [ ETH Header ][ IP Header ][ TCP Header ][ HTTP bytes ][ FCS ]
                                └──────── Ethernet payload ────────────┘
```

**Note:** Real traffic might also involve TLS, VLAN tags, TCP options, tunneling, or other headers. This diagram is intentionally simplified.

## 4. Encapsulation and Decapsulation

**Sender — Encapsulation:** Application data → TCP segment → IP packet → Ethernet frame → physical bits.

**Receiver — Decapsulation:** Physical bits → Ethernet frame → IP packet → TCP segment → application data.

**Why it happens:** At the sender, each protocol layer adds information needed for its responsibility. At the receiver, the corresponding layers interpret that information and deliver the enclosed payload upward.

## 5. How Payload Relates to MTU 1500

Your lab's `STORAGE` virtual switch uses an MTU of **1500**. For a typical Ethernet network, **MTU 1500** refers to the maximum IP packet size carried as Ethernet payload, **not the complete Ethernet frame size**.

### Worked example: IPv4 and TCP, without options

| Component | Size |
|---|---:|
| Application data = TCP payload | 1460 bytes |
| TCP header | 20 bytes |
| IPv4 header | 20 bytes |
| **Total IPv4 packet = Ethernet payload** | **1500 bytes** |
| Ethernet header (untagged) | 14 bytes |
| Ethernet FCS | 4 bytes |
| **Total Ethernet frame (excluding preamble/SFD)** | **1518 bytes** |

**Calculation:** `1500 - 20 (IPv4 header) - 20 (TCP header) = 1460 bytes TCP payload`.

An 802.1Q VLAN tag adds **4 bytes** to the Ethernet header, producing a **1522-byte** tagged frame in this example. TCP options, IPv6, tunnels, and encryption may change the effective application payload capacity.

**MSS (Maximum Segment Size)** is the maximum TCP data payload advertised by an endpoint. A common IPv4/TCP, no-options calculation for a 1500-byte MTU gives **MSS 1460**. MTU and MSS are **not** the same.

## 6. Example from Your VMware ESXi Home Lab

On your `desktop-esxi9` host, `vSwitch0` has a VM port group named `SERVERS`. If you later create two Linux VMs attached to the same port group and VLAN:

```text
Linux VM1 (example IP: 10.0.0.101)
       |
       | Ethernet frame carrying an IP packet
       v
SERVERS Port Group ---> vSwitch0 ---> SERVERS Port Group
                                            |
                                            v
                                 Linux VM2 (10.0.0.102)
```

For same-host, same-VLAN communication, the vSwitch can forward the Ethernet frame internally; the frame does **not** need to leave through `vmnic0`. For an external destination, the vSwitch may send frames through a suitable physical uplink.

**Storage example:** You configured `vmk1` (`172.16.0.102/16`) on the `STORAGE` VMkernel port group on `vSwitch1`, using MTU 1500. Storage protocol payload sizes depend on their own transport and protocol headers. Connectivity to a storage server has **not yet been independently verified**.

## 7. Inspecting Payload with Packet Capture

On a Linux VM, if `tcpdump` is installed, and you have permission to capture traffic on your lab network:

```bash
sudo tcpdump -ni eth0 -c 5
```

**Why:** Capture summaries of five packets on interface `eth0`.

**How to read the options:**

- `sudo` — run with the necessary packet-capture privileges.
- `-n` — avoid translating IP addresses/ports to names.
- `-i eth0` — select the interface named `eth0`.
- `-c 5` — stop after capturing five packets.

Your interface may instead be named `ens160`, `ens33`, etc. Identify the actual name first:

```bash
ip -br link
```

To examine hexadecimal and ASCII packet bytes:

```bash
sudo tcpdump -ni eth0 -s 0 -X -c 3
```

**Why:** Inspect more of each packet's captured bytes, not just a summary.

- `-s 0` — request a full-packet snapshot length.
- `-X` — show bytes in both hexadecimal and ASCII.
- `-c 3` — capture three packets and stop.

**Caution:** HTTPS application content is encrypted and will not appear as readable plaintext. Packet captures can include private information, credentials, or tokens; do not publish raw captures to a public GitHub repository.

### Illustrative output — not an actual lab capture

```text
IP 10.0.0.101.53000 > 10.0.0.102.80: Flags [P.], length 18
```

| Output field | Meaning |
|---|---|
| `10.0.0.101.53000` | Example source IP and TCP source port |
| `10.0.0.102.80` | Example destination IP and TCP destination port |
| `Flags [P.]` | TCP Push and ACK flags, in tcpdump notation |
| `length 18` | TCP application payload length reported in this TCP summary; **not** the Ethernet frame length |

Output formatting depends on the capture method, link type, flags, and traffic.

## 8. Common Misunderstandings

1. **Payload is not always only an application message.** An IP payload may include a TCP header.
2. **MTU is not the complete Ethernet frame size.** Ethernet headers/trailers add overhead.
3. **Frame, packet, and segment are different.** They are PDUs at different protocol layers.
4. **Payload is not bandwidth.** Payload is carried data; bandwidth is transmission capacity or rate.
5. **Encryption does not prevent basic switching.** HTTPS payload can be encrypted while Ethernet frames are still forwarded.
6. **VLAN tags add overhead.** A standard 802.1Q tag is four bytes in an Ethernet frame.

## 9. Interview Questions and Answers

**Q1. What is a payload?**  
A: The content carried within a protocol data unit, excluding that protocol's own header and trailer.

**Q2. What is the difference between TCP payload and IP payload?**  
A: The TCP payload contains application bytes. The IPv4 payload commonly contains the entire TCP segment, including its TCP header.

**Q3. What does an Ethernet frame carry as its payload?**  
A: In a common IPv4 example, it carries the entire IP packet.

**Q4. Why is TCP MSS commonly 1460 with MTU 1500?**  
A: In a basic IPv4/TCP example, 20-byte IPv4 and 20-byte TCP headers leave `1500 - 40 = 1460` bytes for TCP data.

**Q5. Why can an untagged Ethernet frame be 1518 bytes?**  
A: It contains a 14-byte Ethernet header, a 1500-byte payload, and a 4-byte FCS (not counting preamble/SFD).

**Q6. Does an ESXi vSwitch normally process the contents of an HTTP request?**  
A: No. Standard Layer 2 switching forwards Ethernet frames using relevant port, MAC, and VLAN information, rather than parsing an application's HTTP content.

**Q7. Do two VMs on one ESXi host need a physical NIC to communicate?**  
A: Not if they can communicate on the same local vSwitch and VLAN. External network access typically requires an appropriate uplink.

**Q8. Which tools can inspect network payloads?**  
A: `tcpdump` or Wireshark, subject to encryption and capture permissions.

## 10. Self-Practice Checklist

- [ ] Draw Data → Segment → Packet → Frame → Bits.
- [ ] Define TCP, IP, and Ethernet payload in your own words.
- [ ] Calculate MSS 1460 from MTU 1500 for an IPv4/TCP no-options example.
- [ ] Explain how a VLAN tag increases a standard full-size Ethernet frame to 1522 bytes.
- [ ] On a future Linux VM, practice `ip -br link` and an authorized `tcpdump` capture.
- [ ] Explain why same-host, same-VLAN VM-to-VM frames may remain entirely on `vSwitch0`.

---

**Documentation note:** This is a standalone networking explanation connected to your ESXi lab. The sample VM addresses, diagrams, and packet capture output are **illustrative**, not evidence of actual VM packet captures.
