# Phase 07 — 30-Minute Mock Interview + Communication
**Study: Wednesday evening; Thursday morning skim only.**

## Suggested rehearsal clock
| Minutes | Segment | Your goal |
|---|---|---|
| 0–3 | Introduction | 60–90 sec relevant professional story |
| 3–8 | Hardware/BMC | Show safe sequence and troubleshooting |
| 8–15 | PXE/networking | Explain boot flow and isolate failure |
| 15–21 | Linux/Puppet | Show command fluency and transferable experience |
| 21–26 | Incidents/teamwork | Use STAR structure and evidence |
| 26–30 | Your questions | Show interest in actual onboarding process |

## Tell me about yourself (adapt truthfully)
> I'm a Linux systems administrator with experience troubleshooting Linux servers, networking, SSH and automation. I've worked with Bash and Ansible, and I've been building practical infrastructure labs. What interests me about this role is the full server lifecycle—hardware validation, network provisioning, OS installation and verification. I bring a structured troubleshooting approach, careful documentation, and I’m motivated to learn the team's BMC, PXE and Puppet workflows quickly. 

*Customize the number of years and your exact production vs lab experience before using.*

## STAR examples — fill with YOUR real facts
**S = Situation, T = Task, A = Action, R = Result.**

### Story A — Connection troubleshooting
- Situation: __________________________
- Task: ______________________________
- Actions: checked route/SSH/service/firewall/keys and isolated failing layer
- Result (real, measurable): ___________
- Lesson: ____________________________

### Story B — Automation across nodes
- Situation: three-node Ansible lab (only describe as lab if that's how you used it)
- Task: validate managed-node connectivity and make playbook reliable
- Actions: inventory, SSH identities, ping test, output and logs
- Result: ____________________________
- Lesson: ____________________________

### Story C — Incomplete or failing work
- Situation: __________________________
- Task: ______________________________
- Actions: informed stakeholders, collected evidence, escalated, documented workaround
- Result: ____________________________
- Lesson: ____________________________

## Questions for spoken practice
1. Walk me through onboarding a newly racked server.
2. What can a BMC tell you when Linux won't boot?
3. RAID 1 vs JBOD: when would each be requested?
4. Explain how a server boots from network via PXE.
5. DHCP works, boot file fails: what do you inspect?
6. Why might imaging work but Puppet fail?
7. How do you validate a Linux host before production?
8. How would you recognize and escalate a DOA server?
9. What details belong in inventory/handoff records?
10. Tell me about a time you troubleshot a difficult issue.
11. What do you do if you don't know a tool?
12. What questions do you have for us?

## Professional answers when experience is limited
**Don't bluff.** Example: “I haven't administered Puppet in production. I have used Ansible for managed-node configuration, so I understand desired state, repeatability and troubleshooting. I would check the existing Puppet run results, identify the failed resource and follow the established operational procedure.”

## Day-of checklist
- [ ] Interview time and timezone rechecked against invitation.
- [ ] Audio/video/link working; quiet space; notes and résumé ready.
- [ ] Two-minute introduction practiced; five stories truthful.
- [ ] Six key concepts can be explained simply without prompts.
- [ ] Questions for interviewer prepared.
- [ ] Interview ending: thank them and reiterate interest.
