# Network Topology — Brief Study Notes

## 1. Definition

Network topology is the physical and logical arrangement of devices and communication paths in a network.

**Exam-ready definition:** Network topology describes how network devices are physically connected and how data logically flows between them.

## 2. Physical Topology

**What:** The actual arrangement of devices, cables, switches and links, including wireless links.

**Example:** Five computers connected by Ethernet cables to a central switch form a physical star topology.

Physical topology is not limited to cables. Wireless devices and their links also form part of the physical network arrangement.

## 3. Logical Topology

**What:** How data travels or communication is organized across a network, regardless of the physical cabling layout.

**Example:** Computers attached to the same physical switch can belong to VLAN 10 and VLAN 20. These VLANs form separate logical Layer 2 networks. Communication between them requires suitable routing and permissions.

A network can therefore have a physical star layout while its logical communication behavior depends on the technology and configuration.

## 4. Common Topologies

- **Bus:** Devices share a common backbone link.
- **Star:** Devices connect to a central switch or hub.
- **Ring:** Devices form a closed loop.
- **Mesh:** Devices have multiple interconnections that can provide redundancy. Actual resilience depends on available paths and configuration.
- **Tree:** A hierarchical arrangement of connected stars or branches.
- **Hybrid:** A combination of topology types.

## 5. Physical versus Logical — Quick Comparison

| Physical | Logical |
| --- | --- |
| Actual device, cable and wireless-link layout | Data flow and communication paths |
| Answers: How are devices connected? | Answers: How does information move? |
| Example: Star cabling to one switch | Example: VLAN separation and forwarding paths |

## 6. Memory Aid

**Physical = the layout of devices and links.**

**Logical = data flow and the communication arrangement.**
