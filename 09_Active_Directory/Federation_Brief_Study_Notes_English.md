# Federation — Brief Study Notes

## What is federation?

**Federation** is a trust arrangement between separate identity systems. It allows an application to accept an identity authenticated by a trusted identity provider.

## Why is it used?

A user can access an external application with their organization's account without giving that application their organization password.

## Simple example

1. Khalid opens a training application.
2. The application redirects him to the company's **Identity Provider (IdP)** to sign in.
3. The IdP verifies his identity and issues a **signed token/assertion**.
4. The application validates the token and grants access according to its own permissions.

## Important terms

| Term | Meaning |
| --- | --- |
| **Identity Provider (IdP)** | Authenticates an identity and provides proof of authentication |
| **Service Provider (SP)** | The application/service that accepts trusted authentication proof |
| **Token/assertion** | Proof containing identity information and related claims; validated according to the configured trust |
| **AD FS** | Active Directory Federation Services—Microsoft's federation service |

## Federation versus SSO

- **Federation:** trust between separate identity systems.
- **SSO — Single Sign-On:** reducing the need to enter credentials repeatedly.

Federation can enable SSO. **Federation does not require merging user databases**, and successful authentication does not grant permission to every resource.

## Memory aid

> With federation, an application accepts a trusted identity provider's authentication; access permissions are still checked.
