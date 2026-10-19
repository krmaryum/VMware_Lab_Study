# Quick Reference — Encapsulation (Roman Urdu)

```text
Sender:   DATA -> TCP SEGMENT -> IP PACKET -> ETHERNET FRAME -> BITS
Receiver: BITS -> ETHERNET FRAME -> IP PACKET -> TCP SEGMENT -> DATA
```

| Unit | Layer | Kis cheez ko identify karti hai? |
|---|---|---|
| Data | L7–L5 | Application ka data |
| TCP Segment / UDP Datagram | L4 | Source/destination ports |
| IP Packet | L3 | Source/destination IP |
| Ethernet Frame | L2 | MAC, EtherType, FCS |
| Bits | L1 | Link par transmitted signals |

**MTU example:** 1500 - IPv4 Header 20 - TCP Header 20 = **1460-byte TCP MSS**. Ethernet untagged full-size frame = 14 + 1500 + 4 = **1518 bytes**. VLAN tagged = **1522 bytes**.

**ESXi:** `vSwitch0` Layer 2 frames forward karta hai. `vmnic0` external uplink hai. `vmk0` aur `vmk1` VMkernel interfaces hain. Same-host/same-VLAN VM traffic bina physical uplink bhi chal sakta hai.

**Common commands:**

```bash
ip -br addr
ip link show
ip route
ip neigh show
sudo tcpdump -ni <interface-name> -e -c 10 'arp or icmp or tcp'
esxcli network vswitch standard list
esxcli network ip interface list
esxcli network ip interface ipv4 get
```

**Note:** Example addresses aur future packet-capture outputs ko real completed lab evidence na samjhein.
