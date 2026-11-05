# AD DS Forests — English and Roman Urdu

## English

**Forest** is singular; **forests** is plural. A forest is the highest-level logical structure in Active Directory Domain Services (AD DS). It contains one or more domains. Even a lab with just one domain has a forest.

| Level | Meaning | Lab example |
| --- | --- | --- |
| Forest | A collection of one or more AD domains | `vcplab.com` forest |
| Domain | Where accounts, computers, and groups are managed | `vcplab.com` domain |
| OU (Organizational Unit) | A container used to organize objects within a domain | `IT` OU |
| Object | An entry in the directory, such as a user or computer | `Khalid` user account |

If you create a new forest with `vcplab.com` as its first domain, the forest and its first domain share a name. They are different concepts: the forest contains the domain.

**Why “hierarchical”?** A forest contains domains, and domains can use OUs to organize objects. This is a directory hierarchy, not a shared folder for documents.

## Roman Urdu

**Forest** singular hai aur **forests** plural hai. AD DS mein forest sab se upar wali logical structure hai. Ek forest mein ek ya zyada domains ho sakte hain. Agar lab mein sirf ek domain ho, tab bhi us ka ek forest hota hai.

| Level | Kya hota hai? | Lab example |
| --- | --- | --- |
| Forest | Ek ya zyada AD domains ka group | `vcplab.com` forest |
| Domain | Jahan accounts, computers aur groups manage hote hain | `vcplab.com` domain |
| OU (Organizational Unit) | Domain ke andar objects ko organize karne ka container | `IT` OU |
| Object | Directory mein ek entry, jaise user ya computer | `Khalid` user account |

Agar naya forest banate waqt aap pehle domain ka naam `vcplab.com` rakhte hain, to forest aur pehle domain ka naam ek hi hota hai. Dono alag concepts hain: domain forest ke andar hota hai.

**Hierarchical kyun?** Forest mein domains hote hain, aur domain ke andar OUs bana kar objects organize kiye ja sakte hain. Yeh directory ki tarteeb hai; documents ka shared folder nahin.
