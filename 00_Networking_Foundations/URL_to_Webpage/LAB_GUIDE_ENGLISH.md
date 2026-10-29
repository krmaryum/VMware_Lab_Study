# URL to Webpage — Planned Observation Lab

[Folder guide](README.md) | [Theory notes](URL_to_Webpage_Study_Notes_English.md)

## Goal and prerequisites

Observe name resolution, a TCP connection and browser requests from Windows. You need an internet connection, Windows PowerShell and a browser. No DNS server, firewall or ESXi settings need changing. Run these commands in Windows PowerShell if a networking cmdlet is unavailable in another shell.

**Status: planned.** Output below is described, not claimed as captured from your machine.

## 1. Query DNS

```powershell
Resolve-DnsName -Name www.example.com -Type A -DnsOnly
Resolve-DnsName -Name www.example.com -Type AAAA -DnsOnly
```

Record the name, record type, addresses and TTL where shown. Results may include aliases and may change over time. A missing AAAA answer does not by itself mean the website is broken. These commands query your configured resolver; they do not demonstrate every upstream DNS hop or exactly reproduce the browser's DNS behaviour.

## 2. Test TCP port 443

```powershell
Test-NetConnection -ComputerName www.example.com -Port 443
```

Record `RemoteAddress`, `InterfaceAlias`, `SourceAddress` and `TcpTestSucceeded`.

`True` confirms that this TCP connection could be established. It does not prove TLS validation, a successful HTTP response, database health or HTTP/3 connectivity. Failed ping alongside a successful TCP test can occur when ICMP is blocked.

## 3. Observe the browser

1. Open browser Developer Tools with F12 or Ctrl+Shift+I.
2. Select the **Network** panel.
3. Navigate to `https://www.example.com/` or reload it while the panel is open.
4. Select the document request. Record its URL, method, HTTP status, response headers and timing fields that are available.
5. If the browser offers a **Protocol** column, enable it and note the protocol shown.
6. On a richer page of your choice, compare document, CSS, script, image and Fetch/XHR requests. The example page may contain few resources.

A browser may reuse connections, use encrypted DNS or serve local cache entries. Timing fields can therefore be missing or small. Cached results are valid observations. HTTP/3 traffic uses QUIC over UDP; the TCP command above does not test that path.

## 4. Evidence checklist

| Evidence | Your observation |
| --- | --- |
| Date, Windows machine and browser | To complete |
| DNS answer and record type | To complete |
| TCP port 443 result | To complete |
| Browser request URL and HTTP status | To complete |
| Protocol and visible timing | To complete |
| Explanation in your own words | To complete |

Save screenshots in an `evidence` subfolder when you perform the lab. Exclude cookies, authorization headers and other session secrets from public evidence. Developer Tools cannot prove that the application queried a database or reveal all private backend components.

## 5. Troubleshooting by evidence

| Observation | Next investigation |
| --- | --- |
| DNS query fails | Check configured DNS, connectivity and whether the name exists |
| DNS works; TCP fails | Investigate routing, filtering, listener availability or the destination |
| TCP works; browser fails | Inspect the actual browser error, certificate, HTTP status and proxy behaviour |
| HTML loads; some content fails | Inspect the failed resource hostname, response and request details |

Mark the lab completed only after recording your actual observations. See [sources](SOURCES.md) for command documentation.
