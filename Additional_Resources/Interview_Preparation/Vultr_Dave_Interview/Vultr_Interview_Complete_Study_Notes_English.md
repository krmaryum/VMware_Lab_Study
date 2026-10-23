# Vultr Server Onboarding Specialist — Complete Interview Preparation (English)

**Interview:** Thursday, October 15, 2026, 10:00 AM Central (Chicago) | **Join:** 9:50 AM | **Platform:** Google Meet  
**Interviewer:** Scott, Manager of Server Onboarding | **Recruiter:** Dave Franklin, INSPYR Solutions  
**Role:** Remote, direct hire, Server Onboarding Specialist | **Submitted salary:** $80,000 | **Schedule:** rotating four 10-hour shifts

> **Source and accuracy note:** The interview facts, hiring manager guidance, duties, qualifications, and suggested questions below are based on Dave's October 12 email and the job description he included. The technical tutorials, command examples, and sample responses are **additional study material**, not statements of Vultr's internal procedures. The email writes the manager's surname as **Harman**, while its LinkedIn URL contains **hartman**; check the calendar invite before using the surname. Product/company figures in Dave's email are recruiter-provided and may change. Do not present illustrative IPs, VLANs, hardware, commands, or workflows as Vultr's actual environment.

## Table of contents
1. How to use these notes
2. Interview logistics and video readiness
3. Dave's key advice and expected questions
4. Vultr research and business context
5. Role summary and boundaries
6. End-to-end physical server onboarding
7. Rack-and-stack and hardware handoff
8. BMC, IPMI, iDRAC, iLO, and out-of-band IP
9. BIOS, UEFI, firmware, and boot settings
10. RAID, JBOD, storage, and disk health
11. Networking: NICs, VLANs, DHCP, IPv4/IPv6
12. PXE boot and automated Linux imaging
13. Linux validation and essential commands
14. Puppet, Ansible, and existing automation
15. CPU and GPU server considerations
16. DOA, RMA, remote hands, and escalation
17. Fleet inventory, runbooks, and handoff
18. Troubleshooting playbooks and scenarios
19. Behavioral/HR interview questions and model answers
20. Technical interview questions and model answers
21. Honest ways to discuss skill gaps
22. Questions to ask Scott
23. Three-day revision plan and interview checklist
24. Quick-reference cheat sheet
25. Source mapping and important caveats

---

## 1. How to use these notes

Read the end-to-end workflow first, then review the technical chapters. Practice answering aloud in 60–90 seconds, with a specific example when possible. Do not memorize words mechanically: learn the **sequence, reasoning, verification, and handoff**. Your strongest foundation is Linux administration, hardware exposure, troubleshooting, Bash, and Ansible. Avoid implying that you have personally deployed Vultr servers, operated production Puppet, or owned PXE automation unless you can substantiate it.

Use this five-part structure in scenario answers: **scope and safety → verify facts → isolate layer → corrective action within authorization → validate and document**.

## 2. Interview logistics and video readiness

Dave's email says: Google Meet, Thursday October 15 at 10:00 AM Central; log in ten minutes early. Wear a collared or button-down shirt (business casual); find a quiet space; test camera, microphone, speaker, browser, and connection. Keep the invitation, resume, and five to seven thoughtful questions accessible. He advises **not initiating salary or benefits discussions with the interviewer**; if asked, answer professionally. The submitted compensation is $80,000, not a guarantee of offer terms.

**Before 9:50 AM:** charge laptop, connect stable internet, close distracting tabs, silence phone, set a neutral background, check your display name, open a notes page, keep water nearby. **During:** listen fully, clarify ambiguities, think aloud briefly, never bluff. **After:** send a concise thank-you through the appropriate channel.

**Interviewer identity caveat:** email text says Scott Harman, LinkedIn URL says Scott Hartman. Confirm spelling using the invitation; simply say “Scott” if unsure. Dave describes him as a recent Vultr manager (since July 2026) with a long infrastructure career and prior Cisco experience. Treat those as recruiter-provided context, not independently verified biographical facts.

## 3. Dave's key advice and expected questions

Dave specifically asks you to research Vultr, reread the role, know your resume, prepare HR and technical answers, and prepare 5–7 questions. His explicit likely topics:

- What do you know about Vultr? Why Vultr? Why this role? Why are you a fit?
- Why are you leaving your current employer? Where do you see yourself professionally?
- Have you provisioned a physical server from scratch? Which remote management technology?
- Bare-metal provisioning, rack-and-stack, iDRAC/iLO/IPMI.
- Racking, cabling, RAID, PXE, OS imaging, BIOS/firmware, and operations handoff.

**Interpretation:** These are preparation prompts from Dave, not a guarantee that Scott will ask them verbatim.

## 4. Vultr research and business context

Per Dave's material, Vultr provides global cloud infrastructure: **Cloud Compute, Cloud GPU, Bare Metal, Cloud Storage**, and related infrastructure services. The job description describes expanding CPU and GPU capacity across global data centers, and references **32 data center locations**. Its customer count, country reach, and company valuation are statements from the recruiter-supplied description, not verified live metrics. The practical connection: physical hosts must be configured, imaged, checked, recorded, and released correctly before they can contribute reliable customer-facing capacity.

**Explain it in plain English:** “Vultr offers cloud infrastructure products. The onboarding team helps turn newly installed physical CPU and GPU machines into correctly configured, validated infrastructure capacity.”

**What interests me:** hardware-to-OS lifecycle, high-volume standardization, reliability, hands-on troubleshooting, and collaboration between networking, storage, provisioning, and validation teams.

**Useful thoughtful observation:** A single incorrect BMC IP, switch-port mapping, disk mode, or firmware configuration can cause repeated provisioning failures. Consistent inventory and clear runbooks are therefore operational controls, not just paperwork.

## 5. Role summary and boundaries

The job description states that this team receives **newly racked CPU/GPU servers**, follows established runbooks, performs inventory/imaging/configuration/activation, troubleshoots existing onboarding workflows, coordinates with upstream teams, and hands off to validation. It is a **first-line onboarding role** in high-volume deployments, with remote-hands collaboration. Qualifications include 2+ years Linux administration, OOB familiarity, BIOS/RAID/firmware, PXE/DHCP/VLAN/IPv4/IPv6, existing Puppet workflows, basic scripting, communication, and documentation.

**What is not explicitly stated:** VMware ESXi, vCenter, vMotion, managing customer VMs, writing new Puppet manifests, building a PXE platform from scratch, designing network architecture, or doing physical rack work personally while remote. Ask how responsibilities are divided rather than assuming.

**Production-ready versus production-active:** Onboarding validates readiness; a separate team or approval gate may activate a server. Never equate “Linux installed” with “approved for customer use.”

## 6. End-to-end physical server onboarding

```text
Hardware handoff / ticket
       ↓
Asset identity + rack/power/cabling verified
       ↓
BMC/iDRAC reachable; health and logs reviewed
       ↓
Approved BIOS/UEFI + firmware + boot order
       ↓
Approved RAID/JBOD layout; disks healthy
       ↓
NIC link + correct switch port/VLAN + DHCP
       ↓
PXE/iPXE boot + automated Linux image
       ↓
First OS boot + Linux/network/storage checks
       ↓
Run and validate existing Puppet configuration
       ↓
Update inventory, evidence, blockers, tickets
       ↓
Validation-team handoff and authorized activation
```

**Example (illustrative, not Vultr runbook):** A batch of ten Dell PowerEdge machines arrives. Compare serial numbers to the ticket, confirm remote-hands rack/cabling status, connect to the authorized BMC management network, verify firmware and storage profiles, confirm the provisioning VLAN and DHCP path, observe PXE and OS deployment, review Puppet results, verify Linux health, record each asset and exceptions, and request validation/handoff. Track failed hosts individually instead of letting one failure block the entire batch.

## 7. Rack-and-stack and hardware handoff

**Rack-and-stack** means physically mounting equipment in racks, supplying power, cabling network and management interfaces, and labeling connections. A remote onboarding specialist may coordinate with **remote hands** who perform physical actions at the data center.

Verify: asset/serial and rack unit, power supplies and PDU feeds, front-panel hardware status, BMC cable or shared-NIC arrangement, switch/port mapping, correct NIC speed/link, management and provisioning network separation, disk/GPU inventory, and any shipment damage. Do not assume a machine is cabled correctly just because its power LED is on.

**Interview phrase:** “I would compare the rack-and-stack handoff against the asset record, confirm power and management access, and ask remote hands for physical checks when I cannot validate cabling remotely.”

## 8. BMC, IPMI, iDRAC, iLO, and out-of-band IP

- **BMC:** Baseboard Management Controller; embedded hardware management controller.
- **IPMI:** management interface/standard, not a separate brand of physical controller.
- **iDRAC:** Dell's remote management implementation.
- **iLO:** HPE's remote management implementation.
- **OOB:** management path independent of the running OS; it can work when Linux fails if the BMC has standby power and management connectivity.
- **In-band:** management through the host OS, e.g. SSH.

```text
Administrator / VPN / restricted management network
                    |
               Mgmt switch
                    |
             iDRAC/BMC IP: 10.20.30.50 (EXAMPLE)
                    |
           Dell physical server
                    |
             Linux OS IP: 192.168.10.100 (EXAMPLE)
                    |
                  SSH
```

**Important:** A separate management **IP** does not necessarily mean a separate cable. Dedicated BMC Ethernet ports and shared-NIC modes both exist. Management IPs may be statically assigned or delivered via DHCP, according to policy. Never expose BMC interfaces directly to the public internet. Use approved VPN/bastion, MFA, access controls, and credential storage. Do not place BMC passwords in tickets or notes.

**Commands/paths (examples only):**

```bash
ssh admin@192.168.10.100       # In-band OS access, example
ping -c 3 10.20.30.50          # ICMP may be blocked
curl -kI https://10.20.30.50   # Only for authorized diagnosis; -k bypasses TLS validation
ipmitool lan print 1           # On a supported/authorized host; channel may differ
ipmitool chassis status        # Requires supported interface and permissions
```

Prefer a normal browser to `https://<approved-BMC-IP>` with valid certificates where available. For unreachable BMC: verify recorded IP, access to management VPN/VLAN, link and switch port, gateway/subnet, BMC DHCP lease or static configuration, power state, and remote-hands checks. Do not reset BMC or change networking without authorization.

## 9. BIOS, UEFI, firmware, and boot settings

**BIOS/UEFI** initializes hardware and selects boot behavior; **firmware** runs on controllers such as BMC, NIC, storage, and system board. Check vendor/model compatibility, approved baseline, boot mode (UEFI/legacy), boot order, PXE-enabled NIC, CPU/memory recognition, disks, and platform-specific GPU settings if applicable. Firmware updates can cause reboot/downtime; require the approved maintenance process and documented versions.

**Troubleshooting:** If PXE never starts, inspect firmware boot menu, PXE NIC selection, boot order, link state, UEFI/legacy mismatch, and platform policy before blaming the imaging server.

## 10. RAID, JBOD, storage, and disk health

- **RAID 1:** mirrors data across drives; survives one drive failure in a two-drive mirror, at cost of usable capacity.
- **RAID 0:** stripes without redundancy; one member failure loses the array.
- **RAID 5/6:** parity layouts with different fault tolerance and performance tradeoffs; only if platform/runbook requires.
- **RAID 10:** mirrors and stripes, balancing redundancy and performance.
- **JBOD:** disks exposed individually (implementation varies); not the same as RAID redundancy.

Check drive count, size, slot identity, controller health, expected mode, virtual disks or pass-through, failed/predictive-failed media, and firmware. **Do not initialize arrays, delete virtual disks, or change RAID mode without approval**; these actions can destroy data or change device identities.

Useful Linux checks after imaging:

```bash
lsblk -o NAME,SIZE,TYPE,MODEL,MOUNTPOINTS
sudo fdisk -l
findmnt
sudo smartctl -a /dev/sdX   # Only for supported device; correct path required
sudo dmesg -T | grep -Ei 'disk|nvme|scsi|error'
```

## 11. Networking: NICs, VLANs, DHCP, IPv4/IPv6

Understand each layer: physical link and NIC → switch port → correct VLAN → DHCP reachability → PXE boot services → OS networking. A **VLAN** segments Layer 2 broadcast domains; an **IP subnet** is a Layer 3 addressing concept. They often align operationally but are not identical. **DHCP** can supply IP address, mask, router, and boot-related information depending on PXE architecture. IPv6 uses different discovery/boot mechanisms and should be checked against the actual runbook.

```bash
ip -br link                    # Interface/link overview
ip -br addr                    # Addresses
ip route                       # IPv4 routing
ip -6 route                    # IPv6 routing
ethtool eno1                   # Link details if tool and device exist
ping -c 3 <approved-gateway>    # Network reachability
journalctl -b --no-pager | tail -100
```

**Example isolation:** If no DHCP address appears, first confirm NIC link, port/VLAN assignment, then DHCP scope/relay and server logs. A successful BMC connection proves the management path works; it does **not** prove the host's provisioning VLAN works.

## 12. PXE boot and automated Linux imaging

**PXE** = Preboot Execution Environment: a host can boot installation code over the network instead of relying on an installed local OS. Actual deployments may use DHCP plus TFTP, HTTP, iPXE, UEFI HTTP boot, or other mechanisms.

```text
Power on → UEFI/BIOS chooses network boot
        → NIC obtains network/boot information (often DHCP)
        → downloads bootloader/boot program
        → downloads kernel + initramfs
        → launches automated installer
        → writes approved Linux image/config to disk
        → reboots from local disk
        → OS + configuration + validation
```

**Failure categories:**
1. **No link:** cable/NIC/switch port.
2. **No DHCP address:** VLAN, scope, relay, server reachability.
3. **DHCP works but no boot file:** wrong boot filename/architecture, TFTP/HTTP/iPXE access, UEFI mismatch.
4. **Installer starts but fails:** image URL, DNS, storage visibility, hardware driver, repository, network route.
5. **Install completes but won't boot:** boot order, EFI partition, storage/controller mode, OS bootloader.
6. **OS boots but config fails:** Puppet, credentials, dependencies, repository reachability.

**Do not claim:** that all PXE implementations always use TFTP, or that a PXE failure automatically indicates DHCP failure.

## 13. Linux validation and essential commands

After OS installation, check **OS version, kernel, uptime, interfaces, routes, disks, filesystems, CPU, RAM, services, errors, and configuration status**. Compare results with the approved specification rather than arbitrary personal preferences.

| Command | What it verifies |
|---|---|
| `cat /etc/os-release` | OS distribution and version |
| `uname -r` | Running kernel |
| `hostnamectl` | Host identity and OS details |
| `ip -br addr` | Interfaces and IPs |
| `ip route` | Routing |
| `lsblk` | Disk layout |
| `df -hT` | Mounted filesystem usage/types |
| `lscpu` | CPU inventory |
| `free -h` | Memory |
| `systemctl --failed` | Failed systemd units |
| `journalctl -b -p warning` | Boot warnings/errors (review context) |
| `dmesg -T` | Kernel/hardware messages (permissions vary) |
| `lspci` | PCI devices including NICs and GPUs |

**Validation record:** ticket ID, serial, rack/port, image/build ID, network results, disk/GPU inventory, Puppet outcome, failures, evidence, reviewer, and next owner.

## 14. Puppet, Ansible, and existing automation

**Puppet** and **Ansible** are configuration management tools; both aim for consistent state, but their architectures and execution patterns differ. Ansible commonly executes tasks over SSH and uses YAML playbooks; Puppet commonly evaluates manifests/catalogs and applies managed resources through its agent/server ecosystem. **The job explicitly emphasizes running/troubleshooting existing Puppet workflows, not writing new Puppet code.**

**Generic Puppet checks (only if authorized and installed):**

```bash
sudo systemctl status puppet
sudo puppet agent --test          # May apply changes: do NOT run without authorization
sudo journalctl -u puppet --no-pager -n 100
```

If a Puppet run fails: record error and stage; check host identity, DNS, time, certificates/permissions, network path, required package repositories, and service logs; compare with known-good host; coordinate with the owning team; rerun only when authorized; verify final state and document. Avoid speculative fixes in production.

**Honest interview answer:** “My direct Puppet production experience is limited. I have used Ansible for repeatable Linux administration and understand desired-state configuration. I would learn the approved Puppet runbook, inspect job results and logs, isolate failures, and escalate or rerun under the team's process.”

## 15. CPU and GPU server considerations

CPU servers: verify processor count/model, memory, NICs, disks, power/thermals, and OS. GPU servers add: GPU count, model, PCIe recognition, power/thermal requirements, GPU firmware and driver compatibility, and relevant platform validation. The exact GPU model, driver stack, and diagnostic utilities are **not specified** in Dave's job description.

```bash
lscpu
free -h
lspci | grep -Ei 'vga|3d|nvidia|amd'
nvidia-smi     # Only if NVIDIA driver/tool is installed and applicable
```

A GPU not visible in `lspci` suggests a lower-level detection issue; visible in `lspci` but absent from `nvidia-smi` can indicate a driver/tool/compatibility problem. Do not assume every Vultr GPU host uses NVIDIA.

## 16. DOA, RMA, remote hands, and escalation

**DOA:** dead on arrival, hardware that does not function as expected at first deployment. **RMA:** return merchandise authorization, the process to repair/replace eligible failed equipment. A host can fail onboarding for a software or network reason without being DOA.

**Triage:** record symptoms and serial; check power, BMC, SEL/hardware logs, NIC, disks, memory and boot behavior; compare known-good baseline; request safe remote-hands inspection; gather evidence; escalate to hardware/vendor workflow; update ticket and quarantine/flag the host per procedure. Never classify a machine DOA based only on one failed ping.

**Remote hands:** data center personnel perform physical actions on request, such as reseating a cable or reading a hardware LED. Give precise rack/unit, asset/port, action, safety constraints, and expected confirmation. Avoid ambiguous instructions like “reboot the server over there.”

## 17. Fleet inventory, runbooks, and handoff

Track serial/asset tag, model, rack/U position, switch/port, BMC address and secure credential reference, OS/provisioning address, VLAN, disks and GPU population, firmware/BIOS versions, image/build, validation status, ticket, exception, and owner. The job description specifically names **switch assignments, rack locations, BMC credentials, disk inventory, and GPU population**; store credential references securely rather than plaintext passwords.

**Runbook discipline:** follow versioned steps, record deviations, stop on destructive or security-sensitive ambiguity, and propose improvements through change control. **Handoff:** distinguish “imaging completed,” “configuration passed,” “validation approved,” and “production activated.”

## 18. Troubleshooting playbooks and scenarios

### Scenario A — New server powers on but receives no IP during PXE

1. Confirm correct host and provisioning NIC, boot screen message, and whether BMC is reachable.
2. Check physical NIC link and port state; coordinate remote hands if needed.
3. Verify switch port, provisioning VLAN, and port assignment with network team.
4. Verify DHCP scope, available leases, relay/helper, and service reachability.
5. Compare with a known-good server on same rack/VLAN; collect MAC, port, timestamps, DHCP logs.
6. Apply only authorized corrections; retry PXE, capture lease and imaging result; update ticket.

**Answer in 45 seconds:** “I would first distinguish physical link, VLAN, and DHCP issues. I'd verify the intended provisioning NIC and switch port, confirm the correct VLAN and DHCP scope or relay, and compare with a working host. I'd capture the MAC address and timestamps, coordinate with networking or remote hands, and rerun provisioning after the approved fix.”

### Scenario B — iDRAC unreachable

Confirm approved VPN/management path; recorded BMC IP and subnet; switch port and cable; standby power; DHCP lease/static config; compare neighboring hosts; request remote-hands LED/port checks; never change credentials or reset BMC casually.

### Scenario C — PXE gets an IP but fails to download boot program

Check firmware mode/architecture, DHCP boot information, boot server reachability, TFTP/HTTP availability, file path/permissions, and service logs. Distinguish **DHCP success** from **bootloader retrieval failure**.

### Scenario D — Linux installer cannot see disks

Verify storage mode/virtual disk creation in controller/BMC, disk health, controller drivers, BIOS recognition, and installer logs. Do not reconfigure RAID destructively without permission.

### Scenario E — Linux installed but cannot SSH

Check power/console, OS boot, host IP/routes, network link, firewall/security controls, sshd status, and authorized credentials. Use BMC console to distinguish OS/network issues from remote access issues.

### Scenario F — Puppet fails after imaging

Collect exact error, run ID, host and timestamp; inspect service/logs, DNS/time/certificates, repositories and dependencies; compare known-good host; escalate to workflow owner; rerun under approved procedure; revalidate.

### Scenario G — One GPU missing

Confirm expected GPU inventory, hardware/BMC health, PCIe visibility (`lspci`), drivers/tools if applicable, power/thermal alerts, and hardware team assessment. Document exact GPU slot/serial and do not guess DOA immediately.

### Scenario H — A batch of 10 servers, 2 fail

Continue independent healthy hosts where allowed; segregate two failed assets, categorize failures, attach evidence, communicate impact and ETA only when grounded, maintain inventory accuracy, and escalate with owners. Avoid reporting 10/10 complete.

### Scenario I — Firmware mismatch

Compare detected version with approved baseline and model compatibility; evaluate change window/impact; apply only authorized firmware procedure; verify post-update version, health, and boot.

### Scenario J — Wrong switch port recorded

Pause affected provisioning, confirm physical-to-logical mapping using asset tag, MAC, switch neighbor/port evidence, and remote hands; correct record through authorized workflow; validate VLAN and network path; document the discrepancy.

## 19. Behavioral and HR interview questions and model answers

**Q1. Tell me about yourself.**  
“I’m a Linux systems administrator with several years of enterprise experience supporting Linux infrastructure, troubleshooting operating systems and services, and working with automation such as Bash and Ansible. I also have experience around physical server hardware and remote management. I enjoy methodical infrastructure work, and this role interests me because it connects hardware verification, Linux provisioning, troubleshooting, and reliable production handoff.”

**Q2. Why Vultr?**  
“Vultr's focus on global cloud compute, bare-metal and GPU infrastructure appeals to me. I would enjoy contributing to the reliability and repeatability of the physical infrastructure behind those services.”

**Q3. Why this position?**  
“It combines my Linux troubleshooting background with hands-on infrastructure workflows: BMC access, firmware, storage, networking, imaging, validation, and documentation. I like clear procedures and solving issues that prevent systems from becoming production-ready.”

**Q4. Why are you a good fit?**  
“My strengths are Linux administration, systematic troubleshooting, Bash/Ansible automation, careful documentation, and coordination. I can follow runbooks, verify outcomes, and escalate with useful evidence. For tools I have not operated in production, I am transparent and learn quickly from established procedures.”

**Q5. Why leave your last/current company?**  
“I’m looking for a role where I can contribute more directly to infrastructure provisioning and the hardware-to-production lifecycle. The server onboarding focus fits the direction I want to grow.”  
*Adjust the tense and facts to your actual employment status; do not invent reasons or criticize employers.*

**Q6. Where in three years?**  
“I'd like to become highly effective in fleet onboarding, troubleshooting, and automation, and eventually help improve runbooks, validation quality, and operational efficiency.”

**Q7. Tell me about a difficult incident.**  
Use **STAR**: Situation (impact), Task (your responsibility), Action (checks/logs/coordination), Result (verified outcome). Pick a real incident from your experience; do not invent numerical savings or uptime improvements.

**Q8. How do you handle competing urgent tasks?**  
“Prioritize customer or deployment impact and safety, identify blockers, communicate status, keep ticket ownership clear, and escalate dependencies early. Avoid bypassing validation to hit a volume target.”

**Q9. What if you don't know the answer?**  
“I'd say what I know, identify the missing information, check the runbook/logs or a known-good system, and involve the owning team rather than guess.”

**Q10. What is your biggest development area?**  
“My direct experience with Puppet and some automated bare-metal imaging workflows is more limited than my Linux and Ansible experience. I’m actively studying the lifecycle and would follow the established runbooks while learning the team's tooling.”

## 20. Technical interview questions and model answers

**1. What is server onboarding?** Moving newly installed hardware through inventory, management access, firmware/storage/network configuration, OS imaging, configuration, validation, and authorized handoff.

**2. Bare metal vs VM?** Bare metal is a physical server; a VM is a software-defined machine running on a hypervisor. This role emphasizes physical host onboarding.

**3. What is a BMC?** A dedicated controller for hardware management independent of the OS.

**4. IPMI vs iDRAC vs iLO?** IPMI is a management interface standard; iDRAC is Dell's implementation; iLO is HPE's.

**5. Which IP is used for OOB?** The BMC/iDRAC/iLO management IP, normally separate from the OS IP and on a restricted management network; dedicated/shared NIC designs vary.

**6. Can iDRAC work when Linux is down?** Yes, if the BMC still has power and network access.

**7. What is UEFI?** Firmware interface controlling boot and hardware initialization; its mode affects PXE and installer compatibility.

**8. What is firmware?** Software embedded in platform devices/controllers; use approved version baselines and update procedures.

**9. RAID vs JBOD?** RAID groups disks for defined performance/redundancy behavior; JBOD exposes individual disks, depending on controller design.

**10. RAID 1 vs RAID 0?** Mirroring with redundancy versus striping without redundancy.

**11. What is PXE?** Network-based preboot mechanism to fetch a boot program and start installation or diagnostics.

**12. What does DHCP do in PXE?** Supplies network configuration and, in many architectures, boot discovery information; details vary.

**13. What is a VLAN?** Logical Layer 2 network segmentation.

**14. BMC reachable but PXE has no IP—why?** Different network paths: management VLAN works, provisioning NIC/VLAN/DHCP may not.

**15. What if DHCP succeeds but PXE fails?** Check boot filename/architecture, boot service (TFTP/HTTP), paths, firewall, and logs.

**16. What is automated imaging?** Deploying an approved OS image/installer configuration consistently with minimal manual steps.

**17. How do you verify Linux install?** Check OS/kernel, boot, NIC/IP/route, storage, CPU/RAM/GPU inventory, services, logs, and automation results against specification.

**18. What is Puppet?** Configuration management software for applying and verifying declared system configuration.

**19. Puppet vs Ansible?** Both manage configuration; Ansible commonly uses SSH/YAML playbooks, while Puppet commonly applies manifests/catalogs via agent/server workflows.

**20. Puppet run failed—what next?** Capture error, identify dependency layer, check logs/network/DNS/time/certs, coordinate with owner, authorized rerun, verify state.

**21. What is DOA?** Hardware found nonfunctional at initial deployment; prove the hardware issue before classifying.

**22. What is RMA?** Vendor return/replacement process for eligible failed equipment.

**23. What is remote hands?** Onsite data center technicians executing precise physical requests for remote teams.

**24. What must inventory include?** Serial, rack, switch port, BMC, storage, GPU, OS/image, firmware, status, ticket and owner, with secure handling of secrets.

**25. When is a server production-ready?** Only after approved technical checks, documentation, and validation/handoff gates; not merely after OS install.

**26. Is VMware required?** Not explicitly in Dave's supplied job description; virtualization understanding helps, but do not claim ESXi/vCenter is a listed duty.

**27. What if a GPU is missing?** Compare expected inventory, BMC health, PCIe detection, drivers, power, and escalate with evidence.

**28. What if a host doesn't power on?** Verify rack/PDU/PSU and BMC status; request remote hands and review hardware alerts, following safety procedures.

**29. How would you handle 50 hosts at once?** Use inventory-driven tracking, parallelize independent approved steps, flag exceptions, record per-host validation, and maintain accurate handoffs.

**30. What would you ask before changing RAID?** Approved disk layout, data-loss impact, host identity, controller support, change authorization, and rollback/recovery path.

## 21. Honest ways to discuss skill gaps

**Puppet:** “I have strong Ansible experience but limited direct Puppet production work. I understand desired state and would follow the approved Puppet workflow and troubleshooting process.”

**PXE:** “I understand the PXE chain from NIC/DHCP to bootloader, installer and OS validation. I would follow the team's approved imaging runbook and troubleshoot the specific failing stage.”

**GPU servers:** “My background is stronger in Linux and conventional server hardware. I understand that GPU onboarding adds PCIe, inventory, driver, and hardware-health checks, and I would use the platform-specific runbook.”

**VMware:** “I have virtualization exposure and a VMware learning lab. I understand hypervisors, but I wouldn't assume ESXi is part of this role without confirming the team's environment.”

**Never say:** “I managed production Puppet clusters” or “I built Vultr PXE” unless true. A credible bounded answer is stronger than a fabricated expert claim.

## 22. Questions to ask Scott

Prepare 5–7; ask 2–3 based on time and what was already discussed.

1. “What are the biggest technical blockers slowing onboarding today?”
2. “What would you want a new specialist to accomplish in the first 90 days?”
3. “What does strong performance look like—deployment throughput, first-pass validation, quality of records, or other metrics?”
4. “How do onboarding, networking, storage, remote-hands, and validation teams coordinate?”
5. “What differences matter most when onboarding GPU hosts versus CPU-only hosts?”
6. “Which parts of PXE imaging and Puppet are handled by existing automation, and which parts require manual investigation?”
7. “Does the team onboard primarily Linux bare-metal hosts, or does it also configure hypervisors such as KVM or VMware?”

Dave specifically encourages questions about team challenges, 90 days/six months, success metrics, and team composition. Avoid initiating compensation/benefits discussion in this interview, per recruiter guidance.

## 23. Three-day revision plan and interview checklist

**Monday, Oct 12:** company/role overview; OOB management, firmware, storage, networking, PXE; practice intro and Why Vultr.

**Tuesday, Oct 13:** troubleshooting scenarios (no PXE IP, BMC down, installer missing disks, Puppet failure, GPU absent); answer out loud; revisit weak spots.

**Wednesday, Oct 14:** 30-minute mock interview, resume cross-check, STAR incident story, interviewer questions, Meet test, clothes and quiet room.

**Thursday, Oct 15:** join by **9:50 AM Central**. Have water, resume, short note card, and questions. Be concise and ask for clarification if needed.

**Final checklist:**
- [ ] Calendar invite and interviewer spelling checked
- [ ] Google Meet link opens; camera/mic/speakers tested
- [ ] Collared shirt and distraction-free setting
- [ ] Can explain complete onboarding flow in 90 seconds
- [ ] Can explain OOB IP vs Linux OS IP
- [ ] Can troubleshoot PXE no-IP and boot-file failures
- [ ] Can explain RAID/JBOD and destructive-change caution
- [ ] Can discuss Puppet honestly using Ansible analogy
- [ ] Can describe a genuine troubleshooting incident using STAR
- [ ] Five to seven questions ready
- [ ] No unsupported claims about Vultr internals or past work

## 24. Quick-reference cheat sheet

```text
SERVER LIFECYCLE:
Rack/handoff → Asset verify → BMC → BIOS/firmware → RAID/JBOD
→ NIC/VLAN/DHCP → PXE/image → Linux checks → Puppet → Inventory → Validation/handoff

OOB IP = iDRAC/iLO/BMC management; independent of OS IP.
IN-BAND IP = Linux network interface, SSH/services.
BMC reachable ≠ provisioning NIC/VLAN/DHCP working.
DHCP success ≠ PXE boot-file download success.
OS installed ≠ production approved.
RAID change = potentially destructive; verify authorization.
Puppet job failure = collect logs, isolate layer, approved fix/rerun, validate.
Remote work + rack-and-stack = coordinate with authorized onsite remote hands.

KEY LINUX COMMANDS:
ip -br addr | ip route | lsblk | lscpu | free -h | lspci
cat /etc/os-release | systemctl --failed | journalctl -b

INTERVIEW ANSWER PATTERN:
Scope/safety → Verify → Isolate → Act within runbook → Revalidate → Document/handoff
```

## 25. Source mapping and important caveats

**From Dave's October 12 email:** Oct 15, 10 AM Central, Google Meet, early login, dress guidance, company research, HR questions, resume review, interviewer questions, compensation guidance, interviewer background, and full job description. The job description explicitly lists BMC/IPMI, BIOS, RAID/JBOD, firmware, PXE, automated imaging, Puppet, DOA/RMA, CPU/GPU fleet inventory, cross-team coordination, and rotating 4×10 shifts.

**Supplemental educational material (not guaranteed Vultr practice):** example IPs, Linux commands, PXE protocol variants, RAID explanations, troubleshooting trees, GPU diagnostics, model answers, and sample workflows. Confirm actual commands, network topology, access methods, and runbooks after joining.

**Potential ambiguities:** Scott's surname appears two ways; physical rack tasks may be performed by remote hands for this remote role; Puppet and PXE are requirements but level of candidate's personal prior experience must be represented honestly; the source does not identify VMware as a required technology.

---
**Practice principle:** Explain what you personally did, what you know conceptually, and what you would do using approved procedures as three distinct categories.
