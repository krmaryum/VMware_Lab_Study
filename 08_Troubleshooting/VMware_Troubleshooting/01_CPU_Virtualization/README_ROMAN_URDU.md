# Chapter 01 — CPU Virtualization (Roman Urdu)

## Maqsad
Is chapter mein samjhenge ke physical computer ki BIOS/UEFI virtualization aur VMware Workstation ki nested virtualization alag settings hain.

## Basic concept
- **Intel VT-x / AMD-V:** CPU ki hardware virtualization capability hai.
- **BIOS/UEFI virtualization:** Physical computer par yeh capability enable karne ki setting.
- **Nested virtualization checkbox:** VMware Workstation ke guest VM ko VT-x/EPT ya AMD-V/RVI expose karta hai.

Agar aap Workstation mein normal Windows Server, Ubuntu ya Rocky Linux chala rahe hain to nested checkbox aam tor par **zaroori nahin**. Agar Workstation ke andar ESXi, Hyper-V ya KVM chalana hai to aam tor par **zaroori hota hai**.

## Common symptoms
1. VMware VT-x/AMD-V unavailable ka error deta hai.
2. ESXi VM boot nahin hoti.
3. Nested virtualization option select karne ke baad VM start nahin hoti.

**Yaad rahe:** In symptoms ki mukhtalif wajah ho sakti hain. Sirf error dekh kar BIOS ko zimmedar na samjhein.

## Step-by-step diagnosis
1. Guest VM ko poori tarah **Power Off** karein.
2. Windows **Task Manager → Performance → CPU** mein `Virtualization` check karein.
3. `Win + R` se `msinfo32` kholein. `Virtualization-based security` aur hypervisor status dekhein.
4. PowerShell mein:

```powershell
Get-ComputerInfo | Select-Object HyperVisorPresent, HyperVRequirementVirtualizationFirmwareEnabled
'Microsoft-Hyper-V-All','VirtualMachinePlatform','HypervisorPlatform' | ForEach-Object {
  Get-WindowsOptionalFeature -Online -FeatureName $_ | Select-Object FeatureName, State
}
```

5. VMware Workstation version aur VM ka exact error note karein.
6. VM folder mein `vmware.log` check karein; `VT-x`, `AMD-V`, `HV`, `error`, `failed` search karein.

## Solution A — BIOS virtualization disabled
1. Apna kaam save karke computer shutdown karein.
2. Manufacturer ke tareeqay se BIOS/UEFI mein jaayein.
3. `Intel Virtualization Technology` ya `SVM Mode` ko **Enable** karein.
4. Save karke reboot karein.
5. Task Manager mein virtualization status dobara verify karein.

## Solution B — ESXi ko nested virtualization chahiye
1. ESXi VM ko **Power Off** karein.
2. **VMware Workstation → VM Settings → Processors** kholein.
3. **Virtualize Intel VT-x/EPT or AMD-V/RVI** check karein.
4. VM start karke dekhein ke installer/DCUI tak boot hoti hai ya nahin.

## Solution C — Hyper-V / VBS conflict
Windows Hyper-V, VBS aur Memory Integrity kuch host/Workstation combinations mein nested virtualization ko affect kar sakte hain. Pehle compatibility aur logs check karein. **Security features ko baghair approval aur risk assessment disable na karein.**

## Classmate case study
**Observation:** Screenshot mein nested virtualization checkbox unchecked tha aur classmate ne repeated error report kiya.
**Action:** Checkbox enable kiya.
**Reported result:** Problem solve ho gayi.
**Note:** Original error ka exact text confirm nahin hua tha.

## Verification
- [ ] Task Manager mein host virtualization enabled hai.
- [ ] Normal VM boot ho rahi hai.
- [ ] ESXi VM ke liye nested checkbox enabled hai.
- [ ] ESXi installer/DCUI tak boot ho rahi hai.
- [ ] Agar error aaye to screenshot aur `vmware.log` save kiya hai.

## Interview questions
**Q: VT-x aur nested virtualization mein kya farq hai?**
A: VT-x physical CPU ki feature hai; nested virtualization is feature ko guest hypervisor tak expose karti hai.

**Q: Kya normal Windows VM ke liye checkbox zaroori hai?**
A: Aam tor par nahin.

**Q: Task Manager 'Enabled' dikhaye to ESXi nested zaroor chalega?**
A: Zaroori nahin; Workstation compatibility, Hyper-V/VBS aur CPU bhi matter karte hain.

**GitHub tip:** Public screenshot se personal phone number aur sensitive details hata dein.
