# Connect networking foundations to the VMware lab

## Keep the layers distinct

| Item | Where it belongs | Purpose |
|---|---|---|
| Windows physical NIC | Physical Workstation host | Connects the host to its real network |
| VMware VMnet | Workstation | Virtual network with a selected networking mode |
| VM virtual NIC | Guest VM hardware | Connects a guest to a selected virtual network or port group |
| ESXi vmnic | ESXi host's view of a NIC | Uplink available to an ESXi switch; in nested ESXi its backing device is a Workstation virtual NIC |
| ESXi vSwitch | ESXi | Switches traffic among connected ports and configured uplinks |
| ESXi port group | ESXi | Defines attachment/policy settings such as VLAN and security for its ports |
| ESXi VMkernel adapter, vmk | ESXi | IP interface for selected ESXi host services such as management or vMotion |

**VMnet2, vmnic2 and vmk2 are not the same object.** Their numbers do not create a relationship automatically.

## Workstation connection modes

| Mode | Typical connection | Important qualification |
|---|---|---|
| Bridged | Guest connects through the host's selected physical network | LAN policy, Wi-Fi behavior and guest addressing must support it |
| NAT | Guest uses a private virtual network; outbound access is translated | Unsolicited inbound access from the physical LAN normally needs additional configuration |
| Host-only | Isolated host/guest virtual network | Other physical machines do not automatically have routes into it |

Custom VMnet numbers do not universally determine the mode. Check the actual Virtual Network Editor configuration. NAT and host-only networks can have DHCP depending on settings.

## Follow a nested guest connection

A VM running inside nested ESXi can use an ESXi VM port group, its vSwitch, an uplink presented as vmnic, a Workstation virtual NIC and a selected VMnet to reach an external destination. Actual reachability depends on every configured link, VLAN, IP subnet, route and applicable policy. Two guests on the same ESXi switch may communicate without traversing a physical uplink.

A VMkernel IP belongs to the ESXi host service interface. It is not automatically the gateway for guest VMs, and a standard vSwitch does not become an IP router merely because a VMkernel NIC exists.

## Connect to your earlier evidence

Earlier lab records showed desktop Windows `10.0.0.213`, desktop ESXi9 management `10.0.0.10`, and a DNS practice subnet `192.168.0.0/24`. Treat these as recorded examples and verify current addresses before testing. A subnet alone does not establish which VMnet is attached; inspect the configuration.

Your earlier SSH transfer also demonstrated that TCP/22 could work while ping timed out. That supports testing the required application port rather than equating ICMP failure with total network failure.

For redundant uplinks, check the teaming policy, upstream connectivity and failure behavior. Two active vmnics backed by the same physical host NIC do not create independent physical paths.

## Before the next practical

Record the Windows host interface, selected VMnet/mode, ESXi guest adapter, ESXi vmnic/vSwitch, target port group, VMkernel or guest IP, subnet and expected route. Keep management connectivity available before planning changes. This chapter performs no changes.
