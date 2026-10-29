# What Happens When You Type a URL into a Browser?

## Scope and source

These notes expand the video summary supplied by the learner and the accompanying architecture image. They are not a verified transcript of the complete video. Additional explanations clarify the simplified request flow.

Video: https://www.youtube.com/watch?v=_x1GskHtJxo

## 1. The big picture

When you enter `https://www.youtube.com/` and press Enter, your browser needs to locate the service, establish a connection, request content, and display the response. Several systems cooperate to make this happen quickly.

**Core sequence:** Resolve the name → connect securely → send a request → process the request → return content → render the page.

A page may begin appearing in less than a second, but this is not guaranteed. Network conditions, server processing, and the amount of content affect loading time. Videos continue downloading or streaming after the initial page appears.

## 2. URL: the address you enter

**What:** A URL (Uniform Resource Locator) identifies a resource and how to access it.

For `https://www.example.com/videos`:

| Part | Meaning |
|---|---|
| `https` | Scheme indicating HTTP over a secure connection |
| `www.example.com` | Hostname used to locate the service |
| `/videos` | Path identifying the requested resource |

**Why:** The browser needs both the destination and the resource being requested.

## 3. DNS: finding the destination

**What:** DNS (Domain Name System) resolves a hostname to information such as an IPv4 or IPv6 address. An A record contains an IPv4 address; an AAAA record contains an IPv6 address.

**Why:** People remember names more easily, while network communication uses IP addresses.

**How:** The browser or operating system may already have a cached answer. Otherwise, it asks a DNS resolver. The resolver may answer from its cache or query other DNS servers to obtain the answer.

**Analogy:** You look up a person's name in a phone book to find their number.

**Remember:** DNS finds address information. It does not carry the webpage through the rest of the request. DNS may return an address associated with a CDN or load balancer rather than an application server directly. One hostname can have multiple addresses, and answers can vary by location or time.

## 4. Secure connection and HTTP request

**What:** The browser establishes a connection and exchanges HTTP requests and responses with the service.

**Why:** A connection allows data exchange; HTTPS protects it against reading or modification in transit and helps authenticate the service through certificates.

**How:** Many connections use TCP followed by TLS. HTTP/3 uses QUIC over UDP with TLS integrated into the protocol. Existing connections may be reused.

The browser can then request the page, for example:

```http
GET / HTTP/1.1
Host: www.example.com
```

This is an illustrative HTTP/1.1 request, not a capture of YouTube traffic. A real request includes additional information, potentially including cookies used to identify a session.

## 5. Load balancer: distributing requests

**What:** A load balancer distributes incoming traffic among backend servers.

**Why:** Sharing work helps prevent overload and supports availability when a server becomes unhealthy.

**How:** It checks backend health and selects a suitable server according to configured routing and balancing rules. Distribution is not necessarily equal; servers may have different capacities or workloads.

**Example:** A service runs on three application servers. If one fails its health checks, the load balancer can direct new requests to the remaining healthy servers.

**Analogy:** A traffic officer directs cars toward available lanes.

**Remember:** Load balancing distributes work. Adding or removing servers is scaling; these are related but separate functions.

## 6. Application server: running the logic

**What:** An application server runs code that handles requests and applies the application's rules.

**Why:** Personalized and interactive pages require decisions and data processing.

**How:** The application may validate a session, check permissions, obtain data from databases or caches, and return HTML or structured data such as JSON.

**Example:** A video service may use your account and preferences to prepare subscriptions or recommendations. This is a conceptual example, not a description of YouTube's exact internal implementation.

**Remember:** The server does not always build the entire visible page. Browser-side JavaScript may request data and build or update parts of the interface.

## 7. Database: storing application data

**What:** A database stores and organizes data so applications can retrieve or change it.

**Why:** Account information, subscriptions, and activity need to persist beyond a single request.

**How:** The application sends queries or other database operations and uses the results to prepare a response.

**Examples:** User profiles, subscription relationships, video metadata, and watch-history records.

**Remember:** A database can store metadata while video files reside in separate object storage. A request may also be answered from a cache without querying the database. The browser normally communicates with an application or API rather than connecting directly to its private database.

## 8. CDN: delivering content closer to users

**What:** A CDN (Content Delivery Network) is a distributed network of servers that delivers content from locations closer to users in network terms.

**Why:** Shorter delivery paths and cached copies can reduce latency, reduce origin-server load, and improve delivery at scale.

**How:** A CDN can serve a cached copy of an image, script, or video segment. If the requested content is not cached, it may fetch it from an origin or another cache, subject to configuration.

**Example:** A viewer in Chicago may receive video segments from a nearby CDN location instead of a distant origin.

**Remember:** The closest location geographically is not always the best network route. CDNs can deliver more than media, including HTML, CSS, JavaScript, and some dynamic content. Private personalized content requires appropriate access and caching controls.

## 9. Browser: displaying the result

The browser interprets HTML, applies CSS, executes JavaScript, and requests additional resources. It draws the interface and updates it as more data arrives.

Video playback usually involves many requests for media segments rather than downloading a whole video before playback begins.

## 10. Putting the components together

1. You enter a URL.
2. The browser resolves the hostname through DNS, using caches when possible.
3. It establishes or reuses a secure connection to the destination.
4. It sends an HTTP request.
5. A CDN may serve cached content or forward the request toward the origin.
6. A load balancer can select a healthy application server.
7. The application executes logic and may query a database or cache.
8. The response returns to the browser, which renders the page.
9. Further requests fetch images, scripts, data, and video segments, often through a CDN.

**Architecture clarification:** This is one common model. A CDN often sits in front of the origin infrastructure, and media requests can use different hostnames and paths. DNS, load balancing, databases, and CDNs are not a single mandatory chain traversed by every request. The supplied image is a conceptual overview.

## 11. Cloud engineer and software engineer

![Cloud and software engineers supporting website infrastructure](images/Web_Infrastructure_Overview.png)

*Figure 1. Second shared picture: cloud and software engineers support the infrastructure and application behind a website. The image emphasizes running servers and accessible databases. Its arrows illustrate a simplified architecture, not the exact route taken by every request.*


| Role | Typical responsibilities |
|---|---|
| Cloud engineer | Infrastructure, networking, compute, load balancing, access controls, monitoring, automation, capacity, and reliability |
| Software engineer | Application code, APIs, business logic, database access, testing, and application performance |
| Shared work | Security, troubleshooting, deployment, availability, performance, and cost management |

Responsibilities overlap and vary by organization. DevOps, SRE, network, database, and security teams may also contribute.

## 12. Quick revision

| Component | Main question it answers |
|---|---|
| DNS | Where can I reach this hostname? |
| HTTPS | How can I exchange HTTP data securely? |
| Load balancer | Which backend should handle this traffic? |
| Application server | What should the application do for this request? |
| Database | What stored information is needed? |
| CDN | Can content be delivered efficiently from a nearby location? |
| Browser | How should this content appear and behave? |

## 13. Review questions and answers

**1. Why is DNS needed?** To resolve human-readable hostnames to information needed to reach services, such as IP addresses.

**2. Does every request require a new DNS lookup?** No. Cached DNS answers and reused connections can avoid repeated lookups.

**3. What happens if one application server fails?** A properly configured load balancer can stop sending new traffic to that unhealthy server. Requests already in progress may still fail.

**4. What is the difference between an application server and a database?** The application server executes logic; the database stores and retrieves data.

**5. Why does a CDN improve performance?** It can serve cached content closer to users and reduce requests to the origin.

**6. Is a CDN the same as a load balancer?** No. A CDN distributes content delivery across locations; a load balancer distributes traffic among backends. They can work together.

**7. Why might the page appear before the video starts?** The interface and video media are separate resources with separate requests and loading times.

**8. Must all these steps finish in under a second?** No. Timing depends on caching, connection reuse, network conditions, server processing, and resource size.

## 14. Suggested next lab — planned

Use the [English lab guide](LAB_GUIDE_ENGLISH.md) to observe DNS answers, TCP connectivity and browser requests. This is a future exercise, not evidence that the lab has been completed.

## 15. Sources and image context

See [Sources and Image Context](SOURCES.md) for the supplied video and primary technical references. The included picture is the second learner-shared image. The first image was not present in the source ZIP. The image is a simplified illustration, not a network trace or a verified map of YouTube infrastructure.

[Back to folder guide](README.md)
