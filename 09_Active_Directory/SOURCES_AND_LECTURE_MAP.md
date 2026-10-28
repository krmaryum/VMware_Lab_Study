# Source map and technical clarifications

## Supplied sources

- [Original typed class notes](CLASS_NOTES_ORIGINAL.md).
- [Original 22-page teacher lecture](reference/Teacher_ADDS_Lecture.pdf), copied unchanged from the newly supplied attachment.
- [Page 7 Kerberos diagram](images/teacher-kerberos.png), rendered from that PDF; this is class material, not a screenshot of a configured lab.

## PDF coverage

| PDF pages | Lecture topic | Study sections |
|---|---|---|
| 1–3 | Agenda and AD DS overview | 1–4, 11 |
| 4–5 | Database and partitions | 13 |
| 6–7 | Authentication | 12 |
| 8–9 | Objects | 11 |
| 10–13 | Purpose and features | 3, 11–13, 16 |
| 14–15 | Logical and physical AD structure | 8, 11 |
| 16–18 | FSMO roles | 14 |
| 19–20 | Schema versions | 15 |
| 21–22 | Installation requirements | 16 |

## Clarifications applied

1. SAM is not exclusive to workgroup computers; ordinary domain members also have local accounts.
2. LDAP is a protocol. A directory server provides storage; Linux is not the only platform supporting LDAP.
3. Port 389 is not the entirety of AD networking. LDAP may use StartTLS; LDAPS conventionally uses 636.
4. vCenter SSO identity, ESXi root and an SSH alias are separate contexts.
5. X.25 in the supplied shorthand requires teacher confirmation; X.500 is a possible intended reference, not an asserted correction.
6. NT supported NTFS, and historical NT domains existed before Active Directory. DNS and NTFS were not introduced with Windows 2000.
7. AD DS/FS/RMS/LDS are different services, not an equality or a single 2003 R2/SP2 launch event.
8. Workgroup/domain describes management organization; physical topology describes connections. AD logical structure has its own meaning.
9. UPN is a login-name format, not proof of an email mailbox. NetBIOS name and UPN suffix are distinct fields.
10. Group Policy scope is conditional. Resource servers enforce access; a valid identity/trust is not universal permission.
11. AS and TGS are KDC functions; a normal resource server does not need NTDS.dit.
12. Four partition categories do not mean exactly four partitions. Global Catalog partial replicas qualify the domain-replication simplification.
13. KCC calculates topology. SYSVOL file replication is distinct from AD database replication. Inter-site replication is not inherently faster.
14. PDC Emulator is not the only authentication/password-writing DC or the processor of all client GPOs. Role placement and recovery require planning.
15. Schema changes are controlled; routine schema deletion is not equivalent to deleting a user. Schema version and functional level are different.
16. The lecture's 250 MB is not a complete modern server requirement. Example addresses are not verified available. Installation readiness must be checked for the actual lab.

## Official reading

These references support the distinctions and provide further study. They are not evidence of a deployed lab. The notes paraphrase and teach the concepts rather than reproducing vendor articles.

- [AD DS overview](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/active-directory-domain-services)
- [SAM and account domains](https://learn.microsoft.com/en-us/windows/win32/secmgmt/built-in-and-account-domains)
- [User name formats](https://learn.microsoft.com/en-us/windows/win32/secauthn/user-name-formats)
- [LDAP and directory services](https://www.openldap.org/doc/admin26/intro.html)
- [Windows service ports](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/service-overview-and-network-port-requirements)
- [vCenter SSO account context](https://knowledge.broadcom.com/external/article?legacyId=2033875)
- [Microsoft Entra ID name change](https://learn.microsoft.com/en-us/entra/fundamentals/new-name)
- [AD LDS and former ADAM name](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/adam/active-directory-lightweight-directory-services)
- [Replication concepts and KCC](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/replication/active-directory-replication-concepts)
- [FSMO roles](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/fsmo-roles)
- [AD schema versions](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/find-active-directory-schema)
- [AD functional levels](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/active-directory-functional-levels)
- [Kerberos overview](https://learn.microsoft.com/en-us/windows-server/security/kerberos/kerberos-authentication-overview)

[Return to README](README.md)
