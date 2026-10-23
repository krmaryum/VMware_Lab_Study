# NIC Teaming aur Failover

> **Addressing ki correction:** Recorded STORAGE IP 172.16.0.102/16 hai. Proposed 172.16.50.0/24 usi /16 ke andar hai, is liye overlap hai. Neechay 172.16.50.x ko unexecuted source example samjhein. Implementation se pehle verified unused aur non-overlapping subnet choose karke tamam endpoints/interfaces ki values consistently replace hoti hain. Example match karne ke liye existing management/storage IP nahin badalni.


> **Status: proposed repeat exercise.** Recorded lab mein doosra uplink temporarily Active hua tha. Neechay wale isolated network par successful failover ki evidence abhi nahin. Pehle `../09_Optional_Isolated_Practice_Source/00_Preparation_and_Network_Plan.md` ki preparation dekhein. Yeh existing STORAGE/SERVERS configuration se alag exercise hai.

## Concept aur purpose

NIC teaming aik switch ko multiple eligible uplinks deti hai. Failover policy tay karti hai normal traffic kis path par ja sakta hai aur failure par kaunsa doosra path eligible hai. Do uplinks add karne se aik transfer ki speed automatically double nahin hoti.

Originating virtual port ID policy mein aam tor par aik virtual port aik selected uplink use karta hai. Multiple virtual ports alag uplinks par distribute ho sakte hain. Active/standby se resilience samajhna aasaan hai. Active/active mein multiple eligible links hotay hain, lekin traffic barabar half-half hona lazmi nahin.

| State | Matlab |
|---|---|
| Active | Normal traffic ke liye eligible |
| Standby | Active path fail ho to eligible backup |
| Unused | Is policy mein normal traffic aur failover dono se excluded |

Unused koi “corporate mein kabhi use nahin hota” wali category nahin. Yeh specific policy ka decision hai.

## Is exercise ki proposed values

| Object | Proposed value |
|---|---|
| Switch | vSwitch-LabNet |
| Active candidate | vmnic4 — sirf jab abhi unused confirm ho |
| Standby candidate | vmnic5 — sirf jab abhi unused confirm ho |
| VMkernel group / IP | PG-Lab-VMkernel / 172.16.50.10/24 |
| VM group / VM-A | PG-Lab-VMs / 172.16.50.20/24 |
| External Workstation endpoint | 172.16.50.30/24 |
| Outer network | LAB-ESXI-NET — dono uplinks aur endpoint aik hi segment par |
| VLAN / MTU | 0 untagged / 1500 |

Yeh future practice values hain, captured storage IP 172.16.0.102/16 nahin. Preparation mein subnet overlap aur free adapters check hotay hain. Dono adapters MAC se Workstation NICs ke saath match hon. Management vmk0 aur existing vmk1 ko is exercise ka naya adapter samajh kar change na karein. Do virtual paths aik hi Windows host/physical NIC par depend kar sakte hain; yeh independent hardware redundancy nahin.

## Active/standby configuration ka workflow

1. Host Client → Virtual switches → vSwitch-LabNet → Edit settings → Teaming and failover.
2. Load balancing: Route based on originating virtual port ID.
3. Network failure detection: Link status only.
4. Notify switches: Yes. Failback: Yes, is exercise ke liye.
5. vmnic4 ko Active aur vmnic5 ko Standby mein rakha jata hai.
6. Save ke baad actual saved state dobara inspect hoti hai.
7. Dono lab port groups ki teaming inheritance check hoti hai. Unintended override test ko badal sakti hai.

Yeh repeat workflow hai; is policy ki newly saved screenshot abhi recorded nahin.

## Meaningful failover test

External Workstation endpoint se VM-A ki continuous ping test hoti hai:

```bash
ping 172.16.50.20
```

Endpoint se 172.16.50.10 tak bhi baseline connectivity confirm honi chahiye. Phir recorded MAC mapping se sirf vmnic4 wala outer Workstation lab adapter pehchana jata hai. Us ka **Connected** clear karke link-down simulate hota hai; device remove ya Windows physical Ethernet disable nahin ki jati. Management adapter untouched rehta hai.

Ping ka loss/recovery aur ESXi physical-adapter state capture hon. Backup path sahi ho to traffic vmnic5 se recover kar sakti hai. Fixed recovery time guarantee nahin. Phir wohi adapter reconnect, dono links verify aur final state record hoti hai. Same-host VM se same-switch VMkernel tak ping local reh sakti hai; is se uplink failover sabit nahin hota.

## Active/active aur override ka follow-up

1. Originating-port policy rakh kar vmnic5 bhi Active kiya ja sakta hai.
2. Dono groups ki inheritance dobara check hoti hai.
3. External test mein aik waqt mein sirf aik lab link disconnect aur restore hota hai.
4. Distribution dekhne ke liye multiple guest vNICs/VMs aur adapter counters chahiye. Sirf aik ping ya aik transfer ka aik NIC par rehna normal ho sakta hai.
5. Optional PG-Lab-VMkernel override mein vmnic5 Active, vmnic4 Standby ho sakta hai; VM group ki inherited policy alag inspect hoti hai. Exercise ke baad original inheritance restore hoti hai.

## Policies ka sahi farq

IP hash source/destination IP ki bunyaad par uplink select karta hai; “pehla IP fail phir doosra” nahin. Standard-switch IP-hash design ko compatible upstream static aggregation chahiye. LACP standard switch ka feature nahin. Explicit failover order eligible uplinks ko priority deta hai; application-aware DRS nahin. Physical NIC load policy distributed switches ka concept hai.

## Result aur recovery record

| Check | Actual evidence |
|---|---|
| MAC mapping aur same outer network | Abhi capture karni hai |
| Active/standby saved policy | Abhi capture karni hai |
| External baseline ping | Abhi capture karni hai |
| Link-down aur recovery | Abhi capture karni hai |
| Dono adapters reconnect | Abhi capture karni hai |
| Final chosen policy | Abhi capture karni hai |

Management access lost ho to local Workstation console se recorded management configuration inspect aur restore hoti hai. Pehla response poora ESXi network reset karna nahin. Temporary lab links aur policies restore hon; cleanup ka detailed dependency order optional source ke Chapter 07 mein hai.

**Revision sawal:** Dono adapters Active nazar aane se automatic failover verified hai?

**Jawab:** Nahin. External traffic ke saath controlled link fault aur actual recovery evidence chahiye.
