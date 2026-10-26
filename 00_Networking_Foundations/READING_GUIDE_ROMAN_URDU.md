# Roman Urdu reading guide

Bhai, pehle [complete English theory](Networking-Complete-Roadmap-Study-Notes.md) parhein. Uske baad [platform commands](PLATFORM_COMMANDS.md), phir [VMware concepts](VMWARE_LAB_CONNECTIONS.md), aur aakhir mein [evidence worksheet](PRACTICE_AND_EVIDENCE.md) use karein.

## Zaroori farq

- **IP/prefix:** interface ka address aur local subnet samajhne mein madad karta hai.
- **Gateway:** doosre network ki taraf agla router ho sakta hai; har local communication gateway se nahin guzarti.
- **DNS:** naam ko records se resolve karta hai; successful DNS response service chalne ki guarantee nahin.
- **VMnet:** Workstation ka virtual network.
- **vmnic:** ESXi ka uplink NIC; nested lab mein iske peechay Workstation virtual adapter hota hai.
- **vSwitch:** ESXi virtual switching deta hai; apne aap router nahin.
- **Port group:** connection aur policy settings ka group, jaise VLAN.
- **vmk:** ESXi host services ka IP interface; guest VM ki NIC nahin.

Linux ki `ip`, `ss` aur `systemctl` commands ko Windows/ESXi par same assume na karein. Windows PowerShell ke liye command guide alag section deta hai. SSH se ESXi login karne ke baad commands remote ESXi par chalti hain.

Ping fail aur TCP port successful ho sakta hai. Pehle evidence dekhein, phir focused check karein. Sirf IP subnet badalne se Layer 2 broadcast alag nahin hoti; VLAN ya physical segmentation bhi design ke mutabiq chahiye.

Is bundle mein koi network setting change nahin hui. Practical ki actual output worksheet mein baad mein record karein ge. Yeh short reading guide hai, complete theory ka Roman Urdu translation nahin.
