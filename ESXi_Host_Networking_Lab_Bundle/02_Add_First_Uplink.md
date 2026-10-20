# 02 - How to Add the First Uplink

## Study notes

An uplink is an ESXi vmnic assigned to the standard switch. One vmnic cannot simultaneously belong to two standard switches. In your nested lab, ESXi sees virtual Workstation NICs as its physical adapters; their reported 10 Gbit/s is not proof of 10 Gbit/s end-to-end throughput.

Roman Urdu: Uplink switch ka bahar jane wala rasta hai. Nested lab mein yeh Workstation ke virtual adapter se bana hota hai. Asal speed physical NIC, CPU aur doosre resources par depend karegi.

## Task - Attach vmnic4

1. Open Networking -> Physical adapters and verify vmnic4's MAC and current assignment.
2. Confirm its Workstation adapter is connected to LAB-ESXI-NET.
3. Open Virtual switches -> vSwitch-LabNet -> Edit settings.
4. Locate Uplink / Uplinks and choose Add uplink if shown.
5. Select vmnic4. If it is already attached from chapter 01, keep it and continue.
6. Save the settings.
7. Reopen the switch summary and Physical adapters view.

## Expected result

| Check | Expected |
|---|---|
| Physical adapter assignment | vmnic4 -> vSwitch-LabNet |
| Link | Up / connected |
| Switch MTU | 1500 |
| Management access | Still available at 10.0.0.10 |

Capture the switch topology and vmnic4 row. No IP configuration is needed on vmnic4.

## Interpreting Observed IP Ranges

Your teacher's screenshot contains an observed range; your screenshot says No networks. This is traffic-derived information and is separate from configured VMkernel addresses. Do not change an IP merely to populate this column. Use chapter 03's ping test to establish connectivity.

![Teacher observed IP ranges](assets/teacher_observed_ranges.png)

Roman Urdu: Observed IP Ranges adapter ka assigned IP nahi. No networks akela network failure prove nahi karta. vmk ka actual IP VMkernel Adapters mein dekhein.

## Troubleshooting

- Adapter absent in dropdown: check whether another switch already owns it.
- Link down: check Workstation Connected and MAC mapping.
- No external connectivity yet: a switch and uplink alone do not create an IP endpoint; create VMkernel and configure a peer in chapter 03.
