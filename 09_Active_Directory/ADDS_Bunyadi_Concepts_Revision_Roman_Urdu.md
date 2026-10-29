# AD DS — Bunyadi Concepts aur Revision

Roman Urdu | Dono supplied notes ka merged aur structured edition

Yeh guide pehle parhe hue concepts ko aik smooth order mein jama karti hai. Repeated explanations consolidate ki gayi hain; dono files ke unique points retain hain. Misaalein samjhane ke liye hain, completed lab ka evidence nahin. Is guide ko detailed AD DS reference bundle ke saath revision ke liye use karein.

## Contents

1. [Directory kya hai?](#1-directory-kya-hai)
2. [AD DS, Domain aur Domain Controller](#2-ad-ds-domain-aur-domain-controller)
3. [Domain aur Workgroup](#3-domain-aur-workgroup)
4. [Authentication, Authorization aur Access](#4-authentication-authorization-aur-access)
5. [DNS se DC kaise milta hai?](#5-dns-se-dc-kaise-milta-hai)
6. [Kerberos, KDC, TGT aur Service Ticket](#6-kerberos-kdc-tgt-aur-service-ticket)
7. [Objects aur Attributes](#7-objects-aur-attributes)
8. [Security Groups aur Permissions](#8-security-groups-aur-permissions)
9. [OU aur Security Group ka Farq](#9-ou-aur-security-group-ka-farq)
10. [Group Policy, Policy aur Permission](#10-group-policy-policy-aur-permission)
11. [Centralized Management aur Access Control](#11-centralized-management-aur-access-control)
12. [Replication aur Multiple DCs](#12-replication-aur-multiple-dcs)
13. [AD Sites](#13-ad-sites)
14. [Scalability aur Delegation](#14-scalability-aur-delegation)
15. [Combined Revision aur Learning Checkpoint](#15-combined-revision-aur-learning-checkpoint)

## Hamari consistent example

Poore guide mein aik hi example use hogi:

| Cheez | Example mein naam/kaam |
| --- | --- |
| User | Khalid |
| Domain computers | PC1 aur PC2 |
| Domain Controllers | DC1 aur DC2 |
| Security group | Finance-Read |
| OU | Finance OU |
| Resource | File server par Finance folder |
| Doosra department | Sales |

Finance-Read group, Finance OU aur Finance folder teen alag cheezein hain. Milte-julte naam ka matlab same object ya automatic connection nahin.

## 1. Directory kya hai?

Directory maloomat ki managed index hoti hai. Phone directory mein naam aur numbers hote hain; network directory mein users, computers, groups aur doosre resources ki information ho sakti hai.

Kitaab ka index samajhne ki aik misaal hai. Magar directory sirf jagah nahin batati; woh entries ki tafseel bhi rakhti hai. User ka naam, logon name aur department aisi information ki examples hain.

Directory mein shared folder ka reference hona, us folder ki tamam files directory database mein store hona nahin hai.

## 2. AD DS, Domain aur Domain Controller

**AD DS = Active Directory Domain Services.** Yeh Microsoft ki directory service hai jo users, computers, groups aur related network resources ki information centrally rakhne aur manage karne mein madad deti hai.

**Domain** users, computers aur resources ke management ka aik logical daira hai. Domain ke accounts ka central management hota hai.

**Domain Controller (DC)** woh server hai jis par AD DS chalti hai aur directory database ki copy hoti hai. Woh domain authentication aur directory queries jaise kaam provide karta hai. Domain mein aik se zyada DC ho sakte hain.

Example: admin Khalid ka domain account banata hai. DC us account ki information rakhta hai aur domain sign-in verify karne mein hissa leta hai. Finance folder ki read/write access us resource ki permissions se decide hoti hai; file server bhi yeh check karta hai.

| Lafz | Seedhi pehchan |
| --- | --- |
| AD DS | Directory service |
| Domain | Identities aur resources ka logical management daira |
| DC | Directory services dene wala server |
| Admin | Accounts aur settings manage karne wala authorized shakhs |

## 3. Domain aur Workgroup

| Domain | Workgroup |
| --- | --- |
| Domain accounts ka management centrally hota hai | Har computer ke local accounts alag manage hote hain |
| DCs domain directory services dete hain | AD domain jaisa central account nizam nahin hota |
| Aik domain account se allowed domain computers par login mumkin hai | Har machine apne local account authority ke mutabiq credentials check karti hai |

Agar Khalid ka local account PC1 aur PC2 dono par ho, to same naam ke bawajood accounts alag hain. Aik local password badalne se doosra automatically change nahin hota.

Domain account ko har domain computer par alag local account ki tarah banana zaroori nahin. Magar domain account hone se har server par sign-in ki ijazat bhi automatic nahin: login rights aur doosri restrictions apply hoti hain.

Workgroup ka matlab network communication band hona nahin. Suitable credentials aur permissions ke saath file sharing mumkin hai. Ordinary domain-joined computers par local accounts bhi reh sakte hain.

## 4. Authentication, Authorization aur Access

| Lafz | Sawal | Khalid ki misaal |
| --- | --- | --- |
| Authentication | Aap kaun hain? | Khalid ki identity verify karna |
| Authorization | Aap kya kar sakte hain? | Finance folder par Read allow karna |
| Access | Ijazat ke mutabiq resource use ho raha hai? | Khalid file khol kar parh raha hai |

**Ghalat password**, hamari simple example mein, authentication ka masla hai. **Sahi login lekin folder par Access Denied**, jab identity sahi establish ho chuki ho, authorization ki taraf ishara karta hai.

Khalid ko Read permission ho to woh permitted content parh sakta hai; sirf us Read grant se edit ki permission nahin milti. Asal access doosri applicable permissions aur controls se bhi mutasir ho sakti hai.

Yaad rakhein: **pehchan verify hona, har kaam ki ijazat milna nahin hai.** Real troubleshooting mein sirf error label se final cause assume na karein; relevant evidence dekhein.

## 5. DNS se DC kaise milta hai?

DNS naam ko records se resolve karne mein madad karta hai. AD mein uska khaas kaam clients ko DC aur domain services locate karne mein madad dena bhi hai.

Simple flow:

1. Khalid PC1 par domain sign-in karta hai.
2. PC1 DNS ki madad se suitable DC locate karta hai; AD service discovery mein SRV records important hain.
3. PC1 DC se authentication ke liye communicate karta hai.
4. Baad mein Finance folder kholte waqt resource ki permissions check hoti hain.

DNS password verify nahin karta aur folder ki permission nahin deta. DC mil jana aur successful authentication bhi alag checks hain.

Is liye AD client ki DNS settings ko domain records resolve karna aana chahiye. Sirf public internet DNS ka answer milna private domain services discover hone ka proof nahin.

## 6. Kerberos, KDC, TGT aur Service Ticket

Kerberos normal AD domain scenarios ka bunyadi authentication protocol hai. **Protocol** baat-cheet ke fixed ruling ko kehte hain.

DC par **KDC — Key Distribution Center** service hoti hai. KDC authentication aur tickets ke silsilay ko handle karti hai.

### Khalid ka login se Finance folder tak safar

1. Khalid domain sign-in karta hai aur computer KDC se rabta karta hai.
2. Required authentication exchange kamyab ho to computer ko **TGT — Ticket Granting Ticket** milta hai.
3. Khalid Finance file service use karna chahta hai. Computer TGT ki madad se us service ka **service ticket** mangta hai.
4. Computer service ticket file server ko present karta hai.
5. File service authentication material validate karke Khalid ke security context aur permissions ke mutabiq access decide karti hai.

| Cheez | Kaam |
| --- | --- |
| KDC | Kerberos authentication/tickets ki service |
| TGT | Service tickets hasil karne ke liye ticket |
| Service ticket | Khaas service ke liye ticket |

Har resource server ko password bhejne ke bajaye is flow mein tickets use hote hain. Ticket milne ka matlab har file ki permission milna nahin.

Tickets ki muddat hoti hai. Munasib conditions mein system renew karta ya naye tickets leta hai; har baar user se password dobara mangna lazmi nahin. DNS, network connectivity aur accurate time is process ke liye important hain.

Yaad rakhne ki line: **Kerberos se pehchan; permissions se ijazat.**

## 7. Objects aur Attributes

**Object** directory mein kisi cheez ki alag entry hai. **Attributes** us entry ki information hain.

| Example | Object type |
| --- | --- |
| Khalid ka account | User object |
| PC1 ka domain account | Computer object |
| Finance-Read | Security group object |
| Finance OU | Organizational Unit object |

Khalid ka naam, username aur department attributes ki examples hain. OU khud bhi object hai aur us mein doosre allowed objects rakhe ja sakte hain.

Resource ki directory entry aur actual resource alag hain. Directory mein printer ki information hone ka matlab printer ka physical device directory ke andar hona nahin.

## 8. Security Groups aur Permissions

Security group ki madad se multiple accounts ki access manageable hoti hai.

Hamari misaal:

1. Admin Finance-Read security group banata hai.
2. Khalid aur relevant Finance employees us mein add hote hain.
3. Admin Finance folder par group ko Read permission assign karta hai.
4. Khalid group ke zariye applicable Read access hasil kar sakta hai.

**Group banane se khud permission nahin milti.** Resource par relevant permission assign karni hoti hai. Is se har user ko alag permission dene ki mehnat kam hoti hai.

Read ka matlab parhna aur Write ka matlab likhna/tabdeeli karna hai. Windows file sharing mein share aur NTFS permissions dono relevant ho sakti hain. Effective access ko doosri memberships, direct grants aur applicable denies bhi affect kar sakte hain.

User aik waqt mein kai groups ka member ho sakta hai. Khalid ko Sales group mein add karne se Finance-Read membership automatically nahin hatti; admin ko intended access ke mutabiq purani membership review karni hoti hai.

## 9. OU aur Security Group ka Farq

**OU — Organizational Unit** domain ke andar logical container hai, samajhne ke liye folder jaisi misaal le sakte hain. Is mein users, computers, groups aur nested OUs rakhi ja sakti hain. Har object ka OU mein hona lazmi nahin; doosre containers bhi hote hain.

OU ke bunyadi kaam:

- Objects managed karna.
- Applicable users/computers ke liye Group Policy ka scope banana.
- Limited administrative authority delegate karna.

| Cheez | Bunyadi kaam | Hamari example |
| --- | --- | --- |
| Security group | Members ki resource permissions manage karna | Finance-Read ko folder par Read |
| OU | Organization, GPO scope aur delegation | Finance users/computers ko organize karna |

Khalid ko **Finance OU** mein rakhne se **Finance folder** ki access khud nahin milti. Usay relevant security group aur permissions ke zariye access mil sakti hai.

Finance OU ka aik shared password nahin hota. Us mein maujood users ke apne accounts aur passwords hote hain.

## 10. Group Policy, Policy aur Permission

**GPO = Group Policy Object.** Is mein user aur computer configuration settings hoti hain. Admin GPO ko OU se link kar sakta hai; applicable scope ke mutabiq settings process hoti hain.

Example: paanch minute inactivity ke baad screen lock ki suitable setting. Central configuration se har computer par alag ja kar setting karne ki mehnat kam hoti hai.

| Concept | Matlab | Example |
| --- | --- | --- |
| Policy | Settings aur ruling | Inactivity ke baad screen lock |
| Permission | Resource par allowed operation | Finance folder par Read |

OU mein har object par har GPO setting nahin lagti. User settings users aur computer settings computers ke liye hoti hain; placement aur processing context matter karta hai. Security groups GPO filtering mein use ho sakte hain, lekin groups par user/computer settings seedha apply nahin hotin.

**Password-policy clarification:** domain users ki aam password policy domain level par manage hoti hai. Finance OU par password-length GPO link karna Finance domain users ki separate password policy banane ka aam tareeqa nahin. Specific users/groups ke liye fine-grained password policies alag advanced mechanism hain; unka practical is guide ka hissa nahin.

## 11. Centralized Management aur Access Control

### Centralized Management

Users aur computers ka Centralized System se management. Khalid ka domain account centrally banaya aur manage kiya jata hai; har member computer par uska alag local account banana zaroori nahin.

### User and Resource Management

Accounts aur memberships se munasib resource access organize hoti hai. Finance-Read ko folder Read grant aur Khalid ko membership dena hamari example hai.

### Security and Access Control

Zaroorat ke mutabiq access dein aur zaroorat khatam ho to review/remove karein. Khalid Finance se Sales jaye to admin old Finance memberships aur suitable Sales memberships check kare.

Finance-Read se nikaalne par us membership ke zariye milne wali access hatane ka maqsad hota hai, lekin doosre groups ya direct permissions se access phir bhi ho sakti hai. Existing sessions, security tokens aur tickets ki wajah se tabdeeli har jagah foran nazar nahin aati.

Is liye membership change ke baad effective access verify karna zaroori hai; sirf group list edit karna complete verification nahin.

## 12. Replication aur Multiple DCs

**Replication** directory changes ko DCs ke darmiyan pohanchati hai.

Example: DC1 par Khalid ka domain account create hua. Replication se usi domain ka DC2 bhi account ki information hasil karta hai. Yeh doosre independent domain mein Khalid ka poora account copy karne ki example nahin.

Replication ko foran complete assume na karein. Agar account abhi DC2 tak nahin pohncha, us DC se authentication mein masla ho sakta hai.

### Aik se zyada DC kyun?

Availability aur redundancy ke liye. DC1 band ho to suitable available DC2 authentication de sakta hai, basharte network, DNS, required directory information aur doosri zaroori services sahi hon.

| Cheez | Faida/limit |
| --- | --- |
| Multiple DCs | Aik DC ki failure par doosri service copy available ho sakti hai |
| Replication | Directory changes copies tak pohanchti hain |
| Backup | Recovery ke liye alag protection; replication ka replacement nahin |

**Replication backup ka badal nahin.** Ghalti se hui deletion ya unwanted change bhi replicate ho sakti hai. Supported backup aur recovery planning alag chahiye.

## 13. AD Sites

**AD Site** network connectivity se related logical grouping hai jo configured IP subnets se associate hoti hai. Sites clients ko suitable DC locate karne aur replication organize karne mein madad karti hain.

Do offices ya buildings alag sites ho sakte hain, lekin har building lazmi separate AD site nahin. Design ko connectivity aur network requirements ke mutabiq tay karte hain.

Site ko domain ke barabar na samjhein. Aik domain multiple sites mein ho sakta hai; aik site mein multiple domains ke DCs bhi ho sakte hain.
## Beginner level par pehle yeh yaad karein: 
- Domain identity organization hai.
- Site network connectivity ki directory representation hai.

## 14. Scalability aur Delegation

### Scalability

Increasing Demand ke mutabiq system ki gunjaish barhana. Aaj 10 aur kal 100 computers hon to AD DS mazeed objects ka management kar sakta hai; design, capacity aur connectivity phir bhi assess karni hoti hai.

Zaroorat par additional DCs deploy kiye ja sakte hain. Gunjaish kam karna bhi planning ka hissa hai; DC ko hatane ke liye suitable demotion aur verification procedure chahiye. Sirf VM delete kar dena proper DC retirement nahin.

### Delegation

Kisi authorized shakhs ya team ko specific kaam ka limited ikhtiyar dena.

Example: help-desk operator ko Finance OU ke relevant users ke passwords reset karne ki delegated authority di jaye. Usay poore domain ka administrator banana zaroori nahin. Sensitive/protected accounts par additional restrictions ho sakti hain.

Delegation aur folder permission alag hain. Password-reset authority hone ka matlab Finance files par Read access milna nahin.

## 15. Combined Revision aur Learning Checkpoint

### Jaldi revision table

| Lafz | Seedha matlab |
| --- | --- |
| Directory | Entries aur unki information ki munazzam fehrist |
| AD DS | Markazi directory service |
| Domain | Identity/resources ka logical management daira |
| DC | AD DS chalane wala server |
| Authentication | Pehchan ki tasdeeq |
| Authorization | Allowed operation ka faisla |
| Access | Resource ko applicable ijazat ke mutabiq use kar pana |
| DNS | Names/records resolve; AD mein DC discovery bhi |
| KDC | Kerberos authentication aur tickets ki service |
| TGT | Service tickets lene ke liye ticket |
| Service ticket | Khaas service ke liye ticket |
| Object | Directory ki entry |
| Attribute | Entry ki maloomat |
| Security group | Membership ke zariye permission management |
| OU | Objects ka container, GPO scope aur delegation |
| Policy | Settings aur ruling |
| Permission | Resource par allowed operation |
| Replication | Directory changes copies tak pohanchana |
| Site | AD mein subnet/connectivity grouping |
| Scalability | Zaroorat ke mutabiq capacity barhana |
| Delegation | Specific kaam ka limited administrative ikhtiyar |

### Khud se sawalat

Key dekhne se pehle apne alfaaz mein jawab dein.

1. Directory sirf resource ka rasta batati hai ya attributes bhi rakhti hai?
2. AD DS, domain aur DC ka farq kya hai?
3. Workgroup ke PC1 aur PC2 par same username ka matlab same account hai?
4. Ghalat password hamari simple example mein kis stage ka masla hai?
5. Sahi login ke baad folder access deny hone se kya samajh aata hai?
6. DNS login flow mein kya madad karta hai?
7. KDC aur TGT ki full forms kya hain?
8. TGT aur service ticket ka farq kya hai?
9. Kya har service access par user ko password dobara type karna lazmi hai?
10. Khalid ka account object hai; department kya ho sakta hai?
11. Security group create karte hi folder permission mil jati hai?
12. Khalid ko Finance OU mein rakhne se Finance folder mil jayega?
13. User multiple groups ka member ho sakta hai?
14. Policy aur permission ki alag misaal dein.
15. Kya OU mein har object par har GPO setting lagti hai?
16. Sirf Finance OU par password GPO se Finance domain users ki alag password policy ban jati hai?
17. Membership remove hone ke baad purana session foran update hona guaranteed hai?
18. Replication mein kya pohanchta hai, aur kya yeh backup hai?
19. DC1 fail ho to DC2 authentication kab de sakta hai?
20. Kya har building separate AD site hoti hai?
21. Scalability ka simple matlab kya hai?
22. Password-reset delegation ke liye Domain Admin banana zaroori hai?
23. Kya Finance OU ka aik common password hota hai?
24. Password reset karne ki authority se Finance folder access milti hai?

### Jawabat aur short explanations

1. Attributes bhi rakhti hai; directory sirf pointer list nahin.
2. AD DS service hai; domain logical management daira; DC server hai jo service aur directory copy deta hai.
3. Nahin, dono apni local account authorities ke accounts hain.
4. Authentication: pehchan ki tasdeeq nahin hui.
5. Is simplified case mein authorization check karein; login aur resource permission alag hain.
6. Client ko suitable DC/domain services locate karne mein madad deta hai.
7. Key Distribution Center; Ticket Granting Ticket.
8. TGT se service ticket mangte hain; service ticket specific service ko present hota hai.
9. Nahin; valid tickets/session aur renewal conditions ke mutabiq system kaam kar sakta hai.
10. Department user object ka attribute ho sakta hai.
11. Nahin; group ko relevant resource permission assign karni hoti hai.
12. Nahin; OU placement folder permission nahin deta.
13. Ji haan; Sales add karne se Finance membership automatically nahin hatti.
14. Screen-lock policy; Finance folder Read permission.
15. Nahin; user/computer settings, scope aur filtering matter karte hain.
16. Nahin; domain password policy aur fine-grained password policies ke rules alag hain.
17. Nahin; tokens, sessions aur tickets ki wajah se delay/refresh ki zaroorat ho sakti hai.
18. Directory changes pohanchti hain; replication backup nahin.
19. Jab DC2 suitable/available ho aur DNS, network aur required information/services kaam kar rahe hon.
20. Nahin; subnet aur connectivity design matter karta hai.
21. Zaroorat ke mutabiq system capacity barhana.
22. Nahin; suitable OU scope mein specific authority delegate ki ja sakti hai.
23. Nahin; users ke apne accounts aur passwords hain.
24. Nahin; administrative delegation aur file-resource permissions alag hain.

### Aik end-to-end recap

Khalid PC1 par login karta hai → DNS suitable DC locate karne mein madad deta hai → Kerberos exchange se TGT milta hai → file service ke liye service ticket milta hai → file server relevant permissions check karta hai → Finance-Read ke applicable grant se Khalid permitted file parh sakta hai.

Finance OU administration aur policy organization mein madad karti hai. Replication account changes doosre DC tak le jati hai. Additional DC availability improve kar sakta hai. Delegation help desk ko specific kaam deti hai, har resource ki access nahin.

### Learning checkpoint — aage kahan se shuru karna hai?

Dono original files ki stopping points mukhtalif hain:

| Original record | Us waqt likha hua next topic |
| --- | --- |
| Bunyadi notes | AD DS Database aur Partitions |
| Revision notes | AD DS Purpose mein Scalability/Delegation ke baad Directory-Based Service for Network Resources |

Is merge se yeh assume nahin kiya gaya ke aap ne dono remaining topics complete kar liye. Current exact stopping point confirm hona baqi hai. Database file ka naam **NTDS.dit** hai; partitions ka detailed chapter is revision guide mein add nahin kiya gaya.

Aap pehle apna current class/page checkpoint identify karein; phir usi se continue karein. Original stopping notes [coverage map](MERGE_MAP.md) aur archive mein preserved hain.

**Documentation status:** merged study guide ready; practical evidence ya completed AD installation ka claim nahin.

[README par wapas](README.md) | [Source coverage aur changes](MERGE_MAP.md)
