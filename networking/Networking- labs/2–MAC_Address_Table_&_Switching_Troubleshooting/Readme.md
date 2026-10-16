# Lab 02 – MAC Address Table & Switching Troubleshooting

## Objective

Understand how a Layer 2 switch learns MAC addresses, forwards Ethernet frames, and separates network traffic using VLANs. Investigate and resolve a VLAN misconfiguration using Cisco CLI commands.

## Network Topology

- **Switch:** Cisco 2960
- **PC-A:** 192.168.1.10/24 – Fa0/1
- **PC-B:** 192.168.1.20/24 – Fa0/2
- **PC-C:** 192.168.1.30/24 – Fa0/3
- **Software:** Cisco Packet Tracer

## Part 1 – MAC Address Learning

Three PCs were connected to a Cisco 2960 switch using Copper Straight-Through cables.

All PCs were configured in the same IP subnet, and connectivity was verified using ICMP ping tests.

The initial MAC address table contained no dynamic entries. After generating network traffic, the switch learned three dynamic MAC addresses, one for each connected PC.

**Commands used:**
- `show mac address-table`
- `show interfaces status`
- `ping`

**Result:** The switch successfully learned MAC addresses on Fa0/1, Fa0/2 and Fa0/3.

<img width="592" height="314" alt="image" src="https://github.com/user-attachments/assets/7ec1d27d-d021-48c8-981d-e5407dac2d32" />


## Part 2 – ARP & Frame Forwarding

Packet Tracer Simulation Mode was used to observe ARP and ICMP traffic between PC-A and PC-C.

Initially, only ICMP traffic appeared because PC-A already had the destination MAC address in its ARP cache.

After clearing the ARP cache, a new ARP Request was generated.

**Observed traffic:**

1. PC-A sent an ARP Request to discover PC-C's MAC address.
2. The switch flooded the broadcast request to PC-B and PC-C.
3. PC-C responded with an ARP Reply.
4. PC-A and PC-C exchanged ICMP Echo Request and Echo Reply messages.

**Result:** ARP address resolution and Ethernet frame forwarding were successfully observed.

<img width="1702" height="690" alt="image" src="https://github.com/user-attachments/assets/a53208fb-f811-4616-a5ab-2c9815bab997" />


## Part 3 – Incident #003: VLAN Misconfiguration

**Problem:** PC-C could not communicate with PC-A or PC-B.

**Investigation:**

The following commands were used:

- `show vlan brief`
- `show interfaces fa0/3`

The investigation confirmed that Fa0/3 was operational but assigned to VLAN 20, while Fa0/1 and Fa0/2 remained in VLAN 1.

**Root Cause:** Incorrect VLAN assignment isolated PC-C from the other devices at Layer 2.

**Resolution:**

```text
configure terminal
interface fastEthernet 0/3
switchport access vlan 1
end
show vlan brief
```

**Verification:**

- Fa0/3 was successfully reassigned to VLAN 1.
- PC-C → PC-A: 4 replies, 0% packet loss.
- PC-C → PC-B: 4 replies, 0% packet loss.

**Result:** Network connectivity was successfully restored.
<img width="1166" height="1006" alt="image" src="https://github.com/user-attachments/assets/6eac0951-77aa-468e-8bec-b338727d4f8d" />


*Figure 3 – VLAN 20 misconfiguration identified using Cisco CLI.*
<img width="914" height="613" alt="image" src="https://github.com/user-attachments/assets/d05da7ab-fa8b-41f5-8ef3-eabd21401244" />


*Figure 4 – Correct VLAN assignment and successful connectivity tests.*

## Conclusion

This lab demonstrated MAC address learning, ARP address resolution, Ethernet frame forwarding, and VLAN troubleshooting.

The VLAN incident was investigated using Cisco verification commands, and connectivity was restored by correcting the access port configuration.

The exercise reinforced the importance of checking both physical interface status and VLAN membership when troubleshooting Layer 2 connectivity problems.
