# Federation — Brief Study Notes

## Federation kya hai?

**Federation** alag identity systems ke darmiyan trust ka arrangement hai. Is se aik application kisi trusted identity provider ki verify ki hui identity accept kar sakti hai.

## Kyun use karte hain?

User apni organization ke account se external application access kar sakta hai, baghair us application ko apna organization password diye.

## Simple example

1. Khalid training application open karta hai.
2. Application usay company ke **Identity Provider (IdP)** par sign-in ke liye bhejti hai.
3. IdP identity verify karke **signed token/assertion** deta hai.
4. Application token validate karti hai aur apni permissions ke mutabiq access deti hai.

## Important terms

| Term | Matlab |
| --- | --- |
| **Identity Provider (IdP)** | Identity verify karke authentication ka proof deta hai |
| **Service Provider (SP)** | Woh application/service jo trusted proof accept karti hai |
| **Token/assertion** | Identity aur related claims ka proof; configured trust ke mutabiq validate hota hai |
| **AD FS** | Active Directory Federation Services—Microsoft ki federation service |

## Federation aur SSO ka farq

- **Federation:** alag identity systems ke darmiyan trust.
- **SSO — Single Sign-On:** baar baar credentials enter karne ki zaroorat kam hona.

Federation SSO enable kar sakti hai. **Federation mein user databases merge hona zaroori nahin**, aur successful authentication se har resource ki permission nahin milti.

## Yaad rakhne ki line

> Federation mein application trusted identity provider ki tasdeeq accept karti hai; access permissions phir bhi check hoti hain.
