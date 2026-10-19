# Chapter 02 — Port Groups (Roman Urdu)

> **Aapke lab ka confirmed baseline:** `desktop-esxi9`, `vSwitch0`, `vmnic0` (screen par `10000 Full`), `Management Network` par `vmk0 = 10.0.0.10`, `VM Network` par 0 VMs aur VLAN ke saamne `--` nazar aa raha hai. Yeh `desktop-esxi8 = 10.0.0.11` se alag host hai. Jo settings abhi inspect nahin ki gayin, unhein confirmed na samjhein.

> **Hifazati ehtiyat:** Read-only inspection theek hai. Management uplink, VLAN, port group ya `vmk0` IP change karne se remote access band ho sakta hai. Changes se pehle local/VM console aur rollback plan zaroor hona chahiye.

## Is chapter mein kya seekhenge?
VM Port Group, VMkernel Port Group, VLAN IDs aur virtual machine ka apni logical network se attach hona samjhenge.

## Port Group kya hota hai?
**Port Group** virtual switch ke ports ke liye common settings aur policies define karta hai. Guest VM apni **virtual-machine port group** se connect hoti hai. ESXi ki management aur kuch services **VMkernel interface** aur uske port group ke through connect hoti hain.

| Aapke screen ka naam | Kaam | Screenshot se evidence |
|---|---|---|
| `Management Network` | ESXi host ke management/service traffic ke liye | `vmk0`, IP `10.0.0.10` |
| `VM Network` | Virtual machine ki NIC attach karne ke liye | `Virtual Machines (0)` |

### Management aur VM networking mein farq
`Management Network` ko aise samjhein jaise ESXi administrator ke liye raasta. Aap jab browser par Host Client open karte hain to management IP use hota hai. `VM Network` guest VMs ko LAN par traffic bhejne ya doosri VMs se communicate karne ka logical connection deti hai. Dono ek hi `vSwitch0` share kar sakte hain; lekin policies alag ho sakti hain.

## VLAN ID kya hota hai?
VLAN se ek physical/virtual switching network ko logical Layer-2 segments mein divide karte hain. ESXi standard port group mein aam tor par **VLAN 0** untagged traffic ko represent karta hai; **1–4094** VLAN tagging ke use-cases ke liye hain (kuch values platform par special/reserved ho sakti hain); **4095** special guest trunking mode hai, har lab ke liye nahin.

**Aapke screenshot mein `VLAN ID: --` likha hai.** Is ka matlab yeh *nahin* ke hum 100% yaqeen se VLAN 0 declare kar dein. Actual VLAN field ko **Networking → Port Groups** par check karna hoga.

### Misal (aapke host par apply nahin ki gayi)
```text
vSwitch0
|-- Management Network --> vmk0 (10.0.0.10)
`-- VM Network         --> VM-A ka vNIC, VM-B ka vNIC
```

## GUI practical lab — Read-only
1. ESXi Host Client mein **Networking → Port groups** open karein.
2. Har group ka **Name**, **Virtual Switch**, **VLAN ID** aur related VM/VMkernel association record karein.
3. Policy details dekhein, **Edit** ya **Remove** na karein.
4. Future VM banate waqt uski **Network Adapter** settings mein selected Port Group note karein.
5. Screenshot ka naam `02-port-groups.png` rakh sakte hain.

## Screen ke fields ka matlab
- **Name:** Admin ka diya hua label; isay DNS hostname na samjhein.
- **Virtual Switch:** Port group kis vSwitch se linked hai.
- **VLAN ID:** Traffic ki tag/untag configuration; upstream physical/Workstation network ko bhi compatible hona chahiye.
- **Virtual Machines (0):** Filhaal koi VM attached nahin; yeh failure ka signal nahin.

## Ahm ehtiyat
Agar management VLAN galat kar di, to browser se ESXi disconnect ho sakta hai. Pehle console access verify karein; is chapter mein sirf observation hai.

## Practice / Interview sawalat
1. `Management Network` aur `VM Network` ka practical farq batayein.
2. Management VLAN galat change karne ka kya nuksan hai?
3. `VLAN ID: --` dekh kar direct VLAN 0 assume kyun nahin karna chahiye?
