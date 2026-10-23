# Traffic Shaping — Bandwidth Policy aur Measurement

> **Addressing ki correction:** Recorded STORAGE IP 172.16.0.102/16 hai. Proposed 172.16.50.0/24 usi /16 ke andar hai, is liye overlap hai. Neechay 172.16.50.x ko unexecuted source example samjhein. Implementation se pehle verified unused aur non-overlapping subnet choose karke tamam endpoints/interfaces ki values consistently replace hoti hain. Example match karne ke liye existing management/storage IP nahin badalni.


> **Status: proposed performance exercise.** Captured Stage 1 lab mein successful shaping benchmark recorded nahin. Yeh repeatable follow-up hai; values expected settings hain, measured results nahin. Isolated network ki preparation aur complete original English procedure optional source mein hai.

## Concept aur purpose

Traffic shaping transmission rate ko policy ke mutabiq limit karta hai. Standard switch ka shaping yahan VM se nikalne wali outbound traffic ke liye hai. Direction clear rakhna zaroori hai; “switch outbound” ko bina endpoint bataye samajhna confusing ho sakta hai. Distributed switches par capabilities alag hoti hain.

| Parameter | Matlab | Proposed lab value |
|---|---|---|
| Average bandwidth | Sustained allowed rate | 10000 Kbit/s, taqreeban 10 Mbit/s |
| Peak bandwidth | Burst ke waqt maximum configured rate | 20000 Kbit/s, taqreeban 20 Mbit/s |
| Burst size | Temporary burst ke liye data budget | 1024 KB, UI ki actual unit confirm honi hai |

Burst size rate nahin, data ki quantity hai. Average ping ke liye aur peak HTTP/AD ke liye fixed categories nahin. Shaping traffic ka consumption cap karta hai; minimum bandwidth reserve, slow path ko fast ya performance guarantee nahin karta. API aur CLI units UI se mukhtalif ho sakti hain; numbers bina units check kiye copy nahin hotay.

## Test topology aur prerequisites

VM-A ESXi ke andar `PG-Lab-VMs` par `172.16.50.20/24` hai. Separate Linux endpoint Workstation mein directly `LAB-ESXI-NET` par `172.16.50.30/24` hai. ESXi ka proposed lab switch `vSwitch-LabNet` hai. Dono peers ki ping pehle successful honi chahiye. Yeh existing STORAGE subnet se alag proposed network hai.

Dono Linux guests mein iperf3 chahiye; ESXi mein install nahin karna. Guest ke repository access ke liye alag NAT interface ho to test traffic ko explicit lab IP par bind kiya jata hai.

Ubuntu/Debian guest example:

```bash
sudo apt update
sudo apt install iperf3
```

Rocky/RHEL-family guest example, jab suitable repository ho:

```bash
sudo dnf install iperf3
```

Endpoint firewall mein required lab source/interface ke liye TCP 5201 allow hota hai. Poori firewall disable karna zaroori nahin. Firewalld mein pehle lab interface ki actual zone identify hoti hai. Temporary exception ka record aur baad mein removal hota hai.

## Baseline measurement

Pehle switch aur VM port group ki current shaping policy record hoti hai. Test baseline ko affect karne wali lab policy temporarily remove/disable ho; management ki policy nahin badalni.

Endpoint `.30` par:

```bash
iperf3 -s -B 172.16.50.30
```

VM-A par:

```bash
iperf3 -c 172.16.50.30 -B 172.16.50.20 -t 30
```

Sender aur receiver rate record hoti hai. Baseline 10 Mbit/s se zyada ho tab proposed cap ka effect wazeh ho sakta hai. Baseline hi kam ho to CPU/outer path/endpoint bottleneck diagnose ya suitable lower supported cap choose hota hai.

## Sirf test VM group ki policy

1. Networking → Port groups → PG-Lab-VMs → Edit settings → Traffic shaping.
2. Override / Override switch settings, agar UI mein ho, enable hota hai.
3. Shaping Enabled; Average 10000 Kbit/s; Peak 20000 Kbit/s; Burst 1024 KB, actual displayed units ke mutabiq.
4. Save karke dobara reopen aur persistence check hoti hai.
5. Management aur PG-Lab-VMkernel unchanged rehte hain.

Agar UI switch level par setting deta hai to Virtual switches → vSwitch-LabNet → Edit settings → Traffic shaping route ho sakta hai. Is se inheriting groups bhi affect honge; scope record karna zaroori hai. VM-group override is exercise ke liye zyada focused hai.

## Shaped transmission ka test

VM-A se:

```bash
iperf3 -c 172.16.50.30 -B 172.16.50.20 -t 60 -i 1
```

Shaping limiting factor ho to long-run rate taqreeban 10 Mbit/s ke qareeb ho sakti hai. Protocol overhead aur implementation ki wajah se exact number guaranteed nahin. Har pehla interval 20 Mbit/s pohanchna bhi lazmi nahin. Early burst aur sustained rate alag observe hotay hain.

Optional reverse test:

```bash
iperf3 -c 172.16.50.30 -B 172.16.50.20 -t 30 -R
```

Is mein bulk data endpoint se VM-A ki taraf hai. Sirf VM-A outbound policy honay par wohi bulk-data cap lazmi nahin. TCP acknowledgments, CPU aur doosray bottlenecks phir bhi result affect karte hain.

## Restore aur comparison

PG-Lab-VMs ki shaping override disable ya pehli policy restore hoti hai. Agar parent switch shaping enabled hai to inheritance par wapas jana shaping band karne ke barabar nahin; dono levels inspect hon. Original 30-second test dobara run aur recovery compare hoti hai. Endpoint server Ctrl+C se band aur temporary firewall exception remove hoti hai.

| Test | Actual measured Mbit/s | Status |
|---|---|---|
| Baseline, VM-A sends | Abhi measure nahin | Pending |
| Shaped, VM-A sends | Abhi measure nahin | Pending |
| Reverse, optional | Abhi measure nahin | Pending |
| Restored baseline | Abhi measure nahin | Pending |

## Agar cap nazar na aaye

Wrong VM port group, wrong direction, inherited override aur baseline pehle se kam honay ko check karein. Same-host local test aur external endpoint test aik jaisay nahin. Ping reachability batati hai, bandwidth nahin. Policy screenshot aur actual baseline/shaped/restored outputs mil kar meaningful evidence banate hain.

**Revision sawal:** Burst size 1024 KB ko 1024 Kbit/s samajhna sahi hai?

**Jawab:** Nahin. Pehla data budget hai, doosra rate. Units ke baghair comparison ghalat hoga.
