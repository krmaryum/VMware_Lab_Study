# Desktop ESXi Lab — Study Notes

Started: 4 October 2026. Updated: 10 October 2026, America/Chicago. Separate record from the Dell laptop lab.

> **Stopping point:** ESXi 9.1 on the HP desktop boots successfully, root login works, and datastore1 is verified. The user will continue with their instructor. No guest VM inside this ESXi 9 host has been created or powered on. Persistence of the legacy-CPU boot option across an unattended reboot remains unverified.

## Index

1. [1. Goal and hardware](#1-goal-and-hardware)
2. [2. Check Windows hypervisor](#2-check-windows-hypervisor)
3. [3. Check firmware virtualization](#3-check-firmware-virtualization)
4. [4. Locate Workstation installer](#4-locate-workstation-installer)
5. [5. Verify installer signature — next step](#5-verify-installer-signature--next-step)
6. [6. Progress checklist](#6-progress-checklist)
7. [Signature verification result — 06:57 Chicago](#signature-verification-result--0657-chicago)
8. [Windows version and upgrade question — 06:59 Chicago](#windows-version-and-upgrade-question--0659-chicago)
9. [Launch installer and Compatible Setup — 07:04 Chicago](#launch-installer-and-compatible-setup--0704-chicago)
10. [Custom Setup: application location — 07:06 Chicago](#custom-setup-application-location--0706-chicago)
11. [Workstation installation completed — 07:10 Chicago](#workstation-installation-completed--0710-chicago)
12. [First launch: software update notification — 07:14 Chicago](#first-launch-software-update-notification--0714-chicago)
13. [Installed Workstation version verified — 07:16 Chicago](#installed-workstation-version-verified--0716-chicago)
14. [Current storage and ESXi ISO located — 07:19 Chicago](#current-storage-and-esxi-iso-located--0719-chicago)
15. [Name the virtual machine — 07:23 Chicago](#name-the-virtual-machine--0723-chicago)
16. [Desktop VM created: hardware review — 07:27 Chicago](#desktop-vm-created-hardware-review--0727-chicago)
17. [Updated hardware and bridged network — 07:31 Chicago](#updated-hardware-and-bridged-network--0731-chicago)
18. [ESXi desktop boot successful — 07:46 Chicago](#esxi-desktop-boot-successful--0746-chicago)
19. [Host Client login verified — 07:48 Chicago](#host-client-login-verified--0748-chicago)
20. [Datastore verified — 07:52 Chicago](#datastore-verified--0752-chicago)
21. [ESXi 9.1 on the desktop — 10 October 2026](#esxi-91-on-the-desktop--10-october-2026)
22. [Final status and instructor handoff](#final-status-and-instructor-handoff)
23. [Repeat-lab checklist](#repeat-lab-checklist)
24. [References and scope](#references-and-scope)

## 1. Goal and hardware

Keep Windows on the HP desktop and practice ESXi inside VMware Workstation. ESXi 8 and ESXi 9.1 now have separate VMs with successful console boot and browser login. Guest VM operation inside ESXi remains unverified. The table below records the initial checkpoint; the final status table at the end supersedes it.

| Item | Evidence |
|---|---|
| Desktop model | HP 700-074, from earlier hardware query |
| CPU | Intel Core i5-4430 @ 3.00 GHz |
| CPU topology | 1 socket, 4 cores, 4 logical processors |
| RAM | 15.9 GB usable; screenshot shows 5.8 GB in use at that moment |
| Firmware virtualization | Enabled in Task Manager |
| Windows hypervisor | HypervisorPresent: False |
| Workstation | User confirmed not installed |
| Installer located | D:\vmware\VMware-Workstation-Full-25H2-24995812.exe |
| Earlier disk capacity | C: 68.6 GB free of 223.6 GB; D: 1772.7 GB free of 1847.4 GB; recheck before placing VM disks |

The current screenshot labels C: as SSD and the disk containing D:/E: as HDD. Free-space figures above are earlier measurements, not a current check. Windows was subsequently confirmed as Windows 10 Pro for Workstations; see the Windows version checkpoint below.

## 2. Check Windows hypervisor

**Why:** Check whether Windows reports an active hypervisor before diagnosing Workstation nested-virtualization issues.

```powershell
Get-CimInstance Win32_ComputerSystem |
    Select-Object HypervisorPresent
```

User's output:

```text
HypervisorPresent
-----------------
            False
```

**Meaning:** Windows is not reporting an active hypervisor. This is different from the Dell's initial result of True. Do not apply the Dell's registry or boot-setting troubleshooting changes automatically. This result alone does not prove every requirement for nested ESXi is met.

## 3. Check firmware virtualization

**Steps:**

1. Press Ctrl + Shift + Esc to open Task Manager.
2. Select Performance, then CPU.
3. Read the Virtualization field.

**Observed result:** Virtualization: Enabled. CPU details confirm Intel i5-4430, 4 cores and 4 logical processors.

**Why:** Firmware virtualization must be available for this lab. Firmware virtualization being enabled and Windows reporting HypervisorPresent=False describe different things and can both be correct.

Screenshot supplied: image(20261004-115217).png. The image was reviewed; these are its observed values.

## 4. Locate Workstation installer

User confirmed Workstation is not installed. We searched the earlier transfer destination and D:\vmware for the 25H2 executable.

```powershell
Get-ChildItem "C:\Users\filetransfer\Transfers","D:\vmware" `
    -Recurse -Filter "VMware-Workstation-Full-25H2*.exe" `
    -ErrorAction SilentlyContinue |
    Select-Object FullName
```

User's output:

```text
FullName
--------
D:\vmware\VMware-Workstation-Full-25H2-24995812.exe
```

**Explanation:**

- Get-ChildItem searches the two specified locations.
- -Recurse includes subfolders.
- -Filter matches Workstation 25H2 executable names.
- -ErrorAction SilentlyContinue hides errors, including a missing search folder; it does not establish that every path exists.
- Select-Object FullName displays the complete path to each match.
- The backtick continues a PowerShell command on the next line; do not put spaces after it.

We selected 25H2 because that version was successfully signature-verified and installed on the Dell. The desktop copy still needs its own verification.

## 5. Verify installer signature — next step

**Status: signature verified on the desktop at 06:57 Chicago. Installer launch is not yet confirmed.**

```powershell
Get-AuthenticodeSignature `
    -FilePath "D:\vmware\VMware-Workstation-Full-25H2-24995812.exe" |
    Format-List Status, StatusMessage,
        @{Name="Signer";Expression={$_.SignerCertificate.Subject}}
```

**Why:** Inspect the executable's digital-signature status and publisher before running it.

Expected fields to inspect:

- Status: Valid.
- StatusMessage: signature verification message.
- Signer: certificate subject identifying Broadcom Inc.

The result below confirmed Valid with Broadcom Inc as signer. A filename alone does not verify an executable.

## 6. Progress checklist

- [x] Choose desktop for a separate lab attempt.
- [x] Check HypervisorPresent: False.
- [x] Confirm firmware virtualization: Enabled.
- [x] Confirm Workstation not installed.
- [x] Locate 25H2 installer on D:.
- [x] Receive and review desktop installer signature result: Valid, Broadcom Inc.
- [x] Confirm desktop Windows version and Workstation host requirements.
- [x] Install Workstation and verify Help → About.
- [x] Recheck free disk space and choose VM storage location.
- [x] Choose ESXi ISO/version and configure separate ESXi 8 and ESXi 9 VMs.
- [ ] Verify nested-virtualization processor setting.
- [x] Install ESXi, reboot, and log in to its Host Client (8 and 9.1).
- [ ] Create and power on a guest VM inside ESXi to test nested virtualization.

Record each subsequent command, output, screenshot, explanation and actual result here. Do not record passwords or license keys.

## Signature verification result — 06:57 Chicago

```text
Status        : Valid
StatusMessage : Signature verified.
Signer        : CN=Broadcom Inc, O=Broadcom Inc, L=San Jose, S=California, C=US, SERIALNUMBER=6610117, OID.2.5.4.15=Private Organization, OID.1.3.6.1.4.1.311.60.2.1.2=Delaware, OID.1.3.6.1.4.1.311.60.2.1.3=US
```

This verifies the signature of the desktop copy at `D:\vmware\VMware-Workstation-Full-25H2-24995812.exe`. Next check: confirm the desktop Windows version before installation.

```powershell
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture
```

Why: the signature check verifies the publisher signature; operating-system compatibility is a separate check. No Windows version result has been supplied yet.

## Windows version and upgrade question — 06:59 Chicago

```powershell
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture
```

```text
Caption                                   Version    BuildNumber OSArchitecture
-------                                   -------    ----------- --------------
Microsoft Windows 10 Pro for Workstations 10.0.19045 19045       64-bit
```

The desktop is Windows 10 Pro for Workstations, build 19045 (22H2), 64-bit. User requested notes be shared only when this desktop lab is finished.

Windows 11 upgrade assessment: the desktop's fourth-generation Intel i5-4430 is absent from Microsoft's supported Windows 11 Intel processor list. It does not qualify for a supported Windows 11 upgrade on this CPU. Firmware virtualization being enabled does not change CPU eligibility. An installation using requirement bypasses is a different, unsupported route; no upgrade or bypass has been performed.

Windows 10 ordinary support ended 14 October 2025. ESU enrollment and remaining coverage on this machine have not been checked. Do not assume continued Windows 10 security-update coverage.

Sources consulted: https://learn.microsoft.com/en-us/windows-hardware/design/minimum/supported/windows-11-supported-intel-processors and https://learn.microsoft.com/en-us/lifecycle/faq/windows .

## Launch installer and Compatible Setup — 07:04 Chicago

Broadcom's host support table lists Windows 10 as supported for Workstation 25H2: https://knowledge.broadcom.com/external/article/315653 . This checks host OS support, not successful ESXi nested guest operation.

Launch command supplied:

```powershell
Start-Process `
    "D:\vmware\VMware-Workstation-Full-25H2-24995812.exe" `
    -Verb RunAs
```

`-Verb RunAs` requests administrator elevation. The user supplied a running VMware Workstation Pro Setup screenshot, confirming installer launch.

Compatible Setup screen message:

```text
Hyper-V is not detected on the host. Virtual machines will run using VMware's hypervisor.
```

This agrees with the earlier HypervisorPresent=False result. No Windows hypervisor troubleshooting changes were needed for this desktop checkpoint. Next action: click **Next** and inspect the following setup screen. Installation is not complete yet.

Screenshot: image(20261004-120451).png, retained as an uploaded study image.

## Custom Setup: application location — 07:06 Chicago

Screenshot image(20261004-120628).png shows the Workstation application destination:

```text
C:\Program Files (x86)\VMware\VMware Workstation\
```

User asked whether to install on D:. Keep this default on C: (the SSD). This screen controls the Workstation application location; virtual-machine storage is selected separately when creating each VM. Plan VM folders under `D:\VMware-Labs\` to use the desktop's larger drive, subject to rechecking free space. D: is an HDD in the earlier Task Manager screenshot, so VM disk operations can be slower than on the C: SSD. The installer executable may remain in `D:\vmware`; its source location does not set the application or VM destination.

Next action supplied: leave the destination unchanged and click **Next**. Application installation is still pending.

## Workstation installation completed — 07:10 Chicago

Screenshot `image(20261004-121018).png` shows **Completed the VMware Workstation Pro Setup Wizard** for Workstation Pro 25H2. This confirms the installation wizard completed successfully; the installed build and VM operation remain to be checked.

Next steps:
1. Click **Finish** to close setup.
2. If setup requests a restart, restart Windows before proceeding.
3. Open **VMware Workstation Pro** from Start.
4. Open **Help → About VMware Workstation** and record the version and build.

Why: the About dialog verifies the installed application version before creating the desktop ESXi lab. Installer completion alone does not verify nested virtualization or an ESXi VM boot.

## First launch: software update notification — 07:14 Chicago

Screenshot `image(20261004-121406).png` confirms Workstation opens. A Software Updates dialog offers **VMware Workstation version 26.0.1**, labeled **Workstation 26H1u1**, mentioning Secure Boot Platform Key remediation and functional/security fixes. This is an offered update, not proof of the installed version.

The background shows an existing **Rocky Linux 64-bit** VM, powered off, with its configuration under the user's OneDrive Documents Virtual Machines folder. Its listed hardware compatibility is Workstation 17.5 or later; this does not identify the installed Workstation version. No VM was modified.

Next action: click **Remind Me Later** to defer the offered update, then open **Help → About VMware Workstation** and record the installed version/build before making an update decision or creating the ESXi VM.

## Installed Workstation version verified — 07:16 Chicago

About screenshot `image(20261004-121620).png` confirms:

| Item | Observed value |
| --- | --- |
| Product | VMware Workstation Pro 25H2 |
| Version | 25.0.0 |
| Build | 24995812 |
| Host OS | Windows 10 Pro for Workstations, 64-bit |
| Windows build | 19045.6466 |
| Memory shown | 16304 MB |

Application installation and first launch are confirmed. Desktop ESXi VM creation, nested virtualization, and guest power-on remain unverified.

Before creating the VM, next commands supplied to recheck C:/D: space and locate the ESXi 8 installer ISO:

```powershell
Get-Volume -DriveLetter C,D |
    Select-Object DriveLetter,
        @{Name="Free_GB";Expression={[math]::Round($_.SizeRemaining / 1GB, 1)}},
        @{Name="Total_GB";Expression={[math]::Round($_.Size / 1GB, 1)}} |
    Format-Table -AutoSize

Get-ChildItem "D:\vmware" -Recurse -Filter "VMware-VMvisor-Installer-8*.iso" |
    Select-Object FullName |
    Format-List
```

Why: storage planning should use current free space, and the VM wizard requires the actual ISO path. Results are pending; no desktop ESXi version compatibility is assumed from Workstation installation alone.

## Current storage and ESXi ISO located — 07:19 Chicago

User-provided output:

```text
DriveLetter Free_GB Total_GB
----------- ------- --------
          C   130.5    223.6
          D  1712.5   1847.4

FullName : D:\vmware\00 Tool & Application and Operating System\VMeare VCF 9\VMware-VMvisor-Installer-8.0U3e-24677879.x86_64.iso
```

These current free-space values supersede earlier storage readings. The filename identifies the available installer as ESXi 8.0 U3e, build 24677879; ISO integrity and desktop ESXi boot have not yet been verified.

VM placement selected for the next wizard: `D:\VMware-Labs\ESXi-8-Desktop\`. The D: HDD provides ample space; C: SSD is faster but has less free space. Workstation remains installed on C:.

Next user actions: **File → New Virtual Machine → Custom (advanced) → Next**, then share the following screen so hardware compatibility and subsequent settings can be reviewed step by step. VM creation is still pending.

## Name the virtual machine — 07:23 Chicago

Screenshot `image(20261004-122318).png` shows the **Name the Virtual Machine** page. Current name is `VMware ESXi 8`; the default location begins under `C:\Users\krmar\OneDrive\Documents\Virtual Machines\`. Intermediate wizard choices were not shown, so they are not recorded as verified.

Instructions supplied:
- Virtual machine name: `ESXi-8-Desktop`
- Location: `D:\VMware-Labs\ESXi-8-Desktop`
- Click **Next** and share the following page.

Why: the descriptive name distinguishes this host's lab from the Dell labs. The location controls where Workstation stores this VM's configuration and virtual disk files. Selecting the planned D: folder uses the larger drive and places this lab outside the default OneDrive Documents path. VM creation and final hardware settings remain pending.

## Desktop VM created: hardware review — 07:27 Chicago

Screenshot `image(20261004-122741).png` shows the created `ESXi-8-Desktop` VM and its settings, before power-on. Observed configuration: memory 4096 MB (4 GB), processors 2, SCSI disk 250 GB, CD/DVD IDE Auto detect, network NAT, USB controller present. The VM folder path and intermediate wizard choices are not visible in this screenshot.

Next instruction: change memory to **8192 MB (8 GB)**. This is the planned allocation for this small nested ESXi lab on the desktop with approximately 16 GB host RAM, leaving RAM for Windows. Keep the existing Rocky Linux VM powered off while testing ESXi. Then select **Processors** and share that page to verify the CPU topology and nested virtualization setting before powering on.

The ISO must still be attached: the current CD/DVD summary says Auto detect, not the previously located ESXi ISO. Do not power on until CPU, ISO and networking settings have been reviewed.

## Updated hardware and bridged network — 07:31 Chicago

Screenshot `image(20261004-123107).png` is the Network Adapter page. Observed: memory now **8 GB**, processor summary **4**, SCSI disk **250 GB**, CD/DVD using a file whose visible path starts `D:\vmware\00 Tool...`, network **Bridged (Automatic)**, and **Connect at power on** checked. Exact ISO filename and CPU virtualization checkboxes are not visible.

Bridged networking connects the VM to the home LAN, allowing a router-assigned LAN IP and potential management from the other laptops. The actual assigned IP and connectivity must be verified after boot. Leave Bridged selected, Connect at power on checked, and Replicate physical network connection state unchecked on this stationary desktop.

Next instruction: select **Processors** and set **1 processor × 2 cores = 2 virtual CPUs** as a starting allocation on this 4-core/4-thread host. Enable **Virtualize Intel VT-x/EPT or AMD-V/RVI** for the nested ESXi lab, leaving other virtualization engine options unchecked. Share the Processors page before power-on. This requested configuration is not yet verified applied.

## ESXi desktop boot successful — 07:46 Chicago

Screenshot `image(20261004-124651).png` shows the ESXi direct console (DCUI):

```text
VMware ESXi 8.0.3 (VMKernel Release Build 24677879)
VMware, Inc. VMware20,1
2 x Intel(R) Core(TM) i5-4430 CPU @ 3.00GHz
8 GiB Memory
To manage this host, go to:
https://10.0.0.138/ (DHCP)
<F2> Customize System/View Logs
<F12> Shut Down/Restart
```

This confirms ESXi has reached its running console with 2 virtual CPUs, 8 GiB RAM and a DHCP management address on the home LAN. Installation screens and their exact selections were not supplied in this interval, so those steps cannot be reconstructed as observed evidence. Nested guest VM power-on and the VT-x/EPT checkbox remain unverified.

Next step: on the desktop browser visit `https://10.0.0.138/`. If the expected local ESXi site presents a certificate warning, use **Advanced → Proceed** to that address, then log in as **root** using the password set during ESXi installation (not the Windows or filetransfer password). Share the Host Client dashboard after login, without exposing the password. This verifies browser management access. DHCP addresses can change; use the current DCUI address if it changes.

## Host Client login verified — 07:48 Chicago

Screenshot `image(20261004-124844).png` confirms browser management at `https://10.0.0.138/ui/#/host`, logged in as root. Host `localhost.localdomain` has a green status icon and State Normal; version reads 8.0 Update 3. Memory capacity is 8 GB, used 1.68 GB, free 6.32 GB. Storage summary shows capacity 121.75 GB, used 1.41 GB, free 120.34 GB. These guest datastore values differ from the 250 GB virtual disk capacity and the Windows D: free space; ESXi also uses disk space for system partitions.

CPU discrepancy to retain for verification: this dashboard hardware row reads **4 CPUs**, while the preceding DCUI screenshot read **2 x Intel i5-4430**. Current effective CPU topology has not been resolved; do not report it as confirmed 2 vCPUs throughout the notes.

Milestones confirmed: Workstation setup, ESXi running console, management IP and successful Host Client login. Nested guest VM power-on remains pending.

Next requested checkpoint: click **Storage** (disk icon on the left) and share the datastore list, to verify available VM storage before creating a small test guest. A successful test guest power-on will check nested virtualization beyond simply booting ESXi.

## Datastore verified — 07:52 Chicago

Screenshot `image(20261004-125206).png` shows one datastore:

| Name | Capacity | Provisioned | Free | Type | Drive type |
| --- | --- | --- | --- | --- | --- |
| datastore1 | 121.75 GB | 1.41 GB | 120.34 GB | VMFS6 | Non-SSD |

Thin provisioning is shown as Supported; access is Single. This existing datastore has space for a small test VM; no new datastore creation or formatting is needed for the next check.

Next actions supplied: select **Virtual Machines** in the left navigation, click **Create / Register VM**, select **Create a new virtual machine**, then **Next** and share the name/guest OS screen. Proposed test VM name: `Nested-Test-01`. The test will check whether a guest can power on inside ESXi; an installed guest OS is a separate milestone. Actual VM creation and power-on are still pending.


## ESXi 9.1 on the desktop — 10 October 2026

### 1. Separate VM and initial CPU error

The user created a separate Workstation VM named **VMware ESXi 9**. The existing **ESXi-8-Desktop** VM was retained. The new ISO installer identified itself as **ESXi 9.1.0**, rather than merely “version 9.” The exact ISO path was truncated in the screenshots, so it is not recorded as independently verified.

Initial installer system scan stopped with:

```text
CPU_SUPPORT ERROR: The CPU on this host is not supported by ESXi 9.1.0.
Please refer to the VMware Compatibility Guide (VCG) for the list of
supported CPUs. Please refer to KB 82794 for more details.
```

**Meaning:** the installer rejects the CPU exposed to this VM. The physical desktop has an older Intel Core i5-4430. This is a different issue from the Windows hypervisor/VBS problem encountered on the Dell laptop. Adding vCPUs or enabling BIOS virtualization does not change the CPU generation.

Broadcom distinguishes a CPU deprecation warning from discontinued CPU support that blocks installation. Its current CPU-support article is linked in the references. Successful operation in this home lab does not establish supported hardware compatibility.

Evidence: `image(20261010-144906).png`.

### 2. Experimental legacy-CPU installer option

For this disposable learning lab we tried the legacy CPU override:

```text
allowLegacyCPU=true
```

Steps used:

1. Reboot the **ESXi 9 VM** and boot the installer ISO.
2. Click inside the Workstation console so keyboard input goes to the VM.
3. Press **Shift + O** at the initial installer boot screen.
4. Preserve the existing boot options. Add a space, then `allowLegacyCPU=true`.
5. Press **Enter** to apply the options and boot.

An installer boot line with the usual existing options is:

```text
runweasel cdromBoot allowLegacyCPU=true
```

**Spacing lesson:** separate the options with spaces. A later attempt screenshot appeared to show `cdromBootallowLegacyCPU=true` without a separator. We explicitly corrected the documented syntax. Do not copy that malformed line. The next screenshot showed a CPU warning that permitted continuation, but the images alone do not prove how that malformed-looking line was parsed.

This override is an **experimental lab workaround**, not a supported CPU upgrade. It cannot add processor instructions that the hardware lacks. We verified host boot and management access, not every feature or nested guest execution.

Evidence: `image(20261010-154941).png` (initial override), `image(20261010-161629).png` (spacing issue), `image(20261010-162104).png` (warning with Enter → Continue).

### 3. Initial successful boot and memory correction

The first successful DCUI screen reported:

```text
VMware ESXi 9.1.0.0100.25433460 (Release Build)
2 x Intel(R) Core(TM) i5-4430 CPU @ 3.00GHz
16 GiB Memory
https://10.0.0.100/ (DHCP)
```

The Workstation settings showed 16 GB RAM, a processor summary of 4, a 250 GB SCSI disk, and two bridged network adapters. Allocating 16 GB to this VM leaves insufficient headroom on a desktop with approximately 16 GB physical RAM. We reduced the VM allocation to **8192 MB (8 GB)** while it was powered off, leaving memory for Windows. Run one ESXi VM at a time on this machine.

The later settings screenshot verified **8 GB** inside the dialog. The background still displayed 16 GB before the dialog was saved; the final DCUI and dashboard confirm that 8 GB took effect.

Evidence: `image(20261010-145155).png`, `image(20261010-155221).png`, `image(20261010-161309).png`.

### 4. Forgotten ESXi root password

The user attempted console authentication and received:

```text
Authentication failed
Invalid login or password.
```

Checks discussed before reinstalling:

- Use username `root`.
- Use the password created during ESXi installation; the Windows account password is separate.
- Check Caps Lock and keyboard layout.
- Keep passwords in a password manager; never include them in GitHub notes or screenshots.

The user confirmed **no guest virtual machines existed inside ESXi 9**. Broadcom documents reinstalling a standalone ESXi host when its root password is forgotten; vCenter-managed recovery options are a separate scenario. This new lab had no guest workloads to preserve, so we proceeded with reinstalling only the ESXi 9 VM.

Evidence: `image(20261010-160738).png`. This failure screenshot contains no password.

### 5. Reinstallation sequence

1. In Workstation, select **VMware ESXi 9** and power it off.
2. Open **Edit virtual machine settings** and set RAM to **8 GB**.
3. Select the same ESXi 9.1 ISO under **CD/DVD → Use ISO image file** and check **Connect at power on**.
4. Save settings and power on. If the existing installation starts instead, use the firmware boot menu to select the virtual CD/DVD.
5. At installer boot, press **Shift + O** and append the properly separated legacy CPU option.
6. Continue through installer prompts and set a new root password. The exact keyboard-layout choice and intermediate disk-selection pages were not supplied, so they are not claimed as verified.
7. At **CPU_SUPPORT_WARNING**, press **Enter → Continue** for this unsupported practice lab.
8. At **Confirm Install**, the screen stated:

```text
Install ESXi 9.1.0 on mpx.vmhba1:C0:T0:L0
Warning: This disk will be repartitioned.
(F11) Install
```

9. Press **F11** to install. Repartitioning erases the selected virtual disk's previous contents. This was acceptable because this ESXi 9 installation had no guest VMs. Do not apply this choice to a host containing workloads without a preservation plan.
10. Disconnect the installation ISO after completion and reboot when prompted. ISO disconnection was instructed; no separate screenshot confirms that action.

The selected disk was inside the ESXi 9 Workstation VM; this was not a bare-metal installation over Windows. The separate ESXi 8 VM was retained.

Evidence: `image(20261010-162336).png` (Confirm Install).

### 6. Boot the installed system with the legacy option

The installed-system boot screen had an existing line beginning with:

```text
weaselInstalled autoPartition=FALSE bootUUID=...
```

We kept the actual existing text and UUID, and appended:

```text
 allowLegacyCPU=true
```

**Important:** the leading space separates this option from the existing text. Do not type `...` or replace the actual boot UUID. Press **Enter** to boot.

Evidence: `image(20261010-162624).png` (existing options), `image(20261010-162905).png` (appended option checked).

This interactive change applies to that boot. We have **not verified that the option is saved permanently** or that a subsequent unattended restart works without re-entering it. This is a follow-up item for the instructor; no boot.cfg edits were performed in this session.

### 7. Verify console boot after reinstall

The final DCUI screenshot confirmed:

```text
VMware ESXi 9.1.0.0100.25433460 (Release Build)
VMware, Inc. VMware20,1
2 x Intel(R) Core(TM) i5-4430 CPU @ 3.00GHz
8 GiB Memory
https://10.0.0.100/ (DHCP)
```

**Result:** ESXi reached its management console after the reinstall and temporary boot override. RAM reduction took effect. DHCP supplied the management IP; use the currently displayed address if it changes later.

Evidence: `image(20261010-163118).png`.

### 8. Verify browser login and password recovery

Open the displayed local management URL:

```text
https://10.0.0.100
```

Sign in as `root` with the new installation password. The dashboard screenshot proves login succeeded. It reports:

| Field | Observed value |
| --- | --- |
| Hostname | localhost.localdomain |
| Hypervisor | VMware ESXi 9.1.0.0100.25433460 |
| Management IP | 10.0.0.100 |
| Host state | Normal; not connected to any vCenter Server |
| Logical processors | 4 |
| Network interfaces | 2 |
| Memory capacity | 7.999 GB |
| Memory used / free at screenshot | 1.871 GB / 6.128 GB |
| Guest VMs | 0 |
| License banner | License for ESX will expire in 90 days |

The DCUI's “2 x CPU” display must not be treated as proof of only two logical processors. The Host Client explicitly reports **4 logical processors**; a full socket/core topology was not independently checked.

The browser displayed a certificate trust warning indicator. We reached the expected local lab endpoint; certificate deployment and trust configuration were not part of this checkpoint.

Evidence: `image(20261010-163321).png`.

### 9. Verify datastore

Open **Storage → datastore1 → Summary**.

| Field | Observed value |
| --- | --- |
| Name | datastore1 |
| Type | VMFS 6 |
| Hosts | 1 |
| Capacity | 121.75 GB |
| Free | 120.338 GB |
| Used | 1.412 GB |
| Guest VM count in navigation | 0 |

The 250 GB Workstation virtual disk, 121.75 GB VMFS datastore, and physical Windows D: drive are different storage layers. ESXi also allocates disk space to its system partitions. Do not format or create another datastore simply because these capacities differ.

Evidence: `image(20261010-163455).png`.

## Final status and instructor handoff

The user stopped here on **10 October 2026** and will continue with their instructor. The proposed test-VM wizard was not started or verified.

| Milestone | Final status |
| --- | --- |
| Desktop firmware virtualization | Enabled |
| Desktop Windows hypervisor baseline | HypervisorPresent=False |
| Workstation 25H2 installation and signature | Verified |
| ESXi 8 separate VM boot / login / datastore | Verified on 4 October |
| ESXi 9.1 CPU compatibility blocker | Bypassed for this unsupported lab attempt |
| Forgotten root password | Recovered by reinstalling; new root login verified |
| ESXi 9 RAM allocation | 8 GB verified |
| ESXi 9 management IP | 10.0.0.100 via DHCP |
| ESXi 9 datastore | datastore1, VMFS 6, verified |
| Guest VM creation / power-on inside ESXi 9 | Not performed |
| Legacy CPU option survives unattended reboot | Not verified |
| vCenter deployment | Not performed |

Continue with the instructor:

1. Confirm the intended guest OS, resources, and Workstation nested-virtualization processor setting.
2. Create a small guest VM in datastore1 and test power-on. Host boot alone does not prove nested guest execution.
3. Verify persistence of the legacy CPU setting and document any approved persistent configuration.
4. Plan management hostname, IP reservation/static addressing, and licensing as required for the class.

## Repeat-lab checklist

- Inventory the target computer; distinguish the Intel HP desktop, Intel Dell laptop, and ARM Surface.
- Check Windows edition/build, BIOS virtualization, Windows hypervisor status, RAM, and free space.
- Verify the Workstation executable signature before installation.
- Keep application files and VM storage placement separate.
- Use a separate VM for each ESXi version and leave host memory headroom.
- Record the exact installer error; CPU compatibility and Windows VBS are different problems.
- When using a lab override, preserve existing boot text and use spaces between options.
- Record the root password privately before leaving the installer.
- Disconnect the installer ISO and verify boot, browser login, and datastore.
- Test a guest VM before claiming nested virtualization works fully.

## References and scope

- Broadcom CPU Support Deprecation and Discontinuation: https://knowledge.broadcom.com/external/article/318697/cpu-support-deprecation-and-discontinuat.html
- Broadcom lab community discussion of legacy CPU override: https://community.broadcom.com/vmware-cloud-foundation/discussion/issue-with-legacy-cpu-support-setting-not-added-to-bootcfg-fail-deploy-off-full-stack
- Broadcom root password recovery / Host Profile guidance: https://knowledge.broadcom.com/external/article/323617/reset-host-root-password-with-host-profi.html

These notes distinguish observed screenshots and user outputs from instructions, recommendations, and unverified follow-up work. Passwords, product keys, and instructor course files are excluded from this bundle. Do not publish proprietary class materials with the lab documentation.

## Screenshot evidence

The ZIP includes the following reviewed checkpoints under `screenshots/`. Extract the ZIP before opening the Markdown to display these images.

### image(20261004-115217).png

![image(20261004-115217).png](screenshots/image_20261004-115217.png)

### image(20261004-120451).png

![image(20261004-120451).png](screenshots/image_20261004-120451.png)

### image(20261004-120628).png

![image(20261004-120628).png](screenshots/image_20261004-120628.png)

### image(20261004-121018).png

![image(20261004-121018).png](screenshots/image_20261004-121018.png)

### image(20261004-121406).png

![image(20261004-121406).png](screenshots/image_20261004-121406.png)

### image(20261004-121620).png

![image(20261004-121620).png](screenshots/image_20261004-121620.png)

### image(20261004-122318).png

![image(20261004-122318).png](screenshots/image_20261004-122318.png)

### image(20261004-122741).png

![image(20261004-122741).png](screenshots/image_20261004-122741.png)

### image(20261004-123107).png

![image(20261004-123107).png](screenshots/image_20261004-123107.png)

### image(20261004-124651).png

![image(20261004-124651).png](screenshots/image_20261004-124651.png)

### image(20261004-124844).png

![image(20261004-124844).png](screenshots/image_20261004-124844.png)

### image(20261004-125206).png

![image(20261004-125206).png](screenshots/image_20261004-125206.png)

### image(20261010-144906).png

![image(20261010-144906).png](screenshots/image_20261010-144906.png)

### image(20261010-154941).png

![image(20261010-154941).png](screenshots/image_20261010-154941.png)

### image(20261010-161629).png

![image(20261010-161629).png](screenshots/image_20261010-161629.png)

### image(20261010-162104).png

![image(20261010-162104).png](screenshots/image_20261010-162104.png)

### image(20261010-145155).png

![image(20261010-145155).png](screenshots/image_20261010-145155.png)

### image(20261010-155221).png

![image(20261010-155221).png](screenshots/image_20261010-155221.png)

### image(20261010-161309).png

![image(20261010-161309).png](screenshots/image_20261010-161309.png)

### image(20261010-160738).png

![image(20261010-160738).png](screenshots/image_20261010-160738.png)

### image(20261010-162336).png

![image(20261010-162336).png](screenshots/image_20261010-162336.png)

### image(20261010-162624).png

![image(20261010-162624).png](screenshots/image_20261010-162624.png)

### image(20261010-162905).png

![image(20261010-162905).png](screenshots/image_20261010-162905.png)

### image(20261010-163118).png

![image(20261010-163118).png](screenshots/image_20261010-163118.png)

### image(20261010-163321).png

![image(20261010-163321).png](screenshots/image_20261010-163321.png)

### image(20261010-163455).png

![image(20261010-163455).png](screenshots/image_20261010-163455.png)

