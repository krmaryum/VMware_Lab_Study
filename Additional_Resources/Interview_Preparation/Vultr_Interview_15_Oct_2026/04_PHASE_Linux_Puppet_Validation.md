# Phase 04 — Linux Validation, Puppet, Ansible, Config Management
**Priority: High | Study: Tuesday**

## 1. First-boot Linux checklist
- [ ] Hostname and asset match.
- [ ] Expected distro, kernel and CPU architecture.
- [ ] NIC addressing, routes, DNS and link as expected.
- [ ] Disks, partitions, filesystems and mounts match approved layout.
- [ ] Correct NTP/time and critical services.
- [ ] SSH reachability and secure access policy verified.
- [ ] System logs free of blocking hardware/driver errors.
- [ ] Configuration-management run converged successfully.

## 2. Essential Linux diagnostic commands
```bash
hostnamectl
uname -r
cat /etc/os-release
lscpu
free -h
lsblk -f
df -hT
df -i
ip -br a
ip route
ss -tulpn
systemctl --failed
journalctl -p err -b --no-pager
sudo dmesg --level=err,warn | tail -80
```
`dmesg` output/permission and command options vary by distribution; use `sudo` only when permitted.

## 3. Puppet basics
Puppet is **declarative configuration management**: a catalog describes desired system state; agents generally apply that catalog, report results, and help correct configuration drift. Typical states: package installed, service enabled/running, file permissions/content controlled.

### Basic checks (if Puppet is actually installed)
```bash
puppet --version
sudo puppet agent --test
sudo systemctl status puppet
sudo journalctl -u puppet --since '30 minutes ago'
```
`puppet agent --test` can make real changes; only run with authorization and per runbook. Service names vary by deployment. In an interview, say you'd inspect the agent's last run, certificate/connectivity, catalog compilation errors, resource failures, and exit status.

## 4. Puppet vs Ansible
| Topic | Puppet | Ansible |
|---|---|---|
| Common design | Agent-based, pull/report cycle | Often agentless, push over SSH |
| Configuration approach | Declarative resources and catalogs | YAML playbooks/modules, often declarative tasks |
| Onboarding usage | Apply approved host baseline and validate reports | Run approved playbooks, validate and remediate |

**Avoid claiming they are interchangeable:** tools have different execution, authentication, troubleshooting and reporting paths. Your Ansible experience transfers as a foundational concept, not proof of production Puppet experience.

## 5. Scenario — Config management fails after OS install
1. Confirm host alive, correct DNS/time/network path and service state.
2. Inspect the automation job/agent result and exact failing resource.
3. Check credentials/certificates/repo/package connectivity as applicable.
4. Check whether change is safe to rerun and whether partial changes occurred.
5. Follow escalation/runbook; do not mark ready until baseline passes.

## 6. Strong interview answer
> After provisioning, I verify the host identity, network routes, storage layout, OS and service health. Then I run or observe the approved configuration-management process, review failures rather than assuming success, and record validation results. I've worked with Ansible, so I understand desired state and idempotency; for Puppet-specific commands and reporting I would follow the team's existing workflow.

## 7. Practice
- [ ] Explain config drift and idempotency.
- [ ] Tell an incident story where `systemctl`, `journalctl`, or SSH solved an issue.
- [ ] Explain why “image installed” does not mean “production-ready.”

**Roman Urdu:** OS install hona kaafi nahin. Network, disk, services aur logs validate karein. Puppet/Ansible approved settings apply karta hai; automation fail ho to exact error aur logs check kiye bina server ko ready nahin kahenge.
