# Chapter 06 — Troubleshooting and Practical Lab

> **Lab baseline (confirmed from screenshot):** `desktop-esxi9`, `vSwitch0`, `vmnic0` (reported 10000 Full), Management Network `vmk0` / `10.0.0.10`, VM Network with zero VMs, VLAN shown as `--`. This is a different host from `desktop-esxi8` / `10.0.0.11`. Do not assume that uninspected settings are configured as described in the examples.

> **Safety:** Viewing pages and using read-only commands is safe. Changing management uplinks, port groups, VLANs, or vmk0 IP may cut off remote access. Have ESXi console access and a rollback plan before changes.

## Learning objectives
Follow a repeatable read-only diagnostic workflow and distinguish hostname, ICMP, TCP, browser and ESXi problems.

## Checkpoint 0: preserve access
Before network changes, capture switch/port group/vmk/uplink configuration; have working local/VM console access. Do not modify management VLAN or uplink during initial troubleshooting.

## Windows read-only test sequence (replace names/IP only if confirmed)
```powershell
hostname
ipconfig /all
Get-Content C:\Windows\System32\drivers\etc\hosts
Resolve-DnsName desktop-esxi9
Resolve-DnsName desktop-esxi9 -Type A -DnsOnly
ping 10.0.0.10
Test-NetConnection 10.0.0.10 -Port 443
curl.exe -I --max-time 10 http://10.0.0.10/
```

| Command | Why we run it | How to interpret |
|---|---|---|
| `hostname` | Identify test machine | It identifies Windows, not ESXi |
| `ipconfig /all` | Inspect client IP, gateway, DNS | Confirm expected client network |
| `Get-Content ...hosts` | Check manual name mappings | Lines such as `10.0.0.10 desktop-esxi9` override reliance on DNS for many Windows clients |
| `Resolve-DnsName` | Inspect name resolution | May reflect hosts-file resolution |
| `Resolve-DnsName -DnsOnly` | Query DNS path | Failure with normal resolution may indicate a hosts-file mapping |
| `ping` | ICMP reachability | Reply shows basic reachability; timeout alone isn't definitive |
| `Test-NetConnection -Port 443` | TCP handshake to HTTPS | `TcpTestSucceeded : True` does not itself confirm successful HTML loading |
| `curl.exe -I` | HTTP headers/redirect behavior | HTTP 301 with `Location:` indicates redirect; not proof of browser success |

## Diagnostic decision tree
```text
Cannot open ESXi UI
|
+-- IP ping fails? --> Check client/ESXi network, firewall, route; ICMP may be blocked
|
+-- TCP 443 fails? --> Check ESXi management IP, service, firewall, uplinks and VLAN
|
+-- TCP 443 works but browser fails? --> Test https://IP/ui, certificates, proxy/browser, TLS response
|
+-- IP works but hostname fails? --> Compare Windows hosts, DNS, browser hostname/proxy
```

## Example from our previous ESXi8 troubleshooting — separate host
On **desktop-esxi8 / 10.0.0.11**, an earlier experiment showed:
- Windows hosts file: `10.0.0.11 desktop-esxi8` and `10.0.0.10 desktop-esxi9`.
- `nslookup desktop-esxi8` found no DNS record, but Windows normal resolution returned `10.0.0.11`.
- Ping worked; port 80 and 443 TCP checks succeeded.
- Both `https://10.0.0.11/ui` and `https://desktop-esxi8/ui` showed the ESXi login page.
- `curl.exe -I http://desktop-esxi8/` returned HTTP **301** and `Location: https://desktop-esxi8:443/`.
- The **original browser timeout's exact cause remains unconfirmed**; do not claim DNS failure caused it.

These are previous **ESXi8** observations, not measurements of **ESXi9**. See earlier troubleshooting bundle if you want those screenshots.

## Reusable lab evidence log
| Date | Host | Screenshot | Test | Observed output | Conclusion |
|---|---|---|---|---|---|
| 2026-10-18/19 | desktop-esxi9 | `screenshots/01-original-vswitch0.png` | GUI Virtual switches | vSwitch0, vmk0 10.0.0.10, vmnic0 10000 Full | Topology seen; settings not yet altered |

## Roman Urdu
Hamesha pehle read-only checks se shuru karein: IP, naam resolve ho raha hai ya nahi, ping, TCP 443, phir browser. `TcpTestSucceeded True` ka matlab TCP handshake successful hai; yeh guarantee nahi ke login page bhi load hoga. Management network ki settings backup/console ke baghair change na karein.

## Questions
1. Why can `nslookup` fail while a name still resolves in Windows?
2. What does `TcpTestSucceeded: True` actually prove?
3. What does HTTP 301 indicate?
4. Why can a browser timeout have more than one cause?


---
## Stage 1 completion record — What evidence proves
| Item | Observed evidence | Status |
|---|---|---|
| ESXi9 management address | vSwitch0 topology `vmk0: 10.0.0.10` | Confirmed |
| Second uplink active | `vmnic1` listed Active and later displayed alongside `vmnic0` | Confirmed, temporarily |
| Automatic failover | No controlled link-disconnect/ping-loss test captured | Not verified |
| Dedicated storage network | vSwitch1, vmnic1, STORAGE, vmk1 `172.16.0.102/16` | Configured |
| MTU | Storage port properties screenshot shows 1500 | Configured |
| NFS/iSCSI target reachability | No storage endpoint ping, vmkping or datastore evidence | Not verified |
| Server port group | SERVERS on vSwitch0, VLAN None (0), zero VMs | Configured |
| VLAN isolation | No isolated VLAN or tagged packet test | Not verified |
| Workstation adapter modes | VM settings screenshots not supplied | Not verified |

### How to record output accurately
For every command, capture **the command, the full original output, what each relevant field means, the interpretation, and the next step**. Do not use an expected output as if it were observed.

### Suggested next-stage tasks
1. Read-only inventory: VMware Workstation adapter modes, ESXi switches, NICs, port groups, and VMkernel addresses.
2. Storage endpoint setup/verification, then a `vmkping` using the correct stack/interface and a known storage destination.
3. VLAN design and tests only after confirming the virtual and physical network path.
4. Controlled failover test with console recovery and baseline/recovery screenshots.
