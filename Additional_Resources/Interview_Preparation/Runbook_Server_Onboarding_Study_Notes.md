# Runbook — Server Onboarding Study Notes

**English and Roman Urdu | Vultr interview preparation**

> This is an educational example, not Vultr's internal runbook. Follow approved company procedures.

## Table of Contents
1. Definition
2. Why runbooks matter
3. Server onboarding runbook example
4. What a runbook contains
5. PXE troubleshooting scenario
6. Runbook vs SOP vs playbook
7. Interview questions and answers
8. Quick revision

## 1. What Is a Runbook?

**English:** A runbook is a documented, step-by-step set of instructions that engineers follow to complete an operational task safely, consistently, and correctly.

**Roman Urdu:** Runbook ek likhi hui guide hoti hai jismein bataya jata hai ke technical kaam **kis tarah, kis order mein, aur kin checks ke saath** karna hai.

**Example:** If 100 Dell PowerEdge servers arrive, engineers follow a standard onboarding runbook instead of configuring each server differently.

**Roman Urdu:** Agar 100 naye servers aayein, to har engineer apni marzi se settings nahi karega. Approved runbook follow karega.

## 2. Why Do Teams Use Runbooks?

- **Consistency:** Same approved standards across servers.
- **Safety:** Verify prerequisites and permissions before changes.
- **Speed:** Reduce guesswork for repeated tasks.
- **Troubleshooting:** Identify the step that failed.
- **Handoff:** Record what is complete and what remains.
- **Evidence:** Preserve ticket updates, logs, and validation results.

**Roman Urdu:** Runbook mistakes kam karta hai, troubleshooting asaan banata hai, aur team ko pata rehta hai ke server kis stage par hai.

## 3. Example: Server Onboarding Runbook

```text
New physical server / hardware handoff
                 |
                 v
1. Verify rack, cables, power, serial/asset ID
                 |
                 v
2. Access iDRAC/BMC; check hardware health
                 |
                 v
3. Verify BIOS/UEFI and approved firmware
                 |
                 v
4. Verify disks and approved RAID/JBOD mode
                 |
                 v
5. Verify NIC link, switch port, VLAN, DHCP
                 |
                 v
6. PXE boot and automated Linux OS imaging
                 |
                 v
7. Run existing Puppet configuration workflow
                 |
                 v
8. Validate OS, services, disks, network, hardware
                 |
                 v
9. Update inventory, evidence, tickets; handoff
                 |
                 v
Production-ready after validation and approval
```

### Step 1 — Hardware
**English:** Confirm the server's identity, rack position, cables, power, and serial number against the work order.

**Roman Urdu:** Rack location, power, cables aur serial number verify karein. Wrong server par kaam na karein.

### Step 2 — Out-of-band management
**English:** Access the BMC/iDRAC through its authorized management IP. Review power state, hardware health, and logs. The OOB IP is usually different from the Linux OS IP.

**Roman Urdu:** iDRAC/BMC ke management IP se server ki hardware health aur power check karein. OOB IP aam tor par OS IP se alag hota hai.

### Step 3 — BIOS and firmware
**English:** Compare boot mode, boot order, and firmware versions with the approved profile. Update only with authorization.

**Roman Urdu:** BIOS aur firmware company ke approved standard se compare karein. Random changes na karein.

### Step 4 — Storage
**English:** Check disk presence and health, then configure the approved RAID or JBOD mode. Storage changes can be destructive.

**Roman Urdu:** Disks check karein aur approved RAID/JBOD settings use karein. Baghair approval storage changes na karein.

### Step 5 — Networking
**English:** Verify NIC link, switch port, provisioning VLAN, DHCP service, and PXE boot configuration.

**Roman Urdu:** NIC, switch port, VLAN aur DHCP ko step-by-step verify karein.

### Step 6 — Linux imaging
**English:** PXE starts network boot; DHCP provides addressing and boot discovery; configured services provide bootloader/installer components; the installer deploys Linux. Exact protocols vary.

**Roman Urdu:** Server network se boot karta hai, DHCP se IP/boot information leta hai, phir installer Linux install karta hai.

### Step 7 — Configuration management
**English:** Run and monitor existing approved Puppet workflows. Check results and logs before retrying failed tasks. Ansible experience is relevant, but do not claim Puppet production experience you do not have.

**Roman Urdu:** Existing Puppet workflow run karein aur result check karein. Ansible ka experience related hai, lekin dono tools same nahi.

### Step 8 — Validation
**English:** Verify Linux boots, networking works, disks are healthy, required services run, and hardware/GPU checks pass where applicable.

**Roman Urdu:** Sirf Linux boot hona enough nahi. Network, disks, services aur hardware checks pass hone chahiye.

### Step 9 — Documentation and handoff
**English:** Update rack, serial number, BMC reference, network assignments, disk/GPU inventory, validation evidence, issues, and handoff status. Never put passwords in plaintext tickets.

**Roman Urdu:** Inventory aur tickets update karein. Password plain text mein na likhein. Approval ke baad handoff karein.

## 4. What Information Does a Runbook Contain?

| Section | Purpose |
|---|---|
| Title and scope | Identify the task |
| Prerequisites | What must be ready first |
| Required access | Systems and permissions |
| Ordered steps | Exact approved sequence |
| Expected results | How to confirm success |
| Troubleshooting | What to check when a step fails |
| Safety and rollback | Risks, stop conditions, recovery |
| Escalation | Who owns unresolved issues |
| Documentation | Tickets, logs, inventory, evidence |

**Roman Urdu:** Runbook sirf commands ki list nahi; ismein verification, troubleshooting aur escalation bhi hoti hai.

## 5. Real-World Scenario: PXE Boot Does Not Receive an IP

**Problem:** A newly racked server powers on, but no DHCP address appears during PXE boot.

1. Record the exact error, server asset ID, NIC MAC address, and timestamp.
2. Check the network cable, NIC link, and switch port.
3. Confirm the correct NIC is selected for PXE boot and the approved BIOS/UEFI settings are present.
4. Check the documented provisioning VLAN assignment.
5. Investigate DHCP reachability, service availability, and relay configuration where relevant.
6. Capture BMC console evidence and update the ticket.
7. Escalate network/provisioning issues to the responsible team rather than making unauthorized changes.
8. After the approved fix, retry PXE, validate the result, and document the resolution.

**Roman Urdu:** PXE mein IP nahi mil raha to pehle error aur MAC address note karein. Phir cable, NIC, VLAN aur DHCP check karein. Agar issue networking team ka hai, to evidence ke saath escalate karein.

### Example ticket

```text
Asset: EXAMPLE-SRV-104
Stage: PXE provisioning
Issue: No DHCP offer received
Evidence: Console error, MAC address, timestamp
Checks: NIC link verified; VLAN pending review
Owner: Networking team
Status: Blocked
Next step: Retest after approved correction
```

## 6. Runbook vs SOP vs Ansible Playbook

| Term | Meaning | Example |
|---|---|---|
| **Runbook** | Detailed operational instructions | Onboard and validate a Dell server |
| **SOP** | Standard rules/procedures | Every server must pass validation before handoff |
| **Ansible playbook** | Executable YAML automation | Install packages and configure Linux services |

**Roman Urdu:** SOP standard batata hai, runbook detailed steps batata hai, aur Ansible playbook automation chalata hai. Companies terms ko kabhi slightly differently use karti hain.

## 7. Interview Questions and Answers

### Q1. What is a runbook?
**English:** "A runbook is a documented step-by-step operational procedure. It includes prerequisites, instructions, validation, and guidance for troubleshooting or escalation."

**Roman Urdu:** Runbook documented procedure hai jo batata hai kya karna hai, kaise verify karna hai, aur fail ho to kya karna hai.

### Q2. How would you use a runbook during server onboarding?
**English:** "I would verify the hardware and ticket, then follow approved steps for BMC access, BIOS, storage, networking, imaging, and configuration. I would validate each stage, document results, and hand off the server after required approval."

**Roman Urdu:** Main hardware verify karunga, approved steps follow karunga, har stage check karunga, aur documentation ke baad handoff karunga.

### Q3. What if a runbook step fails and no fix is documented?
**English:** "I would stop, capture the error and logs, perform safe authorized checks, and escalate to the responsible team if the next action is unclear. After resolution, I would retest and document the outcome."

**Roman Urdu:** Random changes nahi karunga. Evidence collect karunga, safe checks karunga aur Demand par escalate karunga.

### Q4. How do you know a server is production-ready?
**English:** "I would verify hardware, firmware, network, OS, configuration, and required services, confirm inventory accuracy, and ensure validation and approval steps are complete."

**Roman Urdu:** Linux boot hona enough nahi; tamam checks aur approvals complete hone chahiye.

### Q5. What is the difference between a runbook and an Ansible playbook?
**English:** "A runbook is a human-readable operational guide. An Ansible playbook is executable YAML automation. A runbook may tell an engineer when to run a playbook and how to verify its results."

**Roman Urdu:** Runbook engineer ko guide karta hai; Ansible playbook automated tasks execute karta hai.

### Q6. Why are runbooks important for high-volume deployments?
**English:** "They reduce variation and mistakes, speed up troubleshooting, and make work easier to track and hand off."

**Roman Urdu:** Zyada servers deploy karte waqt runbook consistency aur tracking improve karta hai.

### Q7. What if the runbook seems to target the wrong server?
**English:** "I would stop, verify the asset ID and ticket, and report the mismatch before making any change, especially destructive storage changes."

**Roman Urdu:** Wrong server par change nahi karna; pehle identity aur approval confirm karna hai.

## 8. Quick Revision Checklist

- [ ] I can define a runbook in one sentence.
- [ ] I understand why consistent onboarding matters.
- [ ] I can describe the nine onboarding steps.
- [ ] I know the difference between OS IP and OOB/BMC IP.
- [ ] I can explain basic DHCP/PXE troubleshooting.
- [ ] I can distinguish a runbook, SOP, and Ansible playbook.
- [ ] I know when to stop, document, and escalate.
- [ ] I can explain production validation and handoff.

## Final Interview Answer (45 seconds)

> "A runbook is a documented, step-by-step operational procedure. During server onboarding, I would follow the approved runbook to verify hardware, access the BMC, check BIOS and firmware, configure storage, validate networking, deploy Linux, execute existing configuration workflows, and complete validation. If a step fails, I would gather evidence, troubleshoot within the approved procedure, and escalate when necessary. I would document the outcome and update inventory before handoff."

**Roman Urdu memory line:** Runbook = **Kya karna hai + Kaise karna hai + Kaise verify karna hai + Fail ho to kya karna hai.**
