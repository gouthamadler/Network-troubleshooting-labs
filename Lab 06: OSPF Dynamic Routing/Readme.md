
# Lab 06: OSPF Dynamic Routing

## 📌 Project Overview

This lab demonstrates how to configure **Open Shortest Path First (OSPF)** dynamic routing between two Cisco routers using Cisco Packet Tracer.

The objective is to enable two separate LAN networks to communicate through dynamically learned routes instead of manually configured static routes.

This lab also demonstrates OSPF neighbor formation, routing table verification, end-to-end connectivity testing, and troubleshooting a routing advertisement failure.

## 🎯 Objectives

- Configure IP addressing on routers and PCs.
- Configure OSPF dynamic routing.
- Assign unique OSPF router IDs.
- Configure OSPF Area 0.
- Establish OSPF neighbor adjacency.
- Verify dynamically learned routes.
- Test connectivity between remote LANs.
- Simulate and troubleshoot an OSPF routing issue.

## 🖥️ Network Topology

The network consists of:

- 2 Cisco 2911 Routers (R1 and R2)
- 2 Cisco 2960 Switches (SW1 and SW2)
- 2 PCs (PC1 and PC2)

### Topology Diagram

<img width="600" height="392" alt="image" src="https://github.com/user-attachments/assets/2d971d68-346d-4cfb-a705-d518b489505e" />


### Network Layout

```text
PC1 --- SW1 --- R1 -------- R2 --- SW2 --- PC2
                 G0/1    G0/1

LAN 1: 192.168.10.0/24
WAN:   10.0.0.0/30
LAN 2: 192.168.20.0/24
```

## 🌐 IP Addressing Table

| Device | Interface | IP Address | Subnet Mask |
|---|---|---|---|
| PC1 | FastEthernet0 | 192.168.10.10 | 255.255.255.0 |
| R1 | G0/0 | 192.168.10.1 | 255.255.255.0 |
| R1 | G0/1 | 10.0.0.1 | 255.255.255.252 |
| R2 | G0/1 | 10.0.0.2 | 255.255.255.252 |
| R2 | G0/0 | 192.168.20.1 | 255.255.255.0 |
| PC2 | FastEthernet0 | 192.168.20.10 | 255.255.255.0 |

### Default Gateways

- PC1: `192.168.10.1`
- PC2: `192.168.20.1`

## ⚙️ Router Configuration

### 1. R1 Configuration

```cisco
enable
configure terminal
hostname R1

interface gigabitEthernet 0/0
 description LAN_TO_SW1
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit

interface gigabitEthernet 0/1
 description WAN_TO_R2
 ip address 10.0.0.1 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 1.1.1.1
 network 192.168.10.0 0.0.0.255 area 0
 network 10.0.0.0 0.0.0.3 area 0
 passive-interface gigabitEthernet 0/0
exit

end
```
<img width="642" height="438" alt="image" src="https://github.com/user-attachments/assets/89c86b9a-5dad-4c2e-bad4-6e9273c789ad" />

### 2. R2 Configuration

```cisco
enable
configure terminal
hostname R2

interface gigabitEthernet 0/0
 description LAN_TO_SW2
 ip address 192.168.20.1 255.255.255.0
 no shutdown
exit

interface gigabitEthernet 0/1
 description WAN_TO_R1
 ip address 10.0.0.2 255.255.255.252
 no shutdown
exit

router ospf 1
 router-id 2.2.2.2
 network 192.168.20.0 0.0.0.255 area 0
 network 10.0.0.0 0.0.0.3 area 0
 passive-interface gigabitEthernet 0/0
exit

end
```
<img width="644" height="452" alt="image" src="https://github.com/user-attachments/assets/0e69e9c8-afff-40cf-9f02-8876af3cbb5a" />

### OSPF Configuration Explanation

| Command | Purpose |
|---|---|
| `router ospf 1` | Starts OSPF process 1 |
| `router-id 1.1.1.1` | Assigns a unique router ID to R1 |
| `router-id 2.2.2.2` | Assigns a unique router ID to R2 |
| `network` | Identifies interfaces participating in OSPF |
| `0.0.0.255` | Wildcard mask for a /24 network |
| `0.0.0.3` | Wildcard mask for a /30 network |
| `area 0` | Places the networks in OSPF backbone area 0 |
| `passive-interface` | Advertises the LAN network without sending OSPF hello packets on that LAN interface |

## 🔍 OSPF Verification

The following commands were used to verify the OSPF configuration.

### Check Interface Status

```cisco
show ip interface brief
```

Expected result: Relevant router interfaces should show `up/up`.

### Check OSPF Neighbors

```cisco
show ip ospf neighbor
```

Expected result:

- R1 should identify R2 using router ID `2.2.2.2`.
- R2 should identify R1 using router ID `1.1.1.1`.
- The neighbor state should reach FULL.

### Check Routing Table

```cisco
show ip route
```

Expected OSPF route on R1:

```text
O 192.168.20.0/24 via 10.0.0.2
```

Expected OSPF route on R2:

```text
O 192.168.10.0/24 via 10.0.0.1
```

The letter `O` identifies a route learned through OSPF.

<img width="644" height="541" alt="image" src="https://github.com/user-attachments/assets/1034848a-fcb1-4096-87b3-a103fcec883f" />

### Additional Verification

```cisco
show ip protocols
show ip ospf
show ip ospf interface brief
```
<img width="638" height="636" alt="image" src="https://github.com/user-attachments/assets/41a9ef7d-645c-4d0b-90c3-958740e36f24" />

## 🧪 End-to-End Connectivity Testing

### PC1 to PC2

On PC1:

```text
ping 192.168.20.10
```

Expected result: Successful communication with PC2.

<img width="585" height="201" alt="image" src="https://github.com/user-attachments/assets/c6c62a06-8a2a-427c-9a03-1e5fba97cb77" />


### PC2 to PC1

On PC2:

```text
ping 192.168.10.10
```
<img width="486" height="216" alt="image" src="https://github.com/user-attachments/assets/23edeed1-5602-45c2-ae01-14879000d5d9" />

Expected result: Successful communication with PC1.

## 🛠️ NOC Troubleshooting Exercise

### Problem Description

Simulate an OSPF routing advertisement failure by removing R2's LAN network statement from its OSPF configuration.

### Step 1: Introduce the Fault

On R2:

```cisco
configure terminal
router ospf 1
no network 192.168.20.0 0.0.0.255 area 0
end
```

### Step 2: Identify the Problem

On R1:

```cisco
show ip route
show ip ospf neighbor
```

Expected observations:

- The OSPF neighbor relationship may remain FULL.
- The route to `192.168.20.0/24` should disappear from R1's routing table after convergence.
- PC1 should no longer be able to reach PC2.

### Step 3: Restore the Configuration

On R2:

```cisco
configure terminal
router ospf 1
network 192.168.20.0 0.0.0.255 area 0
end
```

### Step 4: Verify Recovery

On R1:

```cisco
show ip ospf neighbor
show ip route
```

Repeat the connectivity test:

```text
ping 192.168.20.10
```

Expected result: The remote OSPF route returns and end-to-end connectivity is restored.

<img width="620" height="465" alt="image" src="https://github.com/user-attachments/assets/32705663-87cd-4b7d-b641-e2da25f108b9" />



## 📊 Lab Results

| Test | Result |
|---|---|
| Network topology | Completed |
| Router interface configuration | Completed |
| OSPF configuration | Completed |
| OSPF neighbor formation | Observed on R2 |
| OSPF route verification | Initial route observed on R1 |
| End-to-end connectivity | Pending final evidence |
| OSPF route withdrawal | Pending final evidence |
| OSPF recovery | Pending final evidence |

## 🧠 Key Learning Outcomes

Through this lab, I practised:

- Configuring OSPF dynamic routing on Cisco routers.
- Understanding OSPF router IDs and Area 0.
- Understanding wildcard masks in OSPF network statements.
- Verifying OSPF neighbor relationships.
- Identifying OSPF-learned routes in the routing table.
- Understanding passive interfaces.
- Troubleshooting missing route advertisements.
- Verifying end-to-end network connectivity.

## 💼 NOC / Network Support Relevance

This lab represents practical tasks relevant to an entry-level Network Operations Center (NOC) or Network Support role.

Examples include:

- Checking router interface status.
- Investigating missing routes.
- Verifying OSPF neighbor relationships.
- Identifying routing advertisement issues.
- Performing connectivity tests.
- Restoring network connectivity after configuration errors.

## 💾 Project Files

- `Lab-06-OSPF-Dynamic-Routing.pkt` — Cisco Packet Tracer project file.
- `README.md` — Lab documentation.


## 🛠️ Tools Used

- Cisco Packet Tracer
- Cisco IOS CLI
- OSPF Dynamic Routing
- IPv4 Addressing and Subnetting
- GitHub

---

**Project:** Cisco Network Troubleshooting Labs  
**Lab:** 06 — OSPF Dynamic Routing  
**Focus:** Routing, Network Operations and Troubleshooting
