# Phase 01 — Physical Server Onboarding Fundamentals
**Priority: Critical | Study: Sunday**

## 1. What does server onboarding mean?
Taking a newly racked physical server from hardware handoff to a consistent, verified, inventory-tracked, production-ready condition. It is a **process**, not simply an OS installation.

### Interview-ready answer
> I first confirm the hardware handoff, rack position, asset ID, power and cabling. Next I verify out-of-band management through the BMC, confirm firmware and BIOS settings, check storage and network assignments, run the approved provisioning workflow, and verify operating system health and configuration management. I record evidence and only mark the server ready after required checks pass.

## 2. Server components and their checks
| Component | Purpose | Validation idea |
|---|---|---|
| CPU | Executes workloads | Expected model/count, firmware/OS detection |
| RAM | Working memory | Expected capacity, corrected/uncorrected ECC errors |
| SSD/NVMe/HDD | Persistent storage | Expected serials/count, SMART/health, controller view |
| NIC | Network connectivity | MAC, physical link, speed, correct port/VLAN |
| PSU | Electrical power | Both supplies healthy if redundant |
| BMC | Independent management controller | Reachability, hardware alerts, remote console |
| GPU | Accelerator | Expected quantity/model, driver and diagnostic detection |

## 3. Hardware handoff checklist
- [ ] Correct asset/service ticket and rack/U position
- [ ] Switch port, cabling, power feeds and labels match inventory
- [ ] BMC address/identity and authorization available
- [ ] No obvious health faults (temperature, fans, power, memory, storage)
- [ ] Correct hardware count and approved configuration recorded

## 4. Production readiness means
- Hardware healthy; expected server model and components present.
- Correct management and data-plane connectivity.
- Correct firmware, boot policy, storage mode and OS image.
- Provisioning and configuration-management jobs completed.
- Validation and fleet records completed; outstanding faults tracked or resolved.

## 5. Common questions and strong answers
**Q: Bare metal vs VM?** Bare metal runs the workload directly on dedicated hardware; a VM runs on virtualized hardware supplied by a hypervisor. Bare-metal onboarding adds hardware and firmware checks that VM creation may abstract away.

**Q: What is an asset ID?** A tracking identifier connecting a physical server to its ticket, rack, hardware identity, and lifecycle state.

**Q: Why record serial numbers and rack location?** It prevents confusion when multiple identical systems are deployed and makes remote-hands work traceable.

**Q: What if the asset details don't match?** Stop the affected step; verify asset tags and ticket, update the responsible team, do not provision an ambiguous server.

## 6. Self-test
1. Tell the entire onboarding process in one minute.
2. Why is BMC connectivity verified before imaging?
3. What evidence proves the system is production-ready?
4. Which failures are hardware vs network vs OS configuration?

**Simple Roman Urdu:** Server onboarding ka matlab naya physical server receive karna, BMC/BIOS/network/storage verify karna, OS deploy karna, configuration apply karna, phir health check aur documentation ke baad production ke liye ready mark karna.
