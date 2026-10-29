# From URL to Webpage — Networking Foundations

Understand how DNS, secure connections, HTTP, load balancers, applications, databases, CDNs and browsers work together.

Yeh module browser mein URL likhne se page nazar aane tak ka process samjhata hai. English aur Roman Urdu notes ki asal tafseel barqarar rakhi gayi hai.

## Start here

| Order | Material | Purpose |
| --- | --- | --- |
| 1 | [English study notes](URL_to_Webpage_Study_Notes_English.md) | Complete theory, examples and revision answers |
| 1 | [Roman Urdu study notes](URL_to_Webpage_Study_Notes_Roman_Urdu.md) | Wohi theory aur examples Roman Urdu mein |
| 2 | [Infrastructure image](images/Web_Infrastructure_Overview.png) | Conceptual visual overview |
| 3 | [English lab guide](LAB_GUIDE_ENGLISH.md) | Planned observation exercise |
| 3 | [Roman Urdu lab guide](LAB_GUIDE_ROMAN_URDU.md) | Wohi planned exercise Roman Urdu mein |
| 4 | [Sources and image context](SOURCES.md) | References and limitations |

Choose either language for your first reading; both notes cover the same topics.

## Learning route

1. Identify the scheme, hostname and path in a URL.
2. Understand DNS resolution and cached answers.
3. Learn how HTTPS, HTTP requests and responses fit together.
4. Compare load balancing, application logic, databases and CDN delivery.
5. Explain how the browser renders the result.
6. Complete the observation lab later and save your own evidence.

## Status

| Area | Status |
| --- | --- |
| English and Roman Urdu theory | Ready |
| Supplied second image and relative links | Included and checked |
| Sources | Included |
| Practical observation lab | Planned; not yet recorded as completed |
| First shared image | Not present in supplied ZIP |

## Visual overview

![Conceptual website infrastructure and engineering responsibilities](images/Web_Infrastructure_Overview.png)

DNS is a name-resolution step, not a transit point carrying the webpage. Not every request reaches a database or follows every component shown. The notes explain these qualifications.

## Repository placement

Place this complete folder at:

`VMware_Lab_Study/00_Networking_Foundations/URL_to_Webpage/`

Keep the `images` subfolder and all filenames unchanged so the links work.

Add this line to `00_Networking_Foundations/README.md`:

```markdown
- [From URL to Webpage — English and Roman Urdu](URL_to_Webpage/README.md)
```

This is supporting networking theory for the VMware/DNS lab. It does not change your existing DNS or ESXi configuration.
