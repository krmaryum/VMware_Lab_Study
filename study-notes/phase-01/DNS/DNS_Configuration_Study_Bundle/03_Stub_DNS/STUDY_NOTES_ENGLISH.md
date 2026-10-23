# DNS03 — Stub zone lab

## Concept overview

A stub zone holds SOA, NS and necessary address records identifying the authoritative DNS servers. It helps a resolver locate those servers without keeping a complete zone replica. DNS03 refreshes this limited information from its master and uses it to locate the servers that can answer queries for VCPLAB.Com.


## Understanding this configuration

A stub is a directory of the zone’s authorities, not another full host directory. The SOA, NS and necessary address/glue entries identify where authoritative answers can be obtained. “Three record types” does not mean exactly three individual records; multiple name servers and address entries are possible.

For example, a client asks DNS03 for `dns02.vcplab.com`. DNS03 can use the stub’s authority information to resolve the name through a reachable authoritative server and return the answer. Its own stub does not suddenly contain every A, CNAME or PTR record. The captured successful answer proves resolution through DNS03, but packet tracing would be needed to establish the precise live path and caching behavior.

If the required authorities are unavailable and no usable answer is cached, a stub cannot replace their complete zone data. It is therefore not the same resilience mechanism as DNS02’s secondary. It also does not guarantee faster queries. In this lab the stub points to DNS01; do not assume DNS02 will automatically be used without checking the authority information and configuration.

**Comparison:** a secondary copies the records; a stub tracks the authorities; a conditional forwarder, covered next, uses a configured domain-to-server rule.

## Lab values

| Server | Address | Role |
| --- | --- | --- |
| DNS01 | 192.168.0.121 | Standard primary |
| DNS02 | 192.168.0.122 | Secondary |
| DNS03 | 192.168.0.123 | Stub / resolving server |

VMnet2 host-only: 192.168.0.0/24. Forward zone: VCPLAB.Com. Reverse zone: 0.168.192.in-addr.arpa. Internet is unnecessary for these local lookups. A DNS suffix does not join a computer to an AD domain. This lab uses file-backed zones, not AD-integrated zones.

## Purpose in this lab

A stub zone retains SOA, NS and necessary address records identifying authoritative DNS servers. It does not copy every host, alias or PTR. It helps a resolver find the servers responsible for a namespace and refresh that information. It is not a replacement for a full secondary replica.

## Reading the lab record

The procedure below documents the configuration workflow and can be reused for practice. Statements marked verified refer to the supplied record; proposed tests and missing captures are identified separately. The review did not change or re-test the running machines.

## 1. Create the forward stub

1. On DNS03, open DNS Manager → Forward Lookup Zones → New Zone.
2. Next → Stub zone. AD storage is unavailable in this workgroup exercise.
3. Enter VCPLAB.Com.
4. Choose Create a new file: VCPLAB.Com.dns.
5. Enter master 192.168.0.121; confirm validation OK.
6. Next → review Stub / Forward → Finish → Refresh.
7. Confirm SOA, NS pointing to dns01.vcplab.com, and dns01 A record .121. The missing DNS02/DNS03 A and DNS CNAME records are expected here.

## 2. Create the reverse stub

1. Right-click Reverse Lookup Zones → New Zone → Stub zone.
2. Select IPv4 and Network ID 192.168.0 (or enter zone name 0.168.192.in-addr.arpa if requested).
3. Accept a new zone file and use master 192.168.0.121.
4. Finish and refresh. The captured final view shows SOA and NS; the reverse wizard itself was not shared.
5. Do not expect the full list of PTR records in a stub.

## 3. Verify resolving through DNS03

```powershell
nslookup dns02.vcplab.com. 192.168.0.123
nslookup 192.168.0.122 192.168.0.123
```

Screenshot 034759 confirms both queries succeeded through DNS03 .123, returning DNS02 and .122. The forward output says Non-authoritative answer, consistent with recursive resolution rather than DNS03 holding a complete authoritative zone. The screenshot does not prove the packet path or exclude cached answers.

## Troubleshooting

Check master .121, NS target and its address, network/DNS service, and that recursion is enabled for resolving clients. Test the same name directly against DNS01. A stub server's dependence on reachable authoritative servers differs from a secondary holding its own complete copy.


## Screenshot walkthrough

Follow the numbered procedure above; these images preserve the captured stages. Missing wizard pages are not evidence of the exact settings chosen.

## 033557 — DNS03 New Zone

![DNS03 New Zone](screenshots/033557.png)

## 033643 — Wizard welcome

![Wizard welcome](screenshots/033643.png)

## 033722 — Stub selected

![Stub selected](screenshots/033722.png)

## 033806 — VCPLAB.Com name

![VCPLAB.Com name](screenshots/033806.png)

## 033935 — New stub file

![New stub file](screenshots/033935.png)

## 034036 — DNS01 master validates OK

![DNS01 master validates OK](screenshots/034036.png)

## 034058 — Forward stub summary

![Forward stub summary](screenshots/034058.png)

## 034304 — Forward stub records

![Forward stub records](screenshots/034304.png)

## 034336 — Reverse stub final records

![Reverse stub final records](screenshots/034336.png)

## 034759 — DNS03 forward and reverse tests pass

![DNS03 forward and reverse tests pass](screenshots/034759.png)
