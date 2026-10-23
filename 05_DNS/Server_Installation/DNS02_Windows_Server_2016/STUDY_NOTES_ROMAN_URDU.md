# DNS02 — Windows Server 2016 Lab Study Notes (ROMAN URDU)

Prepared from your lab steps and screenshots, through the Library rename and successful connectivity tests.

## Verified lab settings

| Setting | Value |
|---|---|
| Workstation Library name | DNS-Win2016 (originally DNS02) |
| Windows computer name | DNS02 |
| OS | Windows Server 2016 Standard Evaluation (Desktop Experience) |
| VM folder | `D:\VMware-Labs\DNS\DNS02` |
| RAM / disk | 4 GB / 80 GB split virtual disk (NVMe) |
| CPU | 2 vCPUs total; target 1 socket × 2 cores; final topology not independently verified |
| Network | Custom VMnet2, Host-only |
| IPv4 / mask | 192.168.0.122 / 255.255.255.0 |
| Gateway | Blank |
| Preferred / alternate DNS | 192.168.0.122 / blank |
| Membership | WORKGROUP |
| DNS01 peer | DNS-Win2025 / DNS01 / 192.168.0.121 |

Installation ISO supplied by you:

```text
D:\vmware\00 Tool & Application and Operating System\Windows Server\Windows_Server_2016_Datacenter_EVAL_en-us_14393_refresh.ISO
```

## Scope and completion status

This records the completed OS build, hostname, static IP, and successful two-way ping tests. **The DNS Server role and DNS zones are not installed/configured in the evidence for this phase.** VMware Tools installation is not verified. Snapshots were suggested but deferred by you. DNS03 (Windows 11) is a separate next lab and is not included as completed work.

VMnet2 is Host-only on 192.168.0.0/24 with VMware DHCP disabled. The blank gateway is intentional for this local lab; internet access is not configured through this adapter. The host-only network includes the Windows host and VMs attached to VMnet2.

## Read the screenshots correctly

Some screenshots show intermediate or incorrect settings, retained to explain how the lab was corrected. Follow the final values above and the caption beside each image. The teacher reference is labeled separately. A command-line screen from the first install is troubleshooting evidence, not the final desktop.

## Is lab ka maqsad

Windows Server 2016 ki doosri VM banana, us ka naam DNS02 rakhna, static IP set karna aur DNS01 ke sath connectivity verify karna. Abhi DNS role aur zones ka kaam baqi hai.

## Step 01 — Create the VM

File se New Virtual Machine kholen, Typical aur install operating system later select karen. Is tarah edition khud select kar sakte hain.

![Step 01: Create the VM](screenshots/013847.png)

## Step 02 — Select the guest OS

Microsoft Windows aur Windows Server 2016 select karen. Doosri VM ke liye 2025 ki jagah 2016 istemal hua.

![Step 02: Select the guest OS](screenshots/014015.png)

## Step 03 — Set VM name and location

Shuru mein VM ka naam DNS02 rakhen. OneDrive ki default location ki jagah D:\VMware-Labs\DNS\DNS02 rakhen. Baad mein Library ka naam DNS-Win2016 hua, Windows ka naam DNS02 hi raha.

![Step 03: Set VM name and location](screenshots/014215.png)

## Step 04 — Set virtual disk capacity

Disk 60 se 80 GB karen aur multiple files wala option rakhen. Growable disk poore 80 GB foran occupy nahin karti.

![Step 04: Set virtual disk capacity](screenshots/014338.png)

## Step 05 — Review the initial summary

Pehli summary mein 2 GB RAM aur NAT tha. Finish se pehle Customize Hardware se settings theek karen.

![Step 05: Review the initial summary](screenshots/014456.png)

## Step 06 — Allocate RAM and processors

RAM 4096 MB rakhen. Target 1 processor aur 2 cores hai. Screenshot mein 2 processors aur har aik ka 1 core tha; 1 × 2 karne ki hidayat di gayi thi, baad ka CPU screenshot nahin mila.

![Step 06: Allocate RAM and processors](screenshots/014703.png)

## Step 07 — Attach the installation ISO

CD/DVD mein Use ISO image file select kar ke Server 2016 ISO browse karen. Connect at power on checked rakhen.

![Step 07: Attach the installation ISO](screenshots/015008.png)

## Step 08 — Select the lab network

Network Adapter mein Custom aur VMnet2 Host-only select karen. Dono lab VMs aik hi virtual network par hon.

![Step 08: Select the lab network](screenshots/015225.png)

## Step 09 — Review and finish

Summary mein OS, D drive ki location, 80 GB disk, 4 GB RAM, 2 cores aur VMnet2 check kar ke Finish karen.

![Step 09: Review and finish](screenshots/015339.png)

## Step 10 — Organize and boot

Library mein VM ko DNS folder mein rakhen aur power on karen. Yeh sirf VMs organize karne ka folder hai, DNS server ya zone nahin.

![Step 10: Organize and boot](screenshots/015521.png)

## Step 11 — Select installation language

English United States, US time format aur US keyboard rakhen. Next aur Install now karen.

![Step 11: Select installation language](screenshots/015732.png)

## Step 12 — Select Desktop Experience carefully

Windows Server 2016 Standard Evaluation (Desktop Experience) select karen. Pehla Standard option Server Core hota hai. ISO ke naam mein Datacenter hone ke bawajood installer kai editions deta hai.

![Step 12: Select Desktop Experience carefully](screenshots/022152.png)

## Step 13 — Accept license and select Custom

License accept karen, phir Custom: Install Windows only select karen.

![Step 13: Accept license and select Custom](screenshots/020307.png)

## Step 14 — Choose the virtual disk

Nayi VM mein Drive 0 Unallocated Space 80 GB select kar ke Next karen. Setup zaroori partitions khud banata hai.

![Step 14: Choose the virtual disk](screenshots/020414.png)

## Step 15 — Wait for installation

Files copy aur restarts mukammal hone den. Installed disk se boot hone ke waqt dobara ISO boot karne ke liye key na dabayen.

![Step 15: Wait for installation](screenshots/020523.png)

## Step 16 — Recognize the accidental Core installation

Pehli installation command-line screen par aayi kyun ke Server Core install hua tha. Is lab mein sahi Desktop Experience edition ke sath dobara installation ki gayi.

![Step 16: Recognize the accidental Core installation](screenshots/020853.png)

## Step 17 — Recover ISO boot through firmware

VM off kar ke Power On to Firmware use karen. EFI mein virtual SATA CDROM choose karen aur boot prompt par foran Space dabayen.

![Step 17: Recover ISO boot through firmware](screenshots/021705.png)

## Step 18 — Clean reinstall — only the disposable DNS02 disk

Sirf is disposable DNS02 disk par ghalat Core installation ki chaar partitions delete ki gayin. Is se us disk ka data mit jata hai. Zaroori data wali VM par yeh step repeat na karen. Sahi Desktop Experience edition select karen.

![Step 18: Clean reinstall — only the disposable DNS02 disk](screenshots/022411.png)

## Step 19 — Verify unallocated space

Screenshot mein Drive 0 aik 80 GB unallocated area hai. Isay select kar ke Next karen.

![Step 19: Verify unallocated space](screenshots/022826.png)

## Step 20 — Set Administrator password

Apna Administrator password set aur confirm karen. Password private rakhen; notes mein password record nahin kiya gaya.

![Step 20: Set Administrator password](screenshots/023701.png)

## Step 21 — Log in and verify GUI

Lock screen par Ctrl+Alt+Insert ya VM menu se Ctrl+Alt+Del bhejen aur login karen. Server Manager ka GUI sahi edition ki tasdeeq karta hai.

![Step 21: Log in and verify GUI](screenshots/024021.png)

## Step 22 — Rename the Windows computer

Local Server se Computer name aur Change kholen. DNS02 likhen, WORKGROUP rakhen aur restart karen. Yeh Windows hostname hai.

![Step 22: Rename the Windows computer](screenshots/024235.png)

## Step 23 — Verify the new hostname

Restart ke baad Local Server mein DNS02 verify karen. Is phase mein domain join ya Active Directory install nahin ki gayi.

![Step 23: Verify the new hostname](screenshots/024447.png)

## Step 24 — Configure static IPv4

Ethernet ki IPv4 Properties mein IP 192.168.0.122, mask 255.255.255.0 aur gateway blank rakhen. Teacher ke mutabiq preferred DNS bhi 192.168.0.122 aur alternate blank rakhen. Pehle .121 ki suggestion thi jo badal di gayi.

![Step 24: Configure static IPv4](screenshots/025255.png)

## Step 25 — Verify IP configuration

ipconfig /all se hostname, DHCP No, IP .122 Preferred, mask aur DNS .122 verify karen. Apni IP ko DNS mein likhne se DNS Server role install nahin hota.

![Step 25: Verify IP configuration](screenshots/025435.png)

## Step 26 — Investigate the failed ping

DNS02 se ping 192.168.0.121 par pehle chaar timeouts aaye. Sirf ping fail hone se VM disconnected sabit nahin hoti.

![Step 26: Investigate the failed ping](screenshots/030058.png)

## Step 27 — Verify DNS01 network attachment

DNS-Win2025 settings mein VMnet2 aur dono connection boxes checked the. Dono VMs ka network match verify hua.

![Step 27: Verify DNS01 network attachment](screenshots/030436.png)

## Step 28 — Check the ARP table

arp -a mein .121 ka dynamic MAC mila. Is se local link connectivity ka saboot mila, lekin ping abhi blocked tha. ARP IP ko MAC se map karta hai, DNS test nahin karta.

![Step 28: Check the ARP table](screenshots/030704.png)

## Step 29 — Apply the teacher’s isolated-lab firewall setting

DNS01 par wf.msc ki Public Profile mein firewall Off kar ke save kiya gaya. Yeh teacher ki isolated lab setting thi. Maintained environment mein firewall on aur required rules istemal karen.

![Step 29: Apply the teacher’s isolated-lab firewall setting](screenshots/031133.png)

## Step 30 — Verify DNS02 to DNS01

DNS02 se .121 ka ping dobara chalaya: 4 replies aur 0% loss. Firewall change ke baad ping successful hua.

![Step 30: Verify DNS02 to DNS01](screenshots/031337.png)

## Step 31 — Test the reverse direction

DNS02 par bhi Public Profile firewall off karne ki hidayat di gayi; us dialog ka alag screenshot nahin mila. DNS01 se .122 par chaar replies aur 0% loss aaya. Dono taraf connectivity verify hui.

![Step 31: Test the reverse direction](screenshots/032156.png)

## Step 32 — Rename the Library entry

Library ka naam DNS02 se DNS-Win2016 kiya gaya. Windows hostname DNS02 hi hai. Display name badalne se disk folder ka naam nahin badalta.

![Step 32: Rename the Library entry](screenshots/033000.png)

## Command reference

Run inside the indicated Windows guest:

```cmd
hostname
ipconfig /all
arp -a
```

From DNS02:

```cmd
ping 192.168.0.121
```

From DNS01:

```cmd
ping 192.168.0.122
```

`ping` checks ICMP reachability. `arp -a` displays local IP-to-MAC mappings. Neither proves that DNS name resolution is working. DNS resolution testing belongs to the later role/zone configuration phase.

## Final checkpoint

- Windows Server 2016 GUI installed and login verified.
- Guest hostname DNS02; Library name DNS-Win2016.
- Static IP 192.168.0.122/24 and self-DNS .122 verified.
- Same VMnet2 lab network as DNS01.
- Ping successful in both directions: 4 replies and 0% loss.
- Public firewall disabled for the teacher’s lab; DNS02 dialog itself was not captured.
- DNS Server role, zones, VMware Tools, and snapshots remain unverified or pending.

## Troubleshooting lessons

| Symptom | Evidence / correction |
|---|---|
| Command-line login instead of desktop | Wrong edition (Server Core); reinstall Desktop Experience |
| VM boots installed disk instead of ISO | Use Power On to Firmware and select the virtual CD/DVD |
| Ping fails | Verify IP, VMnet2 connection, ARP, then firewall filtering |
| ARP entry exists but ping times out | Local mapping exists; ICMP can still be filtered |
| Library name differs from Windows name | Separate names; record both |
| DNS points to itself | Client configuration only; DNS service still needs installation |

## Next phase

Follow the teacher’s Windows 11 VM build separately. Confirm its intended client role and DNS settings before configuration. Windows 11 does not provide the Windows Server DNS Server role. Do not copy DNS02’s self-DNS setting to the client automatically.
