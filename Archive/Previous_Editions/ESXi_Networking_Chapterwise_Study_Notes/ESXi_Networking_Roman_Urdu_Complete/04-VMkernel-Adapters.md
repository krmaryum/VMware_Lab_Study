# Chapter 04 — VMkernel Adapters (Roman Urdu)

> **Aapke lab ka confirmed baseline:** `desktop-esxi9`, `vSwitch0`, `vmnic0` (screen par `10000 Full`), `Management Network` par `vmk0 = 10.0.0.10`, `VM Network` par 0 VMs aur VLAN ke saamne `--` nazar aa raha hai. Yeh `desktop-esxi8 = 10.0.0.11` se alag host hai. Jo settings abhi inspect nahin ki gayin, unhein confirmed na samjhein.

> **Hifazati ehtiyat:** Read-only inspection theek hai. Management uplink, VLAN, port group ya `vmk0` IP change karne se remote access band ho sakta hai. Changes se pehle local/VM console aur rollback plan zaroor hona chahiye.

## Is chapter mein kya seekhenge?
`vmk` interfaces, management traffic, IP addressing aur default gateway ka role samjhenge.

## VMkernel Adapter kya hai?
**VMkernel network adapter** ESXi host ka khud ka IP-enabled interface hota hai. Is par management, vMotion, kuch storage-network services aur doosri configured VMkernel services chal sakti hain. Yeh **guest VM ke andar ka vNIC nahin** hota.

**Confirmed:** `desktop-esxi9` par `vmk0` `Management Network` port group ke andar hai aur `10.0.0.10` show ho raha hai.

**Abhi confirm nahin:** Subnet mask/prefix, gateway, aur koi doosre vmk adapters, aur enabled services.

```text
Windows Browser
    --> LAN / Workstation Networking
    --> vmnic0 uplink --> vSwitch0
    --> Management Network --> vmk0 (10.0.0.10)
    --> ESXi Host Client
```

## VMkernel aur VM NIC ka farq
- **VMkernel interface (`vmk0`):** ESXi host ko khud network services dene ke liye.
- **Guest `vNIC`:** Linux/Windows guest OS ko virtual network interface dene ke liye.
- **`vSwitch0`:** Layer 2 switching, apna management IP nahin.

## GUI lab — Read-only
1. **Networking → VMkernel adapters** par click karein.
2. `vmk0` select karke IP, prefix/subnet, TCP/IP stack aur enabled services note karein.
3. Jahan display hon wahan host ka default gateway aur DNS server information bhi record karein.
4. Screenshot save karein. Public repo mein sensitive infrastructure details ko zarurat par redact karein.

## Optional ESXi Shell commands
```sh
esxcli network ip interface list
esxcli network ip interface ipv4 get
esxcli network ip route ipv4 list
esxcli network ip dns server list
```

### Command aur output ka matlab
| Command | Kyun chalate hain? | Output ka matlab |
|---|---|---|
| `... interface list` | ESXi host interfaces check karne ko | `vmk0`, status aur details |
| `... interface ipv4 get` | IPv4 addresses/masks dekhne ko | Har vmk ki IP configuration |
| `... route ipv4 list` | Routing table aur default route dekhne ko | Destination, gateway, next-hop information |
| `... dns server list` | Configured DNS servers check karne ko | ESXi kis DNS server ko use karta hai |

**Default route `0.0.0.0/0` ka matlab:** Jo destination direct local subnet mein nahin, us ka traffic aam tor par default gateway ko forward hota hai. Yeh general example hai; aapke ESXi9 ki actual routing-table output abhi available nahin.

## Ehtiyat
Bina console access ke management IP, gateway ya VLAN change mat karein. Galat IP ya network change se remote Host Client disconnect ho sakta hai.

## Practice / Interview sawalat
1. VMkernel adapter aur guest VM ka `vNIC` kaise alag hain?
2. `vmk0` kin services ke liye use ho sakta hai?
3. Doosre subnet se ESXi manage karte waqt gateway kyun important hai?
