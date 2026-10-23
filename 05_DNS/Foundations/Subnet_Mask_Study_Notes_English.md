# Subnet Mask — Study Notes

## Contents

1. [What is a subnet mask?](#1-what-is-a-subnet-mask)
2. [Your desktop example](#2-your-desktop-example)
3. [Local and remote destinations](#3-local-and-remote-destinations)
4. [How the computer decides](#4-how-the-computer-decides)
5. [Subnet mask versus gateway and DNS](#5-subnet-mask-versus-gateway-and-dns)
6. [Windows commands](#6-windows-commands)
7. [Important points and revision](#7-important-points-and-revision)

## 1. What is a subnet mask?

A **subnet mask identifies the network and host portions of an IPv4 address**. Your computer uses its own IP address and subnet mask to determine whether a destination belongs to its local subnet.

A subnet is a logical subdivision of an IP network. A host is a device or network interface with an IP address.

The mask does not identify a network on its own: it must be used together with an IP address.

## 2. Your desktop example

These values come from your previously shared home-lab information; they are not a new live check.

| Setting | Value | Meaning |
| --- | --- | --- |
| Desktop IP address | `10.0.0.213` | Address of the desktop's Ethernet interface |
| Subnet mask | `255.255.255.0` | First 24 bits identify the network |
| CIDR notation | `10.0.0.213/24` | `/24` is another way to express this mask |
| Network address | `10.0.0.0` | Identifies this subnet |
| Usual usable host range | `10.0.0.1`–`10.0.0.254` | Addresses available for interfaces in this subnet |
| Broadcast address | `10.0.0.255` | Broadcast destination for this subnet |
| Default gateway | `10.0.0.1` | Router used when no more specific route matches |

With **this particular mask**, the first three numbers identify the network (`10.0.0`) and the last number identifies the host (`213`).

There are 256 addresses in this `/24` subnet. Conventionally, the network and broadcast addresses are reserved, leaving 254 usable host addresses. Some addresses may already be assigned or reserved in DHCP.

Do not apply the “first three numbers” rule to every subnet mask. For example, `/16` and `/25` create different subnet boundaries.

## 3. Local and remote destinations

From your desktop at `10.0.0.213/24`:

| Destination | Same local subnet? | Expected path |
| --- | --- | --- |
| `10.0.0.8` — Surface laptop | Yes | Direct delivery over the local network |
| `10.0.0.140` — Dell laptop | Yes | Direct delivery over the local network |
| `10.0.0.11` — desktop ESXi 8 lab | Yes | Local delivery, provided the virtual networking connects it to this LAN |
| `192.168.109.101` — Dell ESXi 9 lab | No | A suitable route is required; the home router may not know this virtual subnet |
| `8.8.8.8` — public DNS server | No | Normally sent to the default gateway |

“Direct delivery” can still involve an Ethernet switch or Wi-Fi access point. It means a router is not normally needed between devices on that local subnet.

A remote destination does not automatically become reachable just because a default gateway exists. That gateway and the destination network need suitable routes, including a return path. A VMware host-only network is not automatically reachable from another physical computer.

## 4. How the computer decides

For the simple case of one interface and its connected subnet:

1. The computer combines its own IP address with its subnet mask to obtain the network address.
2. It combines the destination IP address with the same mask.
3. Matching network addresses indicate that the destination is on that connected subnet.
4. For local IPv4 delivery, the computer normally uses ARP to discover the destination's MAC address.
5. For a remote destination, it selects a route and normally uses the next-hop router's MAC address instead.

The calculation uses a bitwise **AND** operation:

```text
Desktop:     10.0.0.213 AND 255.255.255.0 = 10.0.0.0
Surface:     10.0.0.8   AND 255.255.255.0 = 10.0.0.0
Dell ESXi:   192.168.109.101 AND 255.255.255.0 = 192.168.109.0
```

The desktop and Surface results match. The Dell ESXi result does not.

In actual operation, Windows consults its routing table and selects the most specific matching route. VPNs, multiple interfaces and additional routes can therefore change the path.

## 5. Subnet mask versus gateway and DNS

| Component | Main job | Example |
| --- | --- | --- |
| IP address | Identifies an interface on an IP network | `10.0.0.213` |
| Subnet mask | Identifies the network and host portions | `255.255.255.0` |
| Default gateway | Provides a next hop when no more specific route matches | `10.0.0.1` |
| DNS server | Answers DNS queries, such as a hostname's IP address | Your configured DNS resolver |

DNS helps find an address. Routing determines how traffic reaches that address. A successful DNS lookup does not guarantee connectivity.

Two local computers can communicate by IP without DNS. Communication on their connected subnet normally does not require a default gateway.

## 6. Windows commands

Run these read-only commands in PowerShell.

### Display addresses, masks and gateways

```powershell
ipconfig
```

Look under the adapter you are actually using: Ethernet or Wi-Fi. VMware, WSL and VPN adapters may have separate subnets.

### Display interface configuration

```powershell
Get-NetIPConfiguration
```

### Display IPv4 addresses and prefix lengths

```powershell
Get-NetIPAddress -AddressFamily IPv4 |
    Select-Object InterfaceAlias, IPAddress, PrefixLength
```

A prefix length of `24` corresponds to `255.255.255.0`.

### Inspect the IPv4 routing table

```powershell
route print -4
```

The default route appears as `0.0.0.0` with mask `0.0.0.0`. More specific matching routes take priority over it.

### Test a service on another device

```powershell
Test-NetConnection 10.0.0.11 -Port 22
```

`TcpTestSucceeded : True` means a TCP connection to port 22 succeeded. It does not prove that your SSH username, password or key will authenticate successfully.

### Test ICMP reachability

```powershell
ping 10.0.0.11
```

A ping timeout does not prove the device is offline. A firewall may block ICMP while allowing SSH or HTTPS. This happened during your earlier SCP preparation: TCP port 22 worked even though ping timed out.

## 7. Important points and revision

- Being in the same subnet does not guarantee connectivity. Firewalls, Wi-Fi isolation, VLAN separation and virtual-network settings can block traffic.
- Two devices with similar-looking IP addresses are not necessarily in the same subnet. Check their masks.
- Devices should have consistent subnet settings for their intended network; mismatched masks can produce confusing or one-way connectivity.
- Do not change a mask simply to make an unreachable address look local. The underlying network and routing must support the connection.
- A subnet mask does not assign IP addresses, translate names or open firewall ports.

### Quick questions

**What does a subnet mask tell the computer?**

Which bits identify the network and which identify the host, helping determine whether a destination is on a connected subnet.

**Are `10.0.0.213/24` and `10.0.0.8/24` in the same subnet?**

Yes. Both belong to `10.0.0.0/24`.

**Is `192.168.109.101` local to `10.0.0.213/24`?**

No. Reaching it requires a suitable route and compatible network configuration.

**Does `/24` mean 24 devices?**

No. It means 24 network bits. An IPv4 `/24` leaves 8 host bits: 256 total addresses, usually 254 usable host addresses.

### One-sentence definition

> **Subnet mask:** Identifies the network and host portions of an IPv4 address, helping the computer determine whether a destination is on the local subnet or requires routing.
