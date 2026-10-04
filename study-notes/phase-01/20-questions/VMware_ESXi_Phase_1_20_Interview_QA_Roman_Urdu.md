# VMware / ESXi Phase 1 --- 20 Interview & Self-Test Questions with Answers

> **Language:** Roman Urdu + English technical terms\
> **Focus:** Virtualization, Hypervisors, ESXi Fundamentals, and ESXi
> Networking

## 1. Virtualization kya hai?

**Answer:** Virtualization ek technology hai jis se hum ek physical
server ke CPU, RAM, storage aur networking resources ko use karke
multiple Virtual Machines (VMs) chala sakte hain.

``` text
Physical Server → Hypervisor → VM1 / VM2 / VM3
```

## 2. VM kya hoti hai?

**Answer:** VM yani Virtual Machine ek software-based computer hoti hai.
Iske paas apna vCPU, RAM, virtual disk, vNIC aur operating system ho
sakta hai, lekin underlying resources physical host se aate hain.

## 3. Host aur Guest mein kya difference hai?

**Answer:** Host woh system hai jo virtualization ke liye resources
provide karta hai. Guest woh operating system hai jo VM ke andar run
karta hai.

``` text
Physical PC → Windows (Host) → VMware Workstation → Rocky Linux VM (Guest)
```

## 4. Hypervisor kya karta hai?

**Answer:** Hypervisor physical server ke CPU, RAM, storage aur
networking resources ko manage karta hai aur VMs ko resources allocate
karta hai.

## 5. Type 1 aur Type 2 Hypervisor mein kya difference hai?

**Answer:** Type 1 hypervisor physical hardware par directly run karta
hai. Type 2 hypervisor existing operating system ke upar application ki
tarah run karta hai.

``` text
Type 1: Hardware → ESXi → VMs
Type 2: Hardware → Windows/Linux → VMware Workstation → VMs
```

## 6. ESXi Type 1 hai ya Type 2?

**Answer:** VMware ESXi **Type 1 (Bare-Metal) Hypervisor** hai.

## 7. VMware Workstation Type 1 hai ya Type 2?

**Answer:** VMware Workstation **Type 2 Hypervisor** hai kyun ke ye host
operating system ke upar run karta hai.

## 8. ESX aur ESXi mein basic difference kya hai?

**Answer:** ESX VMware ka older hypervisor architecture tha jisme
traditional Linux-based Service Console included thi. ESXi newer,
purpose-built architecture hai jisme traditional ESX Service Console
nahi hoti.

``` text
ESX  = Older architecture + traditional Service Console
ESXi = Newer purpose-built architecture
```

## 9. DCUI kya hai?

**Answer:** DCUI ka full form **Direct Console User Interface** hai. Ye
ESXi host ki local console interface hai jahan se management IP, subnet
mask, gateway, DNS, hostname aur troubleshooting options configure kiye
ja sakte hain.

## 10. VMkernel kya hai?

**Answer:** VMkernel ESXi ka core operating component/kernel hai. Ye CPU
scheduling, memory management, storage, networking aur VMs ke
hardware-resource access jaise important functions handle karta hai.

> **Easy memory:** VMkernel = ESXi ka core.

## 11. vSphere Standard Switch kya hai?

**Answer:** vSphere Standard Switch (vSS) ESXi host ke andar ek
software-based Layer 2 virtual switch hai. Ye VMs aur VMkernel
networking ko connectivity provide karta hai.

``` text
VMs → Port Group → vSwitch → vmnic → Physical Network
```

## 12. vNIC kya hai?

**Answer:** vNIC yani **Virtual Network Interface Card**, VM ka virtual
network adapter hota hai.

``` text
VM → vNIC → Port Group
```

## 13. vmnic0 kya represent karta hai?

**Answer:** `vmnic0` ESXi mein host ki physical network adapter/uplink
ko represent karta hai.

``` text
vSwitch0 → vmnic0 → Physical Switch
```

## 14. VM Port Group ka purpose kya hai?

**Answer:** VM Port Group ka purpose VMs ko network connectivity aur
common network configuration/policies provide karna hai. Port Group par
VLAN aur networking policies configure ki ja sakti hain.

## 15. VMkernel Port ka purpose kya hai?

**Answer:** VMkernel Port/adapter ESXi host ke apne network traffic aur
services ke liye use hota hai, jaise management, vMotion aur
configuration ke mutabiq certain storage traffic.

Common names: `vmk0`, `vmk1`, `vmk2`.

## 16. VM Port Group aur VMkernel Port mein difference kya hai?

**Answer:**

  -----------------------------------------------------------------------
  VM Port Group                       VMkernel Port
  ----------------------------------- -----------------------------------
  VM traffic ke liye                  ESXi host/service traffic ke liye

  VM ka vNIC connect hota hai         VMkernel adapter (`vmk`) use hota
                                      hai

  Example: VM Network                 Example: Management Network

  Guest workload networking           Management, vMotion,
                                      storage-related host traffic, etc.
  -----------------------------------------------------------------------

### Golden Rule

``` text
VM ko network chahiye            → VM PORT GROUP
ESXi host/service ko network chahiye → VMKERNEL PORT
```

## 17. NIC Teaming kyun use karte hain?

**Answer:** NIC Teaming multiple physical NIC uplinks ko use karke
redundancy/failover provide kar sakti hai aur configured teaming policy
ke mutabiq traffic distribution mein help kar sakti hai.

``` text
             vSwitch0
              /              vmnic0   vmnic1
             ↓       ↓
          Physical Network
```

## 18. Traffic Shaping kya control karti hai?

**Answer:** Traffic Shaping network traffic ki bandwidth/rate ko policy
ke mutabiq control karti hai.

Important terms: - Average Bandwidth - Peak Bandwidth - Burst Size

## 19. ESXi host ko browser se access karne ke liye management networking kyun zaroori hai?

**Answer:** Administrator PC aur ESXi host network ke through
communicate karte hain. Isliye ESXi ko proper management IP address,
subnet configuration aur network connectivity chahiye. Routing ki
zarurat ho to gateway important hota hai; DNS hostname-based name
resolution ke liye useful hota hai.

``` text
Admin PC → Physical Network → vmnic → vSwitch → Management Network → vmk0 → ESXi
```

Example:

``` text
https://192.168.1.50
```

## 20. Ek VM ka traffic physical network tak kis path se ja sakta hai?

**Answer:** Typical traffic flow:

``` text
VM
 ↓
vNIC
 ↓
VM Port Group
 ↓
vSwitch
 ↓
vmnic
 ↓
Physical Switch
 ↓
Physical Network
```

# Quick Interview Revision

``` text
ESXi            = Type 1 Hypervisor
VMware Workstation = Type 2 Hypervisor
vNIC            = VM ka virtual NIC
vmnic           = ESXi host ka physical NIC/uplink
vSwitch         = ESXi ka virtual Layer-2 switch
VM Port Group   = VM traffic
VMkernel Port   = ESXi host/service traffic
NIC Teaming     = Multiple uplinks / redundancy
Traffic Shaping = Bandwidth/rate control
```

# Self-Test Checklist

-   [ ] Virtualization
-   [ ] VM
-   [ ] Host vs Guest
-   [ ] Hypervisor
-   [ ] Type 1 vs Type 2
-   [ ] ESXi
-   [ ] ESX vs ESXi
-   [ ] DCUI
-   [ ] VMkernel
-   [ ] vSphere Standard Switch
-   [ ] vNIC
-   [ ] vmnic
-   [ ] VM Port Group
-   [ ] VMkernel Port
-   [ ] NIC Teaming
-   [ ] Traffic Shaping
-   [ ] Complete VM-to-physical-network traffic flow

**Phase 1 Interview & Self-Test Q&A Complete**
