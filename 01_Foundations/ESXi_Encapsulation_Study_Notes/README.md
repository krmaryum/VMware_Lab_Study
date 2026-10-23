# VMware ESXi Networking — Encapsulation, Frames, Packets and Segments

**Study notes | English + Roman Urdu | Home lab reference**

> **Scope:** This chapter expands the encapsulation lesson from our conversation. It is a networking fundamentals chapter connected to your ESXi lab, **not** a record of a completed packet-capture test. Example VM IPs are illustrative.

## Learning objectives

By the end you should be able to distinguish Data, TCP Segment, UDP Datagram, IP Packet, Ethernet Frame and Bits; explain encapsulation/decapsulation; identify the role of a vSwitch; calculate Ethernet frame sizes for MTU 1500; and interpret basic Linux/ESXi networking commands.

## 1. Data units across the OSI model

| OSI layer | Typical protocol/function | PDU or data unit | Main addressing/info |
|---|---|---|---|
| 7 Application | HTTP, DNS | Data | Application request |
| 6 Presentation | Encoding, encryption representation | Data | Data format |
| 5 Session | Session coordination (conceptual OSI layer) | Data | Session state |
| 4 Transport | TCP / UDP | **TCP segment** / **UDP datagram** | Source and destination ports |
| 3 Network | IPv4 / IPv6 | **IP packet** | Source and destination IP addresses |
| 2 Data Link | Ethernet | **Ethernet frame** | Source/destination MAC, EtherType, FCS |
| 1 Physical | Physical signal | **Bits** | Electrical/optical/radio signal encoding |

**Roman Urdu:** Jab data sender se receiver ki taraf jata hai, har neeche wali layer apni zaroori information add karti hai. Layer 4 par TCP segment, Layer 3 par IP packet, aur Layer 2 par Ethernet frame hota hai. Layer 1 in bits ko signals ki shakal mein transmit karti hai.

### Core diagram

```text
Sender — ENCAPSULATION (top -> bottom)

Application data
      |
      v
[TCP HEADER | DATA]                          = TCP SEGMENT
      |
      v
[IP HEADER | TCP HEADER | DATA]              = IP PACKET
      |
      v
[ETH HEADER | IP HEADER | TCP HEADER | DATA | FCS] = ETHERNET FRAME
      |
      v
101100101010...                            = BITS ON A LINK

Receiver — DECAPSULATION (bottom -> top)

Bits -> Frame -> Packet -> Segment -> Application Data
```

**Important:** A TCP segment is used as an example. UDP has a *datagram*, not a TCP segment. Application protocols may use encryption (e.g., TLS), which adds its own records/overhead.

## 2. Encapsulation: what exactly happens?

Suppose **Linux VM1 (example: 10.0.0.101)** makes a web request to **Linux VM2 (example: 10.0.0.102)**.

1. **Application:** VM1 prepares HTTP application data, such as `GET / HTTP/1.1` (plus required headers).
2. **Transport:** TCP adds a source port, destination port (e.g., 80 for HTTP), sequence number, flags and more. The result is a **segment**.
3. **Network:** IPv4 adds source and destination IPs, TTL, protocol and more. Result: an **IP packet**.
4. **Data link:** Ethernet adds source/destination MAC addresses, EtherType and an FCS trailer (on the wire). Result: an **Ethernet frame**.
5. **Physical:** Frame bits are encoded for transmission. In a same-host ESXi virtual path, the hypervisor handles virtual forwarding; no actual physical cable is required for traffic between the two VMs.

**Roman Urdu:** VM1 ka HTTP data pehle TCP header ke sath segment banta hai, phir IP header ke sath packet, phir Ethernet header aur trailer ke sath frame. Agar dono VMs aik hi ESXi host aur VLAN par hon to vSwitch frame ko andar hi forward kar sakta hai.

## 3. Decapsulation: receiver side

The receiver processes link-layer information, delivers the IP packet to its IP stack, passes TCP payload to the correct listening socket by port and finally to the application. TCP can reassemble streams from multiple received segments.

**Roman Urdu:** Receiver par reverse process hota hai. Har layer apna header process karti hai aur upper layer ko payload deti hai. Isi ko decapsulation kehte hain.

## 4. Frame vs Packet vs Segment — interview comparison

| Question | Frame | Packet | Segment (TCP) |
|---|---|---|---|
| OSI layer | 2 | 3 | 4 |
| Addressing | MAC | IP | Port numbers |
| Typical forwarding device | Ethernet switch / ESXi vSwitch | Router / L3 device | End-host TCP stack |
| Encapsulates | IP packet (usually) | TCP segment (example) | Application bytes |
| Changes across routed hops? | Normally new L2 header each hop | IP source/destination normally remain; TTL changes | Usually end-to-end TCP data/ports |

**Nuance:** A router may alter packet fields (e.g., TTL), and NAT may change IP/port values. A vSwitch does not perform routing by default.

## 5. What does an Ethernet frame contain?

Standard untagged Ethernet II framing (without preamble/SFD or inter-frame gap):

```text
| Destination MAC 6B | Source MAC 6B | EtherType 2B |
|                      Payload 46–1500B                     |
|                           FCS 4B                          |
```

- **Destination MAC:** device/interface expected to receive the Ethernet frame on that L2 domain.
- **Source MAC:** sender's source MAC on that hop.
- **EtherType:** identifies payload protocol, e.g. IPv4 (`0x0800`) or ARP (`0x0806`).
- **Payload:** typically contains the IP packet; padding may be added to meet minimum size.
- **FCS:** Frame Check Sequence used for detecting bit errors on the link. Capture utilities often do not show FCS because network hardware strips it before capture.
- **802.1Q VLAN tag:** adds 4 bytes to the Ethernet header when tagged.

> Payload is the data carried inside a network protocol unit, excluding that protocol's own headers and trailers. Its contents depend on the OSI layer.

**Roman Urdu:** Frame ke andar source aur destination MAC address, protocol ki pehchan aur actual packet hota hai. FCS transmission errors detect karne mein help karta hai. VLAN tag ho to 4 bytes extra add hotay hain.

## 6. MTU 1500 vs Ethernet frame size

For a **typical Ethernet IPv4 + TCP packet with no IPv4/TCP options**:

| Component | Bytes |
|---|---:|
| TCP application data (TCP MSS) | 1460 |
| TCP header | 20 |
| IPv4 header | 20 |
| **IP packet = IP MTU** | **1500** |
| Ethernet header (untagged) | 14 |
| FCS | 4 |
| **Ethernet frame (untagged; excluding preamble/SFD)** | **1518** |
| Optional VLAN tag | +4 |
| **802.1Q tagged frame** | **1522** |

Preamble (7 bytes), start-frame delimiter (1 byte) and inter-frame gap (12 byte times) are separately accounted for on a physical Ethernet link, and are **not** included in the 1518/1522 frame counts above.

**Key formula:** `1500 - 20 (IPv4) - 20 (TCP) = 1460-byte TCP MSS` (only for the basic no-options case). IPv6 has a 40-byte base header, and TCP/IP options or tunnels reduce the available payload.

**Roman Urdu:** MTU 1500 aam tor par Ethernet ke andar IP packet ki limit hai. Pura untagged Ethernet frame 1518 bytes tak hota hai (standard example). Agar 802.1Q VLAN tag ho, 1522 bytes tak ho sakta hai.

## 7. Apply this to your ESXi lab

Your screenshots from the earlier lab showed the following intended network arrangement (subject to reconfirmation after future changes):

```text
                  ESXi host: desktop-esxi9

   Linux VM1                              Linux VM2
       |                                      |
       +---------- SERVERS port group -------+
                          |
                      vSwitch0
                       /    \
   Management Network (vmk0)  vmnic0 uplink
   example observed: 10.0.0.10    |
                                 External network

   STORAGE VMkernel: vmk1 (172.16.0.102/16)
                       |
                STORAGE port group
                       |
                     vSwitch1  (MTU 1500)
                       |
                     vmnic1
                       |
               Storage network (peer not yet tested)
```

- **Same host, same VLAN, same vSwitch:** VM-to-VM Ethernet frames can stay inside ESXi; they do not need to leave via `vmnic0`.
- **External destination:** traffic can leave through an assigned uplink if routing and L2 connectivity permit.
- **Different IP subnet:** a router is required for typical inter-subnet communication; a vSwitch alone is **not** a router.
- **Different isolated vSwitches:** there is no automatic Layer 2 connectivity between them.
- **`vSwitch1` MTU 1500:** storage traffic does not require jumbo frames. End-to-end testing is needed before adopting 9000.

**Note:** No VM-to-VM or end-to-end storage packet capture was performed in the conversation. The addresses 10.0.0.101/102 are teaching examples.

## 8. Practical commands: WHY, HOW, WHAT THE OUTPUT MEANS

### Exercise A: Inspect your Linux VM interface

```bash
ip -br addr
ip link show
```

**Why:** Find NIC names, configured IP addresses, interface state and MTU.

**Output guide:** `UP` means administrative state up; `LOWER_UP` usually indicates that the interface has a carrier; `mtu 1500` tells you the maximum IP-layer payload size configured for the interface in this conventional Ethernet context. Interface names vary (`ens160`, `eth0`, etc.).

### Exercise B: Find routing and the next hop

```bash
ip route
ip route get 10.0.0.102
```

**Why:** Determine which interface and route Linux would use for destination `10.0.0.102`.

**Output guide:** `dev ens160` indicates egress interface, `via 10.0.0.1` means the traffic goes to a gateway. The exact output depends on your real Linux VM setup.

### Exercise C: Find L2 neighbor MAC resolution

```bash
ip neigh show
```

**Why:** View IPv4 ARP and IPv6 neighbor-discovery information used to resolve an on-link IP or next-hop IP to a MAC address.

**Output guide:** `lladdr` is the link-layer address; `REACHABLE`, `STALE`, `INCOMPLETE` show neighbor cache states. Not every destination appears until it is contacted.

### Exercise D: Examine packets in a lab Linux VM (only if tcpdump installed)

```bash
sudo tcpdump -ni <interface-name> -e -c 10 'arp or icmp or tcp'
```

Replace `<interface-name>` with a real NIC from `ip -br addr`.

**Why:** `-n` suppresses name resolution, `-i` chooses the interface, `-e` shows Ethernet header/MAC addresses, `-c 10` stops after 10 packets and the filter selects ARP/ICMP/TCP.

**Output guide:** A TCP capture shows IP/port fields, flags and sizes. ARP shows address resolution. **FCS usually will not appear**. Use only on networks you administer or have permission to inspect.

### Exercise E: ESXi switch and VMkernel inspection (ESXi Shell/SSH)

```bash
esxcli network vswitch standard list
esxcli network ip interface list
esxcli network ip interface ipv4 get
```

**Why:** Identify configured standard switches/uplinks/MTU, VMkernel interfaces and IPv4 addressing.

**Output guide:** Verify `vSwitch0` and `vSwitch1`, the relevant uplink assignments, `vmk0` management network and `vmk1` storage network. This is inspection only; commands may require shell access and sufficient permissions.

### Exercise F: Test the storage MTU from ESXi (after a real storage peer exists)

```bash
vmkping -I vmk1 <storage-peer-IP>
```

**Why:** Test reachability **from the storage VMkernel interface**.

**Output guide:** Replies support basic connectivity but do **not** prove that a storage service works. An ICMP failure also does not by itself prove network failure, because ICMP may be blocked. Do not treat this as completed without actual output.

## 9. Common errors and what they mean

| Symptom | Possible explanation | Check |
|---|---|---|
| Ping fails but both VMs show connected | Wrong VLAN/subnet, firewall, guest NIC, routing | `ip -br addr`, `ip route`, Port Group VLAN ID |
| IP works but hostname does not | DNS or hosts-file name resolution | `nslookup`, `Resolve-DnsName`, hosts file |
| TCP port 443 works but browser fails | HTTP/TLS, certificate, browser or server application issue | Test complete HTTPS URL, inspect response |
| Traffic not seen on physical NIC between same-host VMs | Frames can be switched internally | Capture on the correct virtual/guest interface |
| MTU 9000 configured but large packets fail | Not every hop supports jumbo frames | Check ESXi vSwitch, VMkernel, switch, storage peer |

## 10. Interview questions and answers

**Q1. What is encapsulation?** Adding layer-specific headers/trailers as data moves down the stack for transmission.

**Q2. What is decapsulation?** Processing/removing layer information as received data moves up the stack.

**Q3. Segment vs packet vs frame?** TCP segment at L4, IP packet at L3, Ethernet frame at L2.

**Q4. Why does a frame need MAC addresses?** L2 delivery/forwarding within the link-layer domain.

**Q5. Why does a packet need IP addresses?** Logical addressing for network-layer delivery and routing.

**Q6. Can two VMs on the same ESXi vSwitch communicate with no uplink?** Yes, if connected on compatible port groups/VLANs and their guest network configurations permit it.

**Q7. Does a standard vSwitch route between different IP subnets?** No, it is fundamentally an L2 switch.

**Q8. Does MTU 1500 mean an Ethernet frame is 1500 bytes?** No. A standard untagged frame carrying a full 1500-byte IP packet is 1518 bytes including FCS, excluding preamble/SFD.

**Q9. What does VLAN tagging add?** A four-byte 802.1Q tag in an Ethernet frame.

**Q10. Why might tcpdump not display FCS?** The NIC/virtual network stack typically strips it before the packet is captured.

## 11. Knowledge check

1. What PDU name is used at Layer 2?
2. What addresses are added at Layer 3?
3. With IPv4 (20 B) and TCP (20 B) and MTU 1500, what is the typical TCP MSS?
4. If two VMs are on the same host, VLAN and vSwitch, must their frames leave the physical uplink?
5. What does the 4-byte VLAN tag change?

**Answers:** 1) Frame. 2) Source and destination IP addresses. 3) 1460 B. 4) No. 5) Adds an 802.1Q tag and increases the Ethernet frame by 4 bytes.

---

### Suggested next lab

Create two isolated test Linux VMs on `SERVERS`, configure safe test IPs suitable for your real network, inspect their MAC addresses with `ip link`, test connectivity, and observe ARP/ICMP/TCP in a capture. Record screenshots and **actual outputs**, then update these notes. Avoid changing ESXi management networking during tests.

**Revision:** Stage 1 fundamentals, October 2026.
