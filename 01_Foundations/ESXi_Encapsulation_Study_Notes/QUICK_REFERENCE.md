# Quick Reference — Encapsulation and ESXi

```text
DATA -> TCP SEGMENT -> IP PACKET -> ETHERNET FRAME -> BITS
BITS -> ETHERNET FRAME -> IP PACKET -> TCP SEGMENT -> DATA
```

| PDU | Layer | Key information |
|---|---:|---|
| Data | 7–5 | Application bytes |
| TCP segment / UDP datagram | 4 | Ports |
| IP packet | 3 | IP addresses |
| Ethernet frame | 2 | MAC addresses, EtherType, FCS |
| Bits | 1 | Transmitted signal |

**MTU 1500, IPv4+TCP without options:** TCP payload 1460 + TCP header 20 + IPv4 header 20 = IP packet 1500. Ethernet 14 + IP packet 1500 + FCS 4 = frame 1518. With VLAN tag, 1522.

**ESXi:** `vSwitch0` sends L2 frames between VM interfaces/port groups; compatible same-host VM traffic may stay entirely on the host. `vmnic0` is an uplink; `vmk0` and `vmk1` are VMkernel interfaces.

**Roman Urdu:** Data neeche layers mein ja kar headers ke sath segment, packet aur frame banta hai. Receiver par reverse process hota hai. ESXi ka vSwitch Layer 2 par Ethernet frames forward karta hai.
