# AD DS: Domain Environment

## English

A **domain environment** is a setup where users and computers are managed centrally through an Active Directory domain. A user can use the same domain account on joined computers where sign-in is permitted.

**Lab example:** `CLIENT01` and `CLIENT02` are both joined to `ad.example.com`.

| When Khalid… | What happens? |
| --- | --- |
| Signs in to `CLIENT01` with his domain account | A domain controller verifies his identity during online domain sign-in. |
| Signs in to `CLIENT02` | He can use the same domain account if sign-in is allowed. |
| Opens `\\FS01\Finance` | The file server separately checks whether he has permission to access the folder. |

**Workgroup vs. domain:** In a workgroup, each computer manages its own local accounts. In a domain, administrators can manage domain accounts and applicable policies centrally.

**Remember:** Sign-in checks identity. Folder access checks permission.

## Roman Urdu

**Domain environment** aisa setup hai jahan users aur computers ko Active Directory domain ke zariye centrally manage kiya jata hai. User apna wahi domain account un joined computers par use kar sakta hai jahan sign in ki ijazat ho.

**Lab ki misal:** `CLIENT01` aur `CLIENT02` dono `ad.example.com` mein joined hain.

| Jab Khalid… | To kya hota hai? |
| --- | --- |
| `CLIENT01` par domain account se sign in karta hai | Online domain sign-in ke waqt domain controller us ki identity verify karta hai. |
| `CLIENT02` par sign in karta hai | Agar ijazat ho to woh wahi domain account use kar sakta hai. |
| `\\FS01\Finance` kholta hai | File server alag se check karta hai ke usay folder ka access milna chahiye ya nahin. |

**Workgroup aur domain ka farq:** Workgroup mein har computer apne local accounts khud manage karta hai. Domain mein administrators domain accounts aur lagu hone wali policies centrally manage kar sakte hain.

**Yaad rakhein:** Sign-in mein pehchan verify hoti hai. Folder access mein ijazat check hoti hai.

