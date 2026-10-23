# DNS configuration — theory and recorded lab

Reviewed learning edition • English and Roman Urdu

This bundle keeps the original six-topic layout, detailed procedures and screenshot evidence. Each chapter now connects its definition and purpose to the actual lab workflow and the meaning of the results. It is a review of supplied evidence, not a live re-test of the machines.

## Start here / Yahan se shuru karein

Extract the whole ZIP. Open one language per topic and keep its `screenshots` folder beside the Markdown files. A Markdown preview or GitHub displays the images; plain Notepad displays their link text.

**Existing-lab revision:** Primary → Secondary → Stub → Conditional Forwarding → DNSSEC → Suffix.

**Fresh-build reading:** Suffix → Primary → Secondary → Stub → Conditional Forwarding → DNSSEC. Review actual network settings before reproducing any procedure. The folder numbers stay unchanged so the familiar layout is preserved.

Har topic mein pehle concept aur uska purpose, phir recorded steps, screenshots aur result ki explanation hai. Aik waqt mein aik language parhein. Future test ko completed result na samjhein.

| Topic | English notes | Roman Urdu notes |
|---|---|---|
| Primary DNS | [English](01_Primary_DNS/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](01_Primary_DNS/STUDY_NOTES_ROMAN_URDU.md) |
| Secondary DNS | [English](02_Secondary_DNS/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](02_Secondary_DNS/STUDY_NOTES_ROMAN_URDU.md) |
| Stub Zone | [English](03_Stub_DNS/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](03_Stub_DNS/STUDY_NOTES_ROMAN_URDU.md) |
| Conditional Forwarding | [English](04_Conditional_Forwarding/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](04_Conditional_Forwarding/STUDY_NOTES_ROMAN_URDU.md) |
| Trust Points / DNSSEC | [English](05_Trust_Points_DNSSEC/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](05_Trust_Points_DNSSEC/STUDY_NOTES_ROMAN_URDU.md) |
| DNS Suffix | [English](06_DNS_Suffix/STUDY_NOTES_ENGLISH.md) | [Roman Urdu](06_DNS_Suffix/STUDY_NOTES_ROMAN_URDU.md) |

## Recorded environment

| Setting | Supplied lab record |
|---|---|
| Network | VMnet2 host-only, 192.168.0.0/24 |
| Default gateway | Blank in the recorded isolated lab |
| Forward zone | VCPLAB.Com |
| Reverse zone | 0.168.192.in-addr.arpa |
| DNS01 | 192.168.0.121 — standard primary |
| DNS02 | 192.168.0.122 — forward/reverse secondary |
| DNS03 | 192.168.0.123 — forward/reverse stub and resolving server |
| Directory model | Workgroup, file-backed zones; this is not an AD-integrated exercise |

This completed configuration supersedes the earlier installation-only checkpoint at 192.168.23.10. Earlier screenshots describing that checkpoint are historical, not the current configuration recorded here. The example domain name is retained from the private classroom lab; public ownership is not established by these notes.

## Results and limits / Results aur unki had

| Topic | Recorded result | Not established by that result |
|---|---|---|
| Primary | Forward/reverse records and CNAME lookup verified | Every listed repeat command was not separately captured |
| Secondary | Full records loaded; direct forward/reverse lookup passed | Primary outage recovery or a later new-record replication test |
| Stub | Limited authority records loaded; lookups through DNS03 passed | Exact packet path or behavior with all authorities offline |
| Conditional forwarding | KRNC.org rule saved with target 172.16.0.18 | Working route, target availability or successful forwarded lookup |
| DNSSEC | Zone signed, then unsigned; final state unsigned | Trust-anchor import or end-to-end validation |
| DNS suffix | DNS03 suffix/search-list shown after restart | AD domain join or successful registration of every name |

## Compare the concepts

| Feature | Keeps | Purpose |
|---|---|---|
| Standard primary | Writable zone records | Maintain this lab’s authoritative data |
| Secondary | Full read-only zone copy | Answer from replicated records |
| Stub | Limited authority information | Locate authoritative servers |
| Conditional forwarder | Domain-to-DNS-address rule | Send matching queries to configured destinations |
| Trust Point | Trusted public key/digest anchor | Provide a starting point for DNSSEC validation |
| Primary DNS suffix | Computer naming context | Form a full computer name; not an AD join |

## Recommended next session

The highest-value follow-up is a controlled secondary update and outage test, with actual outputs recorded. Conditional forwarding needs a real reachable target and known record. DNSSEC validation should be a separate signed-zone/trust-anchor exercise. None of these has been marked completed in this review.

Production design also considers independent host/storage/network failure domains, restricted transfers, appropriate updates and tested backups. The original isolated-lab choices remain documented as choices, not universal defaults.

## Evidence and included files

All original screenshots and screenshot indexes are retained. Teacher-reference images remain labelled separately from local captures. Missing wizard pages are not reconstructed as evidence. Chapter commands remain usable repeat procedures; successful output is claimed only where the original record supports it.

`separate_bundles` contains freshly rebuilt copies of all six updated chapters, with the same notes as the main folders. Original detailed instructions are retained for reproducibility. `REVIEW_NOTES.md` records the editorial changes and verification scope.

## Official references

- [Microsoft: DNS zones](https://learn.microsoft.com/en-us/windows-server/networking/dns/zone-types)
- [Microsoft: Manage DNS zones](https://learn.microsoft.com/en-us/windows-server/networking/dns/manage-dns-zones)
- [Microsoft: DNS forwarding](https://learn.microsoft.com/en-us/windows-server/networking/dns/forwarding)
- [Microsoft: DNSSEC overview](https://learn.microsoft.com/en-us/windows-server/networking/dns/dnssec-overview)
- [Microsoft: Trust Anchors](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn593672(v=ws.11))
- [Microsoft: Dynamic DNS updates and naming](https://learn.microsoft.com/en-us/windows-server/networking/dns/dynamic-update)

Screenshots are the lab evidence. Official references explain the mechanisms; they do not prove the state of the running machines.
