# DNS02 — Windows Server 2016 Lab Study Notes (ENGLISH)

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

## Step 01 — Create the VM

File → New Virtual Machine → Typical → I will install the operating system later. This avoids automatic installation and lets you select the Windows edition yourself.

![Step 01: Create the VM](screenshots/013847.png)

## Step 02 — Select the guest OS

Select Microsoft Windows and Windows Server 2016. The earlier Windows Server 2025 selection was changed for this second VM.

![Step 02: Select the guest OS](screenshots/014015.png)

## Step 03 — Set VM name and location

Use DNS02 as the original VM name. Change the default OneDrive location to D:\VMware-Labs\DNS\DNS02. The Library display name was later changed to DNS-Win2016; the guest computer name remains DNS02.

![Step 03: Set VM name and location](screenshots/014215.png)

## Step 04 — Set virtual disk capacity

Change 60 GB to 80 GB. Select Split virtual disk into multiple files. Capacity is a maximum; a growable disk does not immediately consume the full 80 GB.

![Step 04: Set virtual disk capacity](screenshots/014338.png)

## Step 05 — Review the initial summary

The initial summary still showed 2 GB RAM and NAT. Use Customize Hardware before finishing; those are not the final lab settings.

![Step 05: Review the initial summary](screenshots/014456.png)

## Step 06 — Allocate RAM and processors

Set memory to 4096 MB. Target 1 processor with 2 cores (2 vCPUs total). The screenshot shows 2 processors × 1 core; the change to 1 × 2 was instructed but not independently verified afterward. Leave nested virtualization options unchecked for this guest.

![Step 06: Allocate RAM and processors](screenshots/014703.png)

## Step 07 — Attach the installation ISO

Under CD/DVD (SATA), select Use ISO image file and browse to the supplied Server 2016 ISO. Keep Connect at power on checked. Do not leave the physical drive Auto detect option selected.

![Step 07: Attach the installation ISO](screenshots/015008.png)

## Step 08 — Select the lab network

Network Adapter → Custom: Specific virtual network → VMnet2 (Host-only). Keep Connect at power on checked. Both lab VMs must use the same virtual network.

![Step 08: Select the lab network](screenshots/015225.png)

## Step 09 — Review and finish

Confirm Windows Server 2016, D: lab location, 80 GB disk, 4 GB RAM, 2 total CPU cores, and VMnet2. Click Finish.

![Step 09: Review and finish](screenshots/015339.png)

## Step 10 — Organize and boot

Place the VM inside the DNS folder in the Workstation Library, then power it on. This Library folder organizes VM entries; it is not a DNS server or a DNS zone.

![Step 10: Organize and boot](screenshots/015521.png)

## Step 11 — Select installation language

Use English (United States), the displayed US time/currency format, and US keyboard for this lab. Click Next, then Install now.

![Step 11: Select installation language](screenshots/015732.png)

## Step 12 — Select Desktop Experience carefully

Choose Windows Server 2016 Standard Evaluation (Desktop Experience). The first Standard option is Server Core. The ISO filename includes Datacenter, but the installer offers multiple editions.

![Step 12: Select Desktop Experience carefully](screenshots/022152.png)

## Step 13 — Accept license and select Custom

Accept the license terms, click Next, then choose Custom: Install Windows only (advanced) for this new VM.

![Step 13: Accept license and select Custom](screenshots/020307.png)

## Step 14 — Choose the virtual disk

For a fresh VM, select Drive 0 Unallocated Space, 80 GB, and click Next. Setup creates the necessary partitions automatically.

![Step 14: Choose the virtual disk](screenshots/020414.png)

## Step 15 — Wait for installation

Allow file copying, feature installation, and restarts to complete. After the installed disk boots, do not press a key to boot the ISO again.

![Step 15: Wait for installation](screenshots/020523.png)

## Step 16 — Recognize the accidental Core installation

The first attempt reached a command-line LogonUI/password screen: Server Core had been installed. Desktop Experience cannot be obtained simply by changing the wallpaper or enabling a GUI option; this lab was reinstalled with the correct edition.

![Step 16: Recognize the accidental Core installation](screenshots/020853.png)

## Step 17 — Recover ISO boot through firmware

With the VM powered off, use Power → Power On to Firmware. In EFI Boot Manager select EFI VMware Virtual SATA CDROM Drive (1.0), press Enter, then promptly press Space at the boot prompt.

![Step 17: Recover ISO boot through firmware](screenshots/021705.png)

## Step 18 — Clean reinstall — only the disposable DNS02 disk

During this specific reinstall, Drive 0 contained Recovery, System, MSR, and Primary partitions from the mistaken Core installation. All four partitions were deleted on the disposable DNS02 virtual disk, leaving 80 GB unallocated. This erases that disk; do not repeat on an existing VM whose data you need. Select the correct Desktop Experience edition before continuing.

![Step 18: Clean reinstall — only the disposable DNS02 disk](screenshots/022411.png)

## Step 19 — Verify unallocated space

The recovery screenshot confirms Drive 0 is one 80 GB unallocated area. Select it and click Next to reinstall.

![Step 19: Verify unallocated space](screenshots/022826.png)

## Step 20 — Set Administrator password

After installation, enter and confirm your own Administrator password. Keep it private; no password is recorded in these notes.

![Step 20: Set Administrator password](screenshots/023701.png)

## Step 21 — Log in and verify GUI

At the lock screen use VM → Send Ctrl+Alt+Del or Ctrl+Alt+Insert, then sign in as Administrator. Server Manager appearing confirms Desktop Experience is installed.

![Step 21: Log in and verify GUI](screenshots/024021.png)

## Step 22 — Rename the Windows computer

Server Manager → Local Server → Computer name → Change. Enter DNS02, keep WORKGROUP, confirm and restart. This is the guest hostname, separate from the Workstation display name.

![Step 22: Rename the Windows computer](screenshots/024235.png)

## Step 23 — Verify the new hostname

After restart, Local Server shows DNS02. No domain join or Active Directory installation has been performed in this phase.

![Step 23: Verify the new hostname](screenshots/024447.png)

## Step 24 — Configure static IPv4

Open Ethernet properties → Internet Protocol Version 4 (TCP/IPv4) → Properties. Select Use the following IP address: 192.168.0.122, mask 255.255.255.0, gateway blank. Set preferred DNS to 192.168.0.122 and alternate DNS blank to match the teacher reference. The earlier suggestion to use .121 was superseded. Click OK and Close.

![Step 24: Configure static IPv4](screenshots/025255.png)

## Step 25 — Verify IP configuration

Run ipconfig /all. Verify hostname DNS02, DHCP Enabled: No, IPv4 192.168.0.122 (Preferred), /24 mask, blank gateway and DNS server 192.168.0.122. Self-DNS is only a client setting at this stage; it does not install the DNS Server role.

![Step 25: Verify IP configuration](screenshots/025435.png)

## Step 26 — Investigate the failed ping

From DNS02 run ping 192.168.0.121. The first test returned four timeouts and 100% loss. A failed ping alone does not prove that the VM is disconnected.

![Step 26: Investigate the failed ping](screenshots/030058.png)

## Step 27 — Verify DNS01 network attachment

DNS-Win2025 settings confirmed Custom VMnet2 (Host-only), Connected, and Connect at power on. This eliminated a visible network-selection mismatch.

![Step 27: Verify DNS01 network attachment](screenshots/030436.png)

## Step 28 — Check the ARP table

Run arp -a on DNS02. Interface 192.168.0.122 listed 192.168.0.121 with dynamic MAC 00-0c-29-86-83-a4. This supported local link connectivity, while ping remained blocked. ARP maps local IPv4 addresses to MAC addresses; it does not test DNS.

![Step 28: Check the ARP table](screenshots/030704.png)

## Step 29 — Apply the teacher’s isolated-lab firewall setting

On DNS01, run wf.msc → Windows Defender Firewall Properties → Public Profile → Firewall state: Off → Apply/OK. The screenshot shows Off selected before closing. This followed the teacher’s isolated VMnet2 lab setup. For a maintained environment, use enabled firewalls with explicit rules instead.

![Step 29: Apply the teacher’s isolated-lab firewall setting](screenshots/031133.png)

## Step 30 — Verify DNS02 to DNS01

Repeat ping 192.168.0.121 from DNS02. Result: 4 sent, 4 received, 0 lost, 0% loss. The successful retest after the firewall change identified firewall filtering as the earlier ping blocker.

![Step 30: Verify DNS02 to DNS01](screenshots/031337.png)

## Step 31 — Test the reverse direction

On DNS02 the same Public Profile firewall-off procedure was instructed to follow the teacher. A separate DNS02 firewall dialog screenshot was not supplied. Then DNS01 ran ping 192.168.0.122: 4 replies, 0% loss. Connectivity is verified both ways.

![Step 31: Test the reverse direction](screenshots/032156.png)

## Step 32 — Rename the Library entry

The Workstation Library entry was changed from DNS02 to DNS-Win2016. The screenshot shows DNS-Win2016 and DNS-Win2025 inside the DNS folder. The guest hostname is still DNS02; renaming the display entry does not rename the disk folder.

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
