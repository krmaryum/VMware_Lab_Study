# Practice and evidence worksheet

Status: exercises are proposed; no successful results have been invented.

## First observation session

1. Identify the machine and shell: Windows host, Linux guest or ESXi host.
2. Display its IP/prefix, DNS and gateway using the matching platform guide.
3. Explain which destination is local and which needs a route.
4. Resolve a known name and compare the returned address with the expected target.
5. Test a service you own: for example, your ESXi management endpoint on TCP/443 from Windows.
6. Compare a ping result with the TCP result; explain why they can differ.
7. Locate the virtual network or port group attachment in the appropriate VMware interface.
8. Draw or describe the observed path; label unknown items rather than guessing.

Do not use sample home-lab IP addresses without checking them. DNS query tools can differ from the application's resolver behavior, so compare with the actual application when needed.

## Evidence template

| Field | Your actual observation |
|---|---|
| Date/time | Pending |
| Machine / OS / shell | Pending |
| Question being tested | Pending |
| Command or UI location | Pending |
| Actual output / screenshot filename | Pending |
| Interpretation | Pending |
| What this does not prove | Pending |
| Next focused check | Pending |

## Example interpretation — not a new test result

If ping times out but `Test-NetConnection <target> -Port 443` succeeds, TCP/443 is reachable. ICMP may be filtered. Continue by opening the intended HTTPS service and checking the actual response. Do not claim the whole system is healthy based on that TCP test alone.

## Revision

- Does a different IP subnet automatically create a different Layer 2 broadcast domain? No; VLAN/physical segmentation must correspond to the design.
- Does a VMkernel NIC route guest traffic automatically? No.
- Does a listening socket prove remote reachability? No.
- Can one host have several IP interfaces on different networks? Yes.
- Should a successful test be marked complete without its real output? No.

Keep passwords, private keys and tokens out of captured output and the repository.
