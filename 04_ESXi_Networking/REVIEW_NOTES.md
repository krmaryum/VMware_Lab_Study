# Review notes

## Source material

The two original Stage 1 English/Roman Urdu archives supply the recorded walkthrough and 17 screenshot stages. The later ESXi Host Networking archive supplies management/adapter inventory evidence and a proposed isolated follow-up exercise. The new bundle combines their scope without presenting the planned exercise as completed.

## Main changes

- Eight separate bilingual topics, matching the DNS-style concept → lab → evidence → interpretation structure.
- Original Stage 1 procedures, lab screenshots and teacher-page images preserved.
- Later uplink inventory shown separately instead of asserting an earlier single-uplink stage is today’s live final state.
- Dedicated teaming and shaping chapters distinguish proposed values and measurements from actual captured results.
- Current-source addressing issue identified: 172.16.50.0/24 overlaps recorded STORAGE 172.16.0.102/16. The inherited examples are retained but explicitly require a verified non-overlapping replacement before use.
- Windows DNS-only checks add -NoHostsFile to avoid treating -DnsOnly alone as bypassing the hosts file.
- Old ASCII topology trees replaced with concise paths; detailed diagrams remain in the supplied reference images.

## Validation and limits

The bundle was built from the source files. Original screenshot bytes were compared with the included copies, local Markdown image/file links were checked, eight bilingual chapter pairs were confirmed, separate chapter archives were rebuilt, and ZIP integrity was checked. No ESXi host was accessed, no new measurements were taken and no absent screenshot was reconstructed. The review does not establish current live settings or independently certify every historical statement in the original sources.
