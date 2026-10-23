# Payload — Quick Revision (Roman Urdu)

**Payload:** Kisi particular protocol ke unit ke andar carry hone wala data, us protocol ke apne header/trailer ke ilawa.

| PDU | Payload |
|---|---|
| TCP Segment | Application data |
| IP Packet | TCP segment / other transport data |
| Ethernet Frame | IP packet / other network payload |

```text
Application Data
  -> [TCP header + data] = Segment
  -> [IP header + segment] = Packet
  -> [Ethernet header + packet + FCS] = Frame
  -> Bits
```

**MTU 1500 example (IPv4/TCP without options):** `1460 app data + 20 TCP + 20 IPv4 = 1500 IP packet`; plus Ethernet 14 header and 4 FCS = **1518 bytes** untagged frame. VLAN tag adds 4 bytes = **1522 bytes**.

**ESXi:** `vSwitch0` Ethernet frame forward karta hai. Same-host aur same-VLAN VMs ke liye physical NIC use karna zaroori nahi.

**Interview line:** "Payload is the content carried by a protocol data unit, excluding that protocol's own headers and trailers."
