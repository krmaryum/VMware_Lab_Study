# Chapter 06 — Troubleshooting aur Practical Lab (Roman Urdu)

> **Aapke lab ka confirmed baseline:** `desktop-esxi9`, `vSwitch0`, `vmnic0` (screen par `10000 Full`), `Management Network` par `vmk0 = 10.0.0.10`, `VM Network` par 0 VMs aur VLAN ke saamne `--` nazar aa raha hai. Yeh `desktop-esxi8 = 10.0.0.11` se alag host hai. Jo settings abhi inspect nahin ki gayin, unhein confirmed na samjhein.

> **Hifazati ehtiyat:** Read-only inspection theek hai. Management uplink, VLAN, port group ya `vmk0` IP change karne se remote access band ho sakta hai. Changes se pehle local/VM console aur rollback plan zaroor hona chahiye.

## Is chapter mein kya seekhenge?
Aisa repeatable read-only troubleshooting workflow jisme hostname, DNS, ICMP, TCP, browser aur ESXi service ke masail ko alag-alag identify kar sakein.

## Checkpoint 0 — Management access ko bachana
Change se pehle `vSwitch0`, uplink, VMkernel IP aur Port Group ki screenshots collect karein. Local console ya VMware VM console ka access verify karein. Shuru mein management VLAN, gateway ya uplink modify na karein.

## Windows par read-only test sequence
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

## Har command: Kyun, aur output ka kya matlab?
| Command | Kyun chalate hain? | Output kaise samjhein? |
|---|---|---|
| `hostname` | Test chalane wale Windows PC ko pehchanne ke liye | Windows computer ka naam; ESXi ka nahin |
| `ipconfig /all` | Client IP, gateway, DNS, adapter details dekhne ke liye | Kya PC expected LAN par hai? |
| `Get-Content ...hosts` | Local manual host mappings check karne ke liye | `10.0.0.10 desktop-esxi9` jaisi mapping ho sakti hai |
| `Resolve-DnsName` | Windows name resolution check karne ke liye | Hosts-file resolution bhi reflect ho sakti hai |
| `Resolve-DnsName -DnsOnly` | DNS query path separately test karne ke liye | Agar yeh fail ho aur normal resolution chale to local mapping ek possibility hai |
| `ping` | ICMP response dekhne ke liye | Reply = reachability; timeout alone final proof nahin |
| `Test-NetConnection -Port 443` | HTTPS port par TCP handshake test karne ke liye | `TcpTestSucceeded: True` = TCP connected, HTML page ki guarantee nahin |
| `curl.exe -I` | HTTP response headers aur redirect dekhne ke liye | `301` aur `Location:` redirect show karte hain |

## Agar ESXi UI open nahin hoti — Decision Tree
```text
ESXi UI open nahin ho rahi
|
+-- IP ping fail? --> IP, route, firewall, LAN check; ICMP block ho sakta hai
|
+-- TCP 443 fail? --> ESXi management IP, service, uplink, VLAN, firewall check
|
+-- TCP 443 theek, browser fail? --> https://IP/ui, proxy, TLS, certificate check
|
+-- IP theek, hostname fail? --> hosts file, DNS aur browser resolution compare
```

## Hamare pehle ESXi8 lab ka real example — Yeh alag host hai
`desktop-esxi8` ka management IP `10.0.0.11` tha. Us troubleshooting se yeh cheezein **confirm** huin:

1. Windows hosts file mein `10.0.0.11 desktop-esxi8` aur `10.0.0.10 desktop-esxi9` mappings thi.
2. `nslookup desktop-esxi8` se DNS record nahin mila, lekin normal Windows resolution `10.0.0.11` de rahi thi.
3. `ping` successful tha aur TCP ports **80** aur **443** connect ho rahe the.
4. Dono `https://10.0.0.11/ui` aur `https://desktop-esxi8/ui` par ESXi login page open hui.
5. `curl.exe -I http://desktop-esxi8/` ne `HTTP/1.1 301 Moved Permanently` aur `Location: https://desktop-esxi8:443/` return kiya.
6. **Original browser timeout ki exact wajah confirm nahin hui.** Isay DNS ki guaranteed failure kehna technically ghalat hoga.

Yeh data **ESXi8** ka hai. ESXi9 par wahi success assume na karein jab tak commands actually run na kar lein.

## Lab evidence log ka format
| Date | Host | Screenshot | Test | Observed output | Nateeja |
|---|---|---|---|---|---|
| 2026-10-18/19 | `desktop-esxi9` | `screenshots/01-original-vswitch0.png` | Virtual switches GUI | vSwitch0, vmk0 10.0.0.10, vmnic0 10000 Full | Topology dekhi; settings change nahin hui |

## Practice / Interview sawalat
1. `nslookup` fail ho aur `ping` hostname resolve kare, aisa kyun ho sakta hai?
2. `TcpTestSucceeded: True` asal mein kya prove karta hai?
3. HTTP 301 ka matlab kya hai?
4. Browser timeout ke multiple possible causes kyun hote hain?


---

## Stage 1 evidence-based checklist

| Test | Command / screen | Purpose | Stage status |
|---|---|---|---|
| Standard switches | `esxcli network vswitch standard list` | Names/uplinks/MTU | GUI confirms two; CLI output pending |
| Port groups | `esxcli network vswitch standard portgroup list` | Switch mapping | GUI confirms SERVERS on vSwitch0 |
| Physical adapters | `esxcli network nic list` | Link/driver | vmnic1 GUI shows Connected 10Gb Full; CLI pending |
| VMkernel IP | `esxcli network ip interface ipv4 get` | vmk IP/mask | GUI confirms vmk0 10.0.0.10, vmk1 172.16.0.102/16 |
| Management reachability | `ping 10.0.0.10` (Windows) | ICMP response | This stage did not capture a fresh ping result |
| Storage reachability | `vmkping -I vmk1 <storage-server-ip>` | vmk1 to storage | Pending actual target address |
| NIC failover | Continuous ping with intentional link down | Observe packet loss/recovery | Not performed; administrative removal only |
| VLAN isolation | Connect two test VMs / capture traffic | Verify separation | Not performed |

### Command output interpret karne ka formula

1. Command **kis source machine** par chala? (Windows PowerShell ya ESXi Shell)
2. **Kis network layer** ko test kiya? (DNS, ICMP, TCP, VMkernel IP, vSwitch)
3. Positive result **exactly kya prove** karta hai? TCP reachability application login ko prove nahi karti; link up storage data access ko prove nahi karta.
4. Negative result ke **alternate reasons** kya ho sakte hain? Host firewall ICMP block, mis-routed Workstation network, wrong mask/VLAN, no destination service.
5. Screenshot / command output with date aur host name save karein.

### Interview scenario

**Q: ESXi mein do vmnics Active karne se NIC Teaming test ho gaya?**

**A:** Nahi. Team membership aur effective traffic distribution/failover do alag claims hain. Teaming policy, upstream compatibility, failure detection aur test packet continuity verify karna zaroori hai.

**Q: STORAGE VMkernel ke MTU 1500 rakhne ka reason?**

**A:** Standard default jo broad compatibility deta hai. Jumbo frames sirf poori end-to-end network path support aur matching configuration ke baad.

