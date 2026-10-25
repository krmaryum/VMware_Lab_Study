# Chapter 01 — CPU Virtualization: Intel VT-x / AMD-V

## Learning objectives
- Distinguish host firmware virtualization from nested virtualization in a guest.
- Diagnose whether the host supports and exposes virtualization.
- Collect evidence before changing host security settings.

## Architecture
Physical CPU (Intel VT-x / AMD-V) → BIOS/UEFI setting → Windows host → VMware Workstation → guest VM.

**Host virtualization:** Enabled in firmware to support hardware-assisted virtualization on the host.
**Nested virtualization:** Workstation VM Settings → Processors → `Virtualize Intel VT-x/EPT or AMD-V/RVI` exposes CPU virtualization features to a guest hypervisor such as ESXi.

Normal Windows/Linux guests generally do **not** need the nested checkbox. ESXi/Hyper-V/KVM guests generally **do** need it, subject to host compatibility.

## Symptoms
- Workstation reports virtualization engine / VT-x / AMD-V unavailable.
- Nested ESXi cannot start or reports unsupported virtualization.
- Nested virtualization checkbox cannot be used, or the VM fails after selecting it.

These symptoms have several possible causes; do not assume BIOS settings are the problem without checking.

## Diagnostic workflow (Windows host)
1. Shut down the guest VM (not merely suspend it).
2. Open **Task Manager → Performance → CPU**. Check **Virtualization: Enabled/Disabled**. This is a quick firmware-related indicator, not proof nested ESXi will work.
3. Open **System Information** (`msinfo32`). Record `Virtualization-based security` and whether a hypervisor has been detected.
4. In **PowerShell**, collect:

```powershell
Get-ComputerInfo | Select-Object HyperVisorPresent, HyperVRequirementVirtualizationFirmwareEnabled, HyperVRequirementSecondLevelAddressTranslation
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All,VirtualMachinePlatform,HypervisorPlatform
```

Note: `Get-WindowsOptionalFeature -FeatureName` accepts one name at a time on some systems. If the combined command fails, run each feature name separately:

```powershell
'Microsoft-Hyper-V-All','VirtualMachinePlatform','HypervisorPlatform' | ForEach-Object {
  Get-WindowsOptionalFeature -Online -FeatureName $_ | Select-Object FeatureName, State
}
```

5. Check the exact Workstation version, host CPU model, guest type, and any on-screen error.
6. If a guest fails to start, inspect the VM's `vmware.log` for `HV`, `VT-x`, `AMD-V`, `monitor`, `error`, or `failed` (interpret results in context).

## Solution A — Firmware virtualization disabled
1. Save work and fully shut down Windows.
2. Enter BIOS/UEFI using the PC manufacturer's documented method.
3. Locate **Intel Virtualization Technology / VT-x** or **SVM Mode / AMD-V** (names vary).
4. Enable it, save changes, and reboot.
5. Recheck Task Manager. Test a normal guest VM first.

**Caution:** BIOS menus differ. Avoid changing unrelated firmware or security settings.

## Solution B — Nested ESXi needs CPU virtualization exposure
1. Power off the ESXi guest.
2. Workstation → **VM → Settings → Processors**.
3. Select **Virtualize Intel VT-x/EPT or AMD-V/RVI**.
4. Start the ESXi VM and check for successful boot.

If the option fails, investigate Windows Hyper-V/VBS coexistence and Workstation compatibility rather than blindly changing settings. Disabling VBS/Memory Integrity reduces host security; only consider after risk assessment and authorization.

## Solution C — Workstation reports hypervisor interference
- Record `msinfo32` and optional Windows features first.
- Check current Workstation support for Windows Hypervisor Platform and the intended nested guest.
- Update Workstation and host firmware/drivers where appropriate.
- Only if necessary and authorized, test changes to Hyper-V/VBS features with a rollback plan. Some Windows features and security protections depend on them.

## Verification checklist
- [ ] Host firmware virtualization is reported enabled.
- [ ] Normal Windows/Linux VM starts.
- [ ] Nested checkbox is enabled only for a guest that requires it.
- [ ] Nested ESXi starts and reaches installer/DCUI as expected.
- [ ] Error text and `vmware.log` were captured if failure persists.

## Case study: classmate's ESXi installation
**Observed:** Screenshot showed nested virtualization checkbox unchecked; classmate reported recurring ESXi startup issue.
**Action reported:** Enabled `Virtualize Intel VT-x/EPT or AMD-V/RVI`.
**Outcome reported:** Problem stopped occurring.
**Limit:** The exact error text was not independently legible, so this is a reported resolution, not a verified diagnosis of every possible failure.

## Interview practice
1. **VT-x vs EPT?** VT-x supports hardware-assisted virtualization; EPT accelerates guest memory address translation.
2. **Does a normal Ubuntu VM need the nested checkbox?** Generally no.
3. **Why does nested ESXi need it?** ESXi needs CPU virtualization capabilities exposed inside its VM.
4. **Is Task Manager 'Virtualization: Enabled' enough to prove nested ESXi will run?** No; host hypervisor, security configuration, hardware and Workstation compatibility still matter.
5. **What should you gather before changing Hyper-V/VBS?** Exact error, `msinfo32`, optional feature state, VM settings, and `vmware.log`.

## Quick commands
```powershell
msinfo32
systeminfo
Get-ComputerInfo | Select-Object HyperVisorPresent
```

> Publishing tip: Redact names, phone numbers, hostnames and sensitive IPs from shared screenshots.
