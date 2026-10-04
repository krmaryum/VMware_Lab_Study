# VMware ESXi Practice Lab: Setup and Troubleshooting Guide

**Session:** 3–4 October 2026 · **Host:** Dell Inspiron 14 7430 2-in-1 · **Status:** ESXi 8.0.3 and ESXi 9.1 installed as separate VMs; first boot and root browser login verified for both.

This guide organizes our actual results into a repeatable workflow. Use the checks first; apply troubleshooting changes only when the corresponding problem is present. The accompanying checkpoint ZIP preserves the chronological notes, screenshots, class materials, and supplied log files. Earlier proposals in that archive are historical; this guide states the current setup.

## Index

1. [Lab outcome and hardware](#1-lab-outcome-and-hardware)
2. [Check hardware from scratch](#2-check-hardware-from-scratch)
3. [Check the home network](#3-check-the-home-network)
4. [Copy files using SSH and SCP](#4-copy-files-using-ssh-and-scp)
5. [Verify and install Workstation](#5-verify-and-install-workstation)
6. [Create the ESXi virtual machine](#6-create-the-esxi-virtual-machine)
7. [Diagnose a startup failure](#7-diagnose-a-startup-failure)
8. [Our troubleshooting sequence](#8-our-troubleshooting-sequence)
9. [The final successful test](#9-the-final-successful-test)
10. [Verify and boot the installer](#10-verify-and-boot-the-installer)
11. [Next-lab checklist](#11-next-lab-checklist)
12. [Restore Windows settings](#12-restore-windows-settings)
13. [Learning points and references](#13-learning-points-and-references)
14. [Prepare the GitHub repository](#14-prepare-the-github-repository)

### Installation and progress checkpoints

- [Latest checkpoint: ESXi welcome screen](#latest-checkpoint-esxi-welcome-screen)
- [Installation completed: 4 October 2026, 01:36 Chicago](#installation-completed-4-october-2026-0136-chicago)
- [First boot verified: 4 October 2026, 01:40 Chicago](#first-boot-verified-4-october-2026-0140-chicago)
- [Host Client reached; username correction needed (01:44 Chicago)](#host-client-reached-username-correction-needed-0144-chicago)
- [Successful Host Client login: 01:46 Chicago](#successful-host-client-login-0146-chicago)
- [Separate ESXi 9 lab: VM settings (01:53 Chicago)](#separate-esxi-9-lab-vm-settings-0153-chicago)
- [ESXi 9.1 installed and console running (02:05 Chicago)](#esxi-91-installed-and-console-running-0205-chicago)
- [Final checkpoint: both installations completed](#final-checkpoint-both-installations-completed)

## 1. Lab outcome and hardware

**What worked:** After the final WindowsHello scenario change and restart, Windows reported `HypervisorPresent=False` and VBS status `0`. Workstation then powered on the VM and booted the ESXi installer. We subsequently completed ESXi 8.0.3 and ESXi 9.1 installations and verified root login to both Host Clients.

| Item | Confirmed result |
|---|---|
| Dell CPU | Intel Core i7-1355U; 10 cores, 12 logical processors; x64 |
| Dell RAM | 16 GB installed; about 15.7 GB usable |
| Dell storage | C: about 607 GB free of 931.9 GB when checked |
| Windows | Windows 11 Home, build 26200 |
| Firmware | UEFI; Secure Boot On; CPU virtualization enabled |
| Workstation | Pro 25H2, build 24995812 |
| ESXi ISO | VMware-VMvisor-Installer-8.0U3e-24677879.x86_64.iso |
| Successful boot screen | ESXi 8.0.3, VMKernel build 24677879; 2 CPUs; 8 GiB RAM |
| VM disk | 250 GB, single growable virtual disk |
| Networking | Five Host-only adapters configured in screenshots |

The Surface Laptop 7 uses an ARM Snapdragon processor, so we used it for file transfers rather than the planned x86 ESXi VM. The earlier HP desktop was replaced by the Dell as our lab host.

**Practical limit:** Start with one ESXi host and small nested workloads. This 16 GB laptop has limited headroom for a full multi-host VCF, vCenter, and NSX environment.

## 2. Check hardware from scratch

**WHY:** CPU architecture, firmware virtualization, RAM, and storage determine whether the host can run the lab.

Run on the intended Windows lab host:

```powershell
Get-CimInstance Win32_Processor |
    Select-Object Name, NumberOfCores, NumberOfLogicalProcessors

Get-CimInstance Win32_ComputerSystem |
    Select-Object Manufacturer, Model,
        @{Name="RAM_GB";Expression={[math]::Round($_.TotalPhysicalMemory / 1GB, 1)}} |
    Format-List

Get-Volume |
    Where-Object DriveLetter |
    Select-Object DriveLetter,
        @{Name="Free_GB";Expression={[math]::Round($_.SizeRemaining / 1GB, 1)}},
        @{Name="Total_GB";Expression={[math]::Round($_.Size / 1GB, 1)}} |
    Format-Table -AutoSize
```

Open **Task Manager → Performance → CPU** and check **Virtualization: Enabled**. If disabled, enable the virtualization setting in the machine's firmware. Keep it enabled throughout this lab.

**NEXT:** Close unnecessary applications. Allocate 8 GB to the ESXi VM rather than all 16 GB. A large pagefile is not a substitute for free physical RAM.

## 3. Check the home network

```powershell
ipconfig
```

| Machine | LAN IPv4 in our session | Role |
|---|---|---|
| Surface | 10.0.0.8 | Source files / SSH server for Dell pull |
| Dell | 10.0.0.140 | Destination / lab host |
| HP desktop | 10.0.0.213 | Earlier transfer destination |
| Router | 10.0.0.1 | Default gateway |

All these LAN addresses used mask `255.255.255.0` (/24). Check them again for future transfers: DHCP addresses may change.

**WHY:** Use the Wi-Fi or Ethernet address for communication between home machines. The Surface's 10.8.0.x adapter and the desktop's VMware 192.168.x.x adapters were separate networks.

```powershell
# On Dell: test the Surface's SSH port
Test-NetConnection 10.0.0.8 -Port 22
```

Our result was `TcpTestSucceeded=True`. Ping can time out while SSH works because they use different protocols. Windows uses `ping -n 2 IP`, while Linux uses `ping -c 2 IP`.

## 4. Copy files using SSH and SCP

**WHY:** The machine accepting SSH connections needs OpenSSH Server; the machine running SCP needs the client. For a pull from Dell, the Surface is the SSH server.

On the source machine, in Administrator PowerShell:

```powershell
Get-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
# Run the next command only if the capability is NotPresent:
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
Start-Service sshd
Set-Service sshd -StartupType Automatic
Get-Service sshd
```

Enable the existing firewall rule if present:

```powershell
Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue
Set-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -Enabled True
```

If no rule exists, create it:

```powershell
New-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -DisplayName "OpenSSH Server" `
    -Enabled True -Direction Inbound -Protocol TCP -LocalPort 22 -Action Allow
```

Use an account on the SSH server with permission to read the source folder. We used a separate standard `filetransfer` account after password problems. An SSH password is the account password, not the Windows Hello PIN. Passwords were not recorded in our notes.

On Dell:

```powershell
Get-Command scp
Test-NetConnection 10.0.0.8 -Port 22
scp -r filetransfer@10.0.0.8:C:/Linux/vmware C:/
Get-ChildItem C:\vmware
```

**WHAT:** `-r` copies folders recursively; the remote address identifies the Surface; the final `C:/` is the Dell destination. This copied the folder to **C:\vmware**. It did not remove the source.

Our earlier laptop-to-desktop push, from `C:\Linux`, was:

```powershell
scp -r .\vmware filetransfer@10.0.0.213:C:/Users/filetransfer/Transfers/
```

The Dell does not have the Surface's `C:\Linux\vmware` path. Using that path on Dell caused “Cannot find path.” Always identify which machine owns a path.

## 5. Verify and install Workstation

**WHY:** Confirm the installer before executing it. A matching hash proves identical bytes between copies; it does not alone prove authenticity.

```powershell
$installer = Get-ChildItem C:\vmware -Recurse -Filter "VMware-Workstation-Full-26H1*.exe" |
    Select-Object -First 1
$installer.FullName
Get-FileHash -LiteralPath $installer.FullName -Algorithm SHA256 | Format-List
Get-AuthenticodeSignature -FilePath $installer.FullName |
    Format-List Status, StatusMessage,
        @{Name="Signer";Expression={$_.SignerCertificate.Subject}}
```

### Our installer results

| Installer | Result | Decision |
|---|---|---|
| 26H1 build 25388281 | NotSigned; signer blank | Did not use this copy |
| 25H2 build 24995812 | Valid; signer Broadcom Inc | Installed successfully |

26H1 SHA256:

```text
906B845FEA38C8332EAB0269F63BAC29A2688232944FB986AD73AA83C4490E4B
```

Inspection of the supplied 26H1 file found extensive zero-filled data; that result applies to that file, not all 26H1 installers. We did not determine when the file became damaged.

Verify the valid alternative:

```powershell
Get-AuthenticodeSignature -FilePath "C:\vmware\VMware-Workstation-Full-25H2-24995812.exe" |
    Format-List Status, StatusMessage,
        @{Name="Signer";Expression={$_.SignerCertificate.Subject}}
```

Install the verified file, follow the setup wizard, and restart if requested. Check **Help → About** after opening Workstation. Our installed version was 25H2, build 24995812. We deferred the offered update during troubleshooting.

## 6. Create the ESXi virtual machine

**WHY:** Workstation runs on Windows; ESXi runs inside the Workstation VM; later, lab guest VMs run inside ESXi. Exposing virtualization to ESXi enables that additional layer.

1. Choose **Create a New Virtual Machine**. We used **Typical**.
2. Choose **I will install the operating system later**.
3. Choose **VMware ESX**, with the ESXi 8 version entry matching the ISO.
4. Name the VM **VMware ESXi 8**.
5. Store it at **C:\vmware\VMware ESXI-01**.
6. Create the virtual disk. We used **250 GB**, single file, growable.
7. Open **Edit virtual machine settings** and apply the following starting settings.

| Setting | Starting configuration | Explanation |
|---|---|---|
| Memory | 8192 MB / 8 GB | Leaves RAM for Windows |
| Processors | 1 processor × 2 cores | Two virtual CPUs for the initial lab |
| Nested virtualization | Virtualize Intel VT-x/EPT or AMD-V/RVI checked | Exposes virtualization to ESXi |
| CD/DVD | ESXi 8.0 U3e ISO; Connect at power on checked | Boots the installation media |
| Guest version | ESXi 8 entry | Matches the installer |
| Disk | 250 GB growable | Maximum virtual capacity; file grows as used |
| Network | Host-only for local practice | Dell can communicate with guests on that virtual network |

We initially selected the ESXi 9 profile, allocated 16 GB and eight CPUs, then corrected the resources. The successful boot screen confirms two CPUs and 8 GiB. A later screenshot has not independently confirmed the guest-profile correction.

Five Host-only adapters were added in the session. They share the default Host-only network; they do not create five separate networks automatically. For the next lab, use the NIC count and separate VMnet segments required by the exercise. Host-only access from another laptop or Internet access needs additional network configuration.

## 7. Diagnose a startup failure

Our initial popup said **Failed to start the virtual machine**. That generic message was insufficient to identify the cause.

```powershell
Get-Content -LiteralPath "C:\vmware\VMware ESXI-01\vmware.log" -Tail 100

Select-String -LiteralPath "C:\vmware\VMware ESXI-01\vmware.log" `
    -Pattern 'Monitor Mode|ULM|WHP|Hyper-V|VBS|VT-x|EPT|unsupported|error|fail' `
    -Context 3,3
```

Key evidence:

```text
IOPL_Init: Hyper-V detected by CPUID
Monitor Mode: ULM
ULM: Failed to set up partition, res 0xc0350005.
Module 'ULM' power on failed.
```

**WHAT:** Workstation selected its Windows-hypervisor execution path and failed before ESXi began installation. We did not assign a precise cause to the numeric error alone.

Check runtime state:

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object HypervisorPresent
Get-CimInstance -Namespace root\Microsoft\Windows\DeviceGuard `
    -ClassName Win32_DeviceGuard |
    Select-Object VirtualizationBasedSecurityStatus,
        SecurityServicesConfigured, SecurityServicesRunning | Format-List
```

| Field | Earlier result | Successful result |
|---|---|---|
| HypervisorPresent | True | False |
| VirtualizationBasedSecurityStatus | 2: running | 0: disabled |
| SecurityServicesRunning | Initially {2}, later {0} | {0} |

**Key lesson:** `{0}` in the service list did not mean VBS was disabled while the separate VBS status still showed `2`.

## 8. Our troubleshooting sequence

This is the history of this Dell, not a list to apply blindly on every computer. These changes reduce Windows virtualization-based protections and affect workloads such as WSL2. Keep firmware virtualization enabled.

| Step | Action | Result after restart |
|---|---|---|
| 1 | Set hypervisorlaunchtype Off | Hypervisor still present |
| 2 | Turn Memory Integrity Off | HVCI disabled; VBS still running |
| 3 | Disable VirtualMachinePlatform and HypervisorPlatform | Features disabled; VBS still running |
| 4 | Set DeviceGuard EnableVirtualizationBasedSecurity=0 | VBS still running |
| 5 | Set vsmlaunchtype Off | Both boot values persisted; VBS still running |
| 6 | Inspect logs, System Information, App Control policies, boot events | Narrowed the problem to remaining VBS configuration |
| 7 | Inspect DeviceGuard recursively | WindowsHello scenario Enabled=1; secure kernel running |
| 8 | Back up WindowsHello scenario; set Enabled=0; restart | HypervisorPresent=False; VBS=0 |
| 9 | Power on the ESXi VM | Installer boot succeeded |

### Commands and checks used along the way

Administrator PowerShell:

```powershell
bcdedit /set '{current}' hypervisorlaunchtype off
bcdedit /enum '{current}'

Get-WindowsOptionalFeature -Online |
    Where-Object FeatureName -Match 'Hyper-V|VirtualMachinePlatform|HypervisorPlatform|Containers-DisposableClientVM' |
    Select-Object FeatureName, State | Format-Table -AutoSize

Disable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform,HypervisorPlatform -NoRestart
```

Memory Integrity was changed through **Windows Security → Device security → Core isolation**. We verified its scenario value was zero.

```powershell
reg export "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard" "C:\vmware\DeviceGuard-before-VBS-change.reg"
# Only after backup succeeds:
reg add "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard" /v EnableVirtualizationBasedSecurity /t REG_DWORD /d 0 /f

bcdedit /enum '{current}' | Out-File "C:\vmware\BCD-before-VSM-test.txt"
bcdedit /set '{current}' vsmlaunchtype off
bcdedit /set '{current}' hypervisorlaunchtype off
```

System Information (`msinfo32`) continued to show **VBS Running** and **App Control for Business policy Enforced**. Smart App Control itself was **Off**.

```powershell
CiTool.exe -lp

Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    ProviderName = 'Microsoft-Windows-Kernel-Boot'
    Id = 153
} -MaxEvents 3 | Format-List TimeCreated, Id, Message

reg query "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard" /s
reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows\DeviceGuard" /s
reg query "HKLM\SOFTWARE\Microsoft\PolicyManager\current\device\DeviceGuard" /s
```

The boot event said VBS was **enabled due to VBS registry configuration**. The two policy registry paths were absent. Recursive local settings showed:

```text
EnableVirtualizationBasedSecurity = 0
HypervisorEnforcedCodeIntegrity\Enabled = 0
KernelShadowStacks\Enabled = 0
KeyGuard\Status\IsSecureKernelRunning = 1
KeyGuard\Status\KeyGuardEnabled = 1
KeyGuard\Status\CredGuardEnabled = 0
WindowsHello\Enabled = 1
```

CiTool listed an enforced Microsoft Windows VBS Policy, but that alone did not prove it was starting VBS. We did not delete signed policies or change Secure Boot. The ESS toggle was absent from the Dell's Sign-in options, even after inspecting the entire Additional settings section.

## 9. The final successful test

**WHY:** WindowsHello was a remaining enabled scenario after the other checked VBS settings were disabled. We tested that single value with a backup. This is a machine-specific troubleshooting result, not a guaranteed or officially documented replacement for the ESS UI toggle.

Before testing, ensure a working account-password sign-in fallback. The change may affect Windows Hello availability or protection.

Administrator PowerShell:

```powershell
reg export "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\WindowsHello" "C:\vmware\WindowsHello-before-test.reg"
```

After the backup succeeds:

```powershell
reg add "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\WindowsHello" /v Enabled /t REG_DWORD /d 0 /f
```

Both returned:

```text
The operation completed successfully.
```

Save work and use **Windows Restart**. After restart, run the runtime checks in section 7.

Our confirmed output:

```text
HypervisorPresent
-----------------
            False

VirtualizationBasedSecurityStatus : 0
SecurityServicesRunning           : {0}
```

**Conclusion for this session:** The final WindowsHello scenario change, together with the preceding configuration changes, achieved the required runtime state. Windows 11 Home remained installed; we did not upgrade or reinstall Windows to achieve this result.

## 10. Verify and boot the installer

Reopen Workstation, verify section 6 settings, and power on the VM.

Our screenshot showed:

```text
VMware ESXi 8.0.3 (VMKernel Release Build 24677879)
VMware, Inc. VMware20,1
2 x 13th Gen Intel(R) Core(TM) i7-1355U
8 GiB Memory
```

The **Removable Devices** popup was informational. Click **OK** and allow the installer to load. At the Welcome screen, click inside the console and press Enter to continue. **Ctrl+Alt** releases mouse/keyboard capture.

**Still pending:** licence screen, installation-disk selection, keyboard selection, root password, installation confirmation, completed install, reboot, management networking, browser access, and a nested guest VM test. Capture each result as the next lab proceeds; do not mark installation complete based on this boot screenshot.

## 11. Next-lab checklist

- [ ] Identify the host CPU architecture, RAM, and free disk.
- [ ] Confirm firmware virtualization is enabled.
- [ ] Verify the Workstation installer signature and version.
- [ ] Check HypervisorPresent and DeviceGuard runtime status.
- [ ] If a VM fails, collect its log before making changes.
- [ ] Apply only troubleshooting steps justified by the current evidence.
- [ ] Back up any registry key before changing it; record original values.
- [ ] Configure ESXi with 8 GB RAM and two virtual CPUs initially on this Dell.
- [ ] Keep nested virtualization checked; match guest profile and ISO.
- [ ] Confirm installer boot, then complete ESXi installation step by step.
- [ ] Record management IP and test browser access from the intended client.
- [ ] Verify a small nested VM can start before declaring the lab complete.

## 12. Restore Windows settings

Restoring Windows hypervisor/VBS functionality may again prevent this Workstation nested lab from running. Shut down the lab VMs first. Restore only settings you actually changed, using the recorded originals.

### Restore the WindowsHello scenario

```powershell
reg import "C:\vmware\WindowsHello-before-test.reg"
```

Restart afterwards. This restores that scenario only.

### Restore the boot overrides to their original default behavior

Both overrides were absent in the original recorded entry. Remove them to return to inherited/default behavior:

```powershell
bcdedit /deletevalue '{current}' vsmlaunchtype
bcdedit /deletevalue '{current}' hypervisorlaunchtype
```

If your purpose is explicitly to enable the Windows hypervisor instead, `bcdedit /set '{current}' hypervisorlaunchtype auto` is an alternative to removing that override.

### Restore the VBS value we added

The original DeviceGuard root did not contain EnableVirtualizationBasedSecurity. To remove our added override:

```powershell
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard" /v EnableVirtualizationBasedSecurity /f
```

Importing a .reg backup alone does not delete values added after the export. Do not treat a broad import as a full rollback of later edits.

### Restore the optional features that were originally enabled

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform,HypervisorPlatform -All -NoRestart
```

Turn Memory Integrity back On through Windows Security if restoring the original security configuration. Save work, restart, and repeat runtime checks. Verify your sign-in and WSL2 workloads as appropriate. We have not executed or verified this restoration sequence.

## 13. Learning points and references

- **Firmware virtualization enabled** and **Windows hypervisor present** are different states.
- A successful SSH port test can coexist with failed ping.
- SCP copies files; it does not remove the source folder.
- A valid digital signature and a SHA256 comparison answer different questions.
- A VM name does not set its guest OS profile.
- ULM startup failure happens before guest OS installation; a generic popup needs log evidence.
- Memory Integrity Off did not stop all VBS on this Dell.
- A policy being listed is different from being enforced.
- The WindowsHello scenario test was the final successful change here, after several earlier changes.
- Successful ESXi boot is different from completed installation and verified nested workloads.

Primary documentation consulted:

- [Workstation and Windows Hyper-V execution mode](https://blogs.vmware.com/cloud-foundation/2020/05/28/vmware-workstation-now-supports-hyper-v-mode/)
- [Broadcom: virtualized Intel VT-x/EPT troubleshooting](https://knowledge.broadcom.com/external/article/389469)
- [Microsoft: BCDEdit /set options](https://learn.microsoft.com/en-us/windows-hardware/drivers/devtest/bcdedit--set)
- [Microsoft: enable memory integrity and DeviceGuard status](https://learn.microsoft.com/en-us/windows/security/hardware-security/enable-virtualization-based-protection-of-code-integrity)
- [Microsoft: CiTool reference](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/operations/citool-commands)
- [Microsoft: built-in App Control policies](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/operations/inbox-appcontrol-policies)
- [Microsoft: Enhanced Sign-in Security](https://support.microsoft.com/en-us/windows/security/identity-signin/enhanced-sign-in-security-in-windows)

## 14. Prepare the GitHub repository

Suggested name: **vmware-esxi-home-lab**. Suggested folders:

| Path | Content |
|---|---|
| README.md | Lab purpose, hardware, current progress, links to notes |
| docs/setup-guide.md | This structured guide |
| docs/troubleshooting-history.md | Chronological checkpoint notes |
| evidence/ | Selected logs and command output |
| screenshots/ | Reviewed screenshots linked from notes |
| .gitignore | Exclude installers, ISOs, VM disks, and private data |

Suggested .gitignore:

```gitignore
*.exe
*.msi
*.iso
*.ova
*.vmdk
*.vmem
*.vmss
*.nvram
*.lck/
*.reg
secrets/
```

Review logs/screenshots before publishing for personal information. Keep passwords, product keys, VM disks, installers, and instructor-owned class materials out of a public repository unless you have permission to share them. The checkpoint archive is for personal study; do not upload the entire archive directly to GitHub.

**Progress:** File transfers complete → Workstation installed → VBS conflict resolved → ESXi installer booted → ESXi installation next.

## Latest checkpoint: ESXi welcome screen

The screenshot shared at 01:25 on 4 October 2026 (America/Chicago) shows **Welcome to the VMware ESXi 8.0.3 Installation** with **Enter: Continue**. This confirms the installer reached its interactive welcome screen. Installation is still pending. Click inside the virtual machine and press Enter to proceed; review the next screen before selecting a disk.

Screenshot: `screenshots/image(20261004-062532).png` in the complete study archive.

## Installation completed: 4 October 2026, 01:36 Chicago

The installation sequence is now confirmed by screenshots:

| Step | Screen / result | Purpose |
|---|---|---|
| 1 | EULA; F11 accepts and continues | Review the license before accepting. |
| 2 | VMware virtual disk, 250.00 GiB, mpx.vmhba1:C0:T0:L0 | Select the VM disk as the installation target. |
| 3 | US Default keyboard layout | Match the keyboard used for console input. |
| 4 | Confirm Install; F11 Install | The selected virtual disk will be repartitioned. |
| 5 | Installation Progress, 59% | Installer caching the required files. |
| 6 | Installation Complete: ESXi 8.0.3 installed successfully | Installation succeeded; first boot and management access are next. |

The root-password entry screen was not supplied in this batch. Do not put passwords in these notes.

### Next: disconnect ISO and reboot

1. In VMware Workstation, open **VM > Settings > CD/DVD**.
2. Clear **Connected** and **Connect at power on** for the installer ISO, then click OK. This removes the virtual installation media without deleting the ISO file.
3. Return to the ESXi console and press **Enter** to reboot.
4. Wait for the installed ESXi console and record its management IP. First boot, network configuration, and browser access are not yet verified.

Screenshots are preserved in the archive under `screenshots/`: image(20261004-062644).png, image(20261004-062743).png, image(20261004-062928).png, image(20261004-063503).png, image(20261004-063629).png, and image(20261004-063650).png.

The completion screen reports a 60-day evaluation period for this installation. Record this as the installer's displayed message, rather than a general claim about every ESXi license.

## First boot verified: 4 October 2026, 01:40 Chicago

The installed ESXi console is running after reboot. Screenshot `screenshots/image(20261004-064002).png` confirms:

- VMware ESXi 8.0.3, VMKernel build 24677879.
- VMware virtual hardware VMware20,1; two virtual CPUs and 8 GiB memory.
- Management IPv4 address **192.168.109.128**, assigned by DHCP.
- Displayed management URL: **https://192.168.109.128/**.

Next verification: open the displayed HTTPS URL in a browser on the Dell host and log in as `root` using the password set during ESXi installation. If a certificate warning appears, check that the URL is exactly the address on this lab console before proceeding. Browser access and login are not yet confirmed. This DHCP address may change; check the console if the URL stops working. It belongs to the virtual network, rather than the home Wi-Fi 10.0.0.x network.

## Host Client reached; username correction needed (01:44 Chicago)

The browser reached `https://192.168.109.128/ui/#/login`, confirming the ESXi Host Client is reachable. The screenshot shows an incorrect username/password error and a value other than `root` in the username field.

Use **root** in the first field and the ESXi root password created during installation in the second field. This is the ESXi account, not the Windows account. Successful authentication is not yet confirmed.

The screenshot is not included in this study archive because the username field appears to contain a password. Its contents are not transcribed. If an actual password was exposed, change it after successful login.

## Successful Host Client login: 01:46 Chicago

The screenshot confirms an authenticated session as `root@192.168.109.128` at `/ui/#/host`. Installation, first boot, management-page reachability, and root login are now verified. The dashboard shows default hostname `localhost.localdomain`, zero virtual machines, and one storage entry. No nested guest workload has been tested yet.

The first-login Customer Experience Improvement Program (CEIP) dialog is open, with participation checked. It describes collecting technical usage information. For this lab, clear the participation checkbox if you prefer not to participate, then click **OK** to reach the dashboard. This choice has not yet been confirmed.

Screenshot: `screenshots/image(20261004-064636).png`. Next: inspect the unobstructed host dashboard, storage and networking before creating a guest VM.

## Separate ESXi 9 lab: VM settings (01:53 Chicago)

User chose a separate VM named **VMware ESXi 9**, preserving the working ESXi 8 lab. Screenshot shows 6144 MB RAM, two processors in the summary, 250 GB SCSI disk, CD/DVD on Auto detect and one NAT network adapter. The ESXi 8 entry still has a green running indicator; shutdown is not confirmed.

Next steps: shut down ESXi 8 gracefully through its Host Client before starting ESXi 9; set the new VM's memory to 8192 MB to match the allocation used in the completed ESXi 8 lab. Open Processors to verify CPU topology and the nested Intel VT-x/EPT checkbox. Do not start installation until the intended ESXi 9 ISO has been selected; Auto detect does not show an ISO mounted. Exact version (9.0 or 9.1), nested-virtualization settings and installer compatibility are not yet verified. NAT is the current setting, different from the earlier ESXi 8 host-only configuration.

Screenshot: `screenshots/image(20261004-065302).png`.

## ESXi 9.1 installed and console running (02:05 Chicago)

Four screenshots confirm these checkpoints:

| Screenshot | Observed checkpoint |
|---|---|
| image(20261004-065657).png | Selected ESXi 9.1 ISO, build 25433460, in the ISO browser. |
| image(20261004-065909).png | VM settings: 8 GB RAM, processor summary 8, 250 GB SCSI disk, CD/DVD using a file, two host-only adapters. NAT was changed to host-only. |
| image(20261004-070115).png | ESXi 9.1.0 installer welcome screen reached. |
| image(20261004-070512).png | ESXi 9.1.0.0100.25433460 Release Build running at its management console; 2 CPUs and 8 GiB RAM displayed; DHCP IPv4 192.168.109.129. |

The latest running console supersedes the earlier settings screenshot for observed CPU count: it shows two CPUs. Intermediate installation screens, final processor settings, and ISO disconnection were not supplied in this batch. The user reports installation; the console confirms ESXi 9.1 runtime is running. Browser login and nested guest operation remain unverified.

Next: open **https://192.168.109.129/** on the Dell laptop, verify the address when handling the lab certificate warning, and log in as **root** with the password created for this ESXi 9 installation. This host differs from ESXi 8 at 192.168.109.128. DHCP addresses may change.

Both ESXi VM tabs show green running indicators in the latest screenshot. Each ESXi VM has 8 GiB RAM, while the Dell has about 16 GB total. Shut down one host gracefully through its Host Client or console before running guest workloads on the other, leaving memory for Windows and Workstation.

All four screenshots are preserved under `screenshots/` in the archive.

## Final checkpoint: both installations completed

At 02:11 on 4 October 2026 (America/Chicago), the final screenshot confirms an authenticated ESXi 9.1 Host Client session as `root@192.168.109.129`. Host Details show VMware ESXi **9.1.0.0100.25433460**, VMware20,1 hardware, and the Intel i7-1355U CPU. The hostname remains `localhost.localdomain`. The dashboard shows zero virtual machines, one storage entry, and one networking entry. It displays a license-expiration warning of **90 days** for this host; this is an observed UI message rather than a universal licensing rule.

| Completed host | Build | Last observed DHCP management IP | Browser login |
|---|---|---|---|
| ESXi 8.0.3 | 24677879 | 192.168.109.128 | Verified as root |
| ESXi 9.1 | 25433460 | 192.168.109.129 | Verified as root |

These addresses may change because DHCP is enabled. Installation and management access are completed. Static addressing, custom hostname, datastore inspection, guest creation, and nested guest power-on remain future lab work. No passwords are stored in these notes.

Final evidence: `screenshots/image(20261004-070838).png` (running ESXi 9 console, Workstation installation helper banner still visible) and `screenshots/image(20261004-071107).png` (authenticated Host Client dashboard). The helper banner's “I Finished Installing” button is separate from ESXi's installation status.

### Next session starting checklist

1. Open VMware Workstation on the Dell.
2. Start only the ESXi host needed for the lesson; leave the other shut down to preserve RAM for Windows and guest workloads.
3. Check its console for the current IP, then open its HTTPS URL on the Dell.
4. Log in as root with that host's password.
5. Review storage and networking before creating a guest VM.
6. Record every change and its verification result.
