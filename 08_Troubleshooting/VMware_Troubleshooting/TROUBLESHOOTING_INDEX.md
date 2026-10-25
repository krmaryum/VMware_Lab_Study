# Troubleshooting Index

These are general diagnostic playbooks, not all confirmed issues in the classmate’s case.

## 01_CPU_Virtualization: 01 CPU Virtualization

**Symptom:** CPU virtualization unavailable.

**Possible causes:** BIOS/UEFI VT-x or AMD-V disabled; host hypervisor interference.

**Troubleshooting:** Check Task Manager > Performance > CPU > Virtualization; enable virtualization in firmware; reboot.

**Verify:** Confirm virtualization is enabled and VM starts.

## 03_HyperV_VBS: 03 HyperV VBS

**Symptom:** Hyper-V / VBS compatibility.

**Possible causes:** Windows Hyper-V, VBS or Memory Integrity may affect nested virtualization on some Workstation/host combinations.

**Troubleshooting:** Check msinfo32 and Windows Features; update Workstation; evaluate Hyper-V/VBS settings before changes. Do not disable security features without approval..

**Verify:** Check VMware VM boot and vmware.log.

## 04_ESXi_Boot: 04 ESXi Boot

**Symptom:** ESXi installer boot stalls.

**Possible causes:** Insufficient resources, ISO issue, incompatible virtual hardware, or nested virtualization disabled.

**Troubleshooting:** Verify ISO checksum, VM guest type, RAM, CPU and virtualization exposure; inspect vmware.log.

**Verify:** Installer reaches interactive setup.

## 05_VM_Network: 05 VM Network

**Symptom:** VM cannot reach network.

**Possible causes:** Wrong VMnet, disconnected adapter, DHCP failure or firewall.

**Troubleshooting:** Inspect virtual NIC Connected setting, ipconfig /all or ip addr, VMware Virtual Network Editor.

**Verify:** Ping gateway and test DNS separately.

## 06_NAT_Bridged_HostOnly: 06 NAT Bridged HostOnly

**Symptom:** NAT vs Bridged vs Host-only.

**Possible causes:** Network mode does not match desired reachability.

**Troubleshooting:** NAT for outbound through host; Bridged for LAN participation; Host-only for isolated host-to-guest lab.

**Verify:** Verify guest address, gateway and routes.

## 07_ESXi_Management: 07 ESXi Management

**Symptom:** ESXi web management inaccessible.

**Possible causes:** Incorrect vmk0 IP, VLAN, uplink, port group, firewall or physical path.

**Troubleshooting:** Use ESXi DCUI to inspect management network, test network and validate uplink/port group.

**Verify:** Open https://<management-ip>/ui from reachable client.

## 08_Resources: 08 Resources

**Symptom:** VM slow or will not start.

**Possible causes:** Memory pressure, CPU oversubscription or storage latency.

**Troubleshooting:** Check host available RAM, VM allocation, CPU usage and free disk space.

**Verify:** VM boots and performs without excessive swapping.

## 09_Disk_Datastore: 09 Disk Datastore

**Symptom:** Virtual disk or datastore errors.

**Possible causes:** Disk full, snapshot growth, unsupported storage, or missing VMDK.

**Troubleshooting:** Check host free space, snapshot manager, datastore capacity and VM configuration; back up before disk operations.

**Verify:** VM disks accessible without warnings.

## 10_Logs: 10 Logs

**Symptom:** Workstation diagnostics.

**Possible causes:** Generic errors hide the underlying cause.

**Troubleshooting:** Power off VM; find vmware.log in VM directory; search for error, failed, HV, VT-x; record exact timestamp and message.

**Verify:** Evidence and reproducible steps documented.

