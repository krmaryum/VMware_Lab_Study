# Payload — Quick Revision (English)

**Payload:** The information carried inside a particular protocol data unit, excluding that protocol's own header and trailer.

| PDU | Payload |
|---|---|
| TCP segment | Application data |
| IP packet | TCP segment or other transport data |
| Ethernet frame | IP packet or another supported network-layer payload |

```text
Application Data
  -> [TCP header + data] = Segment
  -> [IP header + segment] = Packet
  -> [Ethernet header + packet + FCS] = Frame
  -> Bits
```

**MTU 1500 example (IPv4/TCP, no options):** 1460 bytes application payload + 20 bytes TCP header + 20 bytes IPv4 header = **1500-byte IP packet**. Add a 14-byte Ethernet header and 4-byte FCS = **1518-byte untagged frame**. A 4-byte VLAN tag produces a **1522-byte tagged frame**.

**ESXi example:** `vSwitch0` forwards Ethernet frames. Two same-host VMs communicating within the same VLAN need not send their traffic through a physical NIC.

**Interview definition:** “Payload is the content carried by a protocol data unit, excluding that protocol's own headers and trailers.”
