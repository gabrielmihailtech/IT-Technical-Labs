# Lab 03 – VLAN Configuration & Switching Troubleshooting

## Exercise Brief

### Scenario

A company has two departments, Sales and Finance, connected to a Cisco 2960 switch.

The Sales department uses VLAN 10, while the Finance department uses VLAN 20.

Each department must be able to communicate internally, but communication between the two departments is not required.

The network administrator must configure the VLANs, assign the appropriate switch ports, verify connectivity, and troubleshoot a VLAN misconfiguration affecting one of the computers.

### Objectives

- Create and configure VLANs on a Cisco switch.
- Assign multiple access ports to different VLANs.
- Verify VLAN membership using Cisco IOS commands.
- Test connectivity between devices in the same and different VLANs.
- Identify and correct an incorrect VLAN assignment.
- Verify that network connectivity has been restored.

### Equipment

- Cisco Packet Tracer
- 1 × Cisco Catalyst 2960 switch
- 4 × PC-PT computers
- Copper Straight-Through cables

<img width="972" height="474" alt="image" src="https://github.com/user-attachments/assets/084152d8-f133-46fd-9fde-ac51a3330158" />

---

## Network Topology

All four PCs are connected to the same Cisco 2960 switch.

**Sales – VLAN 10**
- PC-A → Switch S1 Fa0/1
- PC-C → Switch S1 Fa0/3

**Finance – VLAN 20**
- PC-B → Switch S1 Fa0/2
- PC-D → Switch S1 Fa0/4

### IP Addressing Table

| Device | IP Address | Subnet Mask | VLAN | Switch Port |
|---|---|---|---|---|
| PC-A | 192.168.10.10 | 255.255.255.0 | 10 | Fa0/1 |
| PC-B | 192.168.20.10 | 255.255.255.0 | 20 | Fa0/2 |
| PC-C | 192.168.10.20 | 255.255.255.0 | 10 | Fa0/3 |
| PC-D | 192.168.20.20 | 255.255.255.0 | 20 | Fa0/4 |

A default gateway is not required because inter-VLAN routing is not configured.

---

# Part 1 – VLAN Configuration

## Exercise Requirements

1. Build the network topology in Cisco Packet Tracer using one switch and four PCs.
2. Configure each PC with the IP address and subnet mask specified in the addressing table.
3. Create VLAN 10 and name it `Sales`.
4. Create VLAN 20 and name it `Finance`.
5. Assign interfaces Fa0/1 and Fa0/3 to VLAN 10.
6. Assign interfaces Fa0/2 and Fa0/4 to VLAN 20.
7. Verify that the VLANs and access port assignments are correctly configured.

## Configuration & Resolution

### Step 1 – Create VLAN 10

The following commands were used:

```text
enable
configure terminal
vlan 10
name Sales
exit
```

### Step 2 – Assign Sales Ports

```text
interface fa0/1
switchport mode access
switchport access vlan 10
exit

interface fa0/3
switchport mode access
switchport access vlan 10
exit
```

### Step 3 – Create VLAN 20

```text
vlan 20
name Finance
exit
```

### Step 4 – Assign Finance Ports

The `interface range` command was used to configure two interfaces simultaneously.

```text
interface range fa0/2, fa0/4
switchport mode access
switchport access vlan 20
end
```

### Step 5 – Verify VLAN Configuration

```text
show vlan brief
```

**Verification Results:**

| VLAN | Name | Assigned Ports | Status |
|---|---|---|---|
| 10 | Sales | Fa0/1, Fa0/3 | Active |
| 20 | Finance | Fa0/2, Fa0/4 | Active |

**Result:** Both VLANs were successfully created, and all four access ports were correctly assigned.

<img width="946" height="552" alt="image" src="https://github.com/user-attachments/assets/b1bf3557-45cb-4462-9828-748dc7750704" />


*Figure 1 – VLAN configuration verified using `show vlan brief`.*

---

# Part 2 – Network Connectivity Verification

## Exercise Requirements

Test communication between the following devices:

1. PC-A → PC-C (both in VLAN 10).
2. PC-A → PC-B (different VLANs).
3. PC-B → PC-D (both in VLAN 20).

Determine whether the results match the expected VLAN configuration.

### Expected Results

| Source | Destination | Expected Result |
|---|---|---|
| PC-A | PC-C | Success |
| PC-A | PC-B | Failure |
| PC-B | PC-D | Success |

## Investigation & Results

### Test 1 – PC-A to PC-C

Command:

```text
ping 192.168.10.20
```

**Observed Result:**
- Packets sent: 4
- Packets received: 4
- Packet loss: 0%

**Status:** Successful.

### Test 2 – PC-A to PC-B

Command:

```text
ping 192.168.20.10
```

**Observed Result:**
- Packets sent: 4
- Packets received: 0
- Packet loss: 100%

**Status:** Failed, as expected.

### Test 3 – PC-B to PC-D

Command:

```text
ping 192.168.20.20
```

**Observed Result:**
- Packets sent: 4
- Packets received: 4
- Packet loss: 0%

**Status:** Successful.

### Question

**Q1. Why can PC-A communicate with PC-C but not with PC-B?**

**Answer:** PC-A and PC-C belong to VLAN 10 and share the same IP subnet. PC-B belongs to VLAN 20 and uses a different IP subnet. Without inter-VLAN routing, communication between these VLANs is not possible.

**Result:** Both VLANs operated as expected, allowing communication within each department while maintaining separation between departments.

---

# Part 3 – Incident #004: VLAN Misconfiguration

## Incident Scenario

A user from the Sales department reports that PC-C cannot communicate with PC-A.

The network administrator must investigate the connectivity issue, identify the root cause, correct the configuration, and verify that communication has been restored.

### Exercise Requirements

1. Introduce a VLAN misconfiguration by assigning Fa0/3 to VLAN 20.
2. Investigate the VLAN assignments on the switch.
3. Identify the incorrectly configured interface.
4. Determine the root cause of the connectivity issue.
5. Restore the correct VLAN configuration.
6. Verify VLAN membership and communication between PC-A and PC-C.

## Investigation & Troubleshooting

### Step 1 – Introduce the Misconfiguration

The following commands were used to simulate the incident:

```text
configure terminal
interface fa0/3
switchport access vlan 20
end
```

This configuration moved PC-C from VLAN 10 to VLAN 20.

### Step 2 – Investigate VLAN Membership

The following command was used:

```text
show vlan brief
```

**Findings:**

| VLAN | Name | Assigned Ports |
|---|---|---|
| 10 | Sales | Fa0/1 |
| 20 | Finance | Fa0/2, Fa0/3, Fa0/4 |

The investigation revealed that Fa0/3 was incorrectly assigned to VLAN 20.

### Root Cause

**Incorrect VLAN assignment on interface Fa0/3.**

PC-C was connected to VLAN 20, while PC-A remained in VLAN 10.

Although both PCs retained their original IP addresses in the 192.168.10.0/24 subnet, they were separated into different Layer 2 broadcast domains.

The network had no inter-VLAN routing configured to provide communication between them.

**Affected OSI Layer:** Layer 2 – Data Link.

<img width="673" height="640" alt="image" src="https://github.com/user-attachments/assets/3710da86-aa97-4c39-8ca6-e316c2b6290d" />

*Figure 2 – Incorrect VLAN assignment identified using `show vlan brief`.*

## Resolution

### Step 3 – Restore VLAN Configuration

The following commands were used to return Fa0/3 to VLAN 10:

```text
configure terminal
interface fa0/3
switchport access vlan 10
end
```

### Step 4 – Verify VLAN Membership

```text
show vlan brief
```

**Verification Results:**

| VLAN | Name | Assigned Ports |
|---|---|---|
| 10 | Sales | Fa0/1, Fa0/3 |
| 20 | Finance | Fa0/2, Fa0/4 |

The switch confirmed that Fa0/3 had been restored to the correct VLAN.

<img width="922" height="379" alt="image" src="https://github.com/user-attachments/assets/340834c2-9abd-48f1-98b5-74f09cf134ab" />


*Figure 3 – Correct VLAN assignments after troubleshooting.*

### Step 5 – Verify Connectivity

From PC-A, the following command was executed:

```text
ping 192.168.10.20
```

**Observed Result:**
- Packets sent: 4
- Packets received: 4
- Packet loss: 0%

**Status:** Successful.

Communication between PC-A and PC-C was restored.

<img width="708" height="329" alt="image" src="https://github.com/user-attachments/assets/5b1066e3-38d3-4278-94de-9f18ce24608a" />


*Figure 4 – Successful ping test after correcting the VLAN configuration.*

---

# Part 4 – Conclusion

This lab demonstrated how VLANs can be used to separate departments within the same physical network infrastructure.

Two VLANs were successfully configured, and access ports were assigned according to departmental requirements.

Connectivity tests confirmed that devices within the same VLAN could communicate, while communication between different VLANs was unavailable without routing.

A simulated VLAN misconfiguration was investigated using Cisco IOS commands. The incorrect VLAN assignment was identified and corrected, restoring connectivity between the affected devices.

### Skills Practised

- Cisco VLAN configuration
- Access port assignment
- `interface range` configuration
- VLAN membership verification
- ICMP connectivity testing
- Layer 2 troubleshooting
- Root cause identification
- Network incident resolution

**Final Result:** Lab completed successfully. Both VLANs were correctly configured, and the simulated connectivity issue was resolved.
