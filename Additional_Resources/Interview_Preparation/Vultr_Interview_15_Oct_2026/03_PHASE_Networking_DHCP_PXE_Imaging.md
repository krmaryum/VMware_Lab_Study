# Phase 03 — VLANs, DHCP, PXE / iPXE, OS Imaging
**Priority: Critical | Study: Monday**

## 1. Key networking basics
- **MAC:** Layer-2 hardware interface identifier.
- **VLAN:** Logical isolation/segmentation at Layer 2; switch-port VLAN selection must match provisioning design.
- **DHCP:** Supplies IP configuration and, in certain PXE architectures, boot-server/boot-file information.
- **DNS:** Resolves names to addresses; normally not the first prerequisite for initial PXE discovery.
- **Default gateway:** Router for traffic outside the local subnet.
- **IPv4 vs IPv6:** Address families with distinct boot/network provisioning options.

## 2. Simplified network-boot flow
```text
Power on → UEFI/NIC PXE boot
      ↓
Link up on correct switch / VLAN
      ↓
DHCP exchange (address + boot information, where applicable)
      ↓
Fetch iPXE/UEFI boot program (TFTP/HTTP depending on setup)
      ↓
Boot kernel/initrd / installer environment
      ↓
Download/install approved OS image or installer config
      ↓
Install bootloader → reboot from local disk → first-boot config
```
**Nuance:** Exact services vary; modern iPXE often uses HTTP, and DHCP options/proxy DHCP/UEFI paths depend on the environment.

## 3. How to troubleshoot PXE step by step
| Symptom | First boundary to check | Next evidence |
|---|---|---|
| “No link” | Cable/NIC/switch interface | Port state, speed, asset mapping |
| No DHCP lease | VLAN/DHCP relay/server/scope | MAC, lease logs, captures from authorized point |
| Lease but missing boot path | DHCP/proxyDHCP/UEFI option selection | Boot server, file path, UEFI vs legacy mode |
| Boot file times out | TFTP/HTTP reachability or filename | Server logs, firewall, path, routing |
| Kernel boots, imaging stops | Image server, disk/partition/driver | Installer logs, hardware compatibility |
| OS installed but reboots to PXE | Boot order/bootloader/drive detection | Firmware settings, installed ESP/boot drive |

### Commands for an already booted Linux host
```bash
ip -br link
ip -br addr
ip route
cat /etc/resolv.conf
sudo ethtool <interface>
sudo journalctl -b --no-pager | tail -100
lsblk -o NAME,SIZE,MODEL,SERIAL,TYPE,MOUNTPOINTS
```
Commands on a failed pre-OS system instead rely on BMC console, switch/DHCP/PXE server logs, and authorized diagnostics.

## 4. Interview answer: PXE boot fails
> I identify the exact stage that failed rather than retry blindly. First I verify power, link, NIC and the correct VLAN. Then I look for a DHCP lease for the server's MAC. If it gets an address, I verify UEFI boot-file selection and reachability of the boot server. If the installer starts, I inspect imaging logs and disk detection. I collect serial/MAC, timestamps and error text before escalating to the owning team.

## 5. iPXE vs PXE
Traditional PXE is firmware network boot. **iPXE** expands network boot workflows with scripting and protocols such as HTTP. Vultr's public bare-metal provisioning documentation lists iPXE as a boot option, but internal staff workflow may differ.

## 6. Practice questions
1. A new server never receives an IP address. What are your checks?
2. A server gets an IP but returns “boot file not found.” Why?
3. What causes a PXE boot loop after successful OS install?
4. Explain VLAN, switch port and DHCP relay simply.
5. What differs between management BMC networking and host PXE networking?

**Roman Urdu:** PXE boot mein server network se OS installer uthata hai. Pehle link aur VLAN check karein; phir DHCP address; phir boot-file/server; us ke baad installer, disk aur bootloader. Problem kis stage par hai, pehle woh identify karein.
