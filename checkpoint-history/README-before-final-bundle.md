# Latest checkpoint: ESXi 9.1 management console running

4 October 2026, 02:05 America/Chicago. Separate ESXi 9.1 build 25433460 console at https://192.168.109.129/ (DHCP); browser login pending. ESXi 8 retained at .128. Earlier checkpoints below are historical.

# Latest checkpoint: successful ESXi Host Client root login

4 October 2026, 01:46 America/Chicago. Installation, reboot and management login verified. CEIP dialog open; guest VMs not yet created. Earlier checkpoints below are historical.

# Latest checkpoint: ESXi Host Client reachable

01:44 America/Chicago: login page reached; correct username to root. Authentication pending. Credential-containing screenshot excluded from archive. Earlier checkpoints below are historical.

# Latest checkpoint: installed ESXi first boot verified

4 October 2026, 01:40 America/Chicago. Management URL https://192.168.109.128/ (DHCP). Browser login is next. Earlier status entries below are historical.

# Latest status: ESXi 8.0.3 installation completed successfully

4 October 2026, 01:36 America/Chicago. Disconnect installation ISO and reboot next. First boot and management access pending. The following earlier checkpoint descriptions are historical.

# VMware Home Lab — Study Checkpoint

Current checkpoint: 4 October 2026, 01:21 America/Chicago. Workstation powered on the VM and the ESXi installer booted successfully. ESXi installation remains pending. Earlier sections below preserve historical proposals and results.

**Start with the [structured setup guide](VMware_ESXi_Lab_Setup_Study_Guide.md) for the current repeatable workflow.**

## Index

- [Hardware checks](#hardware-checks)
- [Network and transfers](#network-and-transfers)
- [Windows and application preparation](#windows-and-application-preparation)
- [Installer troubleshooting](#installer-troubleshooting)
- [Evidence](#evidence)

This checkpoint records commands, supplied results, and explanations from our session. Results below are normalized extracts unless a block is explicitly labeled verbatim. The original screenshot files are included unchanged. No passwords are recorded.

## Hardware checks

WHY: Confirm CPU architecture, virtualization, RAM and free storage before allocating a VM.

```powershell
Get-CimInstance Win32_Processor | Select-Object Name, NumberOfCores, NumberOfLogicalProcessors
Get-CimInstance Win32_ComputerSystem | Select-Object Manufacturer, Model, @{Name="RAM_GB";Expression={[math]::Round($_.TotalPhysicalMemory / 1GB, 1)}} | Format-List
Get-Volume | Where-Object DriveLetter | Select-Object DriveLetter, @{Name="Free_GB";Expression={[math]::Round($_.SizeRemaining / 1GB, 1)}}, @{Name="Total_GB";Expression={[math]::Round($_.Size / 1GB, 1)}} | Format-Table -AutoSize
```

| Machine | Supplied results | Decision |
|---|---|---|
| HP desktop 700-074 | i5-4430, 4 cores/4 logical CPUs, 15.9 GB RAM; C: 68.6/223.6 GB free/total; D: 1772.7/1847.4 GB | Initially selected; later switched to Dell |
| Surface Laptop 7 | Snapdragon X1E80100, 12 cores/12 logical CPUs, 15.6 GB RAM; C: 608/951.6 GB | ARM64; not suitable for the planned x86-64 Workstation/ESXi lab |
| Dell Inspiron 14 7430 2-in-1 | i7-1355U, 10 cores/12 logical CPUs, 15.7 GB usable RAM; C: 607/931.9 GB | Current selected host |

HP virtualization was initially Disabled and later confirmed Enabled after BIOS changes. Dell screenshot shows Virtualization Enabled, SSD (NVMe), memory usage 15.0/15.7 GB (96%), and uptime 10:15:50:51. Restart/closing unnecessary applications was advised before running an 8 GB ESXi VM.

Proposed first lab, not yet created: one ESXi 8.0U3e VM, 2 vCPUs, 8 GB RAM, 64 GB installation disk, 100 GB additional growable datastore disk, one small Linux guest. ESXi 9.1 is a later learning phase, subject to compatibility checks. Full four-host VCF/NSX/vCenter diagram exceeds a comfortable 16 GB lab budget.

## Network and transfers

WHY: Identify LAN interfaces, then distinguish connectivity from authentication.

```powershell
ipconfig
Test-NetConnection 10.0.0.213 -Port 22
```

Initial laptop-to-desktop check: laptop Wi-Fi 10.0.0.8/24; desktop Ethernet 10.0.0.213/24; gateway both 10.0.0.1. Desktop VMware adapters: 192.168.38.1 and 192.168.23.1. These were not the LAN addresses used for copying. Ping and TCP initially failed. After SSH setup, TCP succeeded while ping still timed out. Ping uses ICMP; SSH uses TCP 22. Windows packet-count syntax is `ping -n 2`, not Linux `ping -c 2`.

Desktop SSH setup commands (Administrator PowerShell):

```powershell
Get-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
# Install only if NotPresent:
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
Start-Service sshd
Set-Service -Name sshd -StartupType Automatic
if (Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue) {
    Set-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -Enabled True
} else {
    New-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -DisplayName "OpenSSH Server" -Enabled True -Direction Inbound -Protocol TCP -LocalPort 22 -Action Allow
}
```

`ssh krmar@10.0.0.213` reached the host-key prompt, then failed password authentication. Password was forgotten. A separate standard local account was used:

```powershell
$password = Read-Host "Enter a new password for filetransfer" -AsSecureString
New-LocalUser -Name "filetransfer" -Password $password -Description "Home SSH file transfers"
Add-LocalGroupMember -SID "S-1-5-32-545" -Member "filetransfer"
```

Inside desktop SSH session:

```cmd
mkdir C:\Users\filetransfer\Transfers
exit
```

Successful command from first laptop at `PS C:\Linux>`:

```powershell
scp -r .\vmware filetransfer@10.0.0.213:C:/Users/filetransfer/Transfers/
```

All listed transfers reached 100% and the prompt returned without a reported error. Files included tools, VMware installers, Windows ISOs, VCF/NSX/vCenter/Veeam images and course PDFs. Speeds shown for large images ranged roughly 51.7–87.1 MB/s. One empty archive was 0 bytes and accepted as-is. Desktop destination: `C:\Users\filetransfer\Transfers\vmware`.

Moving desktop folder to D: was advised with `Move-Item -Path "C:\Users\filetransfer\Transfers\vmware" -Destination "D:\vmware"`; screenshots subsequently show D:\vmware.

### Surface to Dell transfer

Current supplied LAN results:

| Check | Result |
|---|---|
| Dell Wi-Fi | 10.0.0.140, mask 255.255.255.0, gateway 10.0.0.1 |
| Dell WSLCore adapter | 192.168.192.1/20, no gateway |
| Surface Wi-Fi | 10.0.0.8/24, gateway 10.0.0.1 |
| Surface other/VPN adapter | 10.8.0.4/24, no gateway |
| Dell Get-Command scp | Application scp.exe, version 9.5.6.2 |
| Dell TCP test to Surface | Remote 10.0.0.8:22; source 10.0.0.140; TcpTestSucceeded True |
| Surface Get-Service sshd | Running, OpenSSH SSH Server |
| Surface TCP self-test | True; this alone does not prove access from another PC |
| Surface Test-Path C:\Linux\vmware | True |
| Surface whoami | khalid-laptop\krmar |

`krmar` login again had a password issue. Creation of `filetransfer` on Surface and read access to the source were advised:

```powershell
icacls "C:\Linux\vmware" /grant "${env:COMPUTERNAME}\filetransfer:(OI)(CI)RX" /T
```

RX grants read/execute; OI/CI inheritance applies to files/subdirectories. Run on Surface with administrator rights. Account existence and ACL command results were not supplied, so do not treat these individual operations as separately verified.

Copy command advised on Dell:

```powershell
scp -r filetransfer@10.0.0.8:C:/Linux/vmware C:/
```

User reported done; subsequent Dell listing verifies C:\vmware contains `00 Tool & Application and Operating System`, `02 Training Docs`, `03 Q&A`, `class notes`, and `VMware-Workstation-Full-25H2-24995812.exe` (291114376 bytes).

## Windows and application preparation

Dell screenshot verifies Windows 11 Home 25H2, build 26200.9457, x64-based processor. No separate Windows 11 guest installation has been performed or confirmed.

User reported uninstalling VirtualBox. Installed-apps screenshot showed Workstation 17.6.4 with Uninstall disabled and Modify available. Removal via Modify > Remove was advised; user subsequently restarted, but final uninstall confirmation was not supplied.

The class outline is vSphere 8; the lab diagram uses VCF 9.1. Instructor teaches both. Plan: ESXi 8 first, then 9.1. Preserve explanations and evidence for a future GitHub repository. Do not commit installers, ISOs or virtual disks as study-note source files.

## Installer troubleshooting

Dell recursive search located:

```text
C:\vmware\00 Tool & Application and Operating System\VMeare VCF 9\VMware-Workstation-Full-26H1-25388281.exe
Length: 287670872 bytes
```

Note the actual folder spelling `VMeare VCF 9` from the supplied output. File existence does not verify package integrity.

Screenshot error:

```text
This installation package could not be opened. Contact the application vendor to verify that this is a valid Windows Installer package.
```

Signature check:

```powershell
$installer = Get-ChildItem C:\vmware -Recurse -Filter "VMware-Workstation-Full-26H1*.exe" |
    Select-Object -First 1
Get-AuthenticodeSignature -FilePath $installer.FullName |
    Format-List Status, StatusMessage, @{Name="Signer";Expression={$_.SignerCertificate.Subject}}
```

Latest supplied output (verbatim):

```text
Status        : NotSigned
StatusMessage : The file C:\vmware\00 Tool & Application and Operating
                System\VMeare VCF 9\VMware-Workstation-Full-26H1-25388281.exe is
                not digitally signed. You cannot run this script on the current
                system. For more information about running scripts and setting
                execution policy, see about_Execution_Policies at
                https:/go.microsoft.com/fwlink/?LinkID=135170
Signer        :
```

WHAT: Windows did not find a recognized Authenticode signature for this file. This alone does not establish maliciousness or prove SCP corrupted it. The generic reference to script execution policy is not a reason to change PowerShell execution policy for an EXE.

NEXT: Obtain a fresh installer directly from Broadcom, verify its signature/publisher, then retry. Alternatively compare source/destination SHA256 hashes to establish whether the transfer changed the file; matching hashes do not establish authenticity. Installation remains blocked.

## Evidence

All eight screenshots attached in this conversation are preserved under `screenshots/`. They include source folder listings, Dell hardware/virtualization, old Workstation installed-apps entry, Windows version, and installer error. Original filenames are retained.

Original class documents are included under `class-materials/` for private reference. Before publishing a public GitHub repository, choose which instructor materials may be redistributed and redact personal identifiers visible in screenshots. This archive itself has not been published to GitHub.

The earlier SCP beginner guide is included if available. This checkpoint is a continuing study record, not a claim that ESXi or Workstation 26H1 has been successfully installed.


## Installer investigation and valid alternative — October 3, 2026, 9:04 PM Chicago

### 26H1: path correction and damaged copy

The Surface source folder was `C:\Linux\vmware`. On the Dell, the copied folder is `C:\vmware`. Searching the Surface path on the Dell produced `Cannot find path 'C:\Linux\' because it does not exist.` Changing the filename filter cannot correct a missing directory.

Commands run on Dell:

```powershell
$installer = Get-ChildItem C:\vmware -Recurse -Filter "VMware-Workstation-Full-26H1*.exe" |
    Select-Object -First 1
$installer.FullName
Get-FileHash -Path $installer.FullName -Algorithm SHA256 | Format-List
Get-AuthenticodeSignature -FilePath $installer.FullName |
    Format-List Status, StatusMessage,
        @{Name="Signer";Expression={$_.SignerCertificate.Subject}}
```

Recorded result:

```text
Path: C:\vmware\00 Tool & Application and Operating System\VMeare VCF 9\VMware-Workstation-Full-26H1-25388281.exe
Algorithm: SHA256
Hash: 906B845FEA38C8332EAB0269F63BAC29A2688232944FB986AD73AA83C4490E4B
Status: NotSigned
Signer: [blank]
```

The uploaded EXE was inspected without executing it: size 287670872 bytes; the certificate area contained zeros, and the range from byte 192000000 through the end was all zeros. This establishes damage in this copy beyond the signature failure alone. The copy checked after consulting the instructor's OneDrive had the same SHA256 hash. Matching hashes mean identical file content, not that a file is good. The precise origin of the damage remains unknown. Unblock or execution-policy changes do not repair missing bytes.

User contacted/was given a draft to contact the instructor requesting a replacement or official download link. While waiting, we checked the existing 25H2 installer instead.

### 25H2: signature verified

WHY: Verify the publisher and signed file integrity before running the alternative installer.

WHAT — command actually run:

```powershell
Get-ChildItem "C:\vmware" -Filter "VMware-Workstation-Full-25H2*.exe" |
    Get-AuthenticodeSignature |
    Format-List Path, Status, StatusMessage,
        @{Name="Signer";Expression={$_.SignerCertificate.Subject}}
```

User-provided output:

```text
Path          : C:\vmware\VMware-Workstation-Full-25H2-24995812.exe
Status        : Valid
StatusMessage : Signature verified.
Signer        : CN=Broadcom Inc, O=Broadcom Inc, L=San Jose, S=California, C=US,
                SERIALNUMBER=6610117, OID.2.5.4.15=Private Organization,
                OID.1.3.6.1.4.1.311.60.2.1.2=Delaware,
                OID.1.3.6.1.4.1.311.60.2.1.3=US
```

Meaning: Windows successfully verified the Authenticode signature belonging to Broadcom Inc. This is distinct from checking whether nested ESXi will work on this particular host.

NEXT — proposed, not yet confirmed executed:

```powershell
Start-Process -FilePath "C:\vmware\VMware-Workstation-Full-25H2-24995812.exe" -Verb RunAs
```

Accept the Windows administrator prompt after checking that it identifies Broadcom. Stop at the first installer screen and report its text. Workstation installation and ESXi setup are still pending.


## Workstation installer: Compatible Setup — October 3, 2026, 9:09 PM Chicago

Screenshot shows: “The installer detected that the host has Hyper-V or Device/Credential Guard enabled. Virtual machines will be launched using Windows Hypervisor Platform.” Buttons: Back, Next, Cancel.

Meaning: The installer opened successfully. Windows is currently using its hypervisor/security virtualization features. This is an informational setup screen. Proposed next step: click Next and share the next screen. For nested ESXi, hardware virtualization exposure must be assessed after installation; Windows hypervisor mode can block that requirement. No Windows security or WSL settings have been changed at this step.


## Custom Setup - October 3, 2026, 9:11 PM Chicago

Observed installation destination: `C:\Program Files (x86)\VMware\VMware Workstation\`. Proposed step: keep this default and click Next. This is the application installation folder; we will choose a separate folder for virtual machine files later. Installation completion is not yet confirmed. Screenshot preserved under screenshots/.


## User Experience Settings - October 3, 2026, 9:13 PM Chicago

Screenshot shows both options selected: Check for product updates on startup; Join the VMware Customer Experience Improvement Program. Recommended next step: leave update checking selected, clear the optional Customer Experience Improvement Program checkbox, then click Next. Update checking looks for newer application/components when Workstation starts. The program shares technical information to help improve VMware products; participating is optional for this lab. The changed selections are recommendations, not yet confirmed by the user.


## Installation completed - October 3, 2026, 9:16 PM Chicago

User reported: "Instaaltion done". Workstation installation is complete according to the user. The installer used was the verified Broadcom-signed 25H2 EXE. Exact installed version/build awaits Help > About confirmation. Next: restart Dell if setup requests it; open Workstation and report Help > About. Nested ESXi readiness remains pending because setup detected Windows Hyper-V or Device/Credential Guard. No ESXi VM has been created yet.


## First launch update notification - October 3, 2026, 9:18 PM Chicago

Workstation opens successfully. Screenshot shows Software Updates offering VMware Workstation version 26.0.1, described as Workstation 26H1u1. This is the available update version, not confirmation of the installed version. Buttons: Get More Information, Skip this Version, Remind Me Later. Next step: choose Remind Me Later to close this notification temporarily, then Help > About VMware Workstation and capture installed version/build. No update has been installed or permanently skipped at this step.


## Installed version confirmed - October 3, 2026, 9:19 PM Chicago

About screenshot confirms VMware Workstation Pro 25H2, version 25.0.0.24995812. Host name MKhalidK; memory 16068 MB; Windows 11 Home 64-bit build 26200.9457. Workstation installation and first launch confirmed. Next read-only checks on Dell PowerShell: `Get-CimInstance Win32_ComputerSystem | Select-Object HypervisorPresent`; `Get-CimInstance -Namespace root\Microsoft\Windows\DeviceGuard -ClassName Win32_DeviceGuard | Select-Object VirtualizationBasedSecurityStatus, SecurityServicesConfigured, SecurityServicesRunning | Format-List`. WHY: installer detected Windows hypervisor/security virtualization features, which can interfere with exposing VT-x/EPT to nested ESXi. No security settings changed; await diagnostic output.


## Windows hypervisor diagnostic - October 3, 2026, 9:23 PM Chicago

Actual user output:

```text
HypervisorPresent : True
VirtualizationBasedSecurityStatus : 2
SecurityServicesConfigured : {2}
SecurityServicesRunning : {2}
```

Meaning: Windows hypervisor present; VBS enabled and running; Memory Integrity (HVCI) configured and running. This differs from BIOS virtualization, which should remain enabled for ESXi.

Proposed reversible lab step, not yet executed: close Workstation, run `bcdedit /set hypervisorlaunchtype off` in administrator PowerShell, save work and restart Dell. This prevents the Windows hypervisor from launching; WSL2 and Hyper-V-based Docker workloads cannot run in that boot mode, and VBS/Memory Integrity protections cannot run. It does not uninstall WSL or delete its distributions. Verify after reboot with `Get-CimInstance Win32_ComputerSystem | Select-Object HypervisorPresent`; expected False. If it remains True, investigate before changing more settings.

Undo: `bcdedit /set hypervisorlaunchtype auto`, then restart; check VBS/Memory Integrity status again. Source: https://knowledge.broadcom.com/external/article/389469/virtualized-intel-vtxept-not-supported-o.html and https://learn.microsoft.com/en-us/windows/security/hardware-security/enable-virtualization-based-protection-of-code-integrity .


## Hypervisor still present after restart - October 3, 2026, 9:29 PM Chicago

User reports `bcdedit /set hypervisorlaunchtype off` succeeded and Dell restarted. Actual post-restart output:

```text
HypervisorPresent
-----------------
             True
```

Result: expected False was not reached. Do not claim the hypervisor has stopped. Next read-only checks in administrator PowerShell: `bcdedit /enum '{current}'`; repeat the Win32_DeviceGuard query for VirtualizationBasedSecurityStatus, SecurityServicesConfigured and SecurityServicesRunning. Inspect the active boot entry and current security services before making additional changes. Ask whether Windows Security > Device security > Core isolation details shows Memory integrity On, without changing it yet. ESXi creation remains pending.


## Boot setting confirmed Off, VBS still running - October 3, 2026, 9:31 PM Chicago

User output confirms current Windows Boot Loader has `hypervisorlaunchtype Off`. DeviceGuard still reports:

```text
VirtualizationBasedSecurityStatus : 2
SecurityServicesConfigured        : {2}
SecurityServicesRunning           : {2}
```

Other boot-loader values: device/osdevice partition=C:, path Windows system32 winload.efi, description Windows 11, locale en-US, recoveryenabled Yes, isolatedcontext Yes, nx OptIn, bootmenupolicy Standard. Boot/recovery identifiers omitted here as they are not necessary for troubleshooting.

Meaning: the boot configuration changed as requested, but runtime VBS/Memory Integrity remain active. The exact cause is not yet established. Next proposed step: Windows Security > Device security > Core isolation details > Memory integrity Off, then restart. This removes that kernel protection while disabled. Keep BIOS virtualization and Secure Boot enabled. After restarting, repeat HypervisorPresent and DeviceGuard queries; target HypervisorPresent False and VBS status other than 2. If still active, inspect additional configuration before more changes. Undo: set hypervisorlaunchtype auto, turn Memory integrity back On and restart; verify services running.


## Second restart: unchanged runtime result - October 3, 2026, 9:36 PM Chicago

User reports a restart after the Memory Integrity instructions. Output remains:

```text
HypervisorPresent : True
VirtualizationBasedSecurityStatus : 2
SecurityServicesConfigured : {2}
SecurityServicesRunning : {2}
```

Memory Integrity switch change was not independently confirmed. Do not assume it stayed Off or that a policy caused this. Next: capture Windows Security > Device security > Core isolation details screenshot. Read optional Windows feature states using administrator PowerShell:

```powershell
Get-WindowsOptionalFeature -Online |
    Where-Object FeatureName -Match 'Hyper-V|VirtualMachinePlatform|HypervisorPlatform|Containers-DisposableClientVM' |
    Select-Object FeatureName, State |
    Format-Table -AutoSize
```

WHY: inspect persistent UI setting and installed Windows hypervisor components before choosing more changes. This query changes nothing. No additional registry or security changes performed. Nested ESXi setup remains pending.


## Windows optional features - October 3, 2026, 9:38 PM Chicago

Actual output:

```text
FeatureName              State
VirtualMachinePlatform Enabled
HypervisorPlatform     Enabled
```

These enabled components are relevant to the Windows hypervisor conflict; their presence alone does not explain why the boot Off setting failed. Memory Integrity UI screenshot is still pending.

Proposed next commands in administrator PowerShell (not yet confirmed executed):

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform,HypervisorPlatform -NoRestart
bcdedit /set '{current}' hypervisorlaunchtype off
```

Then check Memory Integrity is Off, save work and restart Dell. WSL2 and applications relying on these Windows virtualization components will be unavailable while disabled; WSL distributions are not deleted. Keep BIOS virtualization and Secure Boot enabled. Repeat HypervisorPresent/DeviceGuard queries after reboot. If hypervisor remains active, gather policy/registry state rather than claim success.

Restore components later if needed:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform,HypervisorPlatform -All -NoRestart
bcdedit /set '{current}' hypervisorlaunchtype auto
```

Restore Memory Integrity On and restart. Source: Broadcom KB389469, https://knowledge.broadcom.com/external/article/389469/virtualized-intel-vtxept-not-supported-o.html .


## Restart result and Android subsystem message - October 3, 2026, 9:50 PM Chicago

User post-restart output still shows HypervisorPresent True. Screenshot: Windows Subsystem for Android could not start; asks for BIOS virtualization and Virtual Machine Platform enabled. This is consistent with disabling Virtual Machine Platform for the ESXi lab, but does not establish the current optional-feature states. Close Android message with OK; do not change BIOS virtualization. Windows hypervisor remains active and nested ESXi readiness is not confirmed.

Next gather, without changing settings: current boot entry via `bcdedit /enum '{current}'`; VirtualMachinePlatform/HypervisorPlatform optional feature states; full Win32_DeviceGuard runtime query used previously. Request screenshot of Windows Security > Device security > Core isolation details, since Memory Integrity Off has not been visually confirmed. Need determine whether boot Off persists, optional features disabled successfully, and HVCI remains active before registry or policy changes.


## Features disabled; boot Off entry missing - October 3, 2026, 10:01 PM Chicago

Actual diagnostic results:

```text
VirtualMachinePlatform Disabled
HypervisorPlatform     Disabled
VirtualizationBasedSecurityStatus : 2
SecurityServicesConfigured        : {2}
SecurityServicesRunning           : {2}
```

The current Windows Boot Loader listing contains no hypervisorlaunchtype line, whereas an earlier listing explicitly showed Off. Therefore Off is not explicitly confirmed in this latest output. Cause of the missing setting is unknown. Do not assume Windows reset it or that a policy did so. Memory Integrity still runs despite optional components being disabled; disabling those components alone did not stop VBS.

Next: inspect the still-unprovided Core isolation details screenshot. Open via administrator/normal PowerShell `Start-Process 'windowsdefender://coreisolation'`, or navigate Windows Security > Device security > Core isolation details. Capture Memory integrity toggle and any administrator/managed-setting message. No further restart or configuration changes requested until screen is inspected. All prior undo instructions remain in this record.


## Memory Integrity visually confirmed On - October 3, 2026, 10:05 PM Chicago

Screenshot of Core isolation shows Memory integrity On (blue toggle), kernel-mode hardware-enforced stack protection Off. No managed-setting message is visible near the Memory Integrity toggle. This confirms the Memory Integrity setting currently remains On; it does not establish whether it was previously changed or reverted.

Next proposed steps: click the Memory integrity toggle to Off, approve the Windows prompt if shown, verify the label says Off. Run `bcdedit /set '{current}' hypervisorlaunchtype off` in administrator PowerShell and `bcdedit /enum '{current}'` to confirm the Off entry before restarting. Save work, restart, then rerun HypervisorPresent and DeviceGuard checks. Memory Integrity protection is reduced while disabled. Keep BIOS virtualization and Secure Boot enabled. Other security switches need no changes at this step. Workstation/ESXi readiness remains pending.


## Boot setting reapplied and confirmed before restart - October 3, 2026, 10:09 PM Chicago

Actual commands:

```powershell
bcdedit /set '{current}' hypervisorlaunchtype off
bcdedit /enum '{current}'
```

Output: `The operation completed successfully.` Current Windows Boot Loader explicitly shows `hypervisorlaunchtype Off`. User is going to restart. Memory Integrity toggle Off has been instructed; user has not separately supplied its changed screenshot. Post-restart runtime state remains pending. Next run HypervisorPresent query and report result; expect False, without assuming success in advance.


## Hypervisor still True after confirmed boot change - October 3, 2026, 10:14 PM Chicago

User reports another restart; HypervisorPresent remains True. Attached screenshot again shows Windows Subsystem for Android could not start, requiring Virtual Machine Platform and BIOS virtualization. This Android startup error is separate from verifying nested ESXi readiness; no BIOS change or re-enabling VMP requested.

Next gather read-only diagnostic evidence instead of repeating restarts: active boot hypervisorlaunchtype/vsmlaunchtype, DeviceGuard runtime services, and registry configuration/policy for VBS and HVCI. Ask user explicitly whether Memory Integrity toggle reads Off after this restart; screenshot if possible. Cause still unknown, do not claim enforced policy or UEFI lock without evidence. Registry keys may be absent; missing-key errors are useful evidence. No registry changes requested.


## Memory Integrity disabled; VBS remains running - October 3, 2026, 10:17 PM Chicago

Actual results:

```text
hypervisorlaunchtype Off
VirtualizationBasedSecurityStatus : 2
SecurityServicesConfigured : {0}
SecurityServicesRunning : {0}
DeviceGuard: CachedDrtmAuthIndex DWORD 0; RequireMicrosoftSignedBootChain DWORD 1
HypervisorEnforcedCodeIntegrity: Enabled DWORD 0; ChangedInBootCycle QWORD 0x1dd53aa96786040
Policy DeviceGuard key: ERROR: The system was unable to find the specified registry key or value.
```

Memory Integrity is now disabled; no services from the DeviceGuard security-service enumeration are reported configured/running. VBS still reports running. No policy key at this inspected location; this does not rule out every other source of configuration. EnableVirtualizationBasedSecurity value is absent in the queried system DeviceGuard key.

Next proposed documented step (not yet executed): backup DeviceGuard registry key to C:\vmware\DeviceGuard-before-VBS-change.reg using reg export, then set EnableVirtualizationBasedSecurity DWORD 0 via reg add, retain hypervisorlaunchtype Off, restart and repeat runtime queries. If backup fails, stop before registry mutation. Do not alter RequireMicrosoftSignedBootChain or BIOS/Secure Boot settings. VBS protection remains unavailable while disabled; WSL2 features previously disabled.

Undo for this newly created value (originally absent): reg delete HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard /v EnableVirtualizationBasedSecurity /f. Restore earlier hypervisor/features/Memory Integrity using prior restore steps and restart. Source Broadcom KB389469 Phase 2 registry setting.


## VBS change commands succeeded - October 3, 2026, 10:21 PM Chicago

Actual user commands and outputs:

```powershell
reg export "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard" "C:\vmware\DeviceGuard-before-VBS-change.reg"
# The operation completed successfully.
reg add "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard" /v EnableVirtualizationBasedSecurity /t REG_DWORD /d 0 /f
# The operation completed successfully.
bcdedit /set '{current}' hypervisorlaunchtype off
# The operation completed successfully.
```

Backup exists on user's Dell at the specified path according to command success; it has not been uploaded here. User is restarting. Configuration changes succeeded; post-restart runtime effect is still unconfirmed. Next: query HypervisorPresent and report result; expected False. If True, do not repeat unchanged steps without further evidence.


## VBS registry change did not stop hypervisor - October 3, 2026, 10:26 PM Chicago

After another reported restart, actual HypervisorPresent result remains True. Do not claim resolved. Next verify persistence and runtime: current boot entry, EnableVirtualizationBasedSecurity value, Win32_DeviceGuard runtime statuses, and System Information (msinfo32) virtualization-based security rows. No further mutation/restart requested yet. Need establish whether configuration persists before assessing Virtual Secure Mode boot setting or other VBS sources.


## Instructor suggests Windows edition change - October 3, 2026, 10:31 PM Chicago

User reports instructor says lab will not work on Windows Home and shared a Windows file. Exact filename/edition and whether host upgrade or guest installation is intended are unknown. Workstation itself is already installed and launches on Home. Broadcom host compatibility lists Windows 11; current evidence establishes VBS remains active, not that Home alone is the cause or Pro upgrade would fix it. Instructor may require Pro for classroom consistency/tools.

Actual results: hypervisorlaunchtype Off; EnableVirtualizationBasedSecurity DWORD 0; VBS status 2; security services configured/running {0}. Settings persisted but runtime VBS still runs. Screenshot confirms Windows 11 Home build26200, Dell Inspiron14 7430, i7-1355U, UEFI, Secure Boot On, BIOS1.30.0 dated6/29/2026; screenshot does not show lower virtualization-based-security rows.

Next: ask exact Windows filename and intended edition/host-versus-guest use before installation. No reinstall or disk formatting authorized/performed. ISO availability alone does not establish Pro activation entitlement. Preserve personal files and lab data when choosing upgrade path. Sources: Broadcom KB315653 host OS compatibility and KB389469 VBS conflicts.


## Instructor Windows ISO identified by screenshot - October 3, 2026, 10:53 PM Chicago

Screenshot folder: C:\vmware\00 Tool & Application and Operating System\Windows Server. Highlighted filename begins en-us_windows_11_iot_enterprise_version_25...; prior transfer listing supplies full name en-us_windows_11_iot_enterprise_version_25h2_x64_dvd_67098cd6.iso. Filename indicates Windows 11 IoT Enterprise 25H2 x64, not Windows 11 Pro. Screenshot also contains Windows Server2025 and Server2016 ISO entries. Actual ISO contents/signature/source not inspected; edition identification is based on filename.

Windows IoT Enterprise is intended for fixed-purpose devices and has specific licensing; do not treat instructor ISO possession as activation entitlement or assume installing it resolves VBS. Next clarify whether instructor intends replacing the Dell host OS with this edition or using Windows as a VM for class. No host reinstall, formatting or ISO execution performed. Suggested instructor question: Is Windows 11 IoT Enterprise 25H2 intended for my laptop host, or a virtual machine? Can I use Windows 11 Pro instead, and what license is required?


## ESXi virtual machine creation — screenshot checkpoint, 4 October 2026

The screenshots show VM creation and configuration only. ESXi installation and successful nested virtualization have not yet been confirmed.

### Observed steps and settings

1. Opened the New Virtual Machine wizard and selected Typical.
2. Selected “I will install the operating system later,” creating the VM before attaching the installation ISO.
3. Selected VMware ESX with the “VMware ESXi 9 and later” guest profile.
4. Named the VM `VMware ESXi 8` and stored it at `C:\vmware\VMware ESXI-01`. Its configuration file is `VMware ESXi 8.vmx`.
5. Created a 250 GB virtual disk stored as a single file. The wizard says the disk files start small and grow; 250 GB is its maximum capacity, not necessarily its initial physical size.
6. The initial summary showed 6 GB RAM, 2 CPU cores, NAT networking, and Workstation 25H2 compatibility.
7. Changed RAM to 16384 MB and processors to 2 processors with 4 cores each (8 virtual CPUs).
8. Checked “Virtualize Intel VT-x/EPT or AMD-V/RVI.” This exposes virtualization capabilities to the ESXi guest for running nested VMs, provided the host can supply them.
9. Attached `VMware-VMvisor-Installer-8.0U3e-24677879.x86_64.iso` to the virtual SATA CD/DVD drive and checked “Connect at power on.”
10. Opened Windows Network Connections with `ncpa.cpl`. VMnet1 and VMnet8 appeared enabled; the Dell uses Wi-Fi. A WSLCore virtual adapter was also present.
11. Selected Host-only networking and added adapters until five network adapters were shown, all Host-only.
12. The final powered-off VM showed 16 GB RAM, 8 virtual CPUs, a 250 GB disk, the attached ISO, and five Host-only adapters.

### Corrections before first power-on

- Memory: change 16384 MB to **8192 MB (8 GB)**. This Dell has about 15.7 GB usable RAM, so assigning 16 GB to the VM leaves insufficient memory for Windows and Workstation. Eight GB is a starting allocation for a small single-host learning lab, not a full VCF environment.
- Processors: start with **1 processor and 2 cores per processor (2 virtual CPUs)**. Increase later if the exercise requires it and host resources allow. Keep the nested virtualization checkbox selected.
- Guest profile: under Options → General, choose the ESXi 8 entry to match the ESXi 8.0 U3e installation ISO. The existing VM can be edited; it need not be recreated solely because Typical was used.
- Disk: retain the 250 GB growable disk for this lab.
- Networking: five Host-only adapters connect to the same default Host-only network; they do not automatically create five separate networks. Retain them if required by the instructor’s exercise. Host-only supports host-to-guest communication; access from the Surface or to the Internet needs additional networking configuration.
- Host virtualization remains unresolved: previous output still showed HypervisorPresent=True and VBS status=2. Creating the VM does not demonstrate that ESXi or nested guest VMs can start successfully. Record the exact power-on result next.

### Screenshots

- [Screenshot image(20261004-050123)](screenshots/image(20261004-050123).png)
- [Screenshot image(20261004-050227)](screenshots/image(20261004-050227).png)
- [Screenshot image(20261004-050359)](screenshots/image(20261004-050359).png)
- [Screenshot image(20261004-050602)](screenshots/image(20261004-050602).png)
- [Screenshot image(20261004-050733)](screenshots/image(20261004-050733).png)
- [Screenshot image(20261004-050818)](screenshots/image(20261004-050818).png)
- [Screenshot image(20261004-050859)](screenshots/image(20261004-050859).png)
- [Screenshot image(20261004-051134)](screenshots/image(20261004-051134).png)
- [Screenshot image(20261004-051223)](screenshots/image(20261004-051223).png)
- [Screenshot image(20261004-051544)](screenshots/image(20261004-051544).png)
- [Screenshot image(20261004-051659)](screenshots/image(20261004-051659).png)
- [Screenshot image(20261004-051930)](screenshots/image(20261004-051930).png)
- [Screenshot image(20261004-052048)](screenshots/image(20261004-052048).png)
- [Screenshot image(20261004-052148)](screenshots/image(20261004-052148).png)
- [Screenshot image(20261004-052626)](screenshots/image(20261004-052626).png)
- [Screenshot image(20261004-052814)](screenshots/image(20261004-052814).png)


## First power-on attempts: failure persists after RAM correction

Screenshots 052949, 053204, and 053256 show:
- First attempt at 16 GB RAM / 8 virtual CPUs: “Failed to start the virtual machine.”
- RAM was successfully reduced to 8 GB; processors remained at 8.
- Another attempt at 8 GB RAM also failed with the same generic message.
- The VM remains powered off. This message alone does not identify the cause. Earlier HypervisorPresent=True / VBS status=2 remains a possible contributor, not a confirmed diagnosis.

Next: set processors to 1 processor × 2 cores, retain nested virtualization, and collect the VM log before further Windows changes. Run on the Dell:

```powershell
Get-Content -LiteralPath "C:\vmware\VMware ESXI-01\vmware.log" -Tail 100
```

If the log does not exist, list the VM directory to locate logs:

```powershell
Get-ChildItem -LiteralPath "C:\vmware\VMware ESXI-01" -Force |
    Select-Object Name, Length, LastWriteTime
```

- [Screenshot 052949](screenshots/image(20261004-052949).png)
- [Screenshot 053204](screenshots/image(20261004-053204).png)
- [Screenshot 053256](screenshots/image(20261004-053256).png)


## Log diagnosis: ULM partition initialization failure

The full supplied 100-line log excerpt is preserved in `evidence/power-on-log-20261004-053504.txt`. Key lines:

```text
ULM: Failed to set up partition, res 0xc0350005.
Module 'ULM' power on failed.
```

This locates the failure in Workstation's ULM startup path, before ESXi installation begins. Broadcom documents ULM as the mode used when operating through the Windows hypervisor. Together with earlier HypervisorPresent=True / VBS status=2, this points toward the host virtualization path, but the excerpt does not establish why the partition setup failed. Do not label the numeric error as a specific cause without further evidence.

The log also names guest `vmkernel9`, confirming the guest profile still references ESXi 9 although the attached installer is ESXi 8. Correct the profile separately. VMware Tools image messages occur after the ULM power-on failure and do not demonstrate a bad ESXi installation ISO.

Next read-only diagnostic on the Dell:

```powershell
Select-String -LiteralPath "C:\vmware\VMware ESXI-01\vmware.log" `
    -Pattern 'Monitor Mode|ULM|WHP|Hyper-V|VBS|VT-x|EPT|unsupported|error|fail' `
    -Context 3,3
```

Why: the earlier part of the log may contain the monitor selection and the underlying error omitted by the last 100 lines. Avoid further registry edits or a Windows reinstall until this evidence is reviewed.

Reference: https://blogs.vmware.com/cloud-foundation/2020/05/28/vmware-workstation-now-supports-hyper-v-mode/


## Expanded log: Windows hypervisor detected; next VSM boot test

Original output preserved in `evidence/filtered-log-20261004-053933.txt`. At the same 05:32:30 UTC startup attempt, the log explicitly shows `IOPL_Init: Hyper-V detected by CPUID`, `Monitor Mode: ULM`, and the ULM partition failure. This confirms Workstation selected the Windows-hypervisor path during that attempt, but does not establish why previous disabling settings failed to take effect.

Next controlled test: explicitly disable Virtual Secure Mode launch, a setting not previously applied in the recorded session. Microsoft documents vsmlaunchtype Off/Auto as controlling Virtual Secure Mode launch. This is a test, not a guaranteed fix; disabling VSM reduces virtualization-based protection. Existing disabled features also affect WSL2.

On the Dell, in administrator PowerShell:

```powershell
bcdedit /enum '{current}' | Out-File -FilePath "C:\vmware\BCD-before-VSM-test.txt"
bcdedit /set '{current}' vsmlaunchtype off
bcdedit /set '{current}' hypervisorlaunchtype off
bcdedit /enum '{current}'
```

If commands succeed, save work and use Windows Restart. Then:

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object HypervisorPresent
Get-CimInstance -Namespace root\Microsoft\Windows\DeviceGuard -ClassName Win32_DeviceGuard |
    Select-Object VirtualizationBasedSecurityStatus, SecurityServicesRunning | Format-List
```

Desired state: HypervisorPresent=False and VBS status not 2 (preferably 0). Do not assume success until outputs confirm it. Keep firmware virtualization enabled.

Undo the newly added VSM override, which was absent from the prior recorded boot entry:

```powershell
bcdedit /deletevalue '{current}' vsmlaunchtype
```

Full restoration of prior security settings also requires the earlier feature, VBS, memory-integrity, and hypervisor-launch restoration steps in these notes.

Microsoft reference: https://learn.microsoft.com/en-us/windows-hardware/drivers/devtest/bcdedit--set


## VSM boot test applied — restart pending (4 October 2026, 00:43 Chicago)

User ran the boot-entry backup and both BCDEdit changes. Both returned “The operation completed successfully.” The current Windows 11 boot entry explicitly shows:

```text
hypervisorlaunchtype    Off
vsmlaunchtype           Off
```

The backup command produced no reported error: `C:\vmware\BCD-before-VSM-test.txt`. These outputs confirm the configured values, not the runtime state after restart. User is restarting; post-restart HypervisorPresent and DeviceGuard results remain pending.


## VSM test after restart — hypervisor still detected (00:46 Chicago)

User supplied:

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object HypervisorPresent
Get-CimInstance -Namespace root\Microsoft\Windows\DeviceGuard `
    -ClassName Win32_DeviceGuard |
    Select-Object VirtualizationBasedSecurityStatus, SecurityServicesRunning | Format-List
```

```text
HypervisorPresent : True
VirtualizationBasedSecurityStatus : 2
SecurityServicesRunning : {0}
```

The new VSM boot test did not achieve the desired runtime state. VBS status 2 still reports running; {0} does not negate that status or establish which component is keeping the hypervisor active. Do not infer Windows Home is the cause or promise an edition upgrade will fix it.

Next, read the current boot entry again using `bcdedit /enum '{current}'` to verify that both Off settings persisted. Open `msinfo32`, select System Summary and capture the bottom section containing virtualization-based security fields and hypervisor information. Use this evidence before considering further Windows configuration changes. No additional security setting changes are prescribed at this checkpoint.


## System Information checkpoint — policy clue (00:50 Chicago)

After restart, BCDEdit still shows hypervisorlaunchtype Off and vsmlaunchtype Off. Screenshots confirm Windows 11 Home build 26200, Dell Inspiron 14 7430 2-in-1, x64 Intel i7-1355U, UEFI and Secure Boot On.

Security section shows:
- Virtualization-based security: Running
- App Control for Business policy: Enforced
- App Control for Business user mode policy: Off
- Kernel DMA Protection: On
- A hypervisor has been detected
- VBS services configured/running rows appear blank

This adds an enforced App Control policy as an investigation clue. It does not prove that policy is causing persistent VBS or identify its origin. Do not delete policy files, change Secure Boot, or disable Smart App Control based on this screenshot alone. Next inspect Windows Security → App & browser control → Smart App Control settings and capture the current On/Evaluation/Off state without changing it.

Memory snapshot: 16.0 GB installed, 15.7 GB usable, only 3.73 GB available, pagefile 36 GB. Close unnecessary applications before subsequent VM tests; the ULM error remains the observed startup failure.

- [System Information 054943](screenshots/image(20261004-054943).png)
- [System Information 055015](screenshots/image(20261004-055015).png)

## Smart App Control observed Off (00:53 Chicago)

Screenshot `image(20261004-055323).png` shows Smart App Control Off selected. No switch change was requested or established by this screenshot. Thus the earlier “App Control for Business policy: Enforced” entry cannot be assumed to mean Smart App Control is On. Its policy identity remains unknown.

Next read-only command in administrator PowerShell on the Dell:

```powershell
CiTool.exe -lp
```

Microsoft documents -lp / --list-policies as listing all policies, including inactive ones. Review Friendly Name, Platform Policy, and Is Currently Enforced for each entry. This lists information; it does not remove or change policy. An enforced Microsoft platform policy may be ordinary driver protection; its presence alone does not prove the cause of persistent VBS.

Reference: https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/operations/citool-commands


## CiTool policy results (00:55 Chicago)

User ran `CiTool.exe -lp`, ending Operation Successful. All 15 entries reported Platform Policy=true, Signed=true, Status=0. Enforced/authorized entries:

| ID | Friendly name | Version | File on disk |
|---|---|---|---|
| 784c4414-79f4-4c32-a6a5-f0fb42a51d0d | Microsoft Windows Cross Certificates for Code Integrity Exceptions Audit Policy | 10.29611.0.0 | true |
| d2bda982-ccf6-4344-ac5b-0b44427b6816 | Microsoft Windows Driver Policy | 10.0.29520.0 | false |
| 8f9cb695-5d48-48d6-a329-7202b44607e3 | Microsoft Windows Cross Certificates for Code Integrity Exceptions Policy | 10.29611.0.0 | true |
| a072029f-588b-4b5e-b7f9-05aad67df687 | Microsoft Windows Virtualization Based Security Policy | 10.0.29657.0 | true |
| 60fd87f8-4593-44a0-91b0-2e0da022f248 | Microsoft Windows Endpoint Security Policy | 10.0.29526.0 | true |

Other entries not enforced/authorized: VerifiedAndReputableDesktop, VerifiedAndReputableDesktopEvaluation, WindowsE_Lockdown_Policy, WindowsE_Lockdown_Test_Policy_Supplemental, Windows10S_Lockdown_Policy_Supplementable, VerifiedAndReputableDesktopFlightSupplemental, VerifiedAndReputableDesktopEvaluationFlightSupplemental, WindowsE_Lockdown_Flight_Policy_Supplemental, VerifiedAndReputableDesktopTestSupplemental, VerifiedAndReputableDesktopEvaluationTestSupplemental. All these report file on disk true. This section is a structured transcription of the user output.

The VBS policy is active, but its name alone does not prove it starts VBS or overrides BCDEdit. Smart App Control base and evaluation policies are inactive, consistent with the Off screenshot. Do not remove built-in signed platform policies based only on this list.

Next read-only evidence: Windows boot event 153, which may state the VBS enablement source.

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    ProviderName = 'Microsoft-Windows-Kernel-Boot'
    Id = 153
} -MaxEvents 3 | Format-List TimeCreated, Id, Message
```

If no matching events are found, preserve the error; no restart or policy change is required for this read.


## Kernel-Boot event 153: VBS registry configuration (00:58 Chicago)

User command:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    ProviderName = 'Microsoft-Windows-Kernel-Boot'
    Id = 153
} -MaxEvents 3 | Format-List TimeCreated, Id, Message
```

Supplied results:

```text
TimeCreated : 10/4/2026 12:43:50 AM
Id          : 153
Message     : Virtualization-based security (policies: VBS Enabled,VSM
              Required,Boot Chain Signer Soft Enforced) is enabled due to VBS
              registry configuration.

TimeCreated : 10/3/2026 10:09:44 PM
Id          : 153
Message     : Virtualization-based security (policies: VBS Enabled,VSM
              Required,Boot Chain Signer Soft Enforced) is enabled due to VBS
              registry configuration.

TimeCreated : 10/3/2026 9:46:42 PM
Id          : 153
Message     : Virtualization-based security (policies: VBS Enabled,VSM
              Required,Hvci,Boot Chain Signer Soft Enforced) is enabled due to VBS
              registry configuration.
```

The latest returned boot event explicitly attributes VBS enablement to registry configuration, but does not identify an individual key or value. HVCI appears in the older event and is absent from the two newer messages, consistent with the earlier Memory Integrity change. This does not prove that the active VBS code-integrity policy caused the boot behavior.

Next read-only registry inventory on the Dell:

```powershell
reg query "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard" /s
reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows\DeviceGuard" /s
reg query "HKLM\SOFTWARE\Microsoft\PolicyManager\current\device\DeviceGuard" /s
```

Purpose: show scenario settings and possible policy-delivered settings not included in the earlier root-only query. Missing keys are valid diagnostic results. No values are changed by these commands.


## Recursive DeviceGuard inventory: Windows Hello scenario enabled (00:59 Chicago)

Recorded values from user output:

```text
DeviceGuard root:
  CachedDrtmAuthIndex=0
  RequireMicrosoftSignedBootChain=1
  EnableVirtualizationBasedSecurity=0
Scenarios\HypervisorEnforcedCodeIntegrity:
  Enabled=0
  ChangedInBootCycle=0x1dd53aa96786040
Scenarios\KernelShadowStacks:
  AuditModeEnabled=0
  Enabled=0
Scenarios\KeyGuard\Status:
  IsSecureKernelRunning=1
  EncryptionKeyAvailable=1
  EncryptionKeyPersistent=1
  SecretsMode=1
  IsTestConfig=0
  KeyGuardEnabled=1
  CredGuardEnabled=0
  LsaIsoLaunchAttempted=1
  LsaIsoLaunchError=0
  ExecSystemProcessesError=0
Scenarios\WindowsHello:
  Enabled=1
SOFTWARE\Policies\Microsoft\Windows\DeviceGuard: key not found
SOFTWARE\Microsoft\PolicyManager\current\device\DeviceGuard: key not found
```

Windows Hello is the remaining explicitly enabled scenario in this inventory. KeyGuard status reports the secure kernel running. This makes Windows Hello a candidate for investigation, not a proven cause. Microsoft documents Windows Hello Enhanced Sign-in Security (ESS) as using VBS and TPM 2.0.

Next supported UI test: Settings → Accounts → Sign-in options → Additional settings → Enhanced sign-in security. If an On/Off toggle exists and is On, temporarily turn it Off to test (reduces additional biometric protection), then save work and restart. This is reversible through the same toggle. Do not remove PIN, fingerprint enrollment, or credentials. If toggle absent/already Off, capture screenshot before another change. On 24H2 or newer Microsoft says toggle availability depends on relevant sensors.

After restart, repeat HypervisorPresent and DeviceGuard checks; success remains unconfirmed.

Primary source: https://support.microsoft.com/en-us/windows/security/identity-signin/enhanced-sign-in-security-in-windows


## Sign-in options screenshot (01:06 Chicago)

The displayed section shows PIN controls, Security key, and the On toggle for only allowing Windows Hello sign-in for Microsoft accounts. That toggle is not Enhanced sign-in security. ESS is not visible in this screenshot. Next: scroll farther down the Additional settings section and capture the remaining options. Leave existing controls unchanged and do not remove the PIN.


## ESS toggle absent; targeted WindowsHello scenario test proposed (01:08 Chicago)

Screenshots show all Additional settings and the bottom of Sign-in options. Enhanced sign-in security is absent, so no visible toggle should be selected. Keep the Hello-only Microsoft-account sign-in toggle and PIN unchanged.

Because recursive registry evidence shows WindowsHello Enabled=1 while other checked VBS settings are disabled, the next proposed diagnostic is a reversible change of that one scenario value. This is a troubleshooting hypothesis, not an officially documented ESS toggle equivalent or guaranteed fix. It may affect Windows Hello protection or availability. Before restarting, user should know their Windows account password and have Password available under sign-in options as a fallback. If no working password fallback, establish it before the test.

Administrator PowerShell on Dell (run the change only after backup succeeds):

```powershell
reg export "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\WindowsHello" "C:\vmware\WindowsHello-before-test.reg"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\WindowsHello" /v Enabled /t REG_DWORD /d 0 /f
```

Save work and restart; then verify:

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object HypervisorPresent
Get-CimInstance -Namespace root\Microsoft\Windows\DeviceGuard -ClassName Win32_DeviceGuard |
    Select-Object VirtualizationBasedSecurityStatus, SecurityServicesRunning | Format-List
```

Restore the prior scenario value if needed:

```powershell
reg import "C:\vmware\WindowsHello-before-test.reg"
```

Restart after restoration. This only restores the WindowsHello scenario; full restoration of earlier VBS/security changes is recorded separately. Results of this test are pending.

- [Screenshot 060817](screenshots/image(20261004-060817).png)
- [Screenshot 060842](screenshots/image(20261004-060842).png)

## WindowsHello scenario test applied (01:11 Chicago)

User ran:

```powershell
reg export "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\WindowsHello" "C:\vmware\WindowsHello-before-test.reg"
reg add "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\WindowsHello" /v Enabled /t REG_DWORD /d 0 /f
```

Both commands returned `The operation completed successfully.` Backup succeeded before the change. User is restarting. Runtime effect and sign-in behavior are not yet confirmed; repeat HypervisorPresent and DeviceGuard status after restart before testing ESXi power-on.


## Successful post-restart verification (4 October 2026, 01:15 Chicago)

After the WindowsHello scenario Enabled value was changed from 1 to 0 and Windows restarted, user supplied:

```text
HypervisorPresent
-----------------
            False

VirtualizationBasedSecurityStatus : 0
SecurityServicesRunning           : {0}
```

This confirms that the Windows hypervisor is no longer reported present and VBS reports disabled at this checkpoint. The WindowsHello scenario change was the final change preceding this result, alongside the earlier disabled features, VBS registry setting, Memory Integrity setting, and boot launch settings. It is evidence for this machine, not a universal Windows Hello workaround or proof that Windows Home is incompatible with Workstation.

Do not confuse HypervisorPresent=False with firmware virtualization being disabled: leave Intel VT-x enabled in BIOS. ESXi nested virtualization still needs the Workstation processor option checked.

Next Dell VM test:
1. Reopen VMware Workstation.
2. Verify 8 GB RAM, 1 processor × 2 cores (2 vCPU), and Virtualize Intel VT-x/EPT or AMD-V/RVI checked.
3. Set the guest OS version to the ESXi 8 entry to match the attached ESXi 8.0 U3e ISO; CD/DVD Connect at power on should remain checked.
4. Power on the VM and capture the resulting screen or full error.

VM startup, ESXi installation, and nested guest operation remain unverified until tested. If it starts, the vmware.log monitor-mode check can confirm the new execution path. The prior WindowsHello backup and all earlier rollback steps remain in the notes. VBS security protections and Hyper-V-dependent workloads such as WSL2 remain affected by the current lab configuration.


## ESXi VM successfully powered on — installer boot (01:17 Chicago)

Screenshot confirms the VM is running and the attached ESXi installer is booting:

```text
VMware ESXi 8.0.3 (VMKernel Release Build 24677879)
VMware, Inc. VMware20,1
2 x 13th Gen Intel(R) Core(TM) i7-1355U
8 GiB Memory
```

This verifies successful Workstation VM power-on and ESXi installer boot after the WindowsHello scenario test and preceding changes. It does not yet confirm completed installation, network configuration, or nested workload operation.

A Workstation “Removable Devices” informational hint overlays the console; this is not the earlier startup error. Next click OK (optionally check Do not show this hint again) and allow the installer to finish loading. When the Welcome screen appears, click inside the console and press Enter to continue; Ctrl+Alt releases keyboard/mouse capture. Capture the next installer screen before proceeding with installation choices.

[Boot screenshot](screenshots/image(20261004-061737).png)


Latest checkpoint: 4 October 2026, 01:25 America/Chicago. ESXi 8.0.3 installation welcome screen reached; installation pending. Screenshot: screenshots/image(20261004-062532).png.
