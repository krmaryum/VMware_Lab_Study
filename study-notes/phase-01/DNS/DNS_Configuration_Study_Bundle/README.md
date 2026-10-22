# DNS configuration study bundle

English and Roman Urdu • Lab checkpoint: 21 October 2026

Extract the entire ZIP before opening the Markdown files. Keep each chapter's screenshots folder beside its notes so images display correctly. Each topic is also supplied as an individual ZIP in separate_bundles.

## Suggested study order

1. [Primary DNS](01_Primary_DNS/STUDY_NOTES_ENGLISH.md): zones, A/PTR, CNAME and verification.
2. [Secondary DNS](02_Secondary_DNS/STUDY_NOTES_ENGLISH.md): DNS01 permissions and DNS02 replication.
3. [Stub DNS](03_Stub_DNS/STUDY_NOTES_ENGLISH.md): locating authoritative servers.
4. [Conditional forwarding](04_Conditional_Forwarding/STUDY_NOTES_ENGLISH.md): domain-specific query routing.
5. [Trust Points and DNSSEC](05_Trust_Points_DNSSEC/STUDY_NOTES_ENGLISH.md): signing versus validation, then unsigning.

Every chapter has STUDY_NOTES_ENGLISH.md, STUDY_NOTES_ROMAN_URDU.md and screenshot evidence. The primary chapter includes earlier teacher reference images labelled as such; they are not represented as your own captures. These are configuration notes, not a complete OS installation guide. No class PDFs were available as source material for this deliverable.

## Verified status

| Topic | Evidence / current status |
| --- | --- |
| DNS01 primary | Forward records, reverse PTR and alias verified |
| DNS02 secondary | Records transferred; forward/reverse queries to .122 passed |
| DNS03 stub | Stub records loaded; forward/reverse queries to .123 passed |
| KRNC.org forwarder | Saved pointing to 172.16.0.18; resolution untested |
| DNSSEC | Forward zone signed, then unsigned; final state unsigned |
| Trust Points | Concept and future import procedure only; no import/validation evidence |

## Compare the concepts

| Feature | What it keeps | Main job |
| --- | --- | --- |
| Primary | Writable authoritative zone | Maintain original records |
| Secondary | Full read-only authoritative copy | Serve replicated records |
| Stub | SOA, NS, necessary address records | Find authoritative servers |
| Conditional forwarder | Domain-to-DNS-IP rule | Route queries for that domain |
| Trust Point | Trusted key/digest information | Start DNSSEC validation |

## Suggestions for your next study session

Repeat each chapter independently and record both the DNS server queried and the returned result. For DNSSEC, capture the missing key/ZSK/rollover wizard pages before repeating; finish a separate trust-anchor and validation exercise. For conditional forwarding, first establish whether the target is real and reachable and obtain a known record. Failover, automated replication after a new edit, and DNSSEC validation remain optional future tests, not completed claims.

## Reference sources

- [Microsoft: DNS forwarding](https://learn.microsoft.com/en-us/windows-server/networking/dns/forwarding)
- [Microsoft: conditional-forwarder PowerShell](https://learn.microsoft.com/en-us/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone)
- [Microsoft: Trust Anchors](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn593672(v=ws.11))
- [Microsoft: Import a Trust Point](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn593665(v=ws.11))
- [Microsoft: Import-DnsServerTrustAnchor](https://learn.microsoft.com/en-us/powershell/module/dnsserver/import-dnsservertrustanchor)

Screenshots document your captured lab stages; Microsoft links support the explanations. Historical/classroom choices are separated from unverified future procedures.
