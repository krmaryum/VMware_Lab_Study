# Protocol — Brief Study Note

## Protocol kya hai?

**Protocol** communication ke muqarrar rules ko kehte hain. Yeh rules batate hain ke devices data kaise bhejein, receive karein aur samjhein.

## Kyun zaroori hai?

Dono devices ko message ka format aur request/jawab ka tareeqa samajh aana chahiye, taake communication sahi ho.

## Common examples

| Protocol | Bunyadi kaam |
| --- | --- |
| **DNS** | Names ke liye IP addresses jaise records resolve karna |
| **HTTP** | Web requests aur responses exchange karna |
| **SSH** | Secure remote login aur commands chalana |
| **LDAP** | Directory information access karna |
| **Kerberos** | Tickets ke zariye authentication karna |

## AD DS ki example

Khalid domain computer par sign in karta hai. Computer **Kerberos protocol** ke mutabiq Domain Controller ki KDC service se authentication aur tickets ke liye communicate karta hai.

**KDC = Key Distribution Center.**

## Protocol aur port ka farq

Protocol communication ke rules hain; port service ke network endpoint ko identify karne mein madad karta hai.

Misal: **SSH** protocol hai aur aam tor par **TCP port 22** use karta hai.

## Yaad rakhne ki line

> Protocol batata hai: **“Baat-cheet kis tareeqay se hogi?”**
