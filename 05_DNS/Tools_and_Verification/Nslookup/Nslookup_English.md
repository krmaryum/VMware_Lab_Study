# nslookup — DNS troubleshooting study notes

## The purpose

`nslookup` asks a DNS server for DNS information. It is useful when a name returns the wrong IP, one DNS server behaves differently from another, or a reverse lookup is missing. It can query different record types; it does not configure a zone, create records or repair connectivity.

These examples use the recorded private classroom zone `VCPLAB.Com`. They are repeat-practice commands, not new live test results. Example output below is illustrative. A computer running the commands must have a working network path to the chosen DNS server; the Surface does not automatically join a Workstation host-only network.

## 1. Identify the server and the question

| Server | Recorded lab address | Role |
|---|---|---|
| DNS01 | 192.168.0.121 | Standard primary |
| DNS02 | 192.168.0.122 | Secondary |
| DNS03 | 192.168.0.123 | Stub / resolving server |

The DNS network was recorded as VMnet2 host-only, `192.168.0.0/24`. Do not replace those addresses with earlier NAT-network values when following this lab.

```powershell
nslookup dns02.vcplab.com. 192.168.0.121
```

| Part | Meaning |
|---|---|
| nslookup | The DNS query tool |
| dns02.vcplab.com. | The name being queried |
| 192.168.0.121 | The DNS server receiving the question |

The final dot makes the queried name absolute. It avoids relying on suffix expansion. The command means: “Ask DNS01 for the address of DNS02.” Querying DNS02’s name does not mean DNS02 answered.

## 2. Read the output correctly

**Illustrative expected output; not a newly captured result:**

```text
Server:  dns01.vcplab.com
Address: 192.168.0.121

Name:    dns02.vcplab.com
Address: 192.168.0.122
```

The first block identifies the responding DNS server. The second block gives the requested name’s answer. `.121` is the query destination; `.122` is the returned record. Do not confuse them.

Without an explicit second address:

```powershell
nslookup dns02.vcplab.com.
```

The tool uses its default DNS server from the computer’s DNS configuration. For comparing lab servers, provide the server IP explicitly so the test is repeatable.

## 3. Forward and reverse lookups

A forward lookup maps a name to an address. Request IPv4 explicitly with A:

```powershell
nslookup -type=A dns02.vcplab.com. 192.168.0.121
```

A reverse lookup asks for the name associated with an address, using PTR data in a reverse zone:

```powershell
nslookup 192.168.0.122 192.168.0.121
```

For this lab the expected mapping is `.122` to `dns02.vcplab.com`. Forward and reverse data are separate. A working A record does not guarantee a matching PTR, and a PTR is not required for every ordinary forward lookup.

## 4. Compare primary, secondary and stub behavior

Ask the same question of all three:

```powershell
nslookup -type=A dns02.vcplab.com. 192.168.0.121
nslookup -type=A dns02.vcplab.com. 192.168.0.122
nslookup -type=A dns02.vcplab.com. 192.168.0.123
```

| Query destination | Interpretation of a correct answer |
|---|---|
| DNS01 .121 | Primary can answer the record |
| DNS02 .122 | Secondary can answer using its complete zone copy |
| DNS03 .123 | Resolution works through the stub/resolving server; it does not prove a full zone copy exists there |

An ordinary successful query does not prove that the latest edit replicated, that automatic failover works, or which upstream server a cached/recursive answer used. A replication test needs a known new change and queries against the relevant copies. An outage test needs controlled conditions and observed results.

## 5. Query records by type

| Type | Question answered | Lab use |
|---|---|---|
| A | IPv4 address for this name? | DNS01/DNS02/DNS03 host records |
| AAAA | IPv6 address? | Only if the zone contains one |
| PTR | Reverse address-to-name mapping? | Reverse zone |
| CNAME | Which canonical name does this alias reference? | dns.vcplab.com → dns01.vcplab.com |
| NS | Which servers are listed as authoritative? | Inspect zone authority information |
| SOA | What authority/serial/timer metadata is published? | Compare zone versions and refresh settings |
| MX | Which mail exchangers are published? | Not configured by this lab record |
| TXT | What text record is published? | Query only if one exists |

```powershell
nslookup -type=CNAME dns.vcplab.com. 192.168.0.121
nslookup -type=NS vcplab.com. 192.168.0.121
nslookup -type=SOA vcplab.com. 192.168.0.121
nslookup -type=SOA vcplab.com. 192.168.0.122
```

The alias’s target name is not an IP. Its target’s A record provides IPv4 addressing. Equal SOA serials are a useful comparison, not a full audit of every record. An absent MX/AAAA record is not automatically a server fault.

## 6. Interactive mode

Enter these lines one at a time. After `nslookup`, the tool has its own prompt; these are not PowerShell commands:

```text
nslookup
server 192.168.0.121
set type=A
dns02.vcplab.com.
set type=SOA
vcplab.com.
exit
```

`server` changes the query destination for the session. `set type` changes the question’s record type. `exit` returns to the shell. Changing the query type does not create or modify DNS data.

## 7. Understand messages before changing anything

| Message | Meaning and next check |
|---|---|
| Server: Unknown | The chosen server’s reverse display name could not be resolved; examine the actual query answer separately |
| NXDOMAIN / Non-existent domain | A negative response says the queried name does not exist; check spelling, suffix, zone and selected server |
| No records | The requested record type may be absent even though the name exists |
| DNS request timed out | A response did not arrive in time; inspect reachability, firewall, service, listening address and load |
| Non-authoritative answer | The responding server is not authoritative for this answer; a recursive/cached answer can still be correct |
| SERVFAIL / Server failed | The server could not complete processing; investigate upstream dependencies, zone health and, where applicable, validation |
| REFUSED / Query refused | Server policy refused the request; check permitted clients/recursion rather than assuming a missing record |

An initial timeout and a later answer can appear in the same session. A missing PTR can explain an unknown display name; it does not prove the cause of every timeout. A valid NXDOMAIN is not the same as silence and does not normally trigger a search through every alternate DNS server.

## 8. A practical troubleshooting sequence

1. Record the test computer and its network: `hostname`, `ipconfig /all`.
2. Query a complete known name against the primary IP explicitly.
3. Separate “no response” from “negative response” and “wrong returned data.”
4. Compare the same name against the secondary and stub server.
5. Test reverse data separately if PTR or server display naming is the issue.
6. On the server, inspect the relevant zone, record, DNS service and allowed traffic. DNS uses UDP and TCP port 53.
7. Save the actual command/output and a conclusion limited to what it proves.

Optional TCP path check:

```powershell
Test-NetConnection 192.168.0.121 -Port 53
```

This checks TCP connectivity only. It does not test UDP DNS or prove a valid DNS answer. Similarly, ping checks ICMP, not DNS. A successful lookup does not prove RDP/SSH/HTTPS works.

`nslookup` queries DNS directly and is not a test of a Windows hosts-file entry or an SSH config alias. Normal application name resolution can follow a different path. `ipconfig /flushdns` clears the Windows client resolver cache; it is not a general repair for missing server records or a stale server-side zone.

## 9. Practice and evidence sheet

| Task | Command / check | Expected result | Actual result |
|---|---|---|---|
| Primary A | Query dns02 at .121 | .122 | Record during practice |
| Reverse | Query .122 at .121 | DNS02 full name | Record during practice |
| Secondary | Query dns01 at .122 | .121 | Record during practice |
| Stub | Query dns02 at .123 | .122 | Record during practice |
| Alias | Query CNAME dns.vcplab.com at .121 | dns01.vcplab.com | Record during practice |
| Zone version | Compare SOA on .121 and .122 | Investigate any unexpected difference | Record during practice |

Run from a machine with a verified route to this lab. No new successful outputs are claimed here. Keep KRNC.org forwarding and DNSSEC validation as separate exercises; ordinary local nslookup success does not complete those tests.

## Revision questions

1. Which IP at the top of the output identifies the responding server? **The Server/Address block.**
2. Does querying dns02 through .121 test DNS02’s service? **No; the destination is DNS01.**
3. Does Server: Unknown always mean lookup failure? **No; read the answer block.**
4. Does a forward record automatically prove reverse data? **No.**
5. Does a DNS03 answer prove DNS03 holds every zone record? **No; the stub/resolver can obtain an answer elsewhere or use cache.**
6. Does TCP 53 success prove a correct record exists? **No.**

## References

- [Microsoft nslookup command reference](https://learn.microsoft.com/windows-server/administration/windows-commands/nslookup)
- [Microsoft nslookup settings](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup-set)
- [Microsoft query types](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup-set-type)

The addresses and roles come from the supplied lab context. Commands are a study/repeat checklist; sample output is illustrative.
