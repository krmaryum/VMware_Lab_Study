# Windows se ESXi: Passwordless SSH aur Connection Nicknames

Bhai ke home lab ke study notes — 10 October 2026

## Index

1. [Hum ne kya hasil kiya](#hum-ne-kya-hasil-kiya)
2. [Sahi host select karein](#sahi-host-select-karein)
3. [Basic access check karein](#basic-access-check-karein)
4. [Dedicated SSH key banayein](#dedicated-ssh-key-banayein)
5. [Public key ESXi par add karein](#public-key-esxi-par-add-karein)
6. [Key authentication test karein](#key-authentication-test-karein)
7. [Chhota SSH connection naam banayein](#chhota-ssh-connection-naam-banayein)
8. [ESXi 8 aur desktop par repeat karein](#esxi-8-aur-desktop-par-repeat-karein)
9. [Troubleshooting](#troubleshooting)
10. [Final checklist](#final-checklist)

## Hum ne kya hasil kiya

Dell ke ESXi 9 host par Windows PowerShell se yeh command chalane par ESXi account ka password nahin poocha gaya:

```powershell
ssh dell-esxi9
```

Is mein do alag cheezein kaam kar rahi hain:

| Cheez | Kaam |
| --- | --- |
| SSH key authentication | ESXi account ka password type karne ke bajaye private key se pehchan verify hoti hai |
| SSH config ka Host entry | Chhote naam ke andar IP, username aur key ka path define hota hai |

Yeh nickname OpenSSH ki configuration hai. Yeh PowerShell ka `Set-Alias` ya DNS record nahin hai. Sirf nickname banane se passwordless login nahin hota; public key bhi target par authorize karni hoti hai.

**Verified result:** `ssh dell-esxi9` chalane se `[root@localhost:~]` prompt mil gaya. Direct key test bhi successful tha, jis mein account-password fallback band tha. ESXi reboot ke baad key login abhi test nahin kiya gaya.

## Sahi host select karein

| Lab host | SSH nickname | Management IP | Status |
| --- | --- | --- | --- |
| Dell ESXi 9.1 | `dell-esxi9` | `192.168.109.101` | Key login aur nickname dono verified |
| Dell ESXi 8 | `dell-esxi8` | Current DCUI se check karein | Is SSH session mein setup nahin hua |
| Desktop ESXi 9.1 | `desktop-esxi9` | Aakhri observed IP `10.0.0.100`, DHCP | Key setup abhi nahin hua |
| Desktop ESXi 8 | `desktop-esxi8` | Aakhri observed IP `10.0.0.138`, DHCP | Key setup abhi nahin hua |

Dell ESXi 9 ke screenshot mein static IP `192.168.109.101`, subnet mask `255.255.255.0` aur gateway `192.168.109.1` tha. Baad mein successful SSH login se isi IP tak access confirm hua.

Har host ka current management IP uski ESXi console se check karein. DHCP address badal sakta hai. Dell ka `192.168.109.0/24` virtual network kisi doosre Windows computer se automatically reachable nahin hota. Dono computers ghar mein hone ka matlab yeh nahin ke har virtual network bhi reachable hai.

## Basic access check karein

Windows wali commands **PowerShell** mein chalani hain, jahan prompt `PS C:\Users\krmar>` jaisa ho. ESXi shell ke andar yeh PowerShell commands na chalayein.

Agar `[root@localhost:~]` prompt par hain, Windows par wapas aane ke liye:

```sh
exit
```

SSH client aur target ka port check karein:

```powershell
Get-Command ssh
Test-NetConnection 192.168.109.101 -Port 22
```

Expected result: `TcpTestSucceeded : True`. Naye ESXi host par SSH service disabled ho to Host Client ya DCUI ke Troubleshooting Options se enable karein, phir test karein.

Pehle account password ke saath basic login check karein:

```powershell
ssh root@192.168.109.101
```

Pehli connection par SSH server ki host key trust karne ka sawal poochta hai. Fingerprint ko trusted host-side source se verify karke accept karein. Server ki host key aur neeche banne wali client key alag cheezein hain. Prompt se bachne ke liye host-key checking disable na karein.

Hamare lab mein pehla password login successful hua. ESXi ne shell commands log hone ka normal warning message dikhaya.

## Dedicated SSH key banayein

Windows PowerShell mein:

```powershell
New-Item -ItemType Directory -Force "$HOME\.ssh" | Out-Null
Test-Path "$HOME\.ssh\dell_esxi9"
```

Hamare lab ka output:

```text
False
```

Explanation:

- `$HOME` current Windows user ka home folder hai.
- `New-Item` `.ssh` directory banata hai.
- `-Force` existing directory ko reuse karne deta hai.
- `Out-Null` routine output hide karta hai.
- `Test-Path` check karta hai ke proposed private-key file pehle se maujood hai ya nahin.

File maujood nahin thi, is liye RSA 4096-bit key banayi:

```powershell
ssh-keygen -t rsa -b 4096 -f "$HOME\.ssh\dell_esxi9" -C "Dell-ESXi9-lab"
```

| Option | Matlab |
| --- | --- |
| `-t rsa` | RSA type ki key banayein |
| `-b 4096` | Key ki length 4096 bits rakhein |
| `-f` | Private key ka filename aur path |
| `-C` | Key ke liye pehchan wala comment |

Agar file pehle se maujood ho, usi key ko reuse karein ya naya filename choose karein. Yeh samjhe baghair existing key overwrite na karein ke kin hosts par woh use hoti hai.

Key banate waqt **passphrase** ka prompt aata hai. Yeh ESXi root password se alag hai. Passphrase private key ko protect karta hai; SSH agent uski unlock state cache kar sakta hai. Lab mein dono prompts par Enter dabane se key bina passphrase ke banti hai. Hamare successful login mein passphrase prompt nazar nahin aaya, lekin user ne generation ke waqt kya input diya tha woh visible nahin tha.

Observed output:

```text
Your identification has been saved in C:\Users\krmar\.ssh\dell_esxi9
Your public key has been saved in C:\Users\krmar\.ssh\dell_esxi9.pub
The key fingerprint is:
SHA256:NZrzHHpbVf1EeCBXe1WWO+rgDS6g3bX75fqJlOnfF+k Dell-ESXi9-lab
```

| File | Kahan rehni chahiye aur kaise use hogi |
| --- | --- |
| `dell_esxi9` | Private key; Windows par rakhein, ESXi ya GitHub par upload na karein |
| `dell_esxi9.pub` | Public key; iska content ESXi par authorize hota hai |

Password, passphrase ya private-key content commands aur study notes mein na likhein.

## Public key ESXi par add karein

ESXi root ki authorized-key file:

```text
/etc/ssh/keys-root/authorized_keys
```

Yeh aam Linux user ke `~/.ssh/authorized_keys` path se different hai. Broadcom ke reference mein ESXi ka yeh path documented hai. Linked article ki Environment ESXi 7 aur 8 list karti hai; hamare ESXi 9.1 lab mein procedure successful test hua.

### Jo command hum ne pehle chalayi

```powershell
Get-Content "$HOME\.ssh\dell_esxi9.pub" |
    ssh root@192.168.109.101 'umask 077; mkdir -p /etc/ssh/keys-root; cat >> /etc/ssh/keys-root/authorized_keys; chmod 700 /etc/ssh/keys-root; chmod 600 /etc/ssh/keys-root/authorized_keys'
```

ESXi password dene ke baad yeh error aaya:

```text
chmod: /etc/ssh/keys-root: Operation not permitted
```

**Samajhne wali baat:** public key append karne ka command directory permissions badalne se pehle chala tha. Commands semicolon se separate thin, is liye ek error ka matlab yeh nahin ke pehle ke tamam steps fail ho gaye.

Hum ne dobara key upload ya aur permissions changes karne se pehle login test kiya. Test successful hua. Is liye koi additional permissions change zaroori nahin thi aur duplicate key add nahin ki.

### Agle lab ke liye behtar command

Password access se pehle check karein ke root key directory maujood hai:

```powershell
ssh root@192.168.109.101 'ls -ld /etc/ssh/keys-root'
```

Directory maujood ho to public key append karein; directory ka chmod na karein:

```powershell
Get-Content "$HOME\.ssh\dell_esxi9.pub" |
    ssh root@192.168.109.101 'umask 077; cat >> /etc/ssh/keys-root/authorized_keys'
```

Is setup step par ESXi account password dena hoga.

| Hissa | Kaam |
| --- | --- |
| `Get-Content` | Windows ki public-key file read karta hai |
| PowerShell pipe `\|` | Public-key text SSH command ko deta hai |
| `ssh ... '...'` | ESXi par quoted command chalata hai |
| `cat >>` | Existing keys replace kiye baghair nayi key append karta hai |
| `umask 077` | Nayi file bane to restrictive permissions use hoti hain; existing file ki permissions nahin badaltin |

Upload ek martaba karein, phir test karein. Baar baar append karne se duplicate entries ban sakti hain. Directory missing ho ya append fail ho to error inspect karein, phir aage chalein.

## Key authentication test karein

Windows PowerShell se:

```powershell
ssh -i "$HOME\.ssh\dell_esxi9" `
    -o IdentitiesOnly=yes `
    -o PasswordAuthentication=no `
    -o KbdInteractiveAuthentication=no `
    root@192.168.109.101
```

PowerShell ka backtick command ko next line par continue karta hai. Backtick ke baad trailing spaces na hon.

| Option | Kaam |
| --- | --- |
| `-i` | Windows ki private key select karta hai |
| `IdentitiesOnly=yes` | SSH ko configured identities tak restrict karta hai |
| `PasswordAuthentication=no` | Is client command ke liye account-password authentication band karta hai |
| `KbdInteractiveAuthentication=no` | Is command mein keyboard-interactive password fallback rokta hai |

Yeh client options ESXi server par password login disable **nahin** karte. Lab ke dauran root password ka access alternative ke taur par available reh sakta hai.

Hamare lab ka result:

```text
The time and date of this login have been sent to the system logs.
...
[root@localhost:~]
```

Is test se key authentication confirm hui. Agar private-key passphrase poocha jaye to woh Windows wali key unlock karta hai; uska matlab ESXi account password use hona nahin hai.

## Chhota SSH connection naam banayein

`exit` se Windows par wapas aayein, phir:

```powershell
notepad "$HOME\.ssh\config"
```

Existing entries ko preserve karte hue yeh block add karein:

```sshconfig
Host dell-esxi9
    HostName 192.168.109.101
    User root
    IdentityFile ~/.ssh/dell_esxi9
    IdentitiesOnly yes
```

Filename **config** hona chahiye, `config.txt` nahin. Notepad ke Save As mein Demand ho to All files select karein.

| Setting | Matlab |
| --- | --- |
| `Host` | Woh nickname jo `ssh` ke baad likhein ge |
| `HostName` | Asal IP ya resolve hone wala hostname |
| `User` | ESXi ka remote account |
| `IdentityFile` | Windows par private key ka path; `~` local home folder hai |
| `IdentitiesOnly` | Configured identities offer karein; unrelated agent keys avoid hoti hain |

Ab test karein:

```powershell
ssh dell-esxi9
```

Verified output:

```text
PS C:\Users\krmar> ssh dell-esxi9
The time and date of this login have been sent to the system logs.
...
[root@localhost:~]
```

**Nickname aur passwordless key login dono successful hain.** Disconnect karne ke liye `exit` chalayein.

## ESXi 8 aur desktop par repeat karein

Har host ka alag config block aur unique nickname rakhein. Pehle actual current IP verify karein.

### Dell ESXi 8 ke reusable steps

ESXi 8 console se IP dekh kar neeche placeholder replace karein:

```powershell
$esxiTarget = "REPLACE_WITH_CURRENT_ESXI8_IP"
$esxiKey = "$HOME\.ssh\dell_esxi8"

Test-NetConnection $esxiTarget -Port 22
Test-Path $esxiKey
```

Private-key file pehle se maujood na ho to:

```powershell
ssh-keygen -t rsa -b 4096 -f $esxiKey -C "Dell-ESXi8-lab"
```

ESXi ki directory check karein, phir nayi public key authorize karein:

```powershell
ssh "root@$esxiTarget" 'ls -ld /etc/ssh/keys-root'

Get-Content "${esxiKey}.pub" |
    ssh "root@$esxiTarget" 'umask 077; cat >> /etc/ssh/keys-root/authorized_keys'

ssh -i $esxiKey -o IdentitiesOnly=yes -o PasswordAuthentication=no -o KbdInteractiveAuthentication=no "root@$esxiTarget"
```

Test successful ho to config mein actual IP ke saath add karein:

```sshconfig
Host dell-esxi8
    HostName REPLACE_WITH_CURRENT_ESXI8_IP
    User root
    IdentityFile ~/.ssh/dell_esxi8
    IdentitiesOnly yes
```

Final test: `ssh dell-esxi8`.

### Desktop ke hosts

Upar wala workflow in values ke saath repeat karein:

| Host | `$esxiTarget`, current IP confirm karke | `$esxiKey` | Key comment / nickname |
| --- | --- | --- | --- |
| Desktop ESXi 9 | `10.0.0.100` | `$HOME\.ssh\desktop_esxi9` | `Desktop-ESXi9-lab` / `desktop-esxi9` |
| Desktop ESXi 8 | `10.0.0.138` | `$HOME\.ssh\desktop_esxi8` | `Desktop-ESXi8-lab` / `desktop-esxi8` |

Desktop ESXi 9 ka example config, aakhri observed IP ke saath:

```sshconfig
Host desktop-esxi9
    HostName 10.0.0.100
    User root
    IdentityFile ~/.ssh/desktop_esxi9
    IdentitiesOnly yes
```

Sirf yeh block likhne se desktop par key authorize nahin hoti. Pehle key generate karein, uski public key sahi ESXi host par add karein, phir direct key test successful hona chahiye.

Dell ke Windows account par banayi key desktop ke Windows account par automatically available nahin hoti. Doosre Windows computer se connect karna ho to wahan key generate karke uski public key target par authorize karein. Network access ho to Dell ko client bana kar desktop ke ESXi se bhi connect kar sakte hain.

## Troubleshooting

| Masla | Kya check karein |
| --- | --- |
| Port 22 connection fail | Current ESXi IP, SSH service, firewall aur network route |
| `chmod ... keys-root: Operation not permitted` | Pehle key login test karein; pehle ka append successful ho sakta hai |
| `Permission denied (publickey)` | Key path, user, sahi host aur authorized public-key entry; diagnosis ke liye `ssh -vvv` |
| Nickname resolve nahin hota | `$HOME\.ssh\config` file aur Host spelling; `config.txt` to nahin? |
| ESXi account password poochta hai | Key path aur upload check karein; strict direct-key test chalayein |
| Private-key passphrase poochta hai | Client key unlock karein; yeh ESXi account password se alag hai |
| Reinstall ke baad host identification changed | Naye host ka fingerprint verify karein, phir purani known_hosts entry remove karne ka faisla karein |
| Reboot ke baad login fail | SSH service aur authorized key dobara check karein; is lab mein reboot persistence verify nahin hui |

Windows par config filenames check karein:

```powershell
Get-ChildItem "$HOME\.ssh" -Filter "config*"
```

Connect kiye baghair effective configuration dekhein:

```powershell
ssh -G dell-esxi9 |
    Select-String '^(hostname|user|identityfile|identitiesonly) '
```

Detailed debugging:

```powershell
ssh -vvv dell-esxi9
```

Debug output ke saath private key ya passwords share na karein. Logs mein local paths, usernames aur host IPs ho sakte hain.

## Final checklist

- [x] Dell ESXi 9 static IP observed: `192.168.109.101`.
- [x] Pehla root SSH password login successful.
- [x] Dedicated RSA 4096 client key generated.
- [x] Public key add hui; directory chmod error record hua.
- [x] Password fallback disabled hone ke bawajood direct-key login successful.
- [x] `dell-esxi9` nickname successful.
- [ ] Dell ESXi 8 par repeat aur verify karna hai.
- [ ] Desktop hosts par repeat aur verify karna hai.
- [ ] ESXi reboot ke baad key login verify karna hai.

## Reference

Broadcom: Allowing SSH access to VMware vSphere ESXi/ESX hosts with public/private key authentication:

https://knowledge.broadcom.com/external/article/313767/allowing-ssh-access-to-vmware-vsphere-es.html

In notes mein ESXi 9.1 ki success user ke actual output se verified hai. Verified setup ke liye server password authentication disable karne, `sshd_config` edit karne ya SSH service restart karne ki Demand nahin padi.
