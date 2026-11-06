# Active Directory Domain Services (AD DS) - Kitab Par Mabni Mukammal Study Notes

**Zaban:** Roman Urdu  
**Source:** Upload ki gayi lecture PDF, `09 Active Directory Domain Service (ADDS)(3).pdf`, 22 pages.  
**Daira-e-kaar:** Agenda ka har topic, asal maloomat wala har bullet, table ki har entry aur technical diagram ki wazahat neeche di gayi hai. Baar baar aane wali chapter-divider slides ko map kiya gaya hai, dobara tasveer ki surat mein nahin banaya gaya. Yeh tafseeli explanation hai; lafz-ba-lafz transcript ya completed lab report nahin.

**Istemaal honay walay labels:**

- **Kitab ka topic:** Lecture mein cover ki gayi maloomat.
- **Wazahat / durusti:** Aisi correction ya shart jo ghalat samajh se bachane ke liye zaroori hai.
- **Mazeed wazahat:** Lecture ke ilawa samajhne mein madad dene wali maloomat aur misalein.

## Fehrist

1. [Har page ki coverage](#1-har-page-ki-coverage)
2. [AD DS ki bunyaad](#2-ad-ds-ki-bunyaad)
3. [Database partitions aur paths](#3-database-partitions-aur-paths)
4. [Kerberos authentication](#4-kerberos-authentication)
5. [Directory objects](#5-directory-objects)
6. [AD DS ke maqasid](#6-ad-ds-ke-maqasid)
7. [Features aur un-ki-hudood](#7-features-aur-un-ki-hudood)
8. [Logical aur physical structure](#8-logical-aur-physical-structure)
9. [Tamam paanch FSMO roles](#9-tamam-paanch-fsmo-roles)
10. [Schema versions](#10-schema-versions)
11. [Installation ki zarooriyat](#11-installation-ki-zarooriyat)
12. [Tamam concepts ki mushtarka misal](#12-tamam-concepts-ki-mushtarka-misal)
13. [Mustaqbil ke lab ke liye read-only checks](#13-mustaqbil-ke-lab-ke-liye-read-only-checks)
14. [Corrections ka register](#14-corrections-ka-register)
15. [Revision sawalat aur jawab](#15-revision-sawalat-aur-jawab)
16. [Istilahaat ki fehrist](#16-istilahaat-ki-fehrist)
17. [Repo mein jagah aur learning status](#17-repo-mein-jagah-aur-learning-status)
18. [References](#18-references)

## 1. Har page ki coverage

Page numbers PDF mein page ki position ko zahir karte hain; ginti 1 se shuru hoti hai.

| PDF page(s) | Source mein kya hai? | Yahan kahan cover hai? |
| --- | --- | --- |
| 1 | Agenda: AD DS, database/partitions, authentication, objects, maqasid, features, structure, FSMO, schema versions, installation | Sections 2-11 |
| 2 | AD DS chapter divider aur management-console ki picture | Section 2; console tools section 13 mein |
| 3 | Definition, 2000 mein ta'aruf, KCC, aath functions | Section 2 |
| 4 | Database/partitions divider | Section 3 |
| 5 | NTDS.DIT; partition ki chaar categories; default paths; ADSI Edit | Section 3 |
| 6 | Authentication divider | Section 4 |
| 7 | Kerberos, DNS/AD/client diagram, AS/TGS/resource-server diagram | Section 4, diagram ki corrections ke saath |
| 8 | Objects divider | Section 5 |
| 9 | Definition, attributes aur tamam nau object categories | Section 5 |
| 10 | Maqsad ka divider | Section 6 |
| 11 | Tamam nau maqasid | Section 6 |
| 12 | Features divider | Section 7 |
| 13 | Tamam saat features | Section 7 |
| 14 | Structure divider | Section 8 |
| 15 | Domain, tree, forest, OU; site aur DC | Section 8 |
| 16 | FSMO divider aur sajawati shapes | Section 9; shapes mein mazeed technical instructions nahin |
| 17 | Forest-wide FSMO roles | Section 9 |
| 18 | Domain-wide FSMO roles aur transfer/seizure ka note | Section 9 |
| 19 | Schema-version divider | Section 10 |
| 20 | Schema definition aur poori historical version table; heading mein ghalti se FSMO likha hai | Section 10 |
| 21 | Installation-requirements divider | Section 11 |
| 22 | Tamam prerequisites, example IP, 250 MB/200 MB/50 MB figures | Section 11, purani sizing ki wazahat ke saath |

Repeated Windows Server labels, sajawati artwork aur repeated console pictures chapters ka ta'aruf karwati hain; yeh alag procedures add nahin kartin. Is illustrated edition mein lecture ki asal slides aur naye educational diagrams shamil hain. Asal slides ke alfaaz barqarar hain; unhen use karte waqt corrections zaroor dekhein. Naye diagrams conceptual misalein hain, completed lab ke screenshots nahin.

## 2. AD DS ki bunyaad

![AD DS overview](images/01_ADDS_Overview.png)

*Naya educational diagram; source ki tafseel IMAGES_AND_SOURCES.md mein hai.*

**Kitab ka topic - pages 2-3.**

### 2.1 AD DS kya hai?
Active Directory Domain Services (AD DS) Microsoft ki directory service hai jo [domain environment](md/ADDS_Domain_Environment.md) mein [identities](md/ADDS_Identities.md) aur [resources](md/ADDS_Resources.md) ko organize aur centrally manage karne mein madad karti hai.

Directory, [objects](md/ADDS_Objects_Attributes_and_Relationships_Roman_Urdu.md) aur un ke [attributes](md/ADDS_Objects_Attributes_and_Relationships_Roman_Urdu.md) ka ek munazzam collection hoti hai. AD DS users, computers aur groups jaise objects ke saath un ke [relationships](md/ADDS_Objects_Attributes_and_Relationships_Roman_Urdu.md) aur security information bhi store karta hai. Administrators is information ki madad se accounts, access aur resources ka central management karte hain.

**Domain Controller (DC)** woh Windows Server hai jisay AD DS ke zariye domain ki directory aur authentication services provide karne ke liye configure, yani promote, kiya gaya ho. Sirf AD DS role install karne se server Domain Controller nahin banta; promotion bhi mukammal karni hoti hai.

**Misal:** Khalid ka ek domain account hai. Woh us account se un domain computers par sign in kar sakta hai jahan usay ijazat ho. Administrator ko har computer par Khalid ka alag local account banane ki zaroorat nahin hoti.

<img src="images/Windows-Server-AD-DS.png" width="400">

### 2.2 History and hierarchy

Microsoft introduced Active Directory with **Windows 2000 Server**. Modern AD DS evolved from that directory platform.

AD DS is hierarchical because a [**forest**](md/ADDS_Forest.md) contains one or more **domains**, and [**organizational units (OUs)**](md/ADDS_Forest.md) can be created within domains to organize objects such as users, computers, and groups. OUs can also contain other OUs.

The phrase **“hierarchical directory database”** describes this structured organization of directory information. It does not mean a shared folder containing ordinary documents.

<img src="images/Active-Directory-history-hierarchy.png" width="400">

### 2.3 Kitab mein diye gaye tamam functions

| Kitab ka function | Wazahat | Misal ya zaroori shart |
| --- | --- | --- |
| User aur group management | Identities aur group memberships banana/manage karna | Khalid ko Finance access group mein add karna |
| Authentication aur authorization | Pehchan tasdeeq karna aur access decisions ke liye identity/group information dena | DC authenticate karta hai; file server permissions check karta hai |
| Centralized management | Mushtarka tools se bohat se objects manage karna | Active Directory Users and Computers |
| Domain services | Domain identities ki information rakhna aur logon/directory requests handle karna | DC domain authentication handle karta hai |
| Trusts | Domains/forests ke darmiyan authentication ka rasta dena | Trusted domain ke user ko access ke liye consider kiya ja sakta hai |
| Group Policy | GPOs se supported user/computer settings manage karna | Configured inactivity ke baad workstation lock karna |
| DNS integration | DNS records se DCs aur services locate karna | Clients service records query karke DC dhoondte hain |
| Replication | Mutalliq directory changes DCs ke darmiyan copy karna | Naya user doosray replicas par dastiyab ho jata hai |

**Wazahat / durusti - authorization:** AD DS woh component nahin jo seedha har folder kholta hai. Yeh identities aur security information deta hai. Resource server ya application apni permissions aur doosray rules evaluate karti hai. Authentication successful hona access ki guarantee nahin.

**Wazahat / durusti - trusts:** Trust apne aap har resource ki permission nahin deta. Trust ki direction, authentication restrictions aur resource permissions ab bhi aham hain.

**Wazahat / durusti - Group Policy:** GPOs sites, domains aur OUs se link ho sakte hain. Scope, inheritance aur filtering decide karte hain ke kaunsi settings apply hongi. Har GPO har computer par apply nahin hota.

### 2.4 KCC kya karta hai?
KCC = Knowledge Consistency Checker. Yeh AD DS ka built-in component hai jo replication [topology](/00_Networking_Foundations/Network_Topology/Network_Topology_Poster.png) banata aur maintain karta hai. Replication topology batati hai ke kaun se Domain Controllers (DCs) ek doosre se directory changes hasil karte hain.

KCC zaroorat ke mutabiq connection objects banata hai. Har connection object destination DC ko batata hai ke woh kis source DC se changes pull karega. Misal ke taur par, agar DC02 ke paas DC01 se inbound replication ka connection hai, to DC02, DC01 se changes pull karta hai. KCC connection banata hai; changes transfer karna AD DS replication ka kaam hai.

Multi-master replication: Bohat se aam directory updates kisi bhi writable DC par kiye ja sakte hain. Misal ke taur par, agar aap DC01 par kisi user ka department change karein, to woh update replication ke zariye doosre DCs tak pohanch sakta hai. RODC (Read-Only Domain Controller) directory ki read-only copy rakhta hai; directory updates us par originate nahin hote.

Replication foran hona zaroori nahin. DNS ya network connectivity ki problems replication mein rukawat daal sakti hain. Alag sites ke darmiyan site-link schedule bhi tay karta hai ke replication kab ho sakti hai.

Replication availability behtar karti hai, lekin backup ka badal nahin hai. Agar koi user account ghalti se delete ho jaye, to woh deletion bhi doosre DCs tak replicate ho sakti hai.

<img src="images/Windows-Server-KCC-replication.png" width="400">

## 3. Database partitions aur paths

![Database and partition categories](images/02_Database_Partitions.png)

*Naya educational diagram; source ki tafseel IMAGES_AND_SOURCES.md mein hai.*

**Kitab ka topic - pages 4-5.**

### 3.1 NTDS.DIT

**NTDS.DIT, Domain Controller par AD DS ki main database file hai.** Is mein users, computers, groups aur doosre directory objects ke attributes aur relationships store hote hain. AD DS ka local database **Microsoft Extensible Storage Engine (ESE)** use karta hai.

**NTDS** ka historical naam **Windows NT Directory Services** se related hai. **DIT = Directory Information Tree.** Study ke liye exact filename **`ntds.dit`** yaad rakhna zaroori hai.

**Default location:** `C:\Windows\NTDS\ntds.dit`

Domain Controller promotion ke waqt database ke liye doosra location bhi select kiya ja sakta hai.

**Yeh security-sensitive database hai.** Is mein directory information ke saath protected credential-related data bhi hota hai. Isay aam document ki tarah manually edit karna ya sirf file copy ko mukammal backup samajhna durust nahin.

Backup aur recovery ke liye **AD DS-aware backup tools aur supported procedures** use karne chahiye.

### 3.2 Directory partitions ki chaar categories — hamesha sirf chaar partitions nahin

**Directory partition**, jisay **naming context** bhi kehte hain, directory data ka ek logical hissa hai jiska apna replication scope hota hai. Yani us partition ka data un Domain Controllers (DCs) tak replicate hota hai jo us ki replica rakhte hain.

AD DS mein directory partitions ki chaar categories hain:

| Category | Bunyadi contents | Aam replication scope |
| --- | --- | --- |
| **Schema** | Object classes aur attributes ki definitions | Forest ke tamam DCs |
| **Configuration** | Forest ki configuration, sites, subnets, site links aur replication topology ki information | Forest ke tamam DCs |
| **Domain** | Kisi khaas domain ke objects, jaise users, computers, groups aur OUs | Usi domain ke tamam DCs |
| **Application** | Application-specific directory data, jaise AD-integrated DNS data | Us partition ki replica rakhne ke liye selected/enlisted DCs |

**Full replica aur writable replica ka farq:** Har DC apne domain ki full domain replica rakhta hai. Writable DC par updates kiye ja sakte hain. **Read-Only Domain Controller (RODC)** ki replica read-only hoti hai, aur passwords aur doosray secret attributes ki replication par restrictions hoti hain. Is liye “full replica” ka matlab yeh nahin ke har DC par tamam credentials maujood hain ya updates kiye ja sakte hain.

**Global Catalog ki wazahat:** Global Catalog (GC) apne domain ki full replica ke ilawa forest ke doosre domains ki **partial, read-only replicas** bhi rakhta hai. “Partial” ka matlab hai ke un domains ke objects ke sirf selected attributes rakhe jate hain. GC hona kisi RODC ko writable DC nahin banata.

**Chaar categories, exactly chaar partitions nahin:** Ek DC multiple application partitions host kar sakta hai. Misal ke taur par, AD-integrated DNS mein `DomainDnsZones` aur `ForestDnsZones` do alag application partitions ho sakte hain.

**Misal:** Ek single-domain DNS-server DC par schema, configuration aur domain partitions ke saath `DomainDnsZones` aur `ForestDnsZones` bhi hon, to woh paanch directory partitions host karta hai. Yeh sirf ek example hai; har DC ka partition count lazman paanch nahin hota.

**Yaad rakhein:** Yeh directory database ke logical partitions hain; disk ke `C:` ya `D:` jaise partitions nahin.

### 3.3 Schema partition — directory ke rules

**Schema partition** AD DS mein objects aur un ke attributes ki definitions rakhta hai. Yeh directory ka structure define karta hai.

Schema tay karta hai:

- **Object classes:** Kaun se types ke objects ban sakte hain, jaise user, computer ya group.
- **Attributes:** Har object class ke liye kaun se attributes allowed hain, jaise user ka display name aur department.
- **Required aur optional attributes:** Kaun se attributes lazmi hain aur kaun se ikhtiyari.
- **Structural rules:** Object classes ki inheritance aur directory mein un ki allowed placement ke rules.

**Misal:** User class ki definition batati hai ke user object ke kaun se attributes ho sakte hain. Khalid ka actual user object aur us ke department ki value domain partition mein store hoti hai.

**Lecture ki wazahat:** Schema ko “template” kehna samajhne mein madad de sakta hai. Lekin yeh kehna ke schema database ko “form format” mein display karta hai durust nahin. Schema allowed data structure define karta hai; graphical forms display karna management tools ka kaam hai.

**Har forest ka ek mushtarka schema hota hai**, jis ki replica forest ke tamam Domain Controllers par hoti hai.

Schema ko zaroorat ke mutabiq **extend** kiya ja sakta hai, jaise kisi application ke liye nayi object classes ya attributes add karna. Is ke liye munasib permissions aur supported procedures chahiye. Schema ki originating changes **Schema Master** DC par hoti hain aur phir forest ke doosre DCs tak replicate hoti hain.

**Yaad rakhein:** Schema extension forest-wide change hai. Isay planning aur testing ke saath karein; har administrator ko schema change karne ki permission nahin hoti.

### 3.4 Configuration partition — forest ki tarteeb

**Configuration partition** forest ki directory configuration store karta hai. Is mein yeh information shamil hoti hai:

- **Sites:** Network locations aur connectivity ko represent karne wali AD sites.
- **Subnet mappings:** IP subnets ka AD sites ke saath association.
- **Site links:** Sites ke darmiyan replication ke liye cost, schedule aur interval ki settings.
- **Server aur DC topology objects:** Server objects, NTDS Settings aur replication connection objects.
- **Naming-context references:** Forest ke directory partitions ki pehchan aur location se mutalliq references.

**Configuration partition ki replica forest ke tamam Domain Controllers par hoti hai**, taa ke woh forest ki mushtarka configuration use kar saken.

**Misal:** Chicago clients ke IP subnet ko **Chicago site** se map karne se clients ko apni AD site identify karne aur munasib, aam tor par local, Domain Controller dhoondne mein madad milti hai. Sites aur site-link settings, KCC ko intersite replication topology banane mein bhi madad deti hain.

**Yaad rakhein:** Subnet mapping client ki site pehchanne mein madad karti hai; site links replication ki planning ke liye connectivity aur timing model karte hain. Dono related hain, lekin ek hi setting nahin.

### 3.5 Domain partition — domain ke objects

**Domain partition** kisi khaas domain ke directory objects store karta hai. Is mein users, groups, computer accounts, OUs aur mutalliq objects shamil hote hain.

**Misal:** `ad.example.com` domain mein Khalid ka user account aur Finance OU, us domain ke domain partition mein store hote hain.

**Lecture ki wazahat:** Yeh kehna ke domain partition “doosray domains ke saath kabhi replicate nahin hota” mukammal taur par durust nahin. Full domain replication aur Global Catalog replication ka farq samajhna zaroori hai:

- **Apne domain ki full replica:** Har DC apne domain partition ki full replica rakhta hai. Writable DC updates accept karta hai; RODC ki replica read-only hoti hai aur secret attributes ki replication par restrictions hoti hain.
- **Doosray domains ki partial replicas:** Global Catalog (GC) forest ke doosray domains ke domain partitions ki partial, read-only replicas rakhta hai.
- **Partial ka matlab:** Doosray domains ke tamam objects shamil hote hain, lekin un ke sirf selected attributes rakhe jate hain.
- **GC ka apna domain:** Writable GC apne domain ki full, writable replica rakhta hai. RODC bhi GC ho sakta hai, lekin GC banne se writable nahin hota.
- **Domains alag rehte hain:** GC replication tamam domains ko ek mushtarka writable domain partition mein merge nahin karti.

**Yaad rakhein:** Domain partition, AD DS database ka sirf ek hissa hai. Schema aur configuration partitions bhi isi database ka hissa hain; DC additional application partitions bhi host kar sakta hai.

### 3.6 Application partitions

**Application directory partitions** Windows Server 2003 mein introduce huay thay. Yeh application-specific directory data store karte hain, jaise AD-integrated DNS zones aur records.

In ka faida yeh hai ke data ko poore forest ya domain ke har DC par replicate karna lazmi nahin. Replication un DCs tak mehdood rakhi ja sakti hai jo us partition ki replica rakhne ke liye **enlisted** hon.

**Enlisted** ka matlab hai ke DC us application partition ki replica rakhne aur us ki replication mein hissa lene ke liye configured hai.

AD-integrated DNS ki do aam application partitions hain:

| Application partition | Aam replication scope |
| --- | --- |
| **DomainDnsZones** | Usi domain ke DNS-server DCs jo is partition mein enlisted hon |
| **ForestDnsZones** | Forest ke DNS-server DCs jo is partition mein enlisted hon |

**Misal:** Agar DNS zone ka replication scope “all DNS servers running on domain controllers in this domain” select kiya jaye, to zone aam tor par `DomainDnsZones` mein store hota hai. Forest-wide DNS-server scope select karne par zone aam tor par `ForestDnsZones` mein store hota hai.

**Custom application partitions** bhi banaye ja sakte hain, taa ke application ki zaroorat ke mutabiq data aur replication scope manage kiya ja sake.

**Global Catalog ki wazahat:** Application partition ka data sirf GC honay ki wajah se Global Catalog mein shamil nahin hota. GC server application partition ki replica tab rakhta hai jab woh us partition mein enlisted bhi ho; yeh replica GC ke partial domain data se alag hoti hai.

Application partitions ko supported tools aur procedures se create aur remove kiya ja sakta hai.

**Yaad rakhein:** Poora application partition delete karne se us mein stored DNS ya doosra application data bhi remove ho sakta hai. Yeh sirf ek DC ko partition ki replica rakhne se hatane, yani **unenlist** karne, se alag operation hai. Partition deletion routine cleanup nahin; pehle dependencies, backups aur impact verify karein.

### 3.7 Default paths aur un ke contents

AD DS ki database, transaction logs aur SYSVOL ke functions alag hain. Neeche aam default locations di gayi hain; yeh assume karti hain ke Windows `C:\Windows` mein installed hai.

| Item | Aam default location ya tool | Maqsad |
| --- | --- | --- |
| **AD DS database** | `C:\Windows\NTDS\ntds.dit` | Directory objects, attributes aur mutalliq information store karti hai |
| **AD transaction logs** | `C:\Windows\NTDS\` | ESE database changes record karte hain aur consistency/recovery mein madad dete hain |
| **SYSVOL** | `C:\Windows\SYSVOL\` | Group Policy ke file components aur domain logon scripts jaisa replicated content rakhta hai |
| **ADSI Edit** | `adsiedit.msc` | Directory objects aur naming contexts ko view aur edit karne ka advanced management tool |

**ADSI Edit ki wazahat:** ADSI Edit database file ya storage location nahin hai. Yeh directory ko inspect aur modify karne ka management tool hai.

**Storage ki wazahat:** Transaction logs aur SYSVOL, `ntds.dit` ke andar directory partitions nahin hain. Yeh alag files aur folders hain.

#### GPO ke do components

Ek **Group Policy Object (GPO)** ke do related components hote hain:

1. **Group Policy Container (GPC):** AD DS ke domain partition mein stored directory component. Is mein GPO ki properties aur version information jaisi metadata hoti hai.
2. **Group Policy Template (GPT):** SYSVOL mein stored file component. Is mein policy settings ki files aur mutalliq content hota hai.

**Replication ka farq:** GPC, AD DS replication ke zariye replicate hota hai. Modern domains mein GPT aur doosra SYSVOL content **DFS Replication (DFSR)** ke zariye replicate hota hai. Yeh do alag replication mechanisms hain.

**Paths customize ho sakte hain:** Domain Controller promotion ke waqt database, transaction logs aur SYSVOL ke liye doosri supported locations select ki ja sakti hain. Is liye har DC par paths lazman defaults jaise nahin honge.

**Yaad rakhein:** Database files ko manually edit ya move na karein. ADSI Edit mein sirf samajh kar aur supported procedure ke mutabiq changes karein; ghalat changes directory aur applications ko mutasir kar sakte hain.

## 4. Kerberos authentication

![Corrected Kerberos ticket flow](images/03_Kerberos_Corrected.png)

*Naya educational diagram; source ki tafseel IMAGES_AND_SOURCES.md mein hai.*

**Kitab ka topic - pages 6-7.**

### 4.1 Kerberos ka kaam

**Kerberos**, AD DS domain environments mein primary authentication protocol hai. Yeh **tickets** ki madad se client ko kisi service ke samnay apni pehchan prove karne deta hai, baghair har service ko user ka password bheje.

**Misal:** Khalid domain account se sign in karta hai. Jab woh file server ki service access karta hai, to client us service ke liye Kerberos ticket hasil karke pesh kar sakta hai. File server ko Khalid ka password dene ki zaroorat nahin hoti.

#### KDC, AS aur TGS

AD DS mein **Key Distribution Center (KDC)** Domain Controller par chalta hai. Is ke do logical functions hain:

| Function | Poora naam | Bunyadi kaam |
| --- | --- | --- |
| **AS** | Authentication Service | Initial authentication exchange handle karke Ticket-Granting Ticket (TGT) issue karta hai |
| **TGS** | Ticket-Granting Service | TGT ki bunyaad par kisi khaas service ke liye service ticket issue karta hai |

**Lecture ki wazahat:** Diagram mein “Authentication Server” aur “Ticket Granting Server” likha ho sakta hai. AD DS mein inhen KDC ke logical functions samjhein; in ke liye do alag Windows servers lazmi nahin.

Diagram mein **“Real Server”** se murad woh server hai jis ki service client access karna chahta hai. Isay **target server**, **resource server** ya **application server** kehna zyada clear hai.

**Yaad rakhein:** Kerberos authentication pehchan verify karti hai. Resource tak access milna us resource ki permissions aur authorization checks par bhi depend karta hai. Har Windows sign-in ya connection lazman Kerberos use nahin karta.

### 4.2 Kerberos authentication mein components ke roles

Har component ka kaam alag hai. DNS discovery mein madad karta hai, KDC tickets issue karta hai, aur target service authentication ke baad access permissions evaluate karti hai.

| Component | Kaam |
| --- | --- |
| **Client** | User ya kisi doosray security principal ki taraf se tickets request karta hai aur target service ko service ticket pesh karta hai |
| **DNS** | Domain Controllers ko locate karne aur target server ke naam ko IP address mein resolve karne mein madad karta hai |
| **KDC: Authentication Service (AS)** | Initial authentication exchange handle karta hai aur successful authentication par Ticket-Granting Ticket (TGT) aur client–TGS session key provide karta hai |
| **KDC: Ticket-Granting Service (TGS)** | Valid TGT aur mutalliq authenticator ki bunyaad par requested service ke liye service ticket aur client–service session key provide karta hai |
| **Target service** | Service ticket aur mutalliq authenticator validate karke client ki pehchan verify karti hai |
| **Resource/application authorization** | Authenticated identity, group information, permissions aur applicable rules ki bunyaad par decide karti hai ke kaun se operations allowed hain |

**Security principal** aisi identity hoti hai jisay authenticate kiya ja sakta hai aur permissions di ja sakti hain, jaise user, computer ya service account.

**DNS ki wazahat:** DNS ticket issue nahin karta aur user authenticate nahin karta. Yeh client ko munasib DC aur target server dhoondne mein madad deta hai.

**Target service ki wazahat:** Sirf ticket receive karna kaafi nahin; service ticket ke saath mutalliq authenticator bhi validate karti hai. Yeh checks client ko apni pehchan prove karne mein madad dete hain.

**Yaad rakhein:** Authentication ka sawal hai, “Aap kaun hain?” Authorization ka sawal hai, “Aap ko kya karne ki ijazat hai?” Successful authentication har resource ki permission nahin deti.

### 4.3 Password-based authentication ki misal — step-by-step

Yeh misal assume karti hai ke Khalid online domain sign-in kar raha hai, munasib DC available hai aur file service Kerberos support karti hai.

1. **Sign-in shuru hota hai:** Khalid domain-joined computer par apna domain username aur password enter karta hai.

2. **DC locate hota hai:** Client DNS aur domain-discovery information ki madad se munasib Domain Controller dhoondta hai.

3. **Initial authentication hoti hai:** Client, DC ke KDC ki **Authentication Service (AS)** ke saath exchange karta hai. Aam password-based pre-authentication mein client password se derive ki gayi key ka ilm prove karta hai; plain-text password DC ko nahin bhejta.

4. **TGT milta hai:** Successful authentication par KDC client ko **Ticket-Granting Ticket (TGT)** aur protected response mein **client–TGS session key** provide karta hai. Client TGT ke encrypted contents khud nahin parhta; usay baad mein TGS ko pesh karta hai.

5. **Service ticket request hoti hai:** File service access karne ke liye client, KDC ki **Ticket-Granting Service (TGS)** ko TGT aur mutalliq authenticator pesh karta hai. Request mein **Service Principal Name (SPN)** target service ki pehchan karta hai.

6. **Service ticket milta hai:** Request validate honay par KDC, requested service ke liye **service ticket** aur protected response mein **client–service session key** provide karta hai.

7. **Client service se connect karta hai:** Client, file service ko service ticket aur client–service session key se protected naya authenticator pesh karta hai.

8. **Service identity verify karti hai:** File service ticket aur authenticator validate karke client ki pehchan verify karti hai. Mutual authentication request honay par service bhi client ko apni pehchan ka saboot deti hai.

9. **Permissions check hoti hain:** File server authenticated identity, group information, share permissions, NTFS permissions aur doosray applicable rules evaluate karta hai.

10. **Access allow ya deny hota hai:** Khalid sirf woh operations kar sakta hai jin ki authorization checks ijazat deti hain. Successful Kerberos authentication har file ki access guarantee nahin karti.

**Yaad rakhein:** TGT, service tickets hasil karne ke liye hota hai. Service ticket, kisi khaas service ke samnay authenticate karne ke liye hota hai.

**Model ki had:** Yeh beginner explanation hai; har Kerberos message ki poori technical tafseel nahin. Cached logon, pehle se available tickets, smart cards aur Windows Hello for Business jaisay mechanisms initial authentication ka flow badal sakte hain.

### 4.4 TGT aur service ticket ka farq

Kerberos mein **Ticket-Granting Ticket (TGT)** aur **service ticket** ke functions alag hain:

| Ticket | Kis liye istemal hota hai? | Kis ko pesh kiya jata hai? |
| --- | --- | --- |
| **TGT** | KDC ki Ticket-Granting Service se service tickets hasil karne ke liye | **TGS** ko |
| **Service ticket** | Kisi khaas service ke samnay authenticate karne ke liye | **Target service**, jaise file service, ko |

**Misal:** Khalid ka client file service ke liye ticket hasil karne ko TGT, TGS ke samnay pesh karta hai. Phir milne wala service ticket file service ko pesh karta hai. File service ko TGT nahin diya jata.

**Ticket akela kaafi nahin:** Client ticket ke saath mutalliq session key se protected **authenticator** bhi pesh karta hai, jisay receiving service validate karti hai.

**Ticket unlimited permission nahin deta.** Ticket ki validity, intended service ki identity aur authorization information aham hain. Authentication successful honay ke baad bhi resource server permissions aur applicable rules check karta hai.

**Yaad rakhein:** TGT se service ticket milta hai; service ticket se target service par authentication hoti hai; resource permissions tay karti hain ke kya access allowed hai.

### 4.5 Source diagram ki durusti

Lecture ke diagram mein authentication server aur resource server, dono ke neeche `NTDS.DIT` ya domain-partition ke labels dikhaye gaye hain. Is se yeh ghalat fehmi ho sakti hai ke har resource server apni local AD DS database rakhta hai.

**Aam member file/application server ko local AD DS `ntds.dit` database ki zaroorat nahin hoti.** Domain ki directory aur account information, Domain Controllers par store hoti hai.

Kerberos authentication mein target service:

- Apne service account se associated key ki madad se service ticket ke encrypted contents validate karti hai.
- Ticket se hasil honay wali client–service session key ki madad se accompanying authenticator validate karti hai.
- Authentication ke baad permissions aur applicable authorization rules evaluate karti hai.

**Zaroori farq:** Har service-ticket validation ke liye poori AD database resource server par copy nahin hoti. Kuch scenarios mein additional validation ya doosray operations ke liye resource server ko DC se communication ki zaroorat ho sakti hai.

**Exception:** Agar koi server DC bhi ho aur resource service bhi host karta ho, to us par AD DS database ho sakti hai. Lekin aam member resource server ko DC samajhna durust nahin.

#### Diagram mein DNS aur network components

Diagram mein DNS, AD, switch aur multiple clients discovery aur network connectivity ko samjhate hain.

- **DNS:** Munasib DC locate karne aur server names ko IP addresses mein resolve karne mein madad karta hai.
- **DC/KDC:** Kerberos authentication exchanges handle karta aur tickets issue karta hai.
- **Switch:** Network devices ke darmiyan traffic forward karta hai.
- **Client:** DC/KDC aur target service se zaroorat ke mutabiq communicate karta hai.

**Yaad rakhein:** DNS authentication traffic ka gateway nahin hai. Naam resolve honay ke baad client, DC/KDC ya target service se seedha network communication kar sakta hai; har login packet DNS server se nahin guzarta.

### 4.6 Dependencies aur exceptions

Kerberos authentication ke liye sirf domain account hona kaafi nahin. DNS, time synchronization, service identities aur network connectivity bhi sahi honi chahiye.

| Dependency | Kyun zaroori hai? |
| --- | --- |
| **DNS configuration** | Client ko munasib Domain Controller locate karne aur target server ka naam resolve karne mein madad deti hai |
| **Time synchronization** | Client, DC aur target server ke clocks ka farq allowed clock-skew limit ke andar hona chahiye |
| **Service Principal Names (SPNs)** | Requested service ko sahi service account ke saath identify karte hain; missing, duplicate ya ghalat SPNs authentication problems paida kar sakte hain |
| **Network aur firewall rules** | DNS, Kerberos aur required application traffic ke liye zaroori connectivity allow karte hain |

**Time ki wazahat:** Clocks ka bilkul ek second par hona lazmi nahin, lekin un ka farq configured tolerance ke andar hona chahiye. Sirf timezone display mukhtalif honay ka matlab yeh nahin ke actual time synchronization ghalat hai.

#### Exceptions aur zaroori farq

- **Har Windows sign-in Kerberos nahin hota:** Kuch scenarios NTLM, local-account authentication ya doosray mechanisms use karte hain. Successful sign-in akela Kerberos use honay ka saboot nahin.
- **NTLM fallback guaranteed nahin:** Kerberos fail honay par connection lazman NTLM par switch nahin karta. Application, policy aur security configuration fallback ko restrict kar sakti hain.
- **Cached interactive logon:** Agar pehle ke domain sign-in ki information locally cached ho aur policy ijazat de, to DC unavailable honay par bhi user computer par sign in kar sakta hai. Yeh live DC authentication nahin aur tamam domain resources ki access ki guarantee bhi nahin.
- **Single sign-on (SSO):** Supported services ke liye baar baar credentials enter karne ki zaroorat kam karta hai. Har application lazman SSO support nahin karti.
- **Authorization ab bhi zaroori hai:** SSO ya successful Kerberos authentication resource permissions ko bypass nahin karti. Har resource apne applicable access rules evaluate karta hai.

**Yaad rakhein:** Successful sign-in, successful Kerberos authentication aur kisi resource ki access — yeh teen related lekin alag outcomes hain.

## 5. Directory objects

![OU versus security group versus GPO](images/04_OU_Group_GPO.png)

*Naya educational diagram; source ki tafseel IMAGES_AND_SOURCES.md mein hai.*

**Kitab ka topic - pages 8-9.**

**Object** directory ki identifiable entry hai. **Attribute** usay describe karta hai.

User ke example attributes display name, sign-in name aur department ho sakte hain. Object ki directory mein identity aur location hoti hai.

Source mein yeh tamam categories hain:

| Source category | Wazahat aur zaroori farq |
| --- | --- |
| Users | Sign-in aur access control ki user identities |
| Groups | Security permissions ya distribution ke liye collections |
| Computers | Joined machines ke computer accounts; insan users se alag |
| Domain controllers | DC computer accounts aur mutalliq server/NTDS Settings aur doosray directory objects |
| Organizational Units | Domain ke andar organization, GPO scope aur delegation ke containers |
| Shared folders | Published directory entries network shares describe kar sakti hain; asal files storage/file servers par hoti hain |
| Printers | Published print-queue objects printer discovery mein madad karte hain; physical printer AD mein store nahin hota |
| Sites | Network locality ko represent karne walay configuration objects |
| Group Policy | GPO directory objects aur mutalliq SYSVOL content; tamam content NTDS.DIT mein nahin |

### 5.1 OU aur group ka farq

**Organizational Unit (OU)** aur **security group** ke functions alag hain. OU directory objects ko organize karne, administration delegate karne aur Group Policy apply karne ka scope banane mein madad karti hai. Security group identities ko jama karke permissions assign karne mein madad karta hai.

| Pehlu | Organizational Unit (OU) | Security group |
| --- | --- | --- |
| **Bunyadi maqsad** | Objects ki organization, administrative delegation aur Group Policy scope | Permissions aur user rights assign karne ke liye identities ko group karna |
| **Objects se taalluq** | Object directory mein kisi ek parent container ya OU ke andar hota hai | User ya computer multiple groups ka member ho sakta hai |
| **Group Policy** | GPO ko OU se directly link kiya ja sakta hai | GPO directly group se link nahin hota; security group filtering mein use ho sakta hai |
| **Resource permissions** | Sirf OU mein honay se file-share access nahin milta | Security group ko file-share aur NTFS permissions di ja sakti hain |

**Misal:** Khalid ka user object `Finance` OU mein hai. Us OU par linked GPO us ke user account par apply ho sakta hai, agar scope aur filtering ijazat dein. Finance files ki access dene ke liye Khalid ko `Finance-Share-Read` security group mein add kiya ja sakta hai, jisay required permissions di gayi hon.

**Ek parent, multiple memberships:** Khalid ka user object ek waqt mein ek parent container ya OU mein hota hai, lekin woh multiple security groups ka member ho sakta hai.

**OU move aur permissions:** User ko Finance OU mein move karne se file-share permissions khud-ba-khud nahin miltin. Move se applicable GPOs aur inherited directory permissions badal sakti hain; resource access alag evaluate hoti hai.

**Default Users container:** AD DS ka default `Users` container, OU nahin hota. Is se GPO directly link nahin kiya ja sakta. Lekin is ke andar objects par domain ya site se inherited applicable policies phir bhi asar kar sakti hain.

**Yaad rakhein:** OU batati hai ke object directory mein kahan organized hai; security-group membership permission assignment mein madad karti hai.

### 5.2 AD mein istemal honay walay naam aur identifiers

AD DS mein ek account ko mukhtalif names aur identifiers se represent kiya ja sakta hai. Har format ka maqsad alag hai.

| Naam / identifier | Misal | Matlab |
| --- | --- | --- |
| **UPN (User Principal Name)** | `khalid@ad.example.com` | User ka sign-in name; email jaisa format honay ka matlab mailbox hona nahin |
| **Down-level logon name** | `LAB\khalid` | NetBIOS domain name aur user ka `sAMAccountName` |
| **Distinguished Name (DN)** | `CN=Khalid,OU=Finance,DC=ad,DC=example,DC=com` | Directory hierarchy mein object ka poora naam aur location |
| **SID (Security Identifier)** | `S-1-5-21-111111111-222222222-333333333-1105` | Windows access control mein security principal ki pehchan; yeh farzi example hai |

#### UPN aur down-level logon

`khalid@ad.example.com` aur `LAB\khalid` ek hi user account ke do sign-in formats ho sakte hain.

- **UPN:** Username aur UPN suffix par mushtamil hota hai. UPN suffix domain ke DNS naam se mukhtalif bhi configure kiya ja sakta hai.
- **Down-level logon:** `DOMAIN\username` format use karta hai. Is misal mein `LAB` domain ka NetBIOS naam aur `khalid` account ka `sAMAccountName` hai.

**Yaad rakhein:** UPN ka email jaisa nazar aana yeh sabit nahin karta ke account ke paas email mailbox bhi hai.

#### Distinguished Name ko samjhein

`CN=Khalid,OU=Finance,DC=ad,DC=example,DC=com` mein:

- **CN=Khalid:** Object ka Common Name.
- **OU=Finance:** Parent Organizational Unit.
- **DC=ad,DC=example,DC=com:** Domain name `ad.example.com` ke components.

**DN mein `DC` ka matlab Domain Component hai**, Domain Controller server nahin.

Object ko rename ya doosri OU mein move karne se us ka DN badal sakta hai.

#### SID ko samjhein

Domain account ka SID aam tor par **domain SID + Relative Identifier (RID)** se banta hai. RID us security principal ko domain ke andar unique banata hai.

User ka naam badalne ya usay usi domain ki doosri OU mein move karne se us ka SID aam tor par nahin badalta. Lekin account delete karke usi naam se naya account banaya jaye, to naye account ka SID alag hota hai.

**Yaad rakhein:** Sign-in names account tak pohanchne ke liye hain, DN directory mein us ki location batata hai, aur SID Windows permissions mein us ki security identity hoti hai.

## 6. AD DS ke maqasid

**Kitab ka topic - pages 10-11. Tamam nau maqasid cover hain.**

| Maqsad | Matlab | Misal |
| --- | --- | --- |
| Centralized authentication aur authorization support | Mushtarka identities aur group information | Domain sign-in ke baad file-server permission checks |
| User aur resource management | Directory entries aur relationships maintain karna | Job chhorne walay employee ka account disable karna |
| Security aur access control | Identities, permissions aur policies use karna | Finance share ko approved groups tak mehdood rakhna |
| Scalability aur delegation | Directory ko barhana aur mehdood administration taqseem karna | Help desk ko ek OU mein password resets ki ijazat |
| Resources ke liye directory-based service | Directory se information discover karna | Published printer ya application directory entry dhoondna |
| Trust relationships aur federation | Munasib mechanisms se identity environments jorna | Cross-domain authentication ya alag federation service |
| High availability ke liye replication | Directory replicas maintain karna | Outage mein doosra DC munasib requests ka jawab de |
| Cloud aur hybrid support | Additional components se cloud identity/services ke saath integration | Selected identities Microsoft Entra ID se synchronize karna |
| Applications ke liye directory services | Applications ko directory identities/information query ya use karne dena | LDAP-aware business application |

**Wazahat / durusti - federation:** AD DS trusts aur federation ek hi cheez ke do naam nahin. AD FS alag role/service hai. Federation aam tor par identity systems ke darmiyan trust aur tokens/assertions use karti hai.

**Wazahat / durusti - cloud:** Microsoft Entra ID sirf NTDS.DIT ka cloud par hosted writable replica nahin. Hybrid integration ko design aur munasib tools chahiye.

**Wazahat / durusti - delegation:** Sirf task ke liye darkar permissions dein. Finance mein password resets delegate karna Domain Admin rights dene ke barabar nahin.

## 7. Features aur un-ki-hudood

**Kitab ka topic - pages 12-13. Tamam saat features samjhaye gaye hain.**

### 7.1 Group Policy se administration

GPOs supported settings, scripts aur kuch software deployment mechanisms ko centralize karte hain. Windows Installer package deployment ek misal hai; GPO har application-management system ka universal replacement nahin.

Computer/user clients applicable policy process karte hain. GPO ki location, filters aur precedence result par asar dalte hain.

### 7.2 DC ya domain ka naam badalna

Lecture dono ko mumkin features mein shamil karta hai. Yeh alag operations hain jinki prerequisites aur application limitations mukhtalif hain.

Dependencies check kiye baghair DC rename ko aam standalone PC rename jaisa na samjhein. Domain rename ek specialized project hai; environment ki applications isay support na bhi karti hon. Plan aur validate karein; isay routine naam ki safai na samjhein.

### 7.3 Kerberos ke saath integrated security

Kerberos domain authentication aur single sign-on support karta hai. Permissions, secure configuration aur doosray controls ab bhi chahiye; Kerberos ki mojoodgi poora environment khud-ba-khud secure nahin banati.

### 7.4 AD DS install ya uninstall karna

Server ko promote karne se DC banta hai; supported demotion us ki DC wali haisiyat remove karti hai. Demotion planning mein DNS, replication, role holders aur baqi DCs ka hona shamil hai.

Aakhri DC remove karne se domain bhi remove ho sakta hai. DC VM delete karna supported demotion ke barabar nahin.

### 7.5 Cross-forest trust

Prerequisites aur policy ki ijazat ho to forest trust forests ke darmiyan authentication ka rasta de sakta hai. Trust merger, database replication relationship ya khud-ba-khud file permission nahin.

### 7.6 Site-to-site replication

Lecture site-to-site replication ko faster kehta hai.

**Wazahat / durusti:** Intersite replication link cost, schedule aur bandwidth ko madde nazar rakh kar design hoti hai; yeh fitratan intrasite replication se tez nahin. Achhi connectivity wali site ke andar change notification aam tor par jaldi convergence mein madad karti hai. Asal timing configuration aur connectivity par depend karti hai.

### 7.7 Extensible schema

Nayi classes aur attributes applications ki zarooriyat support kar sakti hain.

**Wazahat / durusti:** Schema extensions forest-wide changes hain. Schema class/attribute definitions aam objects nahin jinhen baad mein azadi se delete kar dein. Supported definitions ko kuch halaat mein restrictions ke saath defunct mark kiya ja sakta hai. Test karein, permissions aur backups plan karein aur supported application/vendor procedures use karein.

## 8. Logical aur physical structure

![Logical and physical structure](images/05_Logical_Physical.png)

*Naya educational diagram; source ki tafseel IMAGES_AND_SOURCES.md mein hai.*

**Kitab ka topic - pages 14-15.**

### 8.1 Logical components

| Component | Matlab | Misal |
| --- | --- | --- |
| Domain | Forest mein directory partition aur administrative/replication unit | `ad.example.com` |
| Tree | Ek ya zyada domains jinka DNS namespace contiguous ho | `example.com` aur `hr.example.com` |
| Forest | Ek ya zyada domains jo schema, configuration aur forest-wide relationships share karte hain | Forest mein multiple trees ho sakte hain |
| OU | Domain ke andar organization/delegation/GPO container | Finance OU |

Forest mein sirf ek domain bhi ho sakta hai. Ek forest ke mukhtalif trees alag DNS namespaces use kar sakte hain.

**Mazeed wazahat:** Forest AD ki bunyadi security boundary hai. Domain administrative/replication boundary hai, lekin usi forest ke doosray hissay mein bohat privileged administrators se mukammal security isolation nahin.

### 8.2 DNS namespace

Namespace naamon ko hierarchy mein organize karta hai. `dc01.ad.example.com` mein:

- `com` top-level domain hai.
- `example.com` domain hai.
- `ad.example.com` subdomain hai.
- Is misal mein `dc01` host label hai.

Sirf DNS domain name hona AD domain ke wajood ka saboot nahin. DNS aur AD mutalliq lekin mukhtalif systems hain.

### 8.3 Physical components

| Component | Matlab |
| --- | --- |
| Site | IP subnets ka group jinhen AD locality/topology ke liye achhi connectivity wala samjha jata hai |
| Subnet | Network prefix jo site se map ho |
| Site link | Replication connectivity/cost/schedule model karne wala logical relationship |
| DC | Directory replicas aur domain services host karne wala server |

Site aksar location se milti hai, lekin sirf shehar ka label nahin. Sahi subnet mapping aham hai. Ek domain multiple sites mein phail sakta hai; ek site mein multiple domains ke DCs ho sakte hain.

**Mazeed wazahat:** Logical structure ka sawal hai “Identities kis tarah organize hain?” Physical structure ka sawal hai “Services aur network connections kahan hain?”

## 9. Tamam paanch FSMO roles

![FSMO roles and scopes](images/06_FSMO_Roles.png)

*Naya educational diagram; source ki tafseel IMAGES_AND_SOURCES.md mein hai.*

**Kitab ka topic - pages 16-18.**

**FSMO = Flexible Single Master Operations.**

AD DS aam tor par multi-master hai, lekin conflicts se bachne ke liye kuch khaas operations ek designated role holder karta hai. Ek DC multiple FSMO roles rakh sakta hai.

| Scope | Role | Bunyadi zimmedari |
| --- | --- | --- |
| Forest | Schema Master | Forest schema ki originating changes |
| Forest | Domain Naming Master | Domain/naming-context namespace changes control karna |
| Domain | RID Master | DCs ko RID pools allocate karna |
| Domain | PDC Emulator | Password/lockout coordination, time hierarchy aur preferred administrative functions |
| Domain | Infrastructure Master | Jahan applicable ho cross-domain object references maintain karna |

Har forest ke do roles aur har domain ke teen roles ka matlab **2 + (3 x domains ki tadaad)** role instances hai. Teen-domain forest mein 11 instances honge; lazman 11 alag servers nahin.

### 9.1 Schema Master

Har forest mein ek. Schema updates is ke DC par originate hote hain aur phir replicate hote hain.

Source schema classes/attributes delete karna bhi likhta hai. Yeh misleading hai: classSchema/attributeSchema definitions ki normal deletion allowed nahin. Definitions add/modify karna aur supported defunct handling alag operations hain.

### 9.2 Domain Naming Master

Har forest mein ek. Domains aur application naming contexts add/remove karne jaisi changes coordinate karta hai aur namespace references consistent rakhta hai.

Lecture domain rename ka zikr karta hai. Role ka available hona kisi supported rename workflow ka sirf ek hissa hai; role akela rename ko safe ya supported nahin banata.

### 9.3 RID Master

Har domain mein ek. Writable DCs ko Relative Identifiers ke pools allocate karta hai. Security principals create karte waqt DC apne available pool se RID use karta hai.

**SID ka concept:** Domain SID + RID se principal ki domain SID identity banti hai.

RID Master ko har user-creation request ke liye khud naya RID dena zaroori nahin. Agar yeh unavailable ho to DCs apne usable pools baqi rehne tak principals create karte reh sakte hain.

### 9.4 PDC Emulator

Har domain mein ek. Is ke mutalliq functions mein shamil hain:

- Password updates preferential taur par receive karna.
- Jab doosray DC ke paas purani information ho sakti ho to failed-password authentication attempts resolve karne mein madad.
- Account-lockout processing.
- Windows domain time synchronization mein khaas jagah.
- Group Policy management jaisay tools ke liye preferred administrative target.
- Windows NT ke PDC expectations ke saath historical compatibility.

**Wazahat / durusti:**

- Doosray writable DCs bhi aam password changes accept kar sakte hain.
- PDC akela users authenticate karne wala DC nahin.
- Clients Group Policy process karte hain; PDC khud har client ki settings apply nahin karta.
- Forest-root PDC normal domain time hierarchy ke top par hota hai; is ke liye munasib reliable upstream time source configure karein. Har machine ko seedha usi se query karna zaroori nahin.
- NT 4.0 compatibility tareekh samjhati hai; obsolete clients deploy karne ki recommendation nahin.

### 9.5 Infrastructure Master

Har domain mein ek. Doosray domains ke objects ke references maintain karta hai, jaise changes ke baad cross-domain group membership references.

Is ka practical workload forest design, GC placement aur AD Recycle Bin jaisay features par depend karta hai. Yeh tamam objects replicate karne ya har user ke attributes update karne wala role nahin.

### 9.6 Transfer aur seizure ka farq

Lecture ntdsutil aur GUI tools ka zikr karta hai.

| Operation | Munasib surat |
| --- | --- |
| Transfer | Mojooda role holder available ho; planned move |
| Seizure | Zaroori recovery situation mein role holder recover na ho sake; recovery procedure |

Har role ke GUI tools alag hain: domain roles ke liye AD Users and Computers, Domain Naming Master ke liye AD Domains and Trusts aur Schema Master ke liye Schema snap-in. PowerShell aur ntdsutil bhi supported tools hain.

Seizure chhoti outage ka routine failover nahin. Purana role holder wapas lane se pehle recovery guidance follow karein. Is document mein jaan-boojh kar role-seizure command nahin di gayi.

Role unavailable honay se lazman foran tamam logins band nahin hote. Asar role, cached/pool information, topology aur outage ke duration par depend karta hai.

## 10. Schema versions

**Kitab ka topic - pages 19-20.**

Page 20 ki heading FSMO roles hai, lekin andar schema versions ki maloomat hai.

Schema version, schema naming context ke **objectVersion** attribute mein record hota hai. Yeh schema level batata hai, har DC ka OS nahin.

### 10.1 Source ki poori table, saaf tarteeb mein

| Source mein Windows Server release | Schema objectVersion |
| --- | --- |
| Windows Server 2025 | 91 |
| Windows Server 2022 | 88 |
| Windows Server 2019 | 88 |
| Windows Server 2016 | 87 |
| Windows Server 2012 R2 | 69 |
| Windows Server 2012 | 56 |
| Windows Server 2008 R2 | 47 |
| Windows Server 2008 RTM | 44 |
| Windows Server 2003 R2 | 31 |
| Windows Server 2003 RTM, SP1 aur SP2 | 30 |
| Windows 2000 Server | 13 |

Yeh historical mappings hain; unsupported versions deploy karne ki recommendation nahin.

### 10.2 Version ke teen alag concepts

| Concept | Kya batata hai? |
| --- | --- |
| DC operating-system version | Kisi khaas DC par installed Windows release |
| Schema version | Forest-wide class/attribute schema level |
| Domain/forest functional level | AD capabilities aur allowed DC OS combinations |

Jahan compatibility rules ijazat dein, naya schema puranay supported DCs ke saath coexist kar sakta hai. “Schema 91” ka matlab nahin ke har DC Windows Server 2025 hai.

Windows Server 2019 aur 2022 ka newest functional level Windows Server 2016 hai. Windows Server 2025, 2025 functional level introduce karta hai. Level actual DC compatibility matrix se choose karein, schema table se nahin.

## 11. Installation ki zarooriyat

![AD DS preparation checklist](images/07_Preparation.png)

*Naya educational diagram; source ki tafseel IMAGES_AND_SOURCES.md mein hai.*

**Kitab ka topic - pages 21-22. Har listed prerequisite cover hai.**

| Source prerequisite | Wazahat |
| --- | --- |
| Windows Server OS | DC role ke liye supported Windows Server release use karein |
| Administrator rights | Credentials ki zaroorat new forest, new domain ya additional DC par depend karti hai |
| Static IP, misal 192.168.0.111/24 | Stable addressing aur sahi network configuration dein |
| Unique domain name | Munasib DNS namespace plan karein aur naming conflicts se bachein |
| DNS server | AD ki name/service discovery ensure karein; DNS promotion ke dauran install ho sakta hai |
| DSRM password | Protected Directory Services Restore Mode recovery password set karein |
| Forest functional level | Tamam relevant DCs aur design ke saath compatible level choose karein |
| Domain functional level | Compatible domain capability level choose karein |
| 250 MB minimum: 200 MB database + 50 MB logs | Purana figure; modern Windows Server DC ka valid total size nahin |

### 11.1 OS aur hardware sizing

**Wazahat / durusti:** 250 MB modern server installation ya realistic DC ke liye kaafi nahin. OS, updates, paging, directory database, transaction logs, SYSVOL, monitoring aur growth ka hisaab rakhein. Current Windows Server documentation mein OS storage minimum gigabytes mein hai; minimum installation requirements production recommendations nahin.

Exact OS aur deployment option ki current requirements use karein. Chhotay learning host par modest isolated lab se shuru karein aur host ki capacity se zyada VMs na chalayein. Ek universal CPU/RAM/disk allocation har AD deployment ke liye theek nahin.

### 11.2 Permissions

Naya forest banane ke liye munasib local administrative credentials chahiye. Domain ya DC add karne ki domain/forest permissions alag hain; schema preparation ke liye mazeed privileges chahiye ho sakte hain.

Local Administrator, Domain Admin aur Enterprise Admin ko ek na samjhein. In ka scope alag hai.

### 11.3 Addressing

Source ka address `192.168.0.111/24` IP `192.168.0.111` aur subnet mask `255.255.255.0` ko zahir karta hai.

Yeh misal hai, apne network par lazman yahi address lagane ki instruction nahin. IP, prefix, jahan darkar ho gateway, DNS settings aur subnet reachability plan karein.

### 11.4 DNS aur naming

Clients aur DCs ko aisi DNS services use karni chahiye jo AD namespace aur service records resolve kar saken. Sirf public DNS aam tor par private AD records resolve nahin kar sakta. Munasib forwarding se external resolution di ja sakti hai.

Naye lab ke liye apne control ka namespace use karein; documentation mein munasib reserved example namespace use karein. Learner ke puranay lab mein VCPLAB.com tha; yeh note us public domain ki ownership establish nahin karta.

### 11.5 DSRM

Directory Services Restore Mode, AD DS maintenance/recovery ka recovery mode hai. Is ka password normal domain-account use se alag hai. Isay mehfooz record karein; public repo ya command history mein passwords na rakhein.

### 11.6 Functional levels

DC compatibility aur required features ki bunyaad par choose karein. Domain functional level, forest functional level se neeche nahin ho sakta.

Existing domain mein Windows Server 2025 DCs ke liye Microsoft kam az kam Windows Server 2016 functional level requirement document karta hai. 2025 level tak raise karne ke liye affected scope ke tamam DCs compatible hon. Andaza lagane ke bajaye plan aur verify karein.

### 11.7 Mazeed preparation checklist

**Mazeed wazahat:**

1. Server identity, hostname aur intended role confirm karein.
2. Network, DNS aur time verify karein.
3. Plan karein ke naya forest, child domain ya additional DC banana hai.
4. Supported volumes par database/log/SYSVOL storage plan karein.
5. Updates aur required security configuration apply karein.
6. Zaroori role rules ke saath firewall protection enabled rakhein.
7. Availability zaroori ho to doosra DC aur independent failure domains plan karein.
8. Supported system-state/forest recovery backups plan karein aur recovery test karein.
9. Lab ko existing production domain se isolated rakhein.
10. Promotion se pehle aur baad configuration evidence capture karein.

Lecture prerequisites deta hai, mukammal installation walkthrough nahin. In notes ko promotion already complete honay ka saboot na samjhein.

## 12. Tamam concepts ki mushtarka misal

**Mazeed wazahat - farzi misal; completed environment nahin.**

Organization `ad.example.com`, DC01 aur DC02, Finance OU, Khalid ka user account aur file server FS01 use karti hai.

| Step | Zimmedar component |
| --- | --- |
| Client DC dhoondta hai | DNS aur domain discovery |
| Khalid pehchan prove karke tickets leta hai | Client aur DC ka KDC |
| Khalid Finance files mangta hai | Client aur FS01 |
| Access permission evaluate hoti hai | FS01 identities/groups aur access rules se |
| Help desk Finance password reset karti hai | Mutalliq objects par delegated permissions |
| Password/user changes phailte hain | AD replication |
| Desktop lock setting apply hoti hai | Client Group Policy processing |
| Policy files replicate hoti hain | SYSVOL/DFSR |
| Forest schema update authorize/perform hota hai | Munasib permissions aur Schema Master |

DC01 fail ho to DNS, replication aur connectivity healthy honay ki surat mein DC02 munasib domain services de sakta hai. FS01 apni files ka zimmedar rehta hai. Sirf doosra DC add karna FS01 ki failure ka hal nahin.

## 13. Mustaqbil ke lab ke liye read-only checks

**Mazeed wazahat:** Yeh AD DS install nahin karte. Sirf munasib AD environment mein available tools aur read permissions ke saath chalayein. Neeche kisi output ko aap ke servers se observed claim nahin kiya gaya.

### 13.1 Useful consoles

| Tool | Maqsad |
| --- | --- |
| `dsa.msc` | AD Users and Computers |
| `dssite.msc` | AD Sites and Services |
| `domain.msc` | AD Domains and Trusts |
| `gpmc.msc` | Group Policy Management |
| `adsiedit.msc` | Advanced directory inspection; be-fikri se edit na karein |
| DNS Manager | DNS zones aur records |

### 13.2 Domain forest aur DCs inspect karein

~~~powershell
Import-Module ActiveDirectory

Get-ADDomain |
    Select-Object DNSRoot, DomainMode, PDCEmulator, RIDMaster, InfrastructureMaster

Get-ADForest |
    Select-Object RootDomain, ForestMode, SchemaMaster, DomainNamingMaster

Get-ADDomainController -Filter * |
    Select-Object HostName, Site, IsGlobalCatalog, IsReadOnly
~~~

### 13.3 Schema version inspect karein

~~~powershell
$rootDse = Get-ADRootDSE
Get-ADObject -Identity $rootDse.schemaNamingContext -Properties objectVersion |
    Select-Object DistinguishedName, objectVersion
~~~

Yeh schema metadata read karta hai. Schema extend nahin karta.

### 13.4 Role holders aur replication inspect karein

~~~powershell
netdom query fsmo
repadmin /replsummary
repadmin /showrepl
~~~

netdom/repadmin ki availability installed management tools par depend karti hai. Failures ko topology, DNS aur events ke saath review karein; foran changes force na karein.

### 13.5 Mojooda Kerberos tickets dekhein

~~~powershell
klist
~~~

Output session-specific hai. Tickets nazar aana is par depend karta hai ke aap ne kaise sign in kiya aur kaunsi services access ki hain. Tickets, secrets ya ghair-zaroori identity details publish na karein.

## 14. Corrections ka register

| Source ke alfaaz ya diagram | Durust samajh |
| --- | --- |
| KCC replication service hai | KCC topology banata/maintain karta hai; AD replication directory changes transfer karti hai |
| Chaar partitions | Chaar categories; asal partition count chaar se zyada ho sakta hai |
| Schema database ko forms mein display karta hai | Schema object/attribute structure define karta hai |
| Domain data kabhi domains cross nahin karta | Full domain replication domain-scoped hai; GC doosray domains ke partial replicas rakhta hai |
| Resource server par NTDS.DIT hai | Aam member resource servers ko is ki zaroorat nahin |
| AS aur TGS alag servers hain | Logical KDC functions; alag machines lazmi nahin |
| AD har resource ka access decide karta hai | Resource/application checks identity information se permissions enforce karte hain |
| Site-to-site replication tez hai | Lazman nahin; cost, schedules aur bandwidth intersite replication par asar dalte hain |
| Schema Master definitions delete karta hai | Normal schema-definition deletion allowed nahin; supported defunct handling alag hai |
| PDC password-change master hai | Doosray writable DCs changes accept karte hain; PDC ki khaas coordination zimmedariyan hain |
| PDC tamam GPO updates process karta hai | Clients policy process karte hain; PDC preferred administrative target hai |
| Schema version server OS ke barabar hai | Schema, OS version aur functional levels alag hain |
| 250 MB installation ke liye kaafi hai | Purani sizing; modern full DC/OS requirement nahin |
| ADSI Edit paths ke saath diya hai | Management tool hai, database path nahin |

Yeh corrections lecture ke topics barqarar rakhti hain aur technical precision behtar karti hain.

## 15. Revision sawalat aur jawab

**1. AD DS kya hai?**  
Directory service jo identities/resources organize karti aur domain authentication aur administration support karti hai.

**2. DC kya hai?**  
Aisa server jo AD DS host karne aur domain services dene ke liye promote hua ho.

**3. Kya sirf role install karna kaafi hai?**  
Nahin. Promotion/configuration bhi complete honi chahiye.

**4. Active Directory kab introduce hua?**  
Windows 2000 ke saath.

**5. KCC kya karta hai?**  
Replication topology banata aur maintain karta hai.

**6. Directory database kahan hai?**  
Default taur par `C:\Windows\NTDS\ntds.dit` mein.

**7. Partition ki chaar categories kya hain?**  
Schema, configuration, domain aur application.

**8. Kya iska matlab exactly chaar partitions hain?**  
Nahin. Multiple application partitions aur GC replicas ki wajah se actual count zyada pecheeda ho sakta hai.

**9. Schema kya define karta hai?**  
Allowed object classes, attributes aur mutalliq structural rules.

**10. Configuration mein kya hota hai?**  
Forest-wide configuration, jis mein sites, subnets aur replication topology information shamil hai.

**11. GC kya add karta hai?**  
Forest-wide lookup aur mutalliq functions ke liye doosray domains ke domain objects ke partial replicas.

**12. Kya SYSVOL, NTDS.DIT ke andar hai?**  
Nahin. Yeh replicated filesystem content hai.

**13. Kya AS aur TGS lazman alag servers hain?**  
Nahin. DCs par KDC ke functions hain.

**14. Kya file server aam tor par NTDS.DIT rakhta hai?**  
Nahin. Member file server DC nahin hota.

**15. Kya Kerberos ticket har permission deta hai?**  
Nahin. Service ab bhi operations authorize karti hai.

**16. OU aur group mein kya farq hai?**  
OU organization/delegation/policy scope support karta hai; security groups permission assignment support karte hain.

**17. Kya published share object mein asal documents hote hain?**  
Nahin. Files file server/storage par rehti hain.

**18. Kya trust khud-ba-khud access deta hai?**  
Nahin. Authentication paths aur resource permissions alag hain.

**19. Kya AD FS aur AD DS ek hain?**  
Nahin. Federation alag service/concept hai.

**20. Ek-domain forest mein kitne FSMO roles hain?**  
Paanch instances: do forest-wide aur teen domain-wide.

**21. RID Master kya allocate karta hai?**  
RID pools jo DCs security principals banate waqt use karte hain.

**22. Kya PDC akela writable DC hai?**  
Nahin. Aam writable DCs multi-master updates support karte hain.

**23. Planned move ke liye transfer ya seizure?**  
Mojooda role holder available ho to transfer.

**24. 2019/2022 aur 2025 ke schema versions kya hain?**  
Bil-tarteeb 88 aur 91.

**25. Kya schema 91 sabit karta hai ke tamam DCs 2025 hain?**  
Nahin. DC OS aur functional-level compatibility alag check karein.

**26. Kya 250 MB modern DC disk allocation hai?**  
Nahin. OS aur tamam workload/storage requirements plan karein.

**27. DSRM kis liye hai?**  
Directory-services recovery/maintenance scenarios ke liye.

**28. DNS kyun aham hai?**  
Clients ko munasib DNS records se DCs aur services locate karni hoti hain.

**29. Kya replication backup hai?**  
Nahin. Na-pasandeeda changes bhi replicate ho sakte hain.

**30. Do DCs kyun rakhein?**  
Ek server par dependency kam karne ke liye, bashart-e-ke DNS, replication, connectivity aur failure-domain design bhi kaam karein.

## 16. Istilahaat ki fehrist

| Term | Matlab |
| --- | --- |
| AD DS | Active Directory Domain Services |
| DC | Domain Controller; DN mein DC ka matlab domain component hai |
| NTDS.DIT | AD directory database file |
| ESE | Extensible Storage Engine |
| Naming context | Directory partition |
| KCC | Knowledge Consistency Checker |
| KDC | Key Distribution Center |
| AS | Authentication Service |
| TGS | Ticket-Granting Service |
| TGT | Ticket-Granting Ticket |
| SPN | Service Principal Name |
| GC | Global Catalog |
| OU | Organizational Unit |
| GPO | Group Policy Object |
| SYSVOL | Policy/script files wali shared replicated directory |
| DFSR | Distributed File System Replication |
| FSMO | Flexible Single Master Operations |
| RID | Relative Identifier |
| SID | Security Identifier |
| PDC Emulator | Primary Domain Controller Emulator role |
| DSRM | Directory Services Restore Mode |
| UPN | User Principal Name |
| LDAP | Lightweight Directory Access Protocol; database ka filename nahin |
| AD FS | Active Directory Federation Services |
| RODC | Read-Only Domain Controller |

## 17. References

Uploaded lecture, coverage aur topics ki tarteeb ka primary source hai. Neeche Microsoft references additional explanations aur corrections support karte hain. English version ki tayyari ke waqt documentation check ki gayi thi; deployment se pehle current requirements dekhein.

1. [AD replication concepts](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/replication/active-directory-replication-concepts) - KCC, sites aur GC.
2. [Naming contexts and directory partitions](https://learn.microsoft.com/en-us/windows/win32/ad/naming-contexts-and-partitions) - partition concepts.
3. [Kerberos authentication overview](https://learn.microsoft.com/en-au/windows-server/security/kerberos/kerberos-authentication-overview) - KDC aur tickets.
4. [Kerberos protocol exchanges](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-kile/b4af186e-b2ff-43f9-b18e-eedb366abf13) - AS/TGS/application exchanges.
5. [FSMO roles](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-fsmo-roles) - roles ka scope aur zimmedariyan.
6. [FSMO details and qualifications](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/fsmo-roles) - cross-domain references aur role behaviour.
7. [Active Directory schema specification](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/5859bab0-f545-4db6-80c6-9798b484b3d8) - schema objects aur defunct handling.
8. [Find schema version](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/find-active-directory-schema) - objectVersion mappings.
9. [Install AD DS](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-) - deployment options aur prerequisites.
10. [AD DS functional levels](https://learn.microsoft.com/windows-server/identity/ad-ds/active-directory-functional-levels) - DC/version compatibility.
11. [Raise functional levels](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/raise-domain-forest-functional-levels) - preparation aur compatibility.
12. [Windows Server hardware requirements](https://learn.microsoft.com/en-us/windows-server/get-started/hardware-requirements) - modern OS sizing ki maloomat.



## Appendix - Original lecture visuals

Yeh di hui lecture PDF ke pages ke high-resolution renders hain; naye Microsoft screenshots nahin. Source ki ghaltiyan bhi barqarar hain, jin ki corrections register mein di gayi hain, khaas taur par pages 7, 13, 17, 18 aur 22.

### PDF page 3 - ADDS Foundations

![Lecture page 3](images/source_pages/Page_03_ADDS_Foundations.png)

### PDF page 5 - Database and Partitions

![Lecture page 5](images/source_pages/Page_05_Database_and_Partitions.png)

### PDF page 7 - Authentication

![Lecture page 7](images/source_pages/Page_07_Authentication.png)

### PDF page 9 - Objects

![Lecture page 9](images/source_pages/Page_09_Objects.png)

### PDF page 11 - Purposes

![Lecture page 11](images/source_pages/Page_11_Purposes.png)

### PDF page 13 - Features

![Lecture page 13](images/source_pages/Page_13_Features.png)

### PDF page 15 - Structure

![Lecture page 15](images/source_pages/Page_15_Structure.png)

### PDF page 17 - FSMO Forest

![Lecture page 17](images/source_pages/Page_17_FSMO_Forest.png)

### PDF page 18 - FSMO Domain

![Lecture page 18](images/source_pages/Page_18_FSMO_Domain.png)

### PDF page 20 - Schema Versions

![Lecture page 20](images/source_pages/Page_20_Schema_Versions.png)

### PDF page 22 - Installation Requirements

![Lecture page 22](images/source_pages/Page_22_Installation_Requirements.png)

[Image provenance / Tasaveer ki tafseel](IMAGES_AND_SOURCES.md) | [README](README.md)
