# AD DS: Objects, Attributes aur Relationships

**Object** directory ki ek entry hoti hai. **Attribute** us entry ki detail hoti hai. **Relationship** do entries ke darmiyan link hota hai.

| Object ki type | Lab ki misal | Kya represent karta hai? |
| --- | --- | --- |
| User | `Khalid` | Khalid ka domain account |
| Computer | `CLIENT01` | Domain mein join kiye gaye computer ka account |
| Group | `Finance-Access` | Group jise permissions di ja sakti hain |
| OU | `Finance` | Container jahan directory objects organize kiye ja sakte hain |

**Misal:** Khalid ka account ek **object** hai. Us ka display name, sign-in name aur department **attributes** hain. Khalid ka `Finance-Access` group ka member hona ek **relationship** hai.

Agar file server par Finance folder ki permission `Finance-Access` group ko di gayi ho, to file server Khalid ki group membership ko access check mein use kar sakta hai.

**Yaad rakhein:** Object = entry. Attribute = entry ki detail. Relationship = entries ke darmiyan link.

