# Protocol — Brief Study Note

## What is a protocol?

A **protocol** is a defined set of rules for communication. These rules explain how devices send, receive and interpret data.

## Why is it needed?

Both devices must understand the message format and the request/response process so they can communicate correctly.

## Common examples

| Protocol | Main purpose |
| --- | --- |
| **DNS** | Resolve records for names, such as IP addresses |
| **HTTP** | Exchange web requests and responses |
| **SSH** | Provide secure remote login and command execution |
| **LDAP** | Access directory information |
| **Kerberos** | Authenticate using tickets |

## AD DS example

Khalid signs in to a domain computer. The computer follows the **Kerberos protocol** to communicate with the Domain Controller's KDC service for authentication and tickets.

**KDC = Key Distribution Center.**

## Protocol versus port

A protocol defines the rules for communication; a port helps identify a service's network endpoint.

For example, **SSH** is a protocol and commonly uses **TCP port 22**.

## Memory aid

> A protocol answers: **“What rules will we follow to communicate?”**
