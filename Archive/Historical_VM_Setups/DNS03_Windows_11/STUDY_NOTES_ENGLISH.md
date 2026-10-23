# DNS03 — Windows 11 Installation and Network Lab

Study checkpoint: 21 October 2026. This guide records work completed so far. Wait for the teacher before installing roles or changing the DNS design.

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

Select Microsoft Windows → Windows 11 x64. The earlier Windows Server 2016 choice must be changed.

![Step 1: Choose the guest OS](screenshots/01_034854.png)

### 2. Name and location

Enter DNS-Win11 as the VMware display name. Use D:\VMware-Labs\DNS\DNS-Win11 as the VM location.

![Step 2: Name and location](screenshots/02_035133.png)

### 3. Confirm VM identity

Verify the name and storage location before continuing. DNS is an organizational folder, not a DNS zone.

![Step 3: Confirm VM identity](screenshots/03_035248.png)

### 4. Encryption and virtual TPM

Keep “Only the files needed to support a TPM are encrypted.” Set and securely retain your own encryption password. Do not put it in study notes.

![Step 4: Encryption and virtual TPM](screenshots/04_035342.png)

### 5. Virtual disk

Change the proposed 64 GB disk to 80 GB and select split virtual disk into multiple files.

![Step 5: Virtual disk](screenshots/05_035640.png)

### 6. Customize hardware

Before finishing, open Customize Hardware. Our completed configuration uses 4 GB RAM and 2 total CPU cores.

![Step 6: Customize hardware](screenshots/06_035857.png)

### 7. Select VMnet2

Network Adapter → Custom → VMnet2 (Host-only). Enable Connect at power on. Connected can be unavailable while powered off.

![Step 7: Select VMnet2](screenshots/07_040108.png)

### 8. Attach the ISO

CD/DVD → Use ISO image file → browse to the Windows 11 ISO listed below. Enable Connect at power on.

![Step 8: Attach the ISO](screenshots/08_040318.png)

### 9. Review the summary

Confirm Windows 11 x64, 80 GB split disk, 4096 MB RAM, 2 cores, and Custom VMnet2. Click Finish.

![Step 9: Review the summary](screenshots/09_040445.png)

### 10. Power on and boot the installer

Power on. Click inside the console and press Space when asked to boot from CD/DVD. The powered-off summary shows TPM present. Move the VM into the DNS library folder if needed; final folder placement was not separately verified.

![Step 10: Power on and boot the installer](screenshots/10_040605.png)

### 11. Setup language

Select English (United States) language and regional settings, then Next. If the boot prompt was missed, restart into firmware, choose the virtual CD/DVD, then press a key promptly. This recovery was discussed, not separately captured.

![Step 11: Setup language](screenshots/11_040953.png)

### 12. Keyboard

Select the US keyboard layout and continue.

![Step 12: Keyboard](screenshots/12_041014.png)

### 13. Installation choice

Choose Install Windows 11. Accept the deletion acknowledgment only for this new disposable lab VM disk. If asked for a product key, use “I don’t have a product key” when offered. This does not activate or license Windows.

![Step 13: Installation choice](screenshots/13_041041.png)

### 14. Choose the edition

Select Windows 11 Enterprise, the edition selected in our installer. Although the ISO filename contains IoT, the selected installation edition is Enterprise.

![Step 14: Choose the edition](screenshots/14_041343.png)

### 15. Installation disk

Select Disk 0 Unallocated Space (80 GB), then Next. Let Setup create its partitions.

![Step 15: Installation disk](screenshots/15_041529.png)

### 16. Start installation

Review “Windows 11 Enterprise” and “Keep nothing,” then Install. Allow automatic restarts; do not press a key to boot the ISO again.

![Step 16: Start installation](screenshots/16_041631.png)

### 17. Offline setup

VMnet2 has no internet by design. Click the visible “I don’t have internet” link to continue offline. No bypass command was needed in this installation.

![Step 17: Offline setup](screenshots/17_043705.png)

### 18. Local account

Create a local account and password. The resulting account shown later is khan. Keep passwords private.

![Step 18: Local account](screenshots/18_043904.png)

### 19. Privacy options

Review the privacy pages. Optional settings were suggested off, with required diagnostic data only; the final individual toggle states were not captured. Accept to continue.

![Step 19: Privacy options](screenshots/19_044146.png)

### 20. Desktop reached

The Windows desktop and local account khan confirm completion of GUI installation. VMware Tools installation and Windows activation were not verified.

![Step 20: Desktop reached](screenshots/20_044545.png)

### 21. Rename the guest

Open System Properties → Computer Name → Change. Set DNS03, keep WORKGROUP, and restart if prompted. The screenshot confirms DNS03 as the guest computer name. VMware display name remains DNS-Win11.

![Step 21: Rename the guest](screenshots/21_045156.png)

### 22. Open adapter properties

Win+R → ncpa.cpl → Ethernet0 → Properties → Internet Protocol Version 4 (TCP/IPv4) → Properties.

![Step 22: Open adapter properties](screenshots/22_045341.png)

### 23. Set a static address

Select “Use the following IP address.” Set 192.168.0.123 with mask 255.255.255.0 and leave gateway blank. The first DNS suggestion was .121; the teacher’s later values below supersede it.

![Step 23: Set a static address](screenshots/23_045428.png)

### 24. Teacher’s preferred DNS

The intermediate screenshot shows preferred DNS 192.168.0.123 and alternate blank. This is an intermediate state, not the final configuration.

![Step 24: Teacher’s preferred DNS](screenshots/24_045724.png)

### 25. Apply final teacher settings

Keep IP .123/24, gateway blank, preferred DNS 192.168.0.123, alternate DNS 192.168.0.121. Click OK and close the dialogs.

![Step 25: Apply final teacher settings](screenshots/25_050254.png)

### 26. Verify with ipconfig

Run ipconfig /all. Confirm Host Name DNS03, DHCP Enabled No, address 192.168.0.123, mask /24, blank gateway, DNS .123 followed by .121.

![Step 26: Verify with ipconfig](screenshots/26_050830.png)

### 27. Companion change on DNS02

On DNS02, keep IP 192.168.0.122 and preferred DNS .122; add alternate DNS .121. Its ipconfig screenshot verifies the update. This is included as context and does not change DNS03’s address.

![Step 27: Companion change on DNS02](screenshots/27_051004.png)

## Firewall step — discussed, no settings screenshot supplied

Following the teacher’s isolated lab approach, the instructions for DNS03 were:

1. Win+R → `wf.msc`.
2. Open Windows Defender Firewall Properties.
3. Public Profile → Firewall state: Off.
4. Apply → OK.

No screenshot of DNS03’s firewall properties was supplied, so the exact final firewall setting is not independently confirmed. Successful inbound ping confirms ICMP reachability, not that the entire firewall is disabled. DNS01 and DNS02 had already been tested after their Public-profile firewall changes. This firewall-off procedure is specific to this isolated classroom lab.

### 28. Ping from DNS03

Run ping 192.168.0.121 and ping 192.168.0.122. Both returned 4 replies with 0% loss.

![Step 28: Ping from DNS03](screenshots/28_051601.png)

### 29. Ping from DNS01

On DNS01, run ping 192.168.0.123. The submitted result returned 4 replies and 0% loss. Source identity follows the requested sequence; the cropped console itself does not display the hostname.

![Step 29: Ping from DNS01](screenshots/29_051830.png)

### 30. Ping from DNS02

On DNS02, run ping 192.168.0.123. The submitted result returned 4 replies and 0% loss, completing the requested pairwise connectivity checks.

![Step 30: Ping from DNS02](screenshots/30_051957.png)

## What the results mean

All three VM pairs have successful ping results in both directions, using this session and the earlier DNS01–DNS02 tests. Ping verifies IP reachability; it does not verify DNS queries, zones, records, replication, or a domain.

DNS03 is a Windows 11 client despite its lab name. The Windows Server DNS role has not been installed or verified on any server in this lab sequence. A preferred DNS address pointing to DNS03 does not create a DNS service. The teacher’s self-address DNS setting is recorded as requested; name resolution may fail until a working DNS service is selected/configured.

## Completed and pending

- Completed: Windows GUI installation, guest naming, static IPv4 settings, and connectivity tests.
- Not verified: Windows activation, VMware Tools installation, exact final firewall state, and final library-folder placement.
- Deferred: snapshots, following your instruction.
- Pending teacher guidance: DNS role installation, zones, records, client DNS design, and name-resolution tests. No domain join or AD configuration is recorded.

## Useful commands

```cmd
hostname
ipconfig /all
ping 192.168.0.121
ping 192.168.0.122
ping 192.168.0.123
```

If a ping fails, check that the target VM is running, both adapters use VMnet2 and are connected, addresses are unique in the same /24, and the receiving firewall permits the traffic. A blank gateway is expected for this same-subnet isolated lab. Do not change other VMware networks while troubleshooting.
