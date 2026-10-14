# Lab 01 – Basic Network Connectivity & Troubleshooting

## Objective
Build a small LAN using a Cisco 2960 switch and two PCs, verify network connectivity, identify configuration issues, and restore communication.

## Network Topology
- **Switch:** Cisco 2960 (S1)
- **PC-A:** 192.168.1.10/24 – connected to Fa0/1
- **PC-B:** 192.168.1.20/24 – connected to Fa0/2
- **Network:** 192.168.1.0/24
- **Software:** Cisco Packet Tracer
<img width="853" height="520" alt="image" src="https://github.com/user-attachments/assets/1187e8e2-bf2c-4051-b29b-bfcc2dc2491f" />

## Part 1 – Network Configuration

A basic LAN was created using two PCs connected to a Cisco 2960 switch through Copper Straight-Through cables.

Both PCs were configured with IP addresses in the same subnet. A default gateway was not required because communication remained within the local network.

**Result:** The devices were connected successfully, and the switch interfaces became operational.

## Part 2 – Connectivity Verification

The following commands were used to verify network connectivity and switch operation:

- `ipconfig` – Verified the IP address and subnet mask.
- `ping 192.168.1.20` – Tested communication from PC-A to PC-B.
- `show interfaces status` – Checked the operational state of switch interfaces.
- `show mac address-table` – Verified dynamically learned MAC addresses.

**Findings:**
- PC-A successfully received four ping replies from PC-B.
- Switch interfaces Fa0/1 and Fa0/2 were connected.
- Both interfaces operated in VLAN 1.
- The switch learned two dynamic MAC addresses.
<img width="1151" height="880" alt="image" src="https://github.com/user-attachments/assets/5abea438-e374-4856-aaa3-3ca3a8ac9f71" />



**Result:** Local network connectivity was successfully verified.

## Part 3 – Incident #001: IP Addressing Misconfiguration

**Problem:** PC-A could not communicate with PC-B.

**Investigation:**
- PC-A: 192.168.1.10/24
- PC-B: 192.168.2.20/24
- Ping tests failed.

**Root Cause:** PC-A and PC-B belonged to different IP subnets. The Layer 2 switch could not route traffic between them.

**Resolution:** PC-B was reconfigured with the IP address 192.168.1.20/24.

**OSI Layer:** Layer 3 – Network.

**Result:** The addressing issue was corrected, restoring the intended local network configuration.

## Part 4 – Incident #002: Administratively Disabled Interface

**Problem:** PC-B lost network connectivity.

**Investigation:**
- The ping test from PC-A to PC-B failed.
- `show interfaces status` identified Fa0/2 as disabled.
- The interface had been administratively shut down.

**Root Cause:** Interface Fa0/2 was manually disabled using the `shutdown` command.

**Resolution:**

```text
configure terminal
interface fastEthernet 0/2
no shutdown
end
```

**Verification:**
- Fa0/2 returned to the connected state.
- PC-A successfully received four ping replies from PC-B.

**OSI Layer:** Layer 1 – Physical connectivity, with Layer 2 also affected.

**Result:** Network connectivity was successfully restored.

## Conclusion

This lab demonstrated basic LAN configuration, IP connectivity verification, MAC address learning, and two common network troubleshooting scenarios.

The exercises reinforced the importance of verifying IP configurations, switch interface status, and connectivity before making changes.

Both incidents were successfully diagnosed and resolved.
