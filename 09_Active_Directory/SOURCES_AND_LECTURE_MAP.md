# Sources, lecture map and clarifications

The original teacher PDF and screenshot are preserved. The study guides explain and qualify their points; they do not claim that example addresses or diagrams represent a verified deployment. Earlier unavailable screenshots are not included.

## Important distinctions

- DNS existed before Windows 2000; Windows 2000 introduced Microsoft Active Directory, which relies on DNS.
- The screenshot's “X.25” wording is ambiguous. X.25 is a networking protocol family, not a directory database. We do not silently replace the teacher's term with X.500.
- AD DS, AD FS, AD LDS and AD RMS are different roles/products, not interchangeable names or a literal historical equation.
- KCC calculates replication topology; it is not the entire replication engine.
- Schema/configuration/domain/application describe partition categories. Multiple application partitions can exist; Global Catalog partial replicas qualify simple “domain data never crosses domains” statements.
- AS and TGS are logical KDC functions; the diagram does not require three separate authentication servers. An ordinary resource server does not need NTDS.dit.
- GPO configuration uses both AD and SYSVOL. GPO application depends on links, scope, filtering and processing; it does not automatically affect every machine.
- The PDC emulator is not the sole writable DC or sole login processor. Infrastructure Master placement depends on forest/domain and Global Catalog design.
- Schema version and functional level are different. Schema extension/deactivation is controlled administration, not routine object deletion.
- The lecture's 250 MB figure concerns old database/log context, not a complete modern Windows Server requirement. Lab resource suggestions are not official minimum specifications.
- AD sites represent network topology for directory operations. Site-to-site replication is not inherently faster. Renaming, promotion and demotion require planning.
- Replication is not backup; a second DC is not disaster recovery by itself.

## Original lecture pages

- Page 1: Agenda — [page image](class_reference/page-01.png)
- Page 2: AD DS title — [page image](class_reference/page-02.png)
- Page 3: Overview and functions — [page image](class_reference/page-03.png)
- Page 4: Partitions title — [page image](class_reference/page-04.png)
- Page 5: Database partitions — [page image](class_reference/page-05.png)
- Page 6: Authentication title — [page image](class_reference/page-06.png)
- Page 7: Kerberos diagram — [page image](class_reference/page-07.png)
- Page 8: Objects title — [page image](class_reference/page-08.png)
- Page 9: Object examples — [page image](class_reference/page-09.png)
- Page 10: Purpose title — [page image](class_reference/page-10.png)
- Page 11: Purpose and integration — [page image](class_reference/page-11.png)
- Page 12: Features title — [page image](class_reference/page-12.png)
- Page 13: Features — [page image](class_reference/page-13.png)
- Page 14: Structure title — [page image](class_reference/page-14.png)
- Page 15: Logical and physical structure — [page image](class_reference/page-15.png)
- Page 16: FSMO title — [page image](class_reference/page-16.png)
- Page 17: Forest-wide roles — [page image](class_reference/page-17.png)
- Page 18: Domain-wide roles — [page image](class_reference/page-18.png)
- Page 19: Schema title — [page image](class_reference/page-19.png)
- Page 20: Schema versions — [page image](class_reference/page-20.png)
- Page 21: Requirements title — [page image](class_reference/page-21.png)
- Page 22: Requirements — [page image](class_reference/page-22.png)

## Official references

These support the technical clarifications and provide further reading. Consult current documentation before a production change.

- [AD DS overview](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/active-directory-domain-services)
- [AD replication and KCC](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/replication/active-directory-replication-concepts)
- [Kerberos authentication](https://learn.microsoft.com/en-us/windows-server/security/kerberos/kerberos-authentication-overview)
- [Group Policy overview](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-policy/group-policy-overview)
- [AD database learning module](https://learn.microsoft.com/en-us/training/modules/understand-active-directory-database/)
- [FSMO roles](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/fsmo-roles)
- [Functional levels](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/active-directory-functional-levels)
- [Schema versions](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/find-active-directory-schema)

[README](README.md)
