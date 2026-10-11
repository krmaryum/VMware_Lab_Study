# Windows to ESXi: Passwordless SSH and Connection Nicknames

Study guide for bhai's home lab • 10 October 2026

## Contents

1. [What we achieved](#what-we-achieved)
2. [Choose the correct host](#choose-the-correct-host)
3. [Check basic access](#check-basic-access)
4. [Create a dedicated SSH key](#create-a-dedicated-ssh-key)
5. [Install the public key on ESXi](#install-the-public-key-on-esxi)
6. [Test key authentication](#test-key-authentication)
7. [Create a short SSH connection name](#create-a-short-ssh-connection-name)
8. [Repeat for ESXi 8 and the desktop](#repeat-for-esxi-8-and-the-desktop)
9. [Troubleshooting](#troubleshooting)
10. [Final checklist](#final-checklist)

## What we achieved

The Dell ESXi 9 host accepts this command from Windows PowerShell without asking for the ESXi account password:

```powershell
ssh dell-esxi9
```

Two separate features make this possible:

| Feature | Purpose |
| --- | --- |
| SSH key authentication | Authenticate with a private key instead of typing the ESXi account password |
| SSH config Host entry | Supply a nickname, target IP, username, and key path |

The nickname is an OpenSSH configuration entry, not a PowerShell `Set-Alias` and not a DNS record. A nickname by itself does not remove the password prompt.

Verified result: `ssh dell-esxi9` reached `[root@localhost:~]`. The stricter direct-key test also succeeded with account-password fallback disabled. Login after an ESXi reboot has not yet been tested.

## Choose the correct host

| Lab target | Nickname to use | Management IP | Status |
| --- | --- | --- | --- |
| Dell ESXi 9.1 | `dell-esxi9` | `192.168.109.101` | Key authentication and nickname verified |
| Dell ESXi 8 | `dell-esxi8` | Read current DCUI address | Not configured in this SSH session |
| Desktop ESXi 9.1 | `desktop-esxi9` | Last observed `10.0.0.100` via DHCP | SSH key setup not yet performed |
| Desktop ESXi 8 | `desktop-esxi8` | Last observed `10.0.0.138` via DHCP | SSH key setup not yet performed |

Dell ESXi 9 IPv4 screenshot: static address `192.168.109.101`, mask `255.255.255.0`, gateway `192.168.109.1`. The later successful SSH connection confirms access to that address.

Use the current management IP shown in each ESXi console. DHCP addresses may change. A different Windows machine may not have a route to the Dell's `192.168.109.0/24` virtual network; do not assume access simply because both computers are at home.

## Check basic access

Run commands at a **Windows PowerShell** prompt such as `PS C:\Users\krmar>`, not inside the ESXi shell.

If currently at `[root@localhost:~]`, return to Windows:

```sh
exit
```

Check the client and target SSH port:

```powershell
Get-Command ssh
Test-NetConnection 192.168.109.101 -Port 22
```

Expected network result: `TcpTestSucceeded : True`. If SSH is disabled on a new ESXi host, enable its SSH service through Host Client or DCUI Troubleshooting Options before testing.

Test the account password once:

```powershell
ssh root@192.168.109.101
```

On a first connection, SSH asks whether to trust the server's host key. Verify the fingerprint through a trusted host-side source before accepting it. This server host key is separate from the client key we create below. Do not disable host-key checking to avoid the prompt.

Our first password login succeeded. The session displayed the normal ESXi warning that shell commands are logged.

## Create a dedicated SSH key

On Windows:

```powershell
New-Item -ItemType Directory -Force "$HOME\.ssh" | Out-Null
Test-Path "$HOME\.ssh\dell_esxi9"
```

Observed result:

```text
False
```

`$HOME` refers to the current Windows user's home directory. `-Force` lets the directory-creation command reuse an existing directory; `Out-Null` hides its routine output. `Test-Path` checks whether the proposed private-key file already exists.

Because it did not exist, we generated a dedicated RSA 4096-bit key:

```powershell
ssh-keygen -t rsa -b 4096 -f "$HOME\.ssh\dell_esxi9" -C "Dell-ESXi9-lab"
```

| Option | Meaning |
| --- | --- |
| `-t rsa` | Generate an RSA key |
| `-b 4096` | Use a 4096-bit key |
| `-f` | Specify the private-key filename |
| `-C` | Attach a descriptive comment |

If the file already exists, stop and reuse it or choose a different filename. Do not overwrite an existing key without understanding which hosts use it.

The prompts ask for a **key passphrase**, which is different from the ESXi root password. A passphrase protects the private key; an SSH agent can cache its unlock. Pressing Enter twice creates an unencrypted key for a lab with no passphrase prompt. The successful session here did not display a passphrase prompt, but the user's actual passphrase input was not visible.

Observed key generation output:

```text
Your identification has been saved in C:\Users\krmar\.ssh\dell_esxi9
Your public key has been saved in C:\Users\krmar\.ssh\dell_esxi9.pub
The key fingerprint is:
SHA256:NZrzHHpbVf1EeCBXe1WWO+rgDS6g3bX75fqJlOnfF+k Dell-ESXi9-lab
```

| File | Location and treatment |
| --- | --- |
| `dell_esxi9` | Private key; keep on Windows, never upload to ESXi or GitHub |
| `dell_esxi9.pub` | Public key; its content is authorized on ESXi |

Never put passwords, passphrases, or private-key content in commands or study notes.

## Install the public key on ESXi

ESXi root uses this authorization file:

```text
/etc/ssh/keys-root/authorized_keys
```

This differs from the usual Linux user's `~/.ssh/authorized_keys` path. Broadcom documents the ESXi location; its linked article's Environment lists ESXi 7 and 8. We independently verified the procedure on this ESXi 9.1 lab.

### The command we originally ran

```powershell
Get-Content "$HOME\.ssh\dell_esxi9.pub" |
    ssh root@192.168.109.101 'umask 077; mkdir -p /etc/ssh/keys-root; cat >> /etc/ssh/keys-root/authorized_keys; chmod 700 /etc/ssh/keys-root; chmod 600 /etc/ssh/keys-root/authorized_keys'
```

The command asked for the ESXi password and then reported:

```text
chmod: /etc/ssh/keys-root: Operation not permitted
```

The public-key append occurred before the directory-permission operation. Because the remote commands were separated by semicolons, the error did not imply all previous work had failed. We tested authentication before making further changes. The test succeeded, so no additional permissions changes or duplicate upload were needed.

### Recommended command for the next lab

First check that ESXi's root key directory exists, using password access:

```powershell
ssh root@192.168.109.101 'ls -ld /etc/ssh/keys-root'
```

Once that directory exists, append only the public key without attempting to change its directory permissions:

```powershell
Get-Content "$HOME\.ssh\dell_esxi9.pub" |
    ssh root@192.168.109.101 'umask 077; cat >> /etc/ssh/keys-root/authorized_keys'
```

Enter the ESXi account password for this setup step. `Get-Content` reads the public key; SSH sends it to the remote command through standard input; `cat >>` appends it without replacing existing keys. `umask 077` restricts the permissions if a file is newly created; it does not modify permissions on an existing file.

Run an upload once, then test. Repeating the append can create duplicate entries. If the directory is missing or the append fails, inspect the error before continuing.

## Test key authentication

From Windows PowerShell:

```powershell
ssh -i "$HOME\.ssh\dell_esxi9" `
    -o IdentitiesOnly=yes `
    -o PasswordAuthentication=no `
    -o KbdInteractiveAuthentication=no `
    root@192.168.109.101
```

PowerShell's backtick continues the command; do not put trailing spaces after a backtick.

| Option | Purpose |
| --- | --- |
| `-i` | Select the private key on Windows |
| `IdentitiesOnly=yes` | Restrict the identities SSH offers to configured keys |
| `PasswordAuthentication=no` | Disable account-password authentication for this client invocation |
| `KbdInteractiveAuthentication=no` | Prevent keyboard-interactive password fallback for this invocation |

These options do **not** disable password access on the ESXi server. Keeping the account password available provides another way to sign in during the lab.

Our observed result:

```text
The time and date of this login have been sent to the system logs.
...
[root@localhost:~]
```

This proves key authentication succeeded. If a key passphrase is requested, it unlocks the Windows private key and does not mean ESXi password authentication was used.

## Create a short SSH connection name

Return to Windows using `exit`, then open:

```powershell
notepad "$HOME\.ssh\config"
```

Append this block, preserving all existing entries:

```sshconfig
Host dell-esxi9
    HostName 192.168.109.101
    User root
    IdentityFile ~/.ssh/dell_esxi9
    IdentitiesOnly yes
```

Save it as **config**, with no `.txt` extension. In Notepad's Save As dialog, choose All files if necessary.

| Directive | Meaning |
| --- | --- |
| `Host` | Nickname used after `ssh` |
| `HostName` | Actual target IP or resolvable hostname |
| `User` | Remote ESXi account |
| `IdentityFile` | Windows private-key path; `~` means the local user's home |
| `IdentitiesOnly` | Offer configured identities rather than unrelated agent keys |

Test:

```powershell
ssh dell-esxi9
```

Verified session output:

```text
PS C:\Users\krmar> ssh dell-esxi9
The time and date of this login have been sent to the system logs.
...
[root@localhost:~]
```

The nickname and key authentication are both working. Use `exit` to disconnect.

## Repeat for ESXi 8 and the desktop

Use one block per host and a unique nickname. Confirm the target's actual address before editing the config.

### Dell ESXi 8: reusable commands

Set the current address from the ESXi 8 console (replace the text before running):

```powershell
$esxiTarget = "REPLACE_WITH_CURRENT_ESXI8_IP"
$esxiKey = "$HOME\.ssh\dell_esxi8"

Test-NetConnection $esxiTarget -Port 22
Test-Path $esxiKey
```

If the private-key path does not already exist:

```powershell
ssh-keygen -t rsa -b 4096 -f $esxiKey -C "Dell-ESXi8-lab"
```

Check the ESXi directory, then install the new public key:

```powershell
ssh "root@$esxiTarget" 'ls -ld /etc/ssh/keys-root'

Get-Content "${esxiKey}.pub" |
    ssh "root@$esxiTarget" 'umask 077; cat >> /etc/ssh/keys-root/authorized_keys'

ssh -i $esxiKey -o IdentitiesOnly=yes -o PasswordAuthentication=no -o KbdInteractiveAuthentication=no "root@$esxiTarget"
```

After success, append this configuration with the actual IP:

```sshconfig
Host dell-esxi8
    HostName REPLACE_WITH_CURRENT_ESXI8_IP
    User root
    IdentityFile ~/.ssh/dell_esxi8
    IdentitiesOnly yes
```

Test with `ssh dell-esxi8`.

### Desktop targets

Repeat the same workflow with these substitutions:

| Target | `$esxiTarget` after confirming | `$esxiKey` | Key comment / nickname |
| --- | --- | --- | --- |
| Desktop ESXi 9 | `10.0.0.100` | `$HOME\.ssh\desktop_esxi9` | `Desktop-ESXi9-lab` / `desktop-esxi9` |
| Desktop ESXi 8 | `10.0.0.138` | `$HOME\.ssh\desktop_esxi8` | `Desktop-ESXi8-lab` / `desktop-esxi8` |

Example config for the desktop ESXi 9 address last observed:

```sshconfig
Host desktop-esxi9
    HostName 10.0.0.100
    User root
    IdentityFile ~/.ssh/desktop_esxi9
    IdentitiesOnly yes
```

Creating this entry does not authorize a key on the desktop. First generate its key, install that key's public half on the intended host, and pass the direct-key test.

Keys generated on the Dell Windows account are not automatically available on the desktop Windows account. If connecting from a different Windows computer, generate a key there and authorize that computer's public key. You can also use the Dell as a client for the desktop ESXi host if network connectivity permits.

## Troubleshooting

| Symptom | Check / next action |
| --- | --- |
| Port 22 connection fails | Check the current ESXi IP, SSH service, firewall, and network route |
| `chmod ... keys-root: Operation not permitted` | Do not assume the preceding append failed; test key authentication first |
| `Permission denied (publickey)` | Check the selected key, username, correct host, and authorized public-key entry; run `ssh -vvv` for diagnosis |
| Nickname cannot resolve | Verify `$HOME\.ssh\config` exists and was not saved as `config.txt`; check the Host spelling |
| ESXi account password still requested | Check the key path and upload; repeat the strict direct-key test |
| Private-key passphrase requested | Unlock the client key; this differs from the ESXi account password |
| Host identification changed after reinstall | Verify the replacement host's fingerprint before removing the old known_hosts entry |
| Works until host restart | Recheck SSH service state and the authorized key after restart; persistence is not yet verified in this lab |

Inspect config filenames on Windows:

```powershell
Get-ChildItem "$HOME\.ssh" -Filter "config*"
```

Inspect the effective connection settings without connecting:

```powershell
ssh -G dell-esxi9 |
    Select-String '^(hostname|user|identityfile|identitiesonly) '
```

For debugging:

```powershell
ssh -vvv dell-esxi9
```

Do not share private keys or passwords with debug output. Debug logs may contain local paths, usernames, and host addresses.

## Final checklist

- [x] Dell ESXi 9 static IP observed: `192.168.109.101`.
- [x] Initial root SSH login succeeded.
- [x] Dedicated RSA 4096 client key generated.
- [x] Public key installed; directory chmod error recorded.
- [x] Direct-key login succeeded with account-password fallback disabled.
- [x] `dell-esxi9` config nickname worked.
- [ ] Repeat and verify Dell ESXi 8.
- [ ] Repeat and verify desktop hosts.
- [ ] Verify key authentication after an ESXi reboot.

## Reference

Broadcom: Allowing SSH access to VMware vSphere ESXi/ESX hosts with public/private key authentication:

https://knowledge.broadcom.com/external/article/313767/allowing-ssh-access-to-vmware-vsphere-es.html

The successful ESXi 9.1 test in these notes is user-provided evidence. This guide does not require disabling server password authentication, editing `sshd_config`, or restarting SSH services for the verified setup.
