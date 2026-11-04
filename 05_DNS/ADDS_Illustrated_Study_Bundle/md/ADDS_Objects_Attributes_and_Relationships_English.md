# AD DS: Objects, Attributes, and Relationships

An **object** is an entry in Active Directory. An **attribute** describes that entry. A **relationship** links one entry to another.

| Object type | Lab example | What it represents |
| --- | --- | --- |
| User | `Khalid` | Khalid's domain account |
| Computer | `CLIENT01` | The account for a computer joined to the domain |
| Group | `Finance-Access` | A security group that can be granted permissions |
| Organizational Unit (OU) | `Finance` | A container used to organize directory objects |

**Example:** Khalid's account is an **object**. His display name, sign-in name, and department are **attributes**. His membership in `Finance-Access` is a **relationship**.

If the file server grants the Finance group access to the Finance folder, it can use Khalid's group membership when checking his permissions.

**Remember:** Object = entry. Attribute = detail. Relationship = link.

