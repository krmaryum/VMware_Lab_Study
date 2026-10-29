# Vultr Server Onboarding Specialist — Complete Job Workflow & Interview Study Notes

**Language:** Roman Urdu + English technical terms  
**Purpose:** Samajhna ke ek newly racked physical server ko production-ready banane ke liye aap practical taur par kya karenge, aur interview mein kaise explain karenge.  
**Scenario:** Data center mein 10 naye Dell PowerEdge servers rack aur cable ho chuke hain. Aap Server Onboarding team mein hain.

> **Important:** Yeh role ki discussed responsibilities par based **realistic learning workflow** hai, Vultr ka verified internal runbook nahin. Actual commands, tooling, access, configuration values aur production approvals Vultr ki policies aur Scott Hartman ki team ke runbooks ke mutabiq honge. Aap apne interview mein Puppet, PXE ya GPU provisioning ka past hands-on experience claim na karein jab tak waqai kiya na ho.

---

## Table of Contents

1. Production, production-ready aur onboarding ka matlab
2. Aapka role aur daily responsibilities
3. End-to-end workflow diagram
4. Step 0 — Ticket, change scope aur handoff receive karna
5. Step 1 — Rack, asset aur physical hardware verify karna
6. Step 2 — Out-of-band access: BMC, iDRAC, iLO, IPMI
7. Step 3 — BIOS/UEFI aur firmware baseline
8. Step 4 — Disks, RAID, JBOD aur GPU verification
9. Step 5 — Network: NIC, switch, VLAN, DHCP, IP
10. Step 6 — PXE boot aur automated Linux imaging
11. Step 7 — OS verification aur Puppet configuration
12. Step 8 — Validation, inventory, tickets aur production handoff
13. 10-server daily-work example
14. Failure scenarios: step-by-step troubleshooting
15. Useful Linux commands aur unka purpose
16. Safe operations, security aur escalation
17. Interview answers (English + Roman Urdu explanation)
18. Self-test / mock interview questions
19. Quick revision checklist

---

## 1. Production aur onboarding kya hota hai?

**Production** woh live infrastructure/environment hota hai jahan real customer services chalti hain ya chalne ke liye infrastructure available hota hai. Cloud provider mein production fleet ka server abhi kisi specific customer ko assigned na bhi ho, lekin approved capacity ka hissa ho sakta hai.

**Server onboarding** = newly installed/racked physical server ko inspect, configure, image, validate, document aur approved process ke through production fleet ko hand off karna.

**Power ON hona production-ready hone ke barabar nahin.** Hardware, network, OS, security/configuration, validation aur inventory sab check honge.

| Environment | Asaan Roman Urdu |
|---|---|
| Development | Software banana aur experiment karna |
| Testing / QA | Functionality aur failures test karna |
| Staging | Production jaisi final test environment |
| Production | Live/customer-facing ya approved live fleet |

## 2. Aapki responsibilities — ek nazar mein

- Naye servers ka **hardware handoff** receive aur verify karna.
- **BMC/iDRAC** ke through remote hardware management.
- **BIOS/UEFI, firmware, boot mode/order** ko approved baseline se compare karna.
- **Storage** (RAID/JBOD) aur Demand ke mutabiq **GPU** inventory/health verify karna.
- **Provisioning network** (NIC, switch port, VLAN, DHCP, IPv4/IPv6) validate karna.
- **PXE-based automated OS imaging** ko initiate/monitor/troubleshoot karna.
- OS boot, disks, network, services aur **existing Puppet workflow** ke results verify karna.
- Faulty hardware ko identify kar ke **DOA/RMA** process mein coordinate karna.
- **Asset inventory, evidence, tickets aur runbook notes** update karna.
- Sirf required checks pass hone aur authorization milne par **production handoff** karna.

## 3. End-to-end workflow diagram

```text
Data Center: New Servers Racked & Cabled
                    |
                    v
       [0] Ticket + Handoff + Runbook
                    |
                    v
       [1] Rack / Serial / Power / Cables
                    |
                    v
       [2] BMC / iDRAC Remote Management
                    |
                    v
       [3] BIOS / UEFI / Firmware Baseline
                    |
                    v
       [4] Disk Health / RAID-JBOD / GPU
                    |
                    v
       [5] NIC -> Switch -> VLAN -> DHCP
                    |
                    v
       [6] PXE -> Bootloader -> OS Imaging
                    |
                    v
       [7] Linux + Puppet + Health Checks
                    |
                    v
       [8] Inventory + Evidence + Approval
                    |
                    v
              PRODUCTION HANDOFF

At any failure: STOP -> collect evidence -> troubleshoot
               -> approved fix / escalation -> retest
```

## 4. Step 0 — Ticket, change scope aur handoff receive karna

### Aap kya karenge?

1. Data center / remote-hands team se **server list aur handoff ticket** receive karenge.
2. Har server ka **asset/serial, rack position, expected model, CPU/RAM, NIC, disk/GPU specification** dekhenge.
3. **Approved runbook, maintenance/change authorization, required image aur firmware baseline** identify karenge.
4. Check karenge ke access, provisioning VLAN aur BMC management path available hai.
5. Tracking sheet/ticket mein status rakhenge: `Pending`, `In Progress`, `Blocked`, `Validated`, `Handed Off`.

**Kyun?** Galat machine par configuration, imaging ya destructive storage change se bachna hai.

**Interview line:** “I would start by reviewing the handoff ticket, confirming the asset identifiers, and following the approved onboarding runbook.”

## 5. Step 1 — Physical hardware verification

### Aap kya check karenge?

- Server ka **serial number / asset tag** inventory se match karta hai?
- **Rack / RU location** aur power feed correct hai?
- **Power supply status**, fans, RAM, CPU, disks aur network cards healthy hain?
- Required **network cables** sahi switch ports par connected hain?
- On-site technician ne cabling aur rack installation confirm ki hai?

### Practical example

10 servers mein `server-07` ke PSU health alert aata hai. Aap usko “ready” mark nahin karenge. BMC hardware logs capture karenge, ticket update karenge aur remote-hands/hardware team se coordinate karenge.

### Interview line

“I verify rack and asset information, power and cabling, and hardware health before moving to provisioning.”

## 6. Step 2 — Out-of-band management (BMC/iDRAC)

**Out-of-band (OOB)** ka matlab: operating system se independent management channel. Linux install na ho ya SSH unavailable ho, tab bhi powered BMC ke through hardware access mil sakta hai (agar management connectivity aur standby power available ho).

| Term | Meaning |
|---|---|
| BMC | Baseboard Management Controller, hardware management controller |
| iDRAC | Dell remote management implementation |
| iLO | HPE remote management implementation |
| IPMI | Hardware management interface standard; BMC ka synonym nahin |
| Redfish | Modern REST-based hardware management interface |

### Aap kya karenge?

1. Approved management network se iDRAC/BMC ko access karenge.
2. **Power status**, sensors, temperatures, fans, PSU, memory/storage alerts check karenge.
3. **System Event Log / Lifecycle Log** mein hardware faults dekhenge.
4. **Remote console** se boot process aur BIOS screen observe karenge.
5. BMC firmware aur configuration ko approved baseline se compare karenge.

**Safety:** BMC credentials ko plaintext ticket, screenshot ya GitHub mein kabhi save nahin karna. Management network restricted hona chahiye.

**If unreachable:** management IP/address, routing/VLAN, switch link, ACL/firewall, BMC power aur on-site cabling ko structured order mein verify karein. Production management network par unauthorized scans avoid karein.

## 7. Step 3 — BIOS/UEFI aur firmware

**BIOS/UEFI** boot se pehle hardware initialize aur settings control karta hai. **Firmware** hardware components par chalne wala low-level software hai.

### Aap kya verify karenge?

- **UEFI vs Legacy** mode approved standard ke mutabiq hai?
- **Boot order** PXE/provisioning requirement se match karta hai?
- CPU, RAM, NIC, disks (aur GPU agar applicable) detect ho rahe hain?
- Virtualization-related ya platform-specific settings approved profile se match karti hain?
- BIOS, BMC, NIC, RAID controller firmware approved versions par hain?

**Important:** Random firmware upgrade, BIOS reset, Secure Boot change ya storage mode switch mat karein. Pehle approved runbook aur change scope check karein.

**If PXE fails:** pehle ensure karein ke intended NIC se network boot enabled hai aur UEFI/Legacy mode boot image ke compatible hai.

## 8. Step 4 — Storage, RAID/JBOD aur GPU

### Storage terms

- **RAID 0:** Striping, redundancy nahin.
- **RAID 1:** Mirroring, disk redundancy.
- **RAID 5:** Parity-based redundancy, configuration dependent.
- **RAID 10:** Mirroring + striping, multiple disks required.
- **JBOD:** Disks individually expose ki ja sakti hain; hardware/vendor configuration ke mutabiq exact behavior vary karta hai.

### Aap kya karenge?

1. Expected **number, capacity aur type** of disks verify.
2. Failed/missing disk aur controller alerts check.
3. **Approved RAID/JBOD layout** compare.
4. Agar GPU server hai, GPU count/model, PCIe detection, hardware alerts aur required baseline check.
5. Mismatch par imaging stop karke ticket/escalation.

**Critical warning:** RAID virtual disk create/delete, initialize, reformat, firmware flash ya disk wipe **data-destructive** ho sakta hai. Sirf authorized new-server workflow mein karein.

## 9. Step 5 — Network verification

### Traffic path

```text
Server NIC -> Ethernet Cable -> Physical Switch Port
           -> Provisioning VLAN -> DHCP Service
           -> PXE Boot Service / Imaging Infrastructure
```

### Aap ka checklist

1. **NIC link up** hai? Speed/port health correct?
2. Switch port intended server ke saath map hai?
3. Port par **correct provisioning VLAN** configured hai?
4. DHCP server se expected **IP lease** mil rahi hai?
5. Required PXE boot services reachable hain?
6. IPv4/IPv6 expectations, routes aur DNS (agar relevant) match karte hain?

**DHCP ka role:** Booting machine ko network parameters deta hai; PXE discovery/boot information architecture ke mutabiq DHCP ya related services se aa sakti hai.

**If no IP:** Pehle physical/NIC link, phir switch port/VLAN, phir DHCP scope/relay/logs aur network policies investigate karein. DHCP ko immediately blame na karein.

## 10. Step 6 — PXE boot aur automated Linux installation

**PXE (Preboot Execution Environment)** server ko local installed OS ke baghair network se boot karne deta hai.

```text
Power ON
   |
UEFI/BIOS network boot
   |
NIC discovers network / DHCP
   |
Boot program (for example iPXE/UEFI network loader)
   |
Boot files via TFTP/HTTP or configured transport
   |
Kernel + initramfs / installer
   |
Automated Linux installation
   |
First boot from local disk
```

**Note:** Actual PXE design DHCP, TFTP, HTTP, iPXE, UEFI HTTP Boot ya other mechanisms use kar sakta hai; sab environments ek jaise nahin.

### Aap kya karenge?

1. Confirm karein ke intended server aur approved image selected hai.
2. Network boot initiate/observe karein.
3. Dekhein ke DHCP lease aur boot program mil raha hai.
4. Installer start aur disk selection correct hai.
5. Automated imaging logs aur completion status review karein.
6. First reboot ke baad server local disk se successfully boot hota hai?

### Failure stage mapping

| Symptom | Pehle kahan dekhein? |
|---|---|
| No network link | NIC, cable, switch port |
| No DHCP IP | VLAN, DHCP, relay, port config |
| IP mila, boot file nahin | PXE boot options, boot server, file path/service |
| Kernel/installer load fail | Boot image, HTTP/TFTP, compatibility |
| Disk not found | Storage controller, RAID/JBOD, installer drivers |
| Installed but won't boot | UEFI mode, boot order, disk layout, bootloader |

## 11. Step 7 — Linux validation aur Puppet

**Puppet** desired configuration ko declarative policy ke zariye manage karta hai. Aapka Ansible experience concepts samajhne mein help karega, lekin **Puppet aur Ansible identical nahin**.

### Aap kya verify karenge?

- Linux **successfully boot** hua?
- Expected OS version, hostname aur kernel?
- NIC/IP/route/DNS (agar relevant) sahi?
- Expected disks, filesystems aur mounts?
- Required services running hain?
- Puppet **agent/report** ya team's configuration pipeline successful hai?
- Required security/configuration baseline aur validation tests pass hain?

### Example commands (only when authorized)

```bash
hostnamectl
cat /etc/os-release
uname -r
ip -br addr
ip route
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
findmnt
systemctl --failed
journalctl -b -p warning --no-pager
```

Puppet tools/paths deployment par depend karte hain; example commands **agar Puppet agent installed aur access approved ho**:

```bash
puppet --version
systemctl status puppet --no-pager
journalctl -u puppet --no-pager -n 50
```

**Do not assume** ke har Vultr server par exactly ye Puppet commands ya service names honge. Team-specific workflow use karein. Unauthorized `puppet agent -t` run se changes apply ho sakte hain.

### Troubleshooting framework

**Observe -> Logs -> Hypothesis -> Evidence -> Approved fix/escalation -> Retest -> Document.**

Roman Urdu: Pehle symptom samjhein, phir logs check, ek logical wajah sochain, proof gather karein, approved action lein, dobara test karein aur ticket update karein.

## 12. Step 8 — Production readiness, inventory aur handoff

### Record mein kya hona chahiye?

- Server **serial/asset tag**, hostname/ID, rack/RU.
- Hardware model, CPU/RAM, disk/RAID/JBOD aur GPU details.
- Network port, approved IP/VLAN references, BMC management record.
- BIOS/firmware baseline, OS image/version.
- Hardware health, PXE imaging, OS/Puppet/validation results.
- Exceptions, ticket links, incident/RMA numbers, timestamps.
- Handoff recipient/team aur required approval.

**Passwords/secrets inventory notes mein plaintext nahin.** Secure credential system ka reference use karein.

### Production-ready ka decision

- **PASS:** Required checks complete + documented + approval -> handoff.
- **BLOCKED:** Any critical failure -> isolate from production, ticket + escalation.
- **PENDING:** Technical checks complete but authorization pending -> approval ka wait.

**Interview line:** “I would not mark a server production-ready simply because it powers on. I would confirm the full validation checklist and document the approved handoff.”

## 13. Ek realistic din: 10 Dell PowerEdge servers

| Server | Example status | Aapka next action |
|---|---|---|
| 01 | Hardware/network/OS pass | Evidence record, approval/handoff |
| 02 | Hardware/network/OS pass | Evidence record, approval/handoff |
| 03 | iDRAC unreachable | Management VLAN/cabling/ACL investigate |
| 04 | Disk alert | BMC/storage logs, hardware escalation |
| 05 | DHCP IP nahin | NIC -> switch -> VLAN -> DHCP troubleshoot |
| 06 | PXE boot file missing | Boot service/configuration investigate |
| 07 | OS imaging failed | Installer logs, disk/image validation |
| 08 | Puppet report failed | Logs + team-approved remediation |
| 09 | GPU inventory mismatch | Hardware/PCIe verification + escalation |
| 10 | Validation complete | Inventory, evidence, approval/handoff |

**Yeh illustrative example hai, actual Vultr incident data nahin.** Aapki priority hamesha safety, accurate status aur clear handoff hogi, sirf server count complete karna nahin.

## 14. Common troubleshooting scenarios

### Scenario A — Server powers on, lekin DHCP IP nahin milti

1. BMC console par exact error/symptom note karein.
2. NIC link state aur intended PXE interface check.
3. Rack cable/switch port mapping confirm.
4. Provisioning VLAN aur switch port config network team se verify.
5. DHCP scope, relay, logs aur relevant policies check.
6. Fix sirf authorized process se; PXE retry, evidence document.

**Interview answer:** “I would troubleshoot layer by layer: NIC link, switch port, VLAN, DHCP scope or relay, and finally PXE services, using logs and the approved runbook.”

### Scenario B — iDRAC unreachable

1. Correct management address/DNS and access path verify.
2. Management VLAN/route/firewall/ACL verify.
3. Server standby power aur BMC status check.
4. Remote hands se physical cabling/switch-port verification.
5. Escalate if controller/hardware failure suspected.

### Scenario C — PXE gets IP but cannot download boot file

1. Confirm IP lease and expected boot program/path.
2. Check boot service availability and access controls.
3. Confirm UEFI/Legacy loader compatibility.
4. Examine boot server logs; approved fix; retest.

### Scenario D — Linux installed, but Puppet fails

1. Confirm OS boot and network reachability.
2. Read Puppet report/agent or pipeline logs.
3. Check certificate/auth, environment, dependency or connectivity issues as indicated by evidence.
4. Do not bypass security or rerun blindly.
5. Approved remediation, validation and documentation.

### Scenario E — Disk or GPU missing

1. Compare actual detected hardware to purchase/asset spec.
2. Check BMC and storage/PCIe event logs.
3. Confirm firmware/BIOS configuration and physical detection.
4. Coordinate remote hands or vendor; DOA/RMA if approved.
5. Keep server blocked until validation passes.

### Scenario F — Server boots but health check fails

1. Identify failing check (network, service, disk, firmware, GPU, Puppet).
2. Gather logs and compare expected baseline.
3. Follow approved fix/escalation.
4. Repeat **full required validation**, not only the single failed check.

## 15. Useful commands — what they tell you

| Command | Kya check hota hai? |
|---|---|
| `hostnamectl` | Hostname, OS details |
| `cat /etc/os-release` | Linux distribution/version |
| `uname -r` | Kernel version |
| `ip -br addr` | Interfaces aur IP addresses |
| `ip link` | NIC administrative/link state |
| `ip route` | Routing table |
| `ss -tulpen` | Listening sockets (permissions may vary) |
| `lsblk` | Block devices/disks |
| `findmnt` | Mounted filesystems |
| `df -h` | Filesystem capacity |
| `free -h` | RAM/swap summary |
| `systemctl --failed` | Failed systemd units |
| `journalctl -b` | Current boot logs |
| `dmesg` | Kernel messages (access restricted) |
| `lspci` | PCI devices, NIC/GPU detection |
| `ethtool <interface>` | NIC link/driver info (if installed) |

**Note:** Ye OS boot ke baad useful hain. OS install se pehle BMC, switch aur provisioning-system logs zyada important ho sakte hain.

## 16. Safety, security aur escalation

- **Do not assume access:** Only authorized networks, consoles, tickets and tools.
- **Do not wipe unknown disks:** Confirm asset identity and authorized imaging scope.
- **Do not randomly change BIOS/RAID/firmware:** Baseline and approval first.
- **Do not expose secrets:** No BMC password/token in ticket, chat screenshot or GitHub.
- **Do not force production handoff:** Failed check means blocked until resolution/approval.
- **Escalate early:** Hardware DOA, missing inventory, network team dependencies, unexplained security failures.
- **Document clearly:** Symptom, timestamp, asset, logs/evidence, tests, owner and next action.

## 17. Interview answers — English + Roman Urdu understanding

### Q1. What is production?

**English:** “Production is the live environment where infrastructure supports real customer services or provides approved capacity for those services.”

**Roman Urdu:** Production live environment hai jahan customers ki real services chalti hain ya jahan approved infrastructure un services ke liye ready hota hai.

### Q2. What is server onboarding?

**English:** “Server onboarding is the process of taking a newly racked physical server, validating its hardware and management access, configuring the approved firmware and storage settings, provisioning the operating system, verifying its health and configuration, and documenting it before production handoff.”

**Roman Urdu:** Naye rack hue server ko hardware se le kar Linux, validation aur documentation tak production-ready banana.

### Q3. What would your responsibilities be?

**English:** “I would start by confirming the handoff and hardware inventory. Then I would check iDRAC/BMC access, BIOS and firmware, disk and network readiness, and PXE-based imaging. After installation, I would verify Linux and the results of approved configuration management workflows, troubleshoot failures, update records, and follow the team's production handoff process.”

**Roman Urdu:** Handoff -> hardware -> BMC -> BIOS -> storage -> network -> PXE -> Linux/Puppet -> validation -> inventory -> approval.

### Q4. If the server powers on, is it production-ready?

**English:** “Not necessarily. Powering on only confirms part of the hardware is functioning. I would still need to verify hardware health, firmware and storage configuration, networking, OS imaging, required configuration and validation checks, and approved handoff.”

**Roman Urdu:** Power ON sirf shuruat hai; poori readiness checklist pass hona zaroori hai.

### Q5. What if a PXE server doesn't receive an IP?

**English:** “I would start with the NIC link and switch port, confirm the correct provisioning VLAN, then check DHCP scope, relay and logs. I would isolate the failing stage, follow the approved fix procedure, and retest.”

**Roman Urdu:** NIC -> switch -> VLAN -> DHCP; evidence ke saath layer-by-layer check.

### Q6. Have you used Puppet or production PXE before?

**Truthful English example:** “My hands-on background is primarily Linux administration, troubleshooting and Ansible. I understand the purpose of Puppet and PXE in automated provisioning, and I would be comfortable learning and following your existing runbooks and workflows. I would not claim production Puppet or PXE experience I haven't had.”

**Roman Urdu:** Apna real Ansible/Linux experience confidently batayein, lekin Puppet/PXE ka invented production experience na batayein.

### Q7. What if you discover a faulty disk?

**English:** “I would capture the hardware alert and relevant logs, verify the asset and disk details, mark the server blocked for production, and follow the approved hardware replacement or RMA escalation process.”

**Roman Urdu:** Faulty disk ko ignore nahin karna; evidence, block, ticket, escalation, retest.

### Q8. What does a good handoff look like?

**English:** “A good handoff includes a validated server, accurate inventory and network details, documented test results, resolved or clearly owned exceptions, and the required approval for production.”

**Roman Urdu:** Sirf ‘done’ bolna kafi nahin; proof aur accurate records bhi chahiye.

## 18. Mock interview self-test

In sawalon ke jawab **apne words** mein bol kar practice karein:

1. Production aur production-ready mein kya farq hai?
2. 10 new servers receive hone par sabse pehla step kya hoga?
3. BMC aur SSH mein kya difference hai?
4. iDRAC unreachable ho to kaunse checks karenge?
5. BIOS/UEFI boot order PXE mein kyun important hai?
6. RAID aur JBOD mein conceptual difference kya hai?
7. Server ko DHCP IP na mile to troubleshooting order kya hoga?
8. PXE ko step by step samjhayen.
9. Linux install hone ke baad kya validate karenge?
10. Puppet fail hone par safe troubleshooting approach kya hai?
11. Disk/GPU missing ho to kya karenge?
12. Production handoff se pehle kya evidence chahiye?
13. Aap apna Linux/Ansible experience is role se kaise connect karenge?
14. Kya power-on server automatically production-ready hota hai?
15. Kab aap remote-hands, networking ya hardware team ko escalate karenge?

## 19. Final quick revision checklist

```text
[ ] Correct ticket, server identity and approved runbook
[ ] Rack, power, cables and hardware health checked
[ ] BMC/iDRAC access and event logs verified
[ ] BIOS/UEFI and firmware baseline confirmed
[ ] Disk/RAID/JBOD/GPU requirements verified
[ ] NIC, switch, VLAN, DHCP and PXE path validated
[ ] Correct OS image installed and first boot successful
[ ] Linux, services, network, storage and Puppet checks passed
[ ] Hardware/security/configuration issues resolved or blocked
[ ] Inventory, logs, tickets and validation evidence recorded
[ ] Required production approval and handoff completed
```

### One-line memory trick

**Handoff -> Hardware -> BMC -> BIOS -> Storage -> Network -> PXE -> Linux/Puppet -> Validate -> Document -> Approve.**

**Interview tip:** Answer naturally, not like a memorized script. Start with the big picture, then explain the checks in order, and mention that you follow approved runbooks and escalate safely.

---

*Prepared as interview study notes. Vendor-specific internal processes and production access must always be confirmed with the hiring team.*
