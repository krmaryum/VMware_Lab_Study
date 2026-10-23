# 08 - Revision Questions and Answers

> **Addressing correction:** Existing STORAGE was recorded as 172.16.0.102/16. The proposed 172.16.50.0/24 falls inside that /16, so it overlaps the existing connected network. Treat all 172.16.50.x values below as an unexecuted source example. Before implementation choose a verified unused, non-overlapping subnet and consistently replace every endpoint/interface value. Do not change the existing management or storage addresses merely to follow this example.


1. **Where is the ESXi host IP assigned?** On a VMkernel adapter. Roman Urdu: Host IP vmk par hota hai.
2. **Does a vSwitch need an IP?** No. It forwards Layer 2 frames. Roman Urdu: Switch ko forwarding ke liye IP assign nahi karte.
3. **What is a port group?** A network attachment/policy grouping, including VLAN and policy inheritance. Roman Urdu: Network connection aur policies ka group.
4. **VM port group vs VMkernel group?** Guest vNIC attachment versus ESXi host interface attachment. Roman Urdu: Guest ke liye VM group, host ke liye vmk group.
5. **What is an uplink?** A vmnic assigned to a switch for upstream connectivity. Roman Urdu: Bahar jane ka network path.
6. **Can two standard switches share the same vmnic?** Not simultaneously. Roman Urdu: Ek vmnic aik waqt mein aik standard switch par.
7. **Does the port group assign a guest IP?** No; configure it in the guest or use reachable DHCP. Roman Urdu: Guest IP OS ya DHCP deta hai.
8. **Are two differently named groups isolated?** Names alone do not isolate traffic. Roman Urdu: Sirf naam alag hone se network isolate nahi hota.
9. **What does VLAN 0 mean here?** Untagged traffic. Roman Urdu: Is lab mein VLAN tag add nahi karte.
10. **What does MTU 1500 describe?** For Ethernet IP traffic, the Layer 3 packet size limit carried as payload, not the complete on-wire frame including all Layer 2 overhead. Roman Urdu: Poore frame ka total size 1500 samajhna sahi nahi.
11. **Is vMotion a VMkernel adapter?** It is a host function that uses VMkernel networking. Roman Urdu: vMotion aik kaam hai jo vmk use karta hai.
12. **Active vs standby?** Active can carry normal traffic; standby takes over when eligible active paths fail. Roman Urdu: Standby backup path hai.
13. **Unused?** Excluded by that policy, including failover. Roman Urdu: Is policy mein eligible nahi.
14. **Does active/active double one download?** No automatic guarantee. Roman Urdu: Single transfer ki speed double hona lazmi nahi.
15. **Why use an external failover peer?** Host-local traffic can bypass uplinks. Roman Urdu: Bahar wala peer uplink path test karta hai.
16. **Bandwidth vs throughput?** Capacity versus achieved rate. Roman Urdu: Maximum gunjaish aur asal transfer rate.
17. **Average vs peak?** Sustained rate target versus burst ceiling. Roman Urdu: Routine limit aur temporary upper limit.
18. **Burst size?** Data quantity budget, not a bandwidth rate. Roman Urdu: Extra data ka budget.
19. **Can ping measure bandwidth?** No; use a throughput tool such as iperf3. Roman Urdu: Ping reachability dikhata hai.
20. **Does No networks mean vmk has no IP?** No; inspect the VMkernel IPv4 settings. Roman Urdu: Observed range aur configured IP alag cheezen hain.
21. **Can a standard switch run LACP?** No; do not enable an upstream LACP bundle for this lab. Roman Urdu: Is standard-switch exercise mein LACP nahi.
22. **What remains unchanged in this exercise?** Your existing management path and previously configured switch/VMkernel objects. Roman Urdu: Naye objects par practice karni hai.

## Final assignment

Rebuild the separate lab network from these notes, record actual adapter numbers, prove external failover, and compare measured shaping results. Explain each object aloud without reading its definition. Mark incomplete performance tasks as pending rather than inventing results.
