# AD DS: What Is a Forest?

**A forest is the highest-level logical boundary in Active Directory.** It can contain one domain or multiple domains.

| Term | Lab example | Meaning |
| --- | --- | --- |
| Forest | `ad.example.com` forest | The highest-level AD boundary |
| Forest root domain | `ad.example.com` | The first domain created in the forest |
| Child domain | `chicago.ad.example.com` | A separate domain under the root domain in the same forest |
| Organizational Unit (OU) | `Finance` | A container within a domain for organizing users and computers |

Domains in a forest share a **schema** (rules for objects and attributes) and **forest-wide configuration**. Each domain maintains its own domain data, such as users, groups, and computer accounts.

**Remember:** `DC01` and `DC02` can hold replicas of the same domain. Two domain controllers do not mean two domains or two forests.

**Forest Active Directory ki sab se upar wali logical boundary hai.** Is mein ek domain ya kai domains ho sakte hain.

| Term | Lab ki misal | Matlab |
| --- | --- | --- |
| Forest | `ad.example.com` forest | Sab se upar ki AD boundary |
| Forest root domain | `ad.example.com` | Naye forest ka pehla domain |
| Child domain | `chicago.ad.example.com` | Usi forest mein root domain ke neeche ek alag domain |
| OU | `Finance` | Domain ke andar users aur computers organize karne ka container |

Ek forest ke domains **schema** (objects aur attributes ke rules) aur **forest-wide configuration** share karte hain. Har domain apne users, groups aur computers ka domain data rakhta hai.

**Yaad rakhein:** `DC01` aur `DC02` ek hi domain ki copies rakh sakte hain. Do DCs ka matlab do domains ya do forests nahin.

