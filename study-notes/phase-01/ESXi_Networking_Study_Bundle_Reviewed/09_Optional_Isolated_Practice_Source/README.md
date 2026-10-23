# Optional original isolated practice source

> **Addressing correction:** Existing STORAGE was recorded as 172.16.0.102/16. The proposed 172.16.50.0/24 falls inside that /16, so it overlaps the existing connected network. Treat all 172.16.50.x values below as an unexecuted source example. Before implementation choose a verified unused, non-overlapping subnet and consistently replace every endpoint/interface value. Do not change the existing management or storage addresses merely to follow this example.


This is the original follow-up exercise material, retained in full for its preparation, MAC mapping, command details, class references and cleanup. It proposes vSwitch-LabNet and 172.16.50.0/24; completion of those proposed tasks was not evidenced. Main bilingual chapters are in the parent folder. Do not mistake this source’s “what you will build” for a completed lab report.

# ESXi Host Networking - Complete Study and Lab Bundle

Prepared for Khalid's desktop-esxi9 home lab. English + Roman Urdu. Follow the chapters in order, independently, without waiting for a reply after each step.

## What you will build

A separate standard switch with two uplinks, one VMkernel port group, and one VM port group. You will test host and VM connectivity, configure failover, and measure a traffic-shaping limit.

| Read order | File | Outcome |
|---|---|---|
| 0 | [Preparation](00_Preparation_and_Network_Plan.md) | MAC mapping, addressing, test endpoints |
| 1 | [Create switch](01_Create_Virtual_Switch.md) | New vSphere Standard Switch |
| 2 | [First uplink](02_Add_First_Uplink.md) | Working external path |
| 3 | [VMkernel](03_Create_VMkernel_Port_Group.md) | Host IP on lab network |
| 4 | [VM port group](04_Create_VM_Port_Group.md) | Guest VM connectivity |
| 5 | [Additional uplink](05_Add_Additional_Uplinks.md) | Two mapped uplinks |
| 6A | [NIC teaming](06A_NIC_Teaming_and_Failover.md) | Active/standby and active/active practice |
| 6B | [Traffic shaping](06B_Traffic_Shaping.md) | Measured outbound bandwidth limit |
| 7 | [Verification and troubleshooting](07_Verification_Troubleshooting_and_Rollback.md) | Evidence, fixes and cleanup |
| 8 | [Revision](08_Revision_Questions_and_Answers.md) | Concept and interview revision |
| 9 | [Sources and corrections](09_Sources_and_Class_Notes_Corrections.md) | Class PDF page references and corrections |

## How to use

Extract the entire ZIP, then open README.md in VS Code or another Markdown viewer. Keep the assets folder beside the notes so images render. COMPLETE_STUDY_NOTES.md combines all chapters into one searchable document.

Roman Urdu: ZIP ko poora extract karein. Chapters order mein follow karein. Har task ke baad expected result verify karein. Yeh instructions hain; aapke ESXi par changes automatically perform nahi kiye gaye.

The class PDFs are the teaching foundation. Three relevant original PDFs are included under class_reference. Other PDFs in the uploaded archive cover topics outside this bundle and are not duplicated here. Screenshots document the starting state, not completion of these new exercises.
