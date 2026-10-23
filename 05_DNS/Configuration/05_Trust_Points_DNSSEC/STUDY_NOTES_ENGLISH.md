# Trust Points and DNSSEC — signing and unsigning

## Concept overview

DNSSEC uses digital signatures to authenticate DNS data and detect changes to signed information. A Trust Point holds the trusted key or digest that starts validation. The authoritative server signs record sets; a validating resolver checks signatures through a trusted chain. DNSSEC does not encrypt queries. Signing, importing a trust anchor and proving validation are separate steps; this lab demonstrated signing and unsigning only.


## Understanding this configuration

Ordinary name resolution returns data. DNSSEC additionally allows a validating resolver to check whether signed DNS data is authentic and unchanged. A trusted public key or digest provides a starting point; signatures are checked through the applicable chain of trust. Trust Points in DNS Manager organize that trusted anchor information. They are unrelated to Active Directory domain trusts.

Signing and validation happen in different roles. An authoritative server can publish DNSKEY and RRSIG records even though no client has yet demonstrated validation. Conversely, a screenshot of the Trust Points node does not prove that a particular zone’s signatures were successfully checked.

In this lab, signing produced DNSSEC records and unsigning removed them. The final forward zone is **unsigned**. This accurately demonstrates a signing/unsigning exercise, not an end-to-end validated deployment. The later trust-anchor import example is explanatory; the example file is not a captured lab artifact.

A future validation exercise needs a signed zone, securely obtained public anchor information, a validating resolver and evidence of its validation behavior. Deliberately invalid signatures should fail validation in an isolated test. Private signing keys are not trust-anchor files to distribute. Plan anchor changes and unsigning together so stale trust does not unexpectedly break resolution.

## Purpose in this lab

DNSSEC adds signatures for data-origin authentication, integrity and authenticated denial of existence. It does not encrypt DNS traffic. A Trust Point contains trusted DNSKEY/DS anchor information used as a starting point for validation; it does not create an AD domain trust. Signing publishes signed zone data; validation checks it against a chain ending in a trusted anchor. These are distinct tasks.

| Record/key | Purpose |
| --- | --- |
| KSK | Signs the DNSKEY record set; its public information can establish trust |
| ZSK | Signs other zone record sets |
| DNSKEY | Public DNSSEC key |
| RRSIG | Signature over a DNS record set |
| NSEC3 | Supports authenticated denial of existence using hashed names |

## Reading the lab record

The procedure below documents the configuration workflow and can be reused for practice. Statements marked verified refer to the supplied record; proposed tests and missing captures are identified separately. The review did not change or re-test the running machines.

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
