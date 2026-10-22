# Trust Points and DNSSEC — signing and unsigning

## What and why

DNSSEC adds signatures for data-origin authentication, integrity and authenticated denial of existence. It does not encrypt DNS traffic. A Trust Point contains trusted DNSKEY/DS anchor information used as a starting point for validation; it does not create an AD domain trust. Signing publishes signed zone data; validation checks it against a chain ending in a trusted anchor. These are distinct tasks.

| Record/key | Purpose |
| --- | --- |
| KSK | Signs the DNSKEY record set; its public information can establish trust |
| ZSK | Signs other zone record sets |
| DNSKEY | Public DNSSEC key |
| RRSIG | Signature over a DNS record set |
| NSEC3 | Supports authenticated denial of existence using hashed names |

## 1. Start signing on DNS01

1. DNS Manager → DNS01 → Forward Lookup Zones → VCPLAB.Com.
2. Right-click zone → DNSSEC → Sign the Zone.
3. Read the DNSSEC page → Next.
4. Choose Customize zone signing parameters → Next.
5. Continue to the Key Signing Key information page → Next.
6. On the KSK list, click Add and configure the key.

## 2. Review the signing parameters

The captured summary confirms DNS01 as Key Master and a KSK using RSA/SHA-256, 2048 bits, Microsoft Software Key Storage Provider, DNSKEY signature validity 168 hours. Intermediate key, ZSK, denial-of-existence and rollover pages were not captured. Do not claim their exact values from the summary alone. For a repeat run, follow the teacher's settings on those pages and capture them before proceeding.

1. Review all chosen parameters in the summary.
2. Click Next to start signing; complete the wizard when it succeeds.
3. Refresh VCPLAB.Com. Screenshot 042002 shows DNSKEY, RRSIG and NSEC3 records, confirming the zone was signed.

This signing result alone does not prove a client or resolver performed DNSSEC validation. Reverse-zone signing was not shown.

## 3. Trust Point import — explanation only, NOT completed

A separate future validation exercise would require the zone to remain signed and an authenticated copy of its public trust-anchor information on the validating server. In DNS Manager on that server, right-click Trust Points → Import and select a trusted keyset or dsset file. Do not transfer private signing keys as a trust anchor.

Example only, after a valid file has actually been obtained:

```powershell
Import-DnsServerTrustAnchor -KeySetFile 'C:\DNSKeys\keyset-VCPLAB.Com'
Get-DnsServerTrustPoint
Get-DnsServerTrustAnchor -Name 'VCPLAB.Com'
```

The example file path was not created or verified in this lab. Merely requesting DNSSEC records or seeing RRSIGs does not establish successful validation. A future lab should verify the resolver's validation behavior, including rejection of deliberately invalid signed data in an isolated test.

## 4. Unsign the zone — completed

1. Right-click VCPLAB.Com → DNSSEC → Unsign the Zone.
2. Read the warning that DNSSEC records will be removed and the zone cannot be updated while unsigning.
3. Click Next and complete the wizard.
4. Refresh. Screenshot 042249 shows the original SOA, NS, A and CNAME records without the DNSSEC records.

Final checkpoint: VCPLAB.Com is unsigned. No Trust Point import or DNSSEC-validation test was demonstrated. If repeating DNSSEC validation later, sign first and plan trust-anchor handling before any subsequent unsigning.


## Screenshot walkthrough

Follow the numbered procedure above; these images preserve the captured stages. Missing wizard pages are not evidence of the exact settings chosen.

## 040944 — Zone DNSSEC menu

![Zone DNSSEC menu](screenshots/040944.png)

## 041031 — Sign the Zone

![Sign the Zone](screenshots/041031.png)

## 041149 — DNSSEC introduction

![DNSSEC introduction](screenshots/041149.png)

## 041216 — Customize signing parameters

![Customize signing parameters](screenshots/041216.png)

## 041320 — KSK explanation

![KSK explanation](screenshots/041320.png)

## 041424 — Add KSK; list initially empty

![Add KSK; list initially empty](screenshots/041424.png)

## 041931 — Signing summary

![Signing summary](screenshots/041931.png)

## 042002 — Signed zone records

![Signed zone records](screenshots/042002.png)

## 042153 — Zone context menu before unsigning

![Zone context menu before unsigning](screenshots/042153.png)

## 042217 — Unsign wizard warning

![Unsign wizard warning](screenshots/042217.png)

## 042249 — Final unsigned zone

![Final unsigned zone](screenshots/042249.png)
