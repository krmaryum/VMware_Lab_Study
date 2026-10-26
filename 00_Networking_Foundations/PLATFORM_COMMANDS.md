# Platform commands: choose the right shell

Run Windows commands on Windows, Bash commands on Linux, and ESXi shell commands on the ESXi host. Connecting over SSH changes the remote environment: a Windows terminal connected to ESXi is executing ESXi commands, not PowerShell.

The examples below inspect information. They do not change addresses, switch configuration, firewall policy or domain membership. Some commands require installed tools or sufficient permissions.

## Windows PowerShell

```powershell
# Addresses, gateway and DNS information
Get-NetIPConfiguration
ipconfig /all

# IPv4 routes
Get-NetRoute -AddressFamily IPv4

# Name resolution
Resolve-DnsName example.com
nslookup example.com

# Test a particular TCP service; substitute the actual target
Test-NetConnection <target-IP-or-name> -Port 443

# Local listening TCP sockets
Get-NetTCPConnection -State Listen

# ICMP and route tracing
ping -n 2 <target-IP-or-name>
tracert <target-IP-or-name>
```

Replace angle-bracket placeholders before running commands. `ping -n 2` is the Windows packet-count form; Linux uses `ping -c 2`. If using `curl` examples in Windows PowerShell 5.1, use `curl.exe` to explicitly select the native curl executable when installed; `curl` may resolve to a PowerShell alias.

## Linux Bash

```bash
ip -br addr
ip route
getent hosts example.com
# dig requires a DNS-tools package on some distributions
dig example.com
ss -tuln
ping -c 2 <target-IP-or-name>
```

Service names vary: OpenSSH commonly uses `ssh` on Ubuntu and `sshd` on Rocky/RHEL. `ssh-copy-id` is a Linux-side convenience tool and is not normally bundled with native Windows OpenSSH. ESXi authorized-key paths also differ from ordinary Linux home directories.

## ESXi: use the Host Client first

In the ESXi Host Client inspect **Networking**, then the relevant virtual switches, port groups, physical NICs and VMkernel NICs. Labels can differ by release. For read-only shell inventory, when SSH is already enabled and authorized:

```sh
esxcli network ip interface ipv4 get
esxcli network ip route ipv4 list
esxcli network nic list
esxcli network vswitch standard list
```

These last commands describe standard switches, not a complete distributed-switch configuration. Do not expect Linux `systemctl`, `apt`, `ss` or Windows `Test-NetConnection` to work in the ESXi shell.

## Interpret evidence carefully

| Evidence | What it shows | What it does not prove |
|---|---|---|
| Ping succeeds | Target responded to ICMP | SSH, HTTPS or login works |
| Ping times out | No ICMP response received | Target is definitely powered off |
| TCP test succeeds | TCP connection to that port succeeded | Application health or correct credentials |
| DNS query succeeds | A DNS response was returned | Address is the intended server or service is reachable |
| Local port listens | A process listens locally | A remote client can reach it through firewalls/routing |

A failed ping alone is not a reason to disable a firewall. Test the intended service and inspect the relevant policy.
