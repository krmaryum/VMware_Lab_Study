# Vultr Server Onboarding Specialist — Complete Interview Study Notes (Roman Urdu)

**Interview:** Thursday, 15 October 2026, **10:00 AM Central / Chicago** | **Join:** 9:50 AM  
**Platform:** Google Meet | **Interviewer:** Scott, Manager Server Onboarding | **Recruiter:** Dave Franklin  
**Position:** Remote, direct hire | **Submitted salary:** $80,000 | **Schedule:** 4 din × 10 ghantay rotating shifts

> **Source note:** Interview ka waqt, Dave ki tips, responsibilities aur qualification uski 12 October wali email/job description se li gayi hain. Neeche commands, troubleshooting examples, diagrams aur sample answers **study material** hain; inhein Vultr ki actual internal runbook na samjhein. Email mein naam **Scott Harman** hai lekin LinkedIn URL mein **hartman** hai—invite se confirm karein. Jahan aapko Puppet, PXE ya GPU ka production hands-on experience nahin, wahan “I would follow the approved runbook” kahein; jhoota claim na karein.

## Fehrist / Table of contents
1. Notes ka istemal
2. Interview logistics aur video preparation
3. Dave ki khas instructions
4. Vultr company research
5. Job ki asal responsibility aur boundary
6. Complete server onboarding workflow
7. Rack-and-stack aur hardware handoff
8. BMC, IPMI, iDRAC, iLO aur OOB IP
9. BIOS/UEFI aur firmware
10. RAID/JBOD aur disk health
11. Network, NIC, VLAN, DHCP, IPv4/IPv6
12. PXE boot aur automated Linux imaging
13. Linux validation commands
14. Puppet vs Ansible
15. CPU aur GPU servers
16. DOA, RMA aur remote hands
17. Inventory, runbooks aur handoff
18. Real-world troubleshooting scenarios
19. HR/behavioral interview Q&A
20. Technical interview Q&A
21. Weak technologies ko honestly explain karna
22. Scott se poochne walay sawalat
23. Interview tak revision plan
24. Quick cheat sheet
25. Source aur important cautions

---

## 1. Notes ka istemal

Khalid bhai, pehle workflow yaad karein, phir har technical topic samjhein, phir 60–90 seconds ka answer **English mein bol kar** practice karein. Sirf lines ratta na lagayein: har stage mein **kya check karna hai, failure kahan ho sakta hai, proof kya hai, aur next team ko kya handoff karna hai** samjhein.

Aapki strong side Linux administration, troubleshooting, Bash, Ansible aur server hardware exposure hai. Jo technology directly production mein use nahin ki, usay clearly limited experience kahein. Har scenario mein formula: **Safety → Verify → Isolate → Authorized fix → Recheck → Document**.

## 2. Interview logistics aur video preparation

Dave ke mutabiq Google Meet interview **Thursday 15 October, 10 AM Central** hai. **9:50 AM** tak login karein. Collared/button-down shirt, quiet room, camera/mic/speaker test, stable internet, resume aur questions ready. Salary aur benefits ka topic aap khud initiate na karein; agar interviewer poochay to professional jawab dein. $80,000 recruiter ka submitted figure hai, final offer ki guarantee nahin.

**9:50 se pehle:** laptop charge, meeting link test, phone silent, notifications band, water ready, neutral background. **Interview mein:** sawal poora sunein, zarurat ho to clarification lein, fake confidence ke bajaye logical troubleshooting explain karein. **Baad mein:** short thank-you message.

**Naam ka difference:** Dave ke email mein Scott Harman, LinkedIn URL mein Hartman. Invite check karein; doubt ho to sirf “Scott” kahein. Dave ne bataya ke woh July 2026 se Vultr mein manager hain aur pehle Cisco mein infrastructure roles mein thay—ye recruiter ki di hui information hai, independently verified bio nahin.

## 3. Dave ki khas instructions

Dave ne specifically kaha: company research karein, job description dobara parhein, resume ki har line explain kar sakein, HR answers aur technical answers tayyar karein, aur 5–7 questions ready rakhein. Uske sample sawal:

- Vultr kya karta hai? Aap Vultr kyun join karna chahte hain?
- Ye role kyun? Aap best fit kyun?
- Current/last company se move kyun kar rahe hain? Future goal?
- Kya aapne physical server scratch se provision kiya?
- iDRAC, iLO, IPMI, rack-and-stack, bare-metal provisioning ka experience?
- Cabling, BIOS, firmware, RAID, PXE, Linux OS imaging aur ops handoff?

Ye Dave ki expected preparation list hai; zaroori nahin ke Scott same alfaaz mein poochay.

## 4. Vultr company research

Dave ki supplied information ke mutabiq Vultr global cloud infrastructure provide karta hai: **Cloud Compute, Cloud GPU, Bare Metal aur Cloud Storage**. Job description mein global data centers aur **32 locations** ka zikr hai. Customers/countries/valuation jaisay numbers recruiter material ke hain, current verified statistics nahin. Aapke role ka business connection: naya physical hardware sahi tarah configure, image, validate aur record hoga to company usay reliable capacity ke tor par use kar sakegi.

**English answer:** “Vultr provides global cloud infrastructure, including cloud compute, GPU, bare-metal and storage services. I am interested in the infrastructure lifecycle behind those services, especially making physical hosts reliable and production-ready.”

**Roman Urdu meaning:** “Mujhe wo process pasand hai jisme naya server rack mein aata hai aur step-by-step test hokar production ke liye ready hota hai.”

## 5. Job ki asal responsibility aur boundary

Job description ke mutabiq aap **newly racked CPU/GPU servers** receive karenge, approved runbooks follow karenge, inventory, imaging, configuration, activation workflow aur validation handoff mein kaam karenge. Existing Puppet/onboarding jobs execute/troubleshoot karna, hardware DOA/RMA issues document karna, networking/storage/provisioning/validation teams se coordinate karna aur high-volume deployments track karna important hai.

**VMware?** Is JD mein ESXi/vCenter explicitly required nahin. VMware ka knowledge useful hai lekin main interview focus **bare-metal onboarding** hai. **Remote role ka matlab** physical cable aap khud lagayenge zaroori nahin; remote-hands technicians se coordination ho sakti hai.

**Production-ready ≠ production-active:** OS install ho jana enough nahin. Configuration, validation, documentation aur approval bhi chahiye.

## 6. Complete server onboarding workflow

```text
New server / handoff ticket
          ↓
Serial + rack + power + cables verify
          ↓
iDRAC/BMC management IP aur health verify
          ↓
BIOS/UEFI, firmware aur boot order
          ↓
RAID/JBOD aur disk health
          ↓
NIC link → Switch port → VLAN → DHCP
          ↓
PXE boot → Automated Linux installation
          ↓
OS boot + network + CPU/RAM/disks validation
          ↓
Existing Puppet workflow + result verification
          ↓
Inventory + ticket + screenshots/log evidence
          ↓
Validation team handoff + approved activation
```

**Realistic example (sirf illustration):** 10 Dell PowerEdge servers naye rack mein install hue. Aap ticket se serial match karte hain, remote hands se power/cabling confirm karte hain, BMC IP access karte hain, approved firmware/storage settings check karte hain, provisioning VLAN aur DHCP verify karte hain, PXE imaging monitor karte hain, Linux health/Puppet results check karte hain, phir har host ka inventory update karke validation ko handoff karte hain. Agar 2 hosts fail ho jayein to unko alag track karein; 8 healthy hosts ka progress policy ke mutabiq continue ho sakta hai.

## 7. Rack-and-stack aur hardware handoff

**Rack-and-stack** = server ko rack mein mount karna, power feeds connect karna, network/management cables lagana, labels aur port mappings maintain karna. Remote onboarding specialist onsite **remote hands** ko precise instruction de sakta hai.

**Verify list:** asset tag, serial, model, rack/U, PDU/power, power supply LEDs, management cable ya shared NIC, switch name/port, NIC link, disk count, GPU count, damage aur ticket requirements.

**English answer:** “I would verify the hardware handoff against the asset record, confirm power and management connectivity, and coordinate with remote hands for physical cabling or LED checks.”

**Important:** Sirf power LED jalne se network aur provisioning ready nahin hoti.

## 8. BMC, IPMI, iDRAC, iLO aur OOB IP

- **BMC** = motherboard ka management controller jo OS se independent kaam kar sakta hai.
- **IPMI** = management interface standard.
- **iDRAC** = Dell ka BMC management solution.
- **iLO** = HPE ka management solution.
- **OOB (Out-of-Band)** = hardware management ka alag logical network path.
- **In-band** = Linux OS ke through access, jaise SSH.

```text
Admin laptop / Approved VPN
           |
Management switch / VLAN
           |
iDRAC IP: 10.20.30.50        ← OOB IP (EXAMPLE)
           |
Dell PowerEdge physical server
           |
Linux IP: 192.168.10.100    ← OS/In-band IP (EXAMPLE)
           |
         SSH
```

**Samjhein:** Agar Linux crash ho gaya aur SSH nahin chal raha, iDRAC phir bhi chal sakta hai **agar BMC ko standby power aur management network mil raha ho**. Dedicated iDRAC RJ45 port bhi ho sakta hai, ya BMC shared NIC use kar sakta hai. **Separate IP zaroor hota hai, separate cable har case mein zaroori nahin.** OOB IP static ya DHCP se mil sakta hai, policy par depend karta hai. Public internet par BMC expose na karein.

```bash
ssh admin@192.168.10.100       # Linux OS IP (example)
ping -c 3 10.20.30.50          # BMC IP (ICMP block ho sakta hai)
ipmitool lan print 1           # Supported host/channel aur permission required
ipmitool chassis status
```

**BMC unreachable?** Management VPN, IP record, subnet/gateway, switch port/VLAN, cable, DHCP lease, standby power, remote hands aur logs check karein. Bina approval reset ya password change na karein.

## 9. BIOS/UEFI aur firmware

**BIOS/UEFI** hardware initialize karta hai aur decide karta hai boot kahan se hoga. **Firmware** motherboard, NIC, BMC, RAID controller etc. mein embedded software hota hai. Check karein: approved version, UEFI/legacy boot mode, PXE-enabled NIC, boot order, CPU/RAM detect ho rahe hain, disks, hardware alarms aur required platform settings.

**Agar PXE start hi nahin hota:** boot order, UEFI/legacy mismatch, correct NIC, network link aur firmware settings check karein. Firmware update reboot la sakta hai; change approval aur model compatibility zaroori hai.

**English answer:** “I would compare BIOS and firmware versions with the approved baseline, confirm the intended boot mode and PXE NIC, and apply updates only through the authorized procedure.”

## 10. RAID/JBOD aur disk health

**RAID 0:** striping, redundancy nahin. **RAID 1:** mirroring, do-disk mirror mein ek drive failure tolerate ho sakta hai. **RAID 5/6:** parity-based options, design-specific tradeoffs. **RAID 10:** mirrors plus striping. **JBOD:** disks individual expose hoti hain (controller implementation vary karti hai).

Check: expected disk count/capacity, controller status, approved RAID/JBOD mode, failed disks, disk slot, OS installer ko storage dikh rahi hai ya nahin.

```bash
lsblk -o NAME,SIZE,TYPE,MODEL,MOUNTPOINTS
sudo fdisk -l
findmnt
sudo dmesg -T | grep -Ei 'disk|nvme|scsi|error'
```

**Safety:** Bina approval RAID delete/recreate, initialize, ya mode change na karein—data loss ho sakta hai.

## 11. Network: NIC, VLAN, DHCP, IPv4/IPv6

Network troubleshooting layers:

```text
Physical NIC/link
      ↓
Switch port
      ↓
Correct VLAN
      ↓
DHCP scope / relay / route
      ↓
PXE boot service
      ↓
Linux network configuration
```

**NIC** = network interface. **VLAN** = Layer 2 segmentation. **Subnet** = Layer 3 IP addressing. **DHCP** = IP, mask, gateway aur architecture ke mutabiq boot-related information. **IPv4/IPv6** dono JD mein hain; IPv6 ka PXE/discovery path implementation-specific hota hai.

```bash
ip -br link
ip -br addr
ip route
ip -6 route
ethtool eno1            # Device name different ho sakta hai
ping -c 3 <gateway-IP>
```

**Key concept:** iDRAC reachable hona is baat ka proof nahin ke Linux/PXE NIC ko DHCP mil raha hai—management aur provisioning VLAN alag ho sakti hain.

## 12. PXE boot aur automated Linux imaging

**PXE = Preboot Execution Environment.** Naya server bina installed OS ke network se boot program aur installer la sakta hai. DHCP ke baad TFTP/HTTP/iPXE/UEFI HTTP jaisi methods use ho sakti hain; har environment same nahin hota.

```text
Power on
  ↓
BIOS/UEFI network boot select
  ↓
DHCP se network/boot info
  ↓
Bootloader / boot program download
  ↓
Kernel + initramfs load
  ↓
Automated Linux installer
  ↓
Disk par approved OS install
  ↓
Local disk boot
  ↓
Puppet + validation
```

**Failures ko separate karein:**
1. Link nahin → cable, NIC, switch.
2. IP nahin → VLAN, DHCP, relay.
3. IP hai, boot file nahin → TFTP/HTTP, wrong filename, UEFI mismatch.
4. Installer start, fail → image URL, DNS, repository, disk/controller, driver.
5. Install complete, boot fail → boot order, EFI, disk/controller.
6. OS boot, config fail → Puppet/network/dependencies.

**Interview answer:** “I would identify the exact PXE stage that failed rather than treating all network-boot failures as DHCP issues.”

## 13. Linux validation commands

OS install ke baad confirm karein: OS version, kernel, hostname, IP, route, NICs, disks, CPU, RAM, GPU (agar applicable), failed services, boot logs, Puppet result. Compare approved baseline se.

| Command | Roman Urdu purpose |
|---|---|
| `cat /etc/os-release` | OS version |
| `uname -r` | Kernel |
| `hostnamectl` | Host identity |
| `ip -br addr` | IP aur interfaces |
| `ip route` | Routing |
| `lsblk` | Disks aur partitions |
| `df -hT` | Filesystem space |
| `lscpu` | CPU details |
| `free -h` | RAM |
| `lspci` | PCI devices/GPU |
| `systemctl --failed` | Failed services |
| `journalctl -b -p warning` | Boot warnings |
| `dmesg -T` | Kernel/hardware logs |

**Documentation:** serial, ticket, image version, BMC/OS IP, VLAN, disks/GPU, test results, failures, screenshots/logs aur next owner.

## 14. Puppet vs Ansible

Aapko Ansible ka hands-on hai. **Ansible** aksar SSH aur YAML playbooks se tasks run karta hai. **Puppet** commonly manifests/catalogs aur agent/server workflow se desired configuration enforce karta hai. Dono repeatable configuration mein help karte hain, lekin commands aur architecture same nahin.

JD kehta hai **existing Puppet workflows run aur troubleshoot** karna—zaroori nahin ke naya Puppet code likhna ho.

```bash
sudo systemctl status puppet
sudo journalctl -u puppet --no-pager -n 100
sudo puppet agent --test   # Configuration change kar sakta hai: bina approval RUN NA karein
```

**Failure approach:** exact error + timestamp, DNS/network/time/certificates, repository, service logs, known-good host, workflow owner, authorized rerun, verification, documentation.

**English interview answer:** “My direct production Puppet experience is limited, but I have hands-on Ansible experience for Linux automation. I understand desired-state configuration and would follow the existing Puppet runbooks, review logs, isolate failures, and rerun only when authorized.”

## 15. CPU aur GPU servers

CPU server: CPU count/model, RAM, disks, NIC, firmware, power, thermals aur OS verify. GPU server mein extra: GPU count/model/slot, PCIe recognition, power/temperature, driver compatibility, firmware aur platform-specific validation. JD GPU brand/model specify nahin karta.

```bash
lscpu
free -h
lspci | grep -Ei 'vga|3d|nvidia|amd'
nvidia-smi  # Sirf applicable NVIDIA host/driver par
```

**GPU missing:** inventory expected count compare, BMC hardware health, `lspci`, driver tools, power/thermal, remote hands. Har GPU NVIDIA nahin hota aur har missing GPU DOA nahin hota.

## 16. DOA, RMA aur remote hands

**DOA = Dead On Arrival:** newly deployed hardware start se faulty. **RMA = Return Merchandise Authorization:** vendor repair/replacement process. Sirf ping fail hone se DOA prove nahin hota; network ya OS issue bhi ho sakta hai.

**Triage:** serial, symptoms, power/PDU, BMC/SEL logs, PSU, disks, RAM, NIC, boot behavior, known-good comparison; evidence ticket mein; remote hands se physical check; hardware team/vendor escalation. Host ko approved process ke mutabiq flag/quarantine karein.

**Remote hands** = data center ke onsite technicians jo aapke remote request par cable reseat, LEDs check, drive inspect, etc. kar sakte hain. Request mein rack/U, serial, port, exact action aur expected proof dein.

## 17. Inventory, runbooks aur handoff

Inventory mein: serial/asset tag, model, rack/U, switch/port, BMC IP aur secure credential reference, provisioning/OS IP, VLAN, disks, GPU count, firmware/BIOS, OS/image, status, ticket, issue, owner. Dave ki JD specifically switch assignments, rack locations, BMC credentials, disk inventory, GPU population mention karti hai. **Password plaintext mein ticket mein na likhein.**

**Runbook** = approved step-by-step procedure. Deviation, issue, workaround aur approval properly record karein. **Handoff** = validation team ko tested machine aur complete evidence dena; production activate karna alag approval stage ho sakta hai.

## 18. Real-world troubleshooting scenarios

### Scenario A: Server PXE boot par IP nahin leta

1. Correct server, NIC aur MAC identify.
2. NIC link check, cable/port verify.
3. Switch port aur provisioning VLAN check.
4. DHCP scope, leases, relay/helper aur reachability check.
5. Same VLAN ke working host se compare.
6. Network/remote-hands team se evidence ke sath coordinate.
7. Authorized fix ke baad retry, result aur ticket update.

**English short answer:** “I would verify the intended NIC and physical link first, then the switch port and VLAN, followed by DHCP scope and relay. I would compare with a known-good server, capture the MAC address and logs, coordinate the approved fix, and revalidate PXE boot.”

### Scenario B: iDRAC IP unreachable

Management VPN, IP/subnet, VLAN, switch port, cable, standby power, DHCP/static config, BMC logs aur remote hands verify. Linux SSH failure aur BMC failure ko mix na karein.

### Scenario C: DHCP IP mil gaya, PXE boot file nahin milti

UEFI/legacy mode, boot filename/architecture, TFTP/HTTP server, file permissions/path, network ACL, logs check. **DHCP successful ≠ imaging successful.**

### Scenario D: Linux installer ko disk nahin dikh rahi

Controller, RAID/JBOD mode, virtual disk, disk health, firmware, driver, installer logs. Destructive changes bina approval nahin.

### Scenario E: Linux installed, SSH fail

BMC console se OS boot check, IP/routes, firewall, `sshd`, credentials, network link. Issue OS, access policy ya network mein ho sakta hai.

### Scenario F: Puppet failed

Exact error, run ID, host, timestamp, service logs, DNS/time/certificates, repo, network, known-good compare, workflow owner, approved rerun, revalidation.

### Scenario G: GPU detect nahin ho raha

Expected GPU count, PCIe (`lspci`), BMC alerts, driver/tool if applicable, power/thermals, slot and serial, hardware escalation.

### Scenario H: 10 mein se 2 servers fail

8 healthy machines ka progress policy ke mutabiq continue; 2 fail hosts separate tickets; exact issue category, owner, evidence; kabhi 10/10 complete report na karein.

### Scenario I: Firmware mismatch

Approved baseline, model compatibility, change window, update process, reboot impact, post-update version aur health verify.

### Scenario J: Wrong switch-port mapping

Serial/MAC/rack/port cross-check, switch evidence, remote hands, approved inventory correction, VLAN/PXE retest.

## 19. HR/behavioral interview Q&A — English answers + Roman Urdu samajh

**Q1. Tell me about yourself.**  
**English:** “I’m a Linux systems administrator with several years of experience supporting enterprise Linux infrastructure, troubleshooting services, and using Bash and Ansible for automation. I also have exposure to physical server hardware and remote management. I enjoy structured infrastructure work, and this role is a strong match because it combines hardware, Linux, provisioning, troubleshooting, and reliable handoff.”  
**Roman Urdu:** Apni Linux expertise batayein, hardware exposure ko realistic rakhein, aur role se natural connection banayein.

**Q2. Why Vultr?**  
**English:** “Vultr's cloud compute, bare-metal and GPU infrastructure interests me because I enjoy working on the foundational systems behind reliable cloud services.”  
**Roman Urdu:** Company ke cloud infrastructure aur reliability se apni interest connect karein.

**Q3. Why this role?**  
**English:** “I like the full hardware-to-production lifecycle: verifying servers, troubleshooting networking and imaging, validating Linux systems, and documenting a clean handoff.”  
**Roman Urdu:** Ye job sirf OS maintenance nahin; naya server production-ready banana hai.

**Q4. Why are you a good fit?**  
**English:** “My strengths are Linux administration, troubleshooting, Bash/Ansible, careful documentation, and following repeatable procedures. I communicate blockers with evidence and know when to escalate.”  
**Roman Urdu:** Real strengths ko JD se map karein; unsupported PXE/Puppet expertise claim na karein.

**Q5. Why leave your company?**  
**English:** “I am looking to focus more directly on infrastructure provisioning and the hardware-to-production lifecycle, where I can apply my Linux background and grow.”  
**Roman Urdu:** Positive future-focused reason dein; actual employment status ke mutabiq tense change karein.

**Q6. Where do you see yourself?**  
**English:** “I want to become highly effective in server onboarding, fleet troubleshooting, and automation, and help improve operational reliability and documentation.”  
**Roman Urdu:** Technical growth aur team contribution par focus.

**Q7. Difficult incident?**  
**STAR:** Situation = kya issue; Task = aapki responsibility; Action = commands/logs/team coordination; Result = verified outcome. **Apna real incident use karein**, invented metrics nahin.

**Q8. Multiple urgent tasks?**  
**English:** “I prioritize by impact and safety, track each host and blocker, communicate with owners, and never skip validation to increase deployment counts.”

**Q9. Don't know something?**  
**English:** “I would be transparent, check the approved runbook and logs, compare a known-good host, and ask the responsible team rather than guess.”

**Q10. Weakness/development area?**  
**English:** “My production Puppet experience is more limited than my Linux and Ansible experience. I understand the core configuration-management concepts and am actively preparing for the onboarding workflow.”

## 20. Technical interview Q&A — 30 sawalat

**1. Server onboarding kya hai?** New physical server ko inventory, BMC, BIOS, disks, network, imaging, config, validation aur handoff ke through production-ready banana.

**2. Bare metal vs VM?** Bare metal physical machine; VM hypervisor par software-defined machine. Is role ka primary focus physical host onboarding.

**3. BMC kya hai?** Embedded hardware controller jo OS ke bina hardware management kar sakta hai.

**4. IPMI, iDRAC, iLO difference?** IPMI management standard; iDRAC Dell solution; iLO HPE solution.

**5. OOB mein kaunsa IP?** iDRAC/iLO/BMC management IP; Linux OS IP se normally alag.

**6. Linux crash ho to iDRAC?** Haan, agar BMC ko standby power aur management network available ho.

**7. UEFI kya hai?** Hardware initialization aur boot selection ka firmware interface.

**8. Firmware kya hai?** Device/controller ka embedded software; approved baseline follow hota hai.

**9. RAID vs JBOD?** RAID disks ko defined redundancy/performance layout mein group karta hai; JBOD disks individually expose kar sakta hai.

**10. RAID 0 vs RAID 1?** RAID 0 striping no redundancy; RAID 1 mirroring.

**11. PXE kya hai?** Network se bootloader/installer load karke OS install/diagnostics start karna.

**12. DHCP PXE mein kya karta hai?** IP/network configuration aur kai designs mein boot discovery information deta hai.

**13. VLAN kya hai?** Layer 2 logical segmentation.

**14. BMC works, PXE no IP?** Management network theek, provisioning NIC/VLAN/DHCP alag problem ho sakti hai.

**15. DHCP works, boot fails?** Boot filename, architecture, TFTP/HTTP service, path, ACL aur logs check.

**16. Automated imaging?** Approved OS ko repeatable automated process se deploy karna.

**17. Linux install validate?** OS/kernel, NIC/IP/routes, disks, CPU/RAM/GPU, services, logs, Puppet result.

**18. Puppet?** Configuration management tool jo declared state apply/verify karta hai.

**19. Puppet vs Ansible?** Dono automation/config management; Ansible aksar SSH/YAML playbooks, Puppet agent/catalog/manifests.

**20. Puppet fail?** Error capture, dependencies/logs check, owner coordinate, approved rerun, revalidate.

**21. DOA?** Initial deployment par genuinely faulty hardware.

**22. RMA?** Vendor repair/replacement authorization process.

**23. Remote hands?** Onsite technicians jo remote team ke precise physical tasks karte hain.

**24. Inventory?** Serial, rack, switch/port, BMC, disks, GPU, firmware, image, ticket, status.

**25. Production-ready kab?** Approved configuration, validation, documentation aur handoff ke baad.

**26. VMware required?** Dave ki JD mein explicitly nahin. Useful knowledge hai, confirmed duty nahin.

**27. GPU missing?** Inventory, BMC, `lspci`, driver, power/thermal, escalation.

**28. Server power on nahin?** Rack power/PDU, PSU, BMC logs, remote hands, hardware safety.

**29. 50 hosts onboarding?** Per-host tracking, batch process, exceptions, validation aur clear ownership.

**30. RAID change se pehle?** Approved layout, host identity, data-loss risk, change approval, recovery path.

## 21. Weak technologies ko honestly explain karna

**Puppet:** “My direct production Puppet experience is limited, but I have hands-on Ansible experience and understand desired-state configuration. I would follow the approved Puppet runbook and troubleshoot using logs and validation.”

**PXE:** “I understand the PXE sequence from NIC and DHCP through bootloader, installer and Linux validation. I would isolate the failing stage and follow the approved imaging workflow.”

**GPU:** “My strongest background is Linux and general server hardware. I understand GPU hosts add inventory, PCIe, driver, power and thermal checks, and I would use the platform-specific runbook.”

**VMware:** “I have virtualization exposure and a VMware learning lab, but I understand this role is focused on physical server onboarding rather than assuming ESXi management is required.”

**Yaad rakhein:** “I did”, “I understand”, aur “I would do” teen different statements hain. Sirf sach bolna hai.

## 22. Scott se poochne walay sawalat

5–7 ready rakhein; time ke mutabiq 2–3 pooch sakte hain:

1. “What are the biggest technical challenges the onboarding team faces today?”
2. “What would success look like for a new specialist in the first 90 days?”
3. “How do you measure strong performance: throughput, first-pass validation, or other metrics?”
4. “How do onboarding, networking, storage, remote-hands, and validation teams work together?”
5. “What are the main differences between onboarding CPU and GPU hosts?”
6. “Which steps in PXE imaging and Puppet are automated, and where is manual troubleshooting most common?”
7. “Does this team work primarily on bare-metal Linux hosts, or also configure KVM/VMware hypervisors?”

Dave ne challenges, 90 days/6 months, performance KPIs aur team makeup par sawal poochne ko kaha. Salary/benefits aap khud initiate na karein.

## 23. Interview tak revision plan

**Monday 12 Oct:** Vultr overview, OOB/BMC, BIOS/firmware, RAID, VLAN/DHCP, PXE, intro aur Why Vultr.

**Tuesday 13 Oct:** 10 troubleshooting scenarios, Puppet/Ansible, GPU, DOA/RMA, 60-second answers practice.

**Wednesday 14 Oct:** Full mock interview, resume ki har bullet, real STAR incident, Scott ke questions, camera/mic test.

**Thursday 15 Oct:** **9:50 AM Central** join; shirt, water, notes, calm speaking, honest examples.

**Checklist:**
- [ ] Invite/Scott surname verify
- [ ] Google Meet camera/mic/speakers ready
- [ ] OOB IP aur OS IP ka difference clear
- [ ] End-to-end onboarding 90 seconds mein explain
- [ ] PXE no-IP aur boot-file failure troubleshoot
- [ ] RAID/JBOD aur firmware safety
- [ ] Puppet limited experience ko Ansible se connect
- [ ] Real STAR troubleshooting story
- [ ] 5–7 questions ready
- [ ] Kisi unknown technology ka fake production claim nahin

## 24. Quick cheat sheet

```text
RACK → SERIAL → BMC → BIOS/FIRMWARE → RAID/JBOD
     → NIC/VLAN/DHCP → PXE → LINUX → PUPPET
     → INVENTORY → VALIDATION → APPROVED HANDOFF

OOB IP: iDRAC/iLO/BMC management address
IN-BAND IP: Linux OS / SSH address
BMC reachable ≠ PXE NIC reachable
DHCP successful ≠ PXE image successful
OS installed ≠ production approval
RAID changes: authorization required
Puppet fail: logs → isolate → coordinate → approved rerun → verify

LINUX COMMANDS:
ip -br addr | ip route | lsblk | lscpu | free -h
lspci | cat /etc/os-release | systemctl --failed | journalctl -b

INTERVIEW FORMULA:
Verify → Isolate → Authorized action → Validate → Document
```

## 25. Source aur important cautions

**Dave ki email se:** date/time, Google Meet, early login, dress, company/manager info, HR/technical question examples, $80k submitted pay, job responsibilities aur qualifications, 4×10 rotating schedule.

**Extra learning content:** sample IPs, commands, network/PXE flow details, RAID explanation, troubleshooting, GPU diagnostics, model answers aur daily workflow. Ye Vultr internal architecture ka proof nahin.

**Ambiguities:** Scott surname ka mismatch; remote role mein rack ka physical kaam remote hands kar sakte hain; VMware JD mein listed nahin; aapka actual Puppet/PXE production experience overstate nahin karna.

---
**Final reminder:** Apni real Linux aur hardware experience ko confidently explain karein. Jo directly nahin kiya uske liye logically batayein ke approved runbook ke mutabiq kaise troubleshoot karenge.
