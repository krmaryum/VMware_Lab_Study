# DNS03 — DNS Suffix Study Notes

## Concept overview

A primary DNS suffix combines with a host name to form its full computer name, such as DNS03.VCPLAB.Com. It establishes the intended naming context; suffix search settings can also help expand short names. The suffix is configured in Computer Name → Change → More, applied after restart and checked with ipconfig /all. It does not itself create DNS records, create a zone or join Active Directory.


Lab record: 21 October 2026

Configure the primary DNS suffix on DNS03, following your classroom workflow. This record covers only the suffix procedure and its verification. Network values below are reference values observed in your lab; this procedure does not change them.

A primary DNS suffix combines with the host name to form the full computer name: **DNS03 + VCPLAB.Com = DNS03.VCPLAB.Com**. Setting it does not join Active Directory, create a DNS zone, or guarantee that a DNS record exists. DNS03 remains in WORKGROUP.

## Lab reference

| Setting | Value |
| --- | --- |
| Computer | DNS03 |
| Operating system | Windows Server 2025 Standard (Desktop Experience) |
| Primary DNS suffix | VCPLAB.Com |
| Full computer name | DNS03.VCPLAB.Com |
| Membership | WORKGROUP |
| IPv4 address | 192.168.0.123 |
| Subnet mask | 255.255.255.0 |
| Default gateway | Blank |
| Preferred DNS server | 192.168.0.123 |
| Alternate DNS server | 192.168.0.121 |

## Step 1: Open Local Server

On DNS03, open **Server Manager** and click **Local Server** in the left menu.

![Step 1: Open Local Server](screenshots/step_01.png)

## Step 2: Open the computer properties

Under Properties, click the blue **DNS03** computer name.

![Step 2: Open the computer properties](screenshots/step_02.png)

## Step 3: Open Computer Name/Domain Changes

In **System Properties → Computer Name**, click **Change…**.

![Step 3: Open Computer Name/Domain Changes](screenshots/step_03.png)

## Step 4: Open the DNS suffix settings

Keep the computer name **DNS03** and the membership set to **Workgroup: WORKGROUP**. Click **More…**.

![Step 4: Open the DNS suffix settings](screenshots/step_04.png)

## Step 5: Enter the primary DNS suffix

In **Primary DNS suffix of this computer**, enter **VCPLAB.Com**. Keep the NetBIOS computer name **DNS03**. Leave **Change primary DNS suffix when domain membership changes** checked, as shown. Click **OK**, then **OK** in Computer Name/Domain Changes.

![Step 5: Enter the primary DNS suffix](screenshots/step_05.png)

## Step 6: Restart DNS03

A message says that you must restart the computer to apply the changes. Click **OK**, close System Properties, and choose **Restart Now**. Restart the DNS03 virtual machine. Sign back in after it starts.

![Step 6: Restart DNS03](screenshots/step_06.png)

## Step 7: Verify the result

Open Command Prompt or PowerShell inside DNS03 and run:

```powershell
ipconfig /all
```

Confirm **Host Name: DNS03**, **Primary DNS Suffix: VCPLAB.Com**, and **DNS Suffix Search List: VCPLAB.Com**. The last screenshot confirms these values.

![Step 7: Verify the result](screenshots/step_07.png)

## Quick repeat checklist

Server Manager → Local Server → DNS03 → Change → More → VCPLAB.Com → OK → OK → restart DNS03 → `ipconfig /all`.

## If the result is different

- If the suffix is blank, return to **Change → More**, check the entry, apply it, and restart.
- If you see a domain-controller contact error, check whether **Domain** was selected. This suffix procedure uses **WORKGROUP** and **More…**.
- DNS names are case-insensitive: `VCPLAB.Com` and `vcplab.com` refer to the same name.
- Successful suffix verification checks the computer setting; DNS resolution and zones require separate checks.
