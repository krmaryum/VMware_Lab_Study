# Endpoint, IP Address, Port and TCP/UDP

## English — Structured Study Notes

## 1. Purpose of this lesson

After this lesson, you should understand:

- What an endpoint is and what “logical endpoint” means.
- The separate roles of an IP address, transport protocol and port number.
- Why TCP port 80 and UDP port 80 are different.
- How client and server endpoints participate in communication.
- How these concepts apply to your Windows-to-ESXi SSH lab.

These are theory notes. The temporary ports and connection examples below are illustrative; they were not captured from your machine.

## 2. The simple meaning of endpoint

**An endpoint is one end or point of communication where data is sent or received.**

The word “endpoint” has different meanings in different contexts. In security, a laptop may be called an endpoint. In an API, the URL for an operation may be called an endpoint. In this lesson, we are discussing a **TCP/UDP network communication endpoint**.

To understand the identity of an endpoint in TCP/UDP communication, consider these three things:

**Transport protocol + IP address + port number**

Example:

**TCP + 10.0.0.11 + 22**

This means the communication point at TCP port 22 on that IP address.

## 3. What is a logical endpoint?

Here, **logical** means a point created and managed through software.

A network port is not a physical opening. You do not plug an Ethernet cable into TCP port 22. The cable connects to a physical network adapter; a TCP/UDP port belongs to the operating system's networking software.

| Term | Meaning |
| --- | --- |
| Physical Ethernet port | A hardware connection where a network cable can be plugged in |
| TCP/UDP port | A numbered communication point in software |
| Logical endpoint | A communication end identified through software |

## 4. The role of an IP address — where should the data go?

An IP address is used to deliver data toward an interface or destination on a network.

At beginner level, you can think of it as answering “Which machine should I reach?” More precisely, a machine can have multiple interfaces and multiple IP addresses.

Example from your lab:

| System | IP address |
| --- | --- |
| Windows desktop | 10.0.0.213 |
| Desktop ESXi 8 — previously shared lab address | 10.0.0.11 |

These addresses come from the environment you previously shared. Verify the addresses again in your current lab.

An IP address alone does not tell us whether we want SSH or HTTPS at that destination.

## 5. The role of a port number — which communication point?

One machine can run multiple network services. Port numbers help the operating system deliver traffic to the correct socket or application.

| Service | Common transport | Default server port | Purpose |
| --- | --- | --- | --- |
| SSH | TCP | 22 | Remote command-line access |
| HTTP | TCP | 80 | Web requests |
| HTTPS over HTTP/1.1 or HTTP/2 | TCP | 443 | Secure web communication |
| HTTPS over HTTP/3 | QUIC over UDP | 443 | Another transport model for secure web communication |
| DNS | UDP and TCP | 53 | DNS queries and other DNS operations |

**A default port is a convention, not a requirement.** A service can be configured on another port. Seeing port 22 alone does not conclusively prove that the service is SSH.

Port numbers range from **0 to 65535**. Port 0 is reserved; it is not normally used as the destination port for a service connection.

## 6. The role of the protocol — how will communication take place?

Here, protocol means the transport protocol: TCP or UDP.

| Aspect | TCP | UDP |
| --- | --- | --- |
| Basic model | Connection-oriented byte stream | Datagram-based communication |
| Start | Normally involves a connection handshake | No TCP-style connection handshake |
| Delivery and order | Mechanisms for ordered delivery, acknowledgments and retransmission | Does not itself guarantee delivery, order or retransmission |
| Data boundaries | The application receives a byte stream | Datagram boundaries are preserved |
| Example | SSH | Many ordinary DNS queries |

TCP cannot guarantee successful delivery under every network failure; the connection can fail.

An application or another protocol above UDP can add reliability. For example, QUIC provides transport features over UDP. Therefore, saying “An application using UDP is always unreliable” is incorrect.

## 7. Why are TCP port 80 and UDP port 80 different?

The operating system manages TCP and UDP ports separately. Their numbers can be the same, but their communication points differ because their protocols differ.

| Protocol | IP | Port | Identity |
| --- | --- | --- | --- |
| TCP | 192.168.1.20 | 80 | TCP endpoint |
| UDP | 192.168.1.20 | 80 | A separate UDP endpoint |

If an application is listening on TCP port 80, data sent to UDP port 80 will not be delivered to that TCP listener.

A machine can use TCP 80 and UDP 80 at the same time if suitable sockets or services are configured. The same application can also use both protocols.

**The same number does not mean the same endpoint.**

## 8. The building analogy

| Networking concept | Building analogy |
| --- | --- |
| IP address | The building's address |
| TCP or UDP | Two separate wings |
| Port number | A numbered door within a wing |
| Application/service | The department working behind that door |

Room 80 in the TCP wing and room 80 in the UDP wing are different rooms.

This is a teaching analogy. Real networking does not contain physical TCP wings or UDP doors.

## 9. What does listening mean?

**A service listening** means that it is ready to receive incoming communication at a configured address and port.

A TCP server's listening socket waits for incoming connection requests. With UDP, an application can bind a socket to receive datagrams; it does not use a TCP-style listening handshake.

Example: the ESXi SSH service is available on TCP port 22.

However, a running service alone is not enough. The correct address, a working network route and firewall permission are also needed.

**Service running, port reachable and login successful are three separate checks.**

## 10. A step-by-step example from your SSH lab

On Windows:

~~~powershell
ssh root@10.0.0.11
~~~

By default, the SSH client uses:

| Component | Value | Meaning |
| --- | --- | --- |
| Application protocol | SSH | The remote-login protocol |
| Transport protocol | TCP | The underlying protocol transporting the data |
| Destination IP | 10.0.0.11 | The ESXi host's lab address |
| Destination port | 22 | The default SSH server port |
| Username | root | The login account |

**root is not part of the IP address or port.** It is the account name used for authentication.

If SSH is configured on another port, such as 2222:

~~~powershell
ssh -p 2222 root@10.0.0.11
~~~

This command does not change the server's port. It only tells the client which destination port to connect to.

## 11. The client also has its own endpoint

Suppose the Windows desktop connects from 10.0.0.213. The operating system selects temporary local port 52345.

| Side | Protocol | IP | Port |
| --- | --- | --- | --- |
| Windows SSH client | TCP | 10.0.0.213 | 52345 |
| ESXi SSH server | TCP | 10.0.0.11 | 22 |

52345 is only an illustrative temporary port. Every connection does not have to use this port.

### Request direction

| Field | Value |
| --- | --- |
| Source endpoint | TCP, 10.0.0.213:52345 |
| Destination endpoint | TCP, 10.0.0.11:22 |

### Response direction

| Field | Value |
| --- | --- |
| Source endpoint | TCP, 10.0.0.11:22 |
| Destination endpoint | TCP, 10.0.0.213:52345 |

Source and destination change with the packet's direction. This does not mean that the client and server roles swap.

## 12. What is a temporary or ephemeral port?

The client needs its own local communication point. The operating system usually selects a temporary, or **ephemeral**, port for it.

- The server normally receives requests at a configured service port.
- The client normally uses a temporary source port.
- The exact allocation range depends on the operating system and configuration.
- An application can also explicitly bind its source port when needed.

The server's reply returns to the client's source port. Responses do not all have to go to port 22 on the client.

## 13. How are two SSH sessions distinguished?

Consider two sessions from one Windows desktop to the same ESXi host:

| Session | Client endpoint | Server endpoint |
| --- | --- | --- |
| Session A | TCP, 10.0.0.213:52345 | TCP, 10.0.0.11:22 |
| Session B | TCP, 10.0.0.213:52346 | TCP, 10.0.0.11:22 |

The server IP and port are the same, but the client ports differ. This allows the operating systems to distinguish the sessions.

A TCP connection is identified by its two endpoints: source IP/port and destination IP/port. Including the protocol gives what is often called a **5-tuple**:

1. Source IP
2. Source port
3. Destination IP
4. Destination port
5. Transport protocol

An endpoint and a complete connection are not the same thing. A connection has two endpoints.

## 14. Understanding the colon notation

~~~text
10.0.0.11:22
~~~

The colon separates the IP address and port. This shorthand does not itself specify TCP or UDP. The protocol must be understood from the context or stated separately.

~~~text
TCP 10.0.0.11:22
~~~

Brackets are used with IPv6 addresses to avoid ambiguity:

~~~text
[2001:db8::11]:443
~~~

This is an example IPv6 address for documentation, not an address from your lab.

## 15. Common confusions

| Misunderstanding | Correct understanding |
| --- | --- |
| A port is a physical cable socket | A TCP/UDP port is a logical software point |
| A port number alone is an endpoint | The protocol, IP and port context are needed |
| TCP 80 and UDP 80 are the same | They are separate communication points |
| Port 22 necessarily means SSH | It is a default convention; the service must be verified |
| If the port is open, login must succeed | Authentication and authorization can still fail |
| If ping fails, SSH must also fail | ICMP and TCP are different; SSH can still be reachable |
| The client always uses port 22 too | The client normally uses a temporary local port |
| One service port can serve only one client | A listening server can handle many separate TCP connections |
| The HTTP application protocol and TCP are the same | HTTP/SSH are application protocols; TCP is a transport protocol |

## 16. Understanding evidence from the lab

In your earlier lab, Test-NetConnection succeeded on port 22 and SSH login subsequently succeeded. These established two separate facts:

1. A TCP connection to port 22 could be established.
2. A session opened after SSH authentication.

For future investigations, consider these layers separately:

| Check | Question |
| --- | --- |
| Address | Is the destination IP correct? |
| Transport and port | Are TCP/UDP and the port correct? |
| Reachability | Do routing and firewall rules allow communication? |
| Service | Is the expected application responding? |
| Authentication | Is the account/key/password being accepted? |
| Authorization | What actions are permitted after login? |

This checklist is a way to organize troubleshooting, not a claim that a new practical lab has been completed.

## 17. Revision questions and answers

**1. What is an endpoint?**  
One end of communication where data is sent or received.

**2. Why is it called a logical endpoint?**  
Because it is a software networking point, not a physical cable socket.

**3. What is the role of an IP address?**  
It helps deliver data toward a network destination or interface.

**4. What is the role of a port?**  
It helps deliver traffic to the correct socket or application within a transport protocol.

**5. Why are TCP port 80 and UDP port 80 different?**  
TCP and UDP are separate transport protocols with separate port spaces.

**6. How can you write a default SSH server endpoint?**  
For example: TCP, 10.0.0.11:22.

**7. Why does the client need a temporary port?**  
It needs its own communication point so that replies can return to the correct socket or session.

**8. Does every SSH client use source port 22?**  
No. It normally uses an ephemeral port.

**9. Are an endpoint and a connection the same?**  
No. A TCP connection has two endpoints.

**10. Does a reachable port prove that the password is correct?**  
No. TCP connectivity and authentication are separate checks.

**11. Does 10.0.0.11:22 tell you the protocol by itself?**  
No. The protocol must come from context or a separate label.

**12. Can an application using UDP add its own reliability?**  
Yes. UDP does not provide these guarantees itself, but a higher-level protocol or application can add mechanisms.

## 18. Practice explaining it yourself

Explain this example in your own words:

**TCP, 10.0.0.213:52345 and TCP, 10.0.0.11:22**

Include:

- Which side is the client?
- Which side is the server?
- Which port is temporary?
- Which is the default SSH server port?
- Where will the response return?

**Expected understanding:** The Windows client at 10.0.0.213 uses example source port 52345. The ESXi server at 10.0.0.11 uses SSH port 22. The response returns over TCP to 10.0.0.213:52345.

## 19. A quick memory guide

| Question | Answer |
| --- | --- |
| Where should the data go? | IP address |
| Which transport should it use? | TCP or UDP |
| Which communication point? | Port number |
| Which account should log in? | Username |
| What are the two ends? | Client endpoint and server endpoint |

**Endpoint: one end. Connection: communication between the two ends.**

## 20. Suggested repository placement

You can place this file here:

`VMware_Lab_Study/00_Networking_Foundations/`

Add this link to that folder's README:

~~~markdown
- [Endpoint, IP, Ports and TCP/UDP — English](Endpoint_IP_Ports_TCP_UDP_Study_Notes_English.md)
~~~

