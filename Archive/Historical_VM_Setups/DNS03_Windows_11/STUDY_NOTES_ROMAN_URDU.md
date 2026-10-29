# DNS03 — Windows 11 Installation and Network Lab

Study checkpoint: 21 October 2026. Is guide mein ab tak ki lab record hai. Agli DNS configuration ke liye teacher ke steps ka intezar karein.

## Lab reference

| Item | Value |
|---|---|
| VMware display name | DNS-Win11 |
| Guest hostname | DNS03 |
| Installed edition | Windows 11 Enterprise |
| VM files | `D:\VMware-Labs\DNS\DNS-Win11` |
| RAM / CPU / disk | 4 GB / 2 total cores / 80 GB split NVMe |
| Firmware support | Virtual TPM present; partial encryption selected |
| Network | Custom VMnet2, Host-only, 192.168.0.0/24 |
| IP / mask | 192.168.0.123 / 255.255.255.0 |
| Default gateway | Blank |
| Preferred / alternate DNS | 192.168.0.123 / 192.168.0.121 |
| Workgroup / local account | WORKGROUP / khan |

ISO used:

```text
D:\vmware\00 Tool & Application and Operating System\Windows Server\en-us_windows_11_iot_enterprise_version_25h2_x64_dvd_67098cd6.iso
```

| VM | Guest | Address | Preferred DNS | Alternate DNS |
|---|---|---|---|---|
| DNS-Win2025 | DNS01 | 192.168.0.121 | 192.168.0.121 | Blank |
| DNS-Win2016 | DNS02 | 192.168.0.122 | 192.168.0.122 | 192.168.0.121 |
| DNS-Win11 | DNS03 | 192.168.0.123 | 192.168.0.123 | 192.168.0.121 |

All masks are /24, gateways blank, adapters on VMnet2. VMware DHCP is disabled on this lab network.

## Step-by-step lab / Qadam ba qadam lab

### 1. Choose the guest OS

Microsoft Windows → Windows 11 x64 select karein. Pehli Windows Server 2016 selection ko change karein.

![Step 1: Choose the guest OS](screenshots/01_034854.png)

### 2. Name and location

VMware display name DNS-Win11 aur location D:\VMware-Labs\DNS\DNS-Win11 rakhein.

![Step 2: Name and location](screenshots/02_035133.png)

### 3. Confirm VM identity

Naam aur location check karein. DNS folder sirf VMs organize karta hai; yeh DNS zone nahee hai.

![Step 3: Confirm VM identity](screenshots/03_035248.png)

### 4. Encryption and virtual TPM

TPM ke liye zaroori files ki encryption select rakhein. Apna encryption password set aur mehfooz rakhein; notes mein na likhein.

![Step 4: Encryption and virtual TPM](screenshots/04_035342.png)

### 5. Virtual disk

64 GB ko 80 GB karein aur disk ko multiple files mein split karein.

![Step 5: Virtual disk](screenshots/05_035640.png)

### 6. Customize hardware

Finish se pehle Customize Hardware kholein. Is lab mein 4 GB RAM aur total 2 CPU cores use huay.

![Step 6: Customize hardware](screenshots/06_035857.png)

### 7. Select VMnet2

Network Adapter mein Custom → VMnet2 (Host-only) select karein. Connect at power on check karein.

![Step 7: Select VMnet2](screenshots/07_040108.png)

### 8. Attach the ISO

CD/DVD mein Use ISO image file select kar ke neeche diya gaya Windows 11 ISO attach karein. Connect at power on check karein.

![Step 8: Attach the ISO](screenshots/08_040318.png)

### 9. Review the summary

Windows 11 x64, 80 GB split disk, 4096 MB RAM, 2 cores aur Custom VMnet2 verify kar ke Finish karein.

![Step 9: Review the summary](screenshots/09_040445.png)

### 10. Power on and boot the installer

Power on karein. Console ke andar click kar ke CD/DVD prompt par Space dabayein. TPM present hai. Demand ho to VM ko DNS library folder mein move karein; final placement alag screenshot mein verify nahee hui.

![Step 10: Power on and boot the installer](screenshots/10_040605.png)

### 11. Setup language

English (United States) select kar ke Next karein. Boot prompt miss ho to firmware se virtual CD/DVD boot karein aur waqt par key dabayein. Recovery ka separate screenshot nahee mila.

![Step 11: Setup language](screenshots/11_040953.png)

### 12. Keyboard

US keyboard layout select kar ke continue karein.

![Step 12: Keyboard](screenshots/12_041014.png)

### 13. Installation choice

Install Windows 11 select karein. Delete acknowledgment sirf is nayi lab VM disk ke liye accept karein. Product key na ho aur option mile to I don’t have a product key karein; is se activation nahee hoti.

![Step 13: Installation choice](screenshots/13_041041.png)

### 14. Choose the edition

Windows 11 Enterprise select karein. ISO ke naam mein IoT hai lekin hum ne installer mein Enterprise choose kia.

![Step 14: Choose the edition](screenshots/14_041343.png)

### 15. Installation disk

Disk 0 Unallocated Space (80 GB) select kar ke Next karein. Setup partitions khud banaye ga.

![Step 15: Installation disk](screenshots/15_041529.png)

### 16. Start installation

Windows 11 Enterprise aur Keep nothing verify kar ke Install karein. Restart par dobara ISO boot karne ke liye key na dabayein.

![Step 16: Start installation](screenshots/16_041631.png)

### 17. Offline setup

VMnet2 par internet na hona expected hai. Nazar aane wala I don’t have internet link click karein. Is installation mein bypass command ki Demand nahee pari.

![Step 17: Offline setup](screenshots/17_043705.png)

### 18. Local account

Local account aur password banayein. Baad ke screenshot mein actual account khan nazar aata hai. Password private rakhein.

![Step 18: Local account](screenshots/18_043904.png)

### 19. Privacy options

Privacy options review karein. Optional settings off aur required diagnostic data suggest kia gaya; final har toggle verify nahee hua. Accept kar ke continue karein.

![Step 19: Privacy options](screenshots/19_044146.png)

### 20. Desktop reached

Desktop aur khan account GUI installation complete honay ka saboot hain. VMware Tools aur Windows activation verify nahee huay.

![Step 20: Desktop reached](screenshots/20_044545.png)

### 21. Rename the guest

System Properties → Computer Name → Change mein DNS03 set karein, WORKGROUP rakhein aur prompt par restart karein. Guest name DNS03 aur VMware display name DNS-Win11 alag cheezein hain.

![Step 21: Rename the guest](screenshots/21_045156.png)

### 22. Open adapter properties

Win+R → ncpa.cpl → Ethernet0 → Properties → Internet Protocol Version 4 (TCP/IPv4) → Properties kholein.

![Step 22: Open adapter properties](screenshots/22_045341.png)

### 23. Set a static address

Static IP 192.168.0.123 aur mask 255.255.255.0 dein. Gateway blank rakhein. Pehli DNS suggestion .121 thi; teacher ki neeche wali final settings follow ki gayi hain.

![Step 23: Set a static address](screenshots/23_045428.png)

### 24. Teacher’s preferred DNS

Intermediate screenshot mein preferred DNS 192.168.0.123 aur alternate blank hai. Final settings aglay step mein hain.

![Step 24: Teacher’s preferred DNS](screenshots/24_045724.png)

### 25. Apply final teacher settings

IP .123/24 aur gateway blank rakhein. Preferred DNS 192.168.0.123 aur alternate DNS 192.168.0.121 set kar ke OK karein.

![Step 25: Apply final teacher settings](screenshots/25_050254.png)

### 26. Verify with ipconfig

ipconfig /all chalayein. DNS03, DHCP No, IP .123, /24 mask, blank gateway aur DNS .123 phir .121 verify karein.

![Step 26: Verify with ipconfig](screenshots/26_050830.png)

### 27. Companion change on DNS02

DNS02 par IP .122 aur preferred DNS .122 rakha gaya aur alternate .121 add kia. Screenshot is update ko verify karta hai; DNS03 ka IP change nahee hota.

![Step 27: Companion change on DNS02](screenshots/27_051004.png)

## Firewall ka step — discuss hua, settings ka screenshot nahee mila

Teacher ke isolated lab tareeqay ke mutabiq DNS03 par yeh steps diye gaye:

1. Win+R → `wf.msc`.
2. Windows Defender Firewall Properties kholein.
3. Public Profile → Firewall state: Off.
4. Apply → OK.

DNS03 ki firewall properties ka screenshot nahee mila, is liye exact final setting independently confirm nahee hai. Inbound ping ka successful hona ICMP connectivity verify karta hai; poori firewall off honay ka saboot nahee. Yeh firewall-off procedure sirf is isolated classroom lab ke liye tha.

### 28. Ping from DNS03

DNS03 se ping 192.168.0.121 aur ping 192.168.0.122 chalayein. Dono mein 4 replies aur 0% loss mila.

![Step 28: Ping from DNS03](screenshots/28_051601.png)

### 29. Ping from DNS01

DNS01 par ping 192.168.0.123 chalaya gaya aur 4 replies, 0% loss mila. Source machine ka naam requested sequence se liya gaya; cropped console mein hostname nahee dikh raha.

![Step 29: Ping from DNS01](screenshots/29_051830.png)

### 30. Ping from DNS02

DNS02 par ping 192.168.0.123 ka result 4 replies aur 0% loss hai. Is se teen VMs ki requested connectivity checking complete hui.

![Step 30: Ping from DNS02](screenshots/30_051957.png)

## Results ka matlab

Teen VMs ke har pair mein dono taraf ping successful hai; is mein pehlay DNS01–DNS02 tests bhi shamil hain. Ping sirf IP connectivity verify karta hai. DNS queries, zones, records, replication aur domain abhi verify nahee huay.

DNS03 naam ke bawajood Windows 11 client hai. Is lab sequence mein kisi server ka DNS role install ya verify nahee hua. Preferred DNS mein apna IP denay se DNS service nahee banti. Teacher ki settings record ki gayi hain; working DNS service configure honay tak name resolution fail ho sakti hai.

## Complete aur pending kaam

- Complete: Windows GUI installation, DNS03 naam, static IPv4 aur connectivity tests.
- Verify nahee hua: activation, VMware Tools, exact final firewall state aur final library-folder placement.
- Snapshots aap ki hidayat par baad ke liye chhoray gaye.
- Teacher ke liye pending: DNS role, zones, records, client DNS design aur name-resolution tests. AD ya domain join record nahee hua.

## Useful commands

```cmd
hostname
ipconfig /all
ping 192.168.0.121
ping 192.168.0.122
ping 192.168.0.123
```

Ping fail ho to target VM running, VMnet2 selection, adapter connected, unique IPs aur same /24 mask check karein. Receiving firewall ko traffic allow karna chahiye. Is isolated same-subnet lab mein blank gateway expected hai. Troubleshooting mein doosray VMware networks change na karein.
