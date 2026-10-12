# Vultr Server Onboarding Specialist — Complete Job Workflow & Interview Study Notes

**Language:** English  
**Purpose:** Understand, step by step, what you would do to prepare a newly racked physical server for production and how to explain the process in your interview.  
**Scenario:** Ten new Dell PowerEdge servers have been installed and cabled in a data center. You are a member of the Server Onboarding team.

> **Important:** This is a realistic educational workflow based on the responsibilities discussed for the role, **not a verified internal Vultr runbook**. Actual tools, commands, permissions, configuration values, and production approvals depend on Vultr policies and the team's procedures. Do not claim previous production experience with Puppet, PXE, or GPU provisioning unless you genuinely have it.

---

## Table of Contents

1. Production, production readiness, and server onboarding
2. Your responsibilities at a glance
3. End-to-end workflow diagram
4. Step 0 — Receive the ticket, scope, and handoff
5. Step 1 — Verify physical hardware
6. Step 2 — Out-of-band management: BMC, iDRAC, iLO, IPMI
7. Step 3 — BIOS/UEFI and firmware baseline
8. Step 4 — Storage, RAID/JBOD, and GPU verification
9. Step 5 — Network: NIC, switch, VLAN, DHCP, IP
10. Step 6 — PXE boot and automated Linux imaging
11. Step 7 — Linux verification and Puppet configuration
12. Step 8 — Validation, inventory, tickets, and production handoff
13. A realistic workday with ten servers
14. Troubleshooting scenarios, step by step
15. Useful Linux commands and their purpose
16. Safe operations, security, and escalation
17. Interview questions and model answers
18. Mock interview self-test
19. Final revision checklist

---

## 1. What are production and server onboarding?

**Production** is the live infrastructure or environment supporting real customer services, or providing approved capacity for those services. At a cloud provider, a server may belong to the production fleet even before a specific customer workload has been assigned to it.

**Server onboarding** is the process of inspecting, configuring, imaging, validating, and documenting a newly installed physical server before handing it over to the production fleet through an approved process.

**Power ON does not mean production-ready.** You must verify hardware, networking, operating system, configuration/security requirements, validation results, and inventory.

| Environment | Purpose |
|---|---|
| Development | Build software and experiment |
| Testing / QA | Test functionality and detect failures |
| Staging | Perform final tests in a production-like environment |
| Production | Support live customer services or approved live capacity |

## 2. Your responsibilities at a glance

- Receive and verify the **hardware handoff** for new servers.
- Use **BMC/iDRAC** for remote hardware management.
- Compare **BIOS/UEFI, firmware, boot mode, and boot order** with the approved baseline.
- Validate **storage** (RAID/JBOD) and, when applicable, **GPU** inventory and health.
- Verify the **provisioning network**: NIC, switch port, VLAN, DHCP, IPv4/IPv6.
- Initiate, monitor, or troubleshoot **PXE-based automated OS imaging** as authorized.
- Verify Linux boot, disks, network, services, and the results of the **existing Puppet workflow**.
- Identify defective hardware and coordinate the **DOA/RMA** process.
- Update **asset inventory, evidence, tickets, and runbook notes**.
- Complete **production handoff** only after required checks and authorization.

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

## 4. Step 0 — Receive the ticket, change scope, and handoff

### What you would do

1. Receive the **server list and handoff ticket** from the data center or remote-hands team.
2. Review each server's **asset/serial number, rack position, model, expected CPU/RAM, NIC, disk, and GPU specifications**.
3. Identify the **approved runbook, change/maintenance authorization, OS image, and firmware baseline**.
4. Confirm that you have authorized access to the BMC management network and provisioning environment.
5. Track each machine using states such as `Pending`, `In Progress`, `Blocked`, `Validated`, and `Handed Off`.

**Why this matters:** It prevents you from configuring or imaging the wrong server or making an unauthorized destructive storage change.

**Interview line:** “I would start by reviewing the handoff ticket, confirming the asset identifiers, and following the approved onboarding runbook.”

## 5. Step 1 — Physical hardware verification

### What to check

- Does the **serial number / asset tag** match the inventory record?
- Is the **rack / rack-unit (RU) location** correct, and are power feeds connected?
- Are the **PSUs, fans, RAM, CPU, disks, and network adapters** healthy?
- Are required **network cables** connected to the intended switch ports?
- Has the on-site technician confirmed installation and cabling?

### Practical example

Of ten servers, `server-07` reports a PSU health alert. Do **not** mark it ready. Capture BMC hardware logs, update the ticket, and coordinate with remote hands or the hardware team.

**Interview line:** “I verify rack and asset information, power and cabling, and hardware health before moving to provisioning.”

## 6. Step 2 — Out-of-band management (BMC/iDRAC)

**Out-of-band (OOB)** management provides a path to manage the hardware independently of the operating system. Even when Linux is not installed or SSH is unavailable, a powered BMC may remain accessible if standby power and management network connectivity are available.

| Term | Meaning |
|---|---|
| BMC | Baseboard Management Controller; a hardware management controller |
| iDRAC | Dell's remote server management implementation |
| iLO | HPE's remote server management implementation |
| IPMI | A hardware management interface standard; not another name for the BMC |
| Redfish | A modern REST-based hardware management interface |

### What you would do

1. Connect to iDRAC/BMC through the **approved management network**.
2. Check **power status, sensors, temperature, fans, PSUs, memory, and storage alerts**.
3. Review **System Event Logs / Lifecycle Logs** for hardware faults.
4. Use the **remote console** to observe startup and access BIOS settings.
5. Compare BMC firmware and configuration with the approved baseline.

**Security:** Never put BMC credentials in plain-text tickets, screenshots, or GitHub. Access to the management network should be restricted.

**If iDRAC is unreachable:** Check the management IP/address, route/VLAN, switch link, ACL/firewall, BMC power, and physical cabling in a structured order. Avoid unauthorized scanning of production management networks.

## 7. Step 3 — BIOS/UEFI and firmware

**BIOS/UEFI** initializes hardware and controls settings before the operating system starts. **Firmware** is low-level software running on hardware components.

### What to verify

- Is **UEFI or Legacy boot mode** consistent with the approved standard?
- Does the **boot order** support the required PXE/provisioning process?
- Are the CPU, RAM, NICs, disks, and any GPUs detected correctly?
- Do virtualization or platform-specific settings match the approved profile?
- Are BIOS, BMC, NIC, and RAID-controller firmware on approved versions?

**Important:** Do not randomly upgrade firmware, reset BIOS, change Secure Boot, or switch storage modes. Confirm the runbook and change scope first.

**If PXE fails:** Confirm network boot is enabled on the intended NIC and that UEFI/Legacy mode is compatible with the boot image.

## 8. Step 4 — Storage, RAID/JBOD, and GPU

### Storage terms

- **RAID 0:** Striping without redundancy.
- **RAID 1:** Mirroring for disk redundancy.
- **RAID 5:** Parity-based redundancy; behavior depends on configuration.
- **RAID 10:** Mirroring plus striping; requires multiple disks.
- **JBOD:** Disks may be presented individually; exact behavior varies by controller and vendor.

### What you would do

1. Verify the expected **number, capacity, and type** of disks.
2. Check for missing or failed disks and storage-controller alerts.
3. Compare the configured **RAID/JBOD layout** with the approved specification.
4. For GPU servers, verify GPU count/model, PCIe detection, hardware alerts, and the required baseline.
5. If there is a mismatch, pause imaging and document/escalate the issue.

**Critical warning:** Creating/deleting RAID virtual disks, initializing drives, formatting, flashing firmware, or wiping disks may **destroy data**. Perform such actions only within an explicitly authorized new-server workflow.

## 9. Step 5 — Network verification

### Network path

```text
Server NIC -> Ethernet Cable -> Physical Switch Port
           -> Provisioning VLAN -> DHCP Service
           -> PXE Boot Service / Imaging Infrastructure
```

### Your checklist

1. Is the **NIC link up**? Is the link speed/port health as expected?
2. Is the physical switch port mapped to the correct server?
3. Is the **correct provisioning VLAN** configured on that port?
4. Does the machine receive the expected **DHCP IP lease**?
5. Can it reach the necessary PXE boot services?
6. Do IPv4/IPv6, routing, and DNS settings match requirements, where relevant?

**DHCP's role:** It supplies network parameters to the booting machine. Depending on the design, PXE discovery/boot information can be provided through DHCP or related services.

**If there is no IP address:** Start with physical/NIC link, then switch port and VLAN, followed by DHCP scope, relay, logs, and network policies. Do not immediately assume DHCP itself is broken.

## 10. Step 6 — PXE boot and automated Linux installation

**PXE (Preboot Execution Environment)** enables a server to start booting over the network without an operating system already installed on its local disk.

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

**Note:** Implementations vary. They may use DHCP, TFTP, HTTP, iPXE, UEFI HTTP Boot, or other mechanisms. Not every environment has the same boot chain.

### What you would do

1. Confirm the **correct server and approved OS image** are selected.
2. Initiate or observe network boot as authorized.
3. Verify that DHCP and the network boot program are working.
4. Check that the installer starts and targets the intended disk.
5. Review imaging logs and completion status.
6. Confirm that the first reboot successfully boots from the local disk.

### Map the symptom to the failing stage

| Symptom | First place to investigate |
|---|---|
| No network link | NIC, cable, switch port |
| No DHCP IP | VLAN, DHCP, relay, port configuration |
| IP received but no boot file | PXE boot options, boot server, file path/service |
| Kernel or installer fails to load | Boot image, HTTP/TFTP, compatibility |
| Disk not found | Storage controller, RAID/JBOD, installer drivers |
| OS installed but won't boot | UEFI mode, boot order, disk layout, bootloader |

## 11. Step 7 — Linux verification and Puppet

**Puppet** manages desired system configuration through declarative policies. Your Ansible experience helps with the underlying concepts, but **Puppet and Ansible are not identical**.

### What to verify

- Did Linux **boot successfully**?
- Are the OS version, hostname, and kernel correct?
- Are NICs, IP addresses, routes, and DNS correct where relevant?
- Are expected disks, filesystems, and mounts present?
- Are required services running?
- Did the **Puppet agent/report** or the team's configuration pipeline complete successfully?
- Did required security/configuration baselines and validation tests pass?

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

Puppet tooling depends on deployment. These commands are examples **if the Puppet agent is installed and you are authorized to inspect it**:

```bash
puppet --version
systemctl status puppet --no-pager
journalctl -u puppet --no-pager -n 50
```

**Do not assume** every Vultr server uses these exact Puppet commands or service names. Follow the team's specific workflow. Running `puppet agent -t` without authorization can apply configuration changes.

### Troubleshooting framework

**Observe -> Logs -> Hypothesis -> Evidence -> Approved fix/escalation -> Retest -> Document.**

First identify the symptom, inspect logs, form a reasonable hypothesis, gather supporting evidence, take only approved action, retest, and update the ticket.

## 12. Step 8 — Production readiness, inventory, and handoff

### What should be recorded?

- Server **serial/asset tag**, hostname/ID, rack/RU location.
- Hardware model, CPU/RAM, disks, RAID/JBOD, and GPU details.
- Network port, approved IP/VLAN references, and BMC management record.
- BIOS/firmware baseline and OS image/version.
- Hardware health, PXE imaging, OS/Puppet, and validation results.
- Exceptions, ticket links, incident/RMA numbers, and timestamps.
- Handoff recipient/team and required approval.

**Never store passwords or secrets in plain-text inventory notes.** Use references to the approved secure credential system.

### Production readiness decision

- **PASS:** Required checks completed, documented, and approved → handoff.
- **BLOCKED:** A critical check fails → keep out of production; ticket and escalate.
- **PENDING:** Technical checks passed but authorization is still outstanding → wait for approval.

**Interview line:** “I would not mark a server production-ready simply because it powers on. I would confirm the full validation checklist and document the approved handoff.”

## 13. A realistic day: ten Dell PowerEdge servers

| Server | Example status | Your next action |
|---|---|---|
| 01 | Hardware/network/OS passed | Record evidence; approval/handoff |
| 02 | Hardware/network/OS passed | Record evidence; approval/handoff |
| 03 | iDRAC unreachable | Investigate management VLAN/cabling/ACL |
| 04 | Disk alert | Review BMC/storage logs; hardware escalation |
| 05 | No DHCP IP | Troubleshoot NIC → switch → VLAN → DHCP |
| 06 | PXE boot file missing | Investigate boot service/configuration |
| 07 | OS imaging failed | Review installer logs, disks, and image |
| 08 | Puppet report failed | Review logs; team-approved remediation |
| 09 | GPU inventory mismatch | Hardware/PCIe verification and escalation |
| 10 | Validation complete | Inventory, evidence, approval/handoff |

**This is an illustrative example, not actual Vultr incident data.** Prioritize safety, accurate status, and clear handoff rather than merely maximizing the number of servers marked complete.

## 14. Common troubleshooting scenarios

### Scenario A — Server powers on but gets no DHCP IP

1. Note the exact error or symptom on the BMC console.
2. Check NIC link status and the intended PXE interface.
3. Confirm rack cable and switch-port mapping.
4. Verify provisioning VLAN and switch-port configuration with the network team.
5. Investigate DHCP scope, relay, logs, and relevant policies.
6. Apply only an authorized fix; retry PXE and document evidence.

**Interview answer:** “I would troubleshoot layer by layer: NIC link, switch port, VLAN, DHCP scope or relay, and finally PXE services, using logs and the approved runbook.”

### Scenario B — iDRAC unreachable

1. Verify the correct management address/DNS and access path.
2. Check management VLAN, route, firewall, and ACL.
3. Confirm server standby power and BMC status.
4. Ask remote hands to verify physical cabling and switch port.
5. Escalate if a controller or hardware failure is suspected.

### Scenario C — PXE receives an IP but cannot download the boot file

1. Confirm the IP lease and expected boot program/path.
2. Check boot-service availability and access controls.
3. Verify UEFI/Legacy bootloader compatibility.
4. Examine boot-server logs, follow approved remediation, and retest.

### Scenario D — Linux is installed, but Puppet fails

1. Confirm OS boot and network reachability.
2. Read Puppet reports, agent logs, or pipeline logs.
3. Check certificates/authentication, environment, dependencies, or connectivity according to the evidence.
4. Do not bypass security checks or blindly rerun the process.
5. Apply approved remediation, validate, and document.

### Scenario E — A disk or GPU is missing

1. Compare detected hardware with the purchase/asset specification.
2. Check BMC and storage/PCIe event logs.
3. Confirm firmware/BIOS configuration and physical detection.
4. Coordinate with remote hands or the vendor; use DOA/RMA if approved.
5. Keep the server blocked until it passes validation.

### Scenario F — Server boots, but a health check fails

1. Identify the failing check: network, service, disk, firmware, GPU, or Puppet.
2. Gather logs and compare results with the approved baseline.
3. Follow approved remediation or escalation.
4. Repeat the **entire required validation suite**, not just the failed test.

## 15. Useful commands — what they tell you

| Command | Purpose |
|---|---|
| `hostnamectl` | Hostname and OS details |
| `cat /etc/os-release` | Linux distribution/version |
| `uname -r` | Kernel version |
| `ip -br addr` | Network interfaces and IP addresses |
| `ip link` | Interface administrative/link state |
| `ip route` | Routing table |
| `ss -tulpen` | Listening sockets (visibility depends on permissions) |
| `lsblk` | Block devices and disks |
| `findmnt` | Mounted filesystems |
| `df -h` | Filesystem capacity |
| `free -h` | RAM/swap summary |
| `systemctl --failed` | Failed systemd units |
| `journalctl -b` | Current boot logs |
| `dmesg` | Kernel messages (access may be restricted) |
| `lspci` | PCI devices, including NIC/GPU detection |
| `ethtool <interface>` | NIC link and driver information, if installed |

**Note:** These commands are useful after the OS boots. Before Linux installation, BMC, switch, and provisioning-system logs may be more important.

## 16. Safety, security, and escalation

- **Do not assume access:** Use only authorized networks, consoles, tickets, and tools.
- **Do not wipe unknown disks:** Verify the asset and authorized imaging scope.
- **Do not randomly change BIOS/RAID/firmware:** Check the baseline and approvals first.
- **Do not expose secrets:** Never paste BMC passwords/tokens into tickets, chat screenshots, or GitHub.
- **Do not force production handoff:** Keep servers with failed critical checks blocked until resolved/approved.
- **Escalate early:** DOA hardware, inventory mismatch, network-team dependencies, or unexplained security failures.
- **Document clearly:** Symptom, timestamp, asset, logs/evidence, tests, owner, and next action.

## 17. Interview questions and model answers

### Q1. What is production?

**Answer:** “Production is the live environment where infrastructure supports real customer services or provides approved capacity for those services.”

**Key point:** A server can be approved production capacity without already hosting a particular customer workload.

### Q2. What is server onboarding?

**Answer:** “Server onboarding is the process of taking a newly racked physical server, validating its hardware and management access, configuring the approved firmware and storage settings, provisioning the operating system, verifying its health and configuration, and documenting it before production handoff.”

**Key point:** Think beyond installing Linux: hardware → network → imaging → validation → documentation.

### Q3. What would your responsibilities be?

**Answer:** “I would start by confirming the handoff and hardware inventory. Then I would check iDRAC/BMC access, BIOS and firmware, disk and network readiness, and PXE-based imaging. After installation, I would verify Linux and the results of approved configuration management workflows, troubleshoot failures, update records, and follow the team's production handoff process.”

**Memory sequence:** Handoff → hardware → BMC → BIOS → storage → network → PXE → Linux/Puppet → validation → inventory → approval.

### Q4. If the server powers on, is it production-ready?

**Answer:** “Not necessarily. Powering on only confirms part of the hardware is functioning. I would still need to verify hardware health, firmware and storage configuration, networking, OS imaging, required configuration and validation checks, and approved handoff.”

**Key point:** A successful power-on is just the beginning.

### Q5. What if a PXE server doesn't receive an IP?

**Answer:** “I would start with the NIC link and switch port, confirm the correct provisioning VLAN, then check DHCP scope, relay and logs. I would isolate the failing stage, follow the approved fix procedure, and retest.”

**Memory sequence:** NIC → switch → VLAN → DHCP, using evidence at every stage.

### Q6. Have you used Puppet or production PXE before?

**Truthful example:** “My hands-on background is primarily Linux administration, troubleshooting and Ansible. I understand the purpose of Puppet and PXE in automated provisioning, and I would be comfortable learning and following your existing runbooks and workflows. I would not claim production Puppet or PXE experience I haven't had.”

**Key point:** Explain your real Linux/Ansible background confidently without inventing past Puppet/PXE experience.

### Q7. What if you discover a faulty disk?

**Answer:** “I would capture the hardware alert and relevant logs, verify the asset and disk details, mark the server blocked for production, and follow the approved hardware replacement or RMA escalation process.”

**Key point:** Evidence → block → ticket → escalation → retest.

### Q8. What does a good handoff look like?

**Answer:** “A good handoff includes a validated server, accurate inventory and network details, documented test results, resolved or clearly owned exceptions, and the required approval for production.”

**Key point:** Saying “done” is not enough; provide verifiable results and accurate records.

## 18. Mock interview self-test

Practice answering these questions **in your own words**, out loud:

1. What is the difference between production and production-ready?
2. What is your very first step after receiving ten new servers?
3. What is the difference between BMC and SSH?
4. How would you troubleshoot an unreachable iDRAC?
5. Why is BIOS/UEFI boot order important for PXE?
6. What is the conceptual difference between RAID and JBOD?
7. In what order would you troubleshoot a server receiving no DHCP IP?
8. Explain PXE boot step by step.
9. What would you validate after Linux is installed?
10. What is a safe troubleshooting approach when Puppet fails?
11. What would you do if a disk or GPU is missing?
12. What evidence is needed before production handoff?
13. How does your Linux/Ansible experience relate to this role?
14. Is a powered-on server automatically production-ready?
15. When would you escalate to remote hands, networking, or the hardware team?

## 19. Final quick revision checklist

```text
[ ] Correct ticket, server identity, and approved runbook
[ ] Rack, power, cables, and hardware health checked
[ ] BMC/iDRAC access and event logs verified
[ ] BIOS/UEFI and firmware baseline confirmed
[ ] Disk/RAID/JBOD/GPU requirements verified
[ ] NIC, switch, VLAN, DHCP, and PXE path validated
[ ] Correct OS image installed and first boot successful
[ ] Linux, services, network, storage, and Puppet checks passed
[ ] Hardware/security/configuration issues resolved or blocked
[ ] Inventory, logs, tickets, and validation evidence recorded
[ ] Required production approval and handoff completed
```

### One-line memory trick

**Handoff → Hardware → BMC → BIOS → Storage → Network → PXE → Linux/Puppet → Validate → Document → Approve.**

**Interview tip:** Speak naturally rather than memorizing a script. Start with the overall workflow, explain your checks in order, and emphasize approved runbooks, careful troubleshooting, and timely escalation.

---

*Prepared for interview study. Always confirm vendor-specific internal procedures and production access with the hiring team.*
