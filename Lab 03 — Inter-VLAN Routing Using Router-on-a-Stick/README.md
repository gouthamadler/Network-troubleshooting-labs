
# Lab 03 — Inter-VLAN Routing Using Router-on-a-Stick

## 1. Overview

This Cisco Packet Tracer lab demonstrates **inter-VLAN routing using Router-on-a-Stick (ROAS)**.

Two departments—Sales and HR—are separated into VLANs 10 and 20 on a Cisco 2960 switch. A Cisco 2911 router provides communication between the VLANs using IEEE 802.1Q trunking and router subinterfaces.

The lab includes a troubleshooting exercise involving an incorrectly configured allowed-VLAN list on the switch trunk.

## 2. Objectives

- Create and configure VLANs on a Cisco switch.
- Assign switch access ports to the correct VLANs.
- Configure an IEEE 802.1Q trunk.
- Configure router subinterfaces for inter-VLAN routing.
- Assign default gateways to end devices.
- Verify Layer 2 and Layer 3 connectivity.
- Troubleshoot VLAN trunk configuration problems.
- Practice Cisco IOS verification commands.

## 3. Tools and Technologies

- Cisco Packet Tracer
- Cisco 2911 Router
- Cisco Catalyst 2960 Switch
- Cisco IOS CLI
- IPv4 Addressing and Subnetting
- VLANs and IEEE 802.1Q
- Inter-VLAN Routing
- ARP and ICMP
- Network Troubleshooting

## 4. Network Topology

<img width="518" height="457" alt="image" src="https://github.com/user-attachments/assets/d0bf5a9a-3b7a-4176-bccd-af7e9cc72a66" />


### Topology Description

```text
                   R1 (Cisco 2911)
                  GigabitEthernet0/0
                         |
                    802.1Q Trunk
                         |
                  Gi0/1 — SW1
                   Cisco 2960
                         |
           +-------------+-------------+
           |             |             |
         VLAN 10                     VLAN 20
          SALES                        HR
           |                           |
      +----+----+                 +----+----+
      |         |                 |         |
     PC1       PC2               PC3       PC4
```

### Device Connections

| Device | Interface | Connected To |
|---|---|---|
| R1 | GigabitEthernet0/0 | SW1 Gi0/1 |
| PC1 | FastEthernet0 | SW1 Fa0/1 |
| PC2 | FastEthernet0 | SW1 Fa0/2 |
| PC3 | FastEthernet0 | SW1 Fa0/3 |
| PC4 | FastEthernet0 | SW1 Fa0/4 |

## 5. IP Addressing Plan

| Device | IPv4 Address | Subnet Mask | Default Gateway | VLAN |
|---|---|---|---|---|
| PC1 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 | 10 |
| PC2 | 192.168.10.20 | 255.255.255.0 | 192.168.10.1 | 10 |
| PC3 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 | 20 |
| PC4 | 192.168.20.20 | 255.255.255.0 | 192.168.20.1 | 20 |

### Router Gateway Addresses

| Interface | VLAN | IPv4 Address |
|---|---|---|
| G0/0.10 | 10 — SALES | 192.168.10.1/24 |
| G0/0.20 | 20 — HR | 192.168.20.1/24 |

## 6. Switch Configuration — SW1

### Create VLANs

```cisco
enable
configure terminal

hostname SW1

vlan 10
 name SALES
exit

vlan 20
 name HR
exit
```
<img width="461" height="149" alt="image" src="https://github.com/user-attachments/assets/6b8b568b-2761-4591-a616-dafdfdf2fd85" />


### Configure Access Ports

**VLAN 10 — SALES**

```cisco
interface range fastEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 10
exit
```
<img width="376" height="63" alt="image" src="https://github.com/user-attachments/assets/90c819f7-b6c7-4497-9e11-b83d40ba1af5" />


**VLAN 20 — HR**

```cisco
interface range fastEthernet 0/3 - 4
 switchport mode access
 switchport access vlan 20
exit
```
<img width="382" height="65" alt="image" src="https://github.com/user-attachments/assets/6eff01c9-6439-494f-934c-a34c4affb997" />


### Configure 802.1Q Trunk

```cisco
interface gigabitEthernet 0/1
 description TRUNK_TO_R1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
exit

end
copy running-config startup-config
```
<img width="381" height="254" alt="image" src="https://github.com/user-attachments/assets/b8427c3b-f36b-47ba-a71a-bb2d80229b7a" />


### Verification

```cisco
show vlan brief
show interfaces trunk
show mac address-table dynamic
```

Expected VLAN membership:

- VLAN 10: Fa0/1, Fa0/2
- VLAN 20: Fa0/3, Fa0/4
- Gi0/1: 802.1Q trunk allowing VLANs 10 and 20

<img width="586" height="530" alt="image" src="https://github.com/user-attachments/assets/87b2f614-2e69-4134-b1e6-ac5269d47e53" />


## 7. Router Configuration — R1

### Enable Physical Interface

```cisco
enable
configure terminal

hostname R1

interface gigabitEthernet 0/0
 description TRUNK_TO_SW1
 no shutdown
exit
```
<img width="625" height="199" alt="image" src="https://github.com/user-attachments/assets/f57abb75-437d-435a-b134-cabe0740e6f7" />


### Configure VLAN 10 Subinterface

```cisco
interface gigabitEthernet 0/0.10
 description GATEWAY_SALES
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
exit
```
<img width="459" height="74" alt="image" src="https://github.com/user-attachments/assets/a5bbe66c-aa21-4587-8761-b96d8fab11b5" />


### Configure VLAN 20 Subinterface

```cisco
interface gigabitEthernet 0/0.20
 description GATEWAY_HR
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

end
copy running-config startup-config
```
<img width="403" height="206" alt="image" src="https://github.com/user-attachments/assets/7ae67881-a6a7-4a29-bd41-b059f1c9f9e3" />


### Verify Router Interfaces

```cisco
show ip interface brief
show ip route
```

Expected connected networks:

```text
C 192.168.10.0/24 is directly connected, GigabitEthernet0/0.10
C 192.168.20.0/24 is directly connected, GigabitEthernet0/0.20
```

<img width="573" height="364" alt="image" src="https://github.com/user-attachments/assets/9acd75eb-72b2-44bc-829c-1f8f7b84dde1" />

## 8. Connectivity Verification

### PC1 Verification Commands

```text
ipconfig
ping 192.168.10.20
ping 192.168.20.10
ping 192.168.20.20
ping 192.168.10.1
ping 192.168.20.1
```

### Connectivity Test Results

| Source | Destination | Expected | Actual |
|---|---|---|---|
| PC1 | PC2 | Success | Success |
| PC1 | PC3 | Success | Success |
| PC1 | PC4 | Success | Success |
| PC1 | R1 G0/0.10 | Success | Success |
| PC1 | R1 G0/0.20 | Success | Success |

<img width="412" height="846" alt="image" src="https://github.com/user-attachments/assets/4304bdd6-5145-4e99-beb5-dc036e58a4db" />




## 9. How Inter-VLAN Routing Works

When PC1 (192.168.10.10) sends traffic to PC3 (192.168.20.10):

1. PC1 determines that the destination belongs to a different IPv4 subnet.
2. PC1 forwards the packet to its default gateway, 192.168.10.1.
3. PC1 resolves the gateway MAC address using ARP if it is not already cached.
4. SW1 forwards the Ethernet frame toward R1 through the 802.1Q trunk, tagged with VLAN 10.
5. R1 receives the frame on subinterface G0/0.10.
6. R1 checks its routing table and identifies 192.168.20.0/24 as directly connected.
7. R1 resolves PC3's MAC address if necessary and sends a new Ethernet frame through G0/0.20 using VLAN 20 tagging.
8. SW1 forwards the frame to PC3 through access port Fa0/3.

The IP packet is routed between networks, while the Layer 2 Ethernet headers are rebuilt for the outgoing VLAN.

The router also decreases the IPv4 TTL when forwarding the packet.

## 10. Troubleshooting Scenario

### Incident

Devices within the same VLAN can communicate, but inter-VLAN communication fails.

### Fault Introduced

VLAN 20 was intentionally removed from the switch trunk's allowed VLAN list.

```cisco
configure terminal

interface gigabitEthernet 0/1
 switchport trunk allowed vlan 10
exit

end
```
<img width="454" height="99" alt="image" src="https://github.com/user-attachments/assets/dab8e95d-46f8-4a02-88e8-03c889112447" />


### Symptoms

- Same-VLAN connectivity remains available.
- Traffic requiring VLAN 20 across the router trunk fails.
- The trunk is operational but its allowed-VLAN list is incorrect.

### Diagnostic Command

```cisco
show interfaces trunk
```

Expected fault observation:

```text
Vlans allowed on trunk
Gi0/1    10
```
<img width="532" height="184" alt="image" src="https://github.com/user-attachments/assets/bafa758b-1928-4c82-a228-87f220c47ec1" />


### Root Cause

VLAN 20 was missing from the trunk's allowed VLAN list.

This prevented VLAN 20 traffic from traversing the switch-to-router connection.

### Corrective Action

```cisco
configure terminal

interface gigabitEthernet 0/1
 switchport trunk allowed vlan 10,20
exit

end
copy running-config startup-config
```
<img width="418" height="204" alt="image" src="https://github.com/user-attachments/assets/842e6fb3-6dec-486f-83f9-ac412437e8b9" />


### Verification

```cisco
show interfaces trunk
```

Expected allowed VLAN list:

```text
Gi0/1    10,20
```
<img width="501" height="180" alt="image" src="https://github.com/user-attachments/assets/fb1d46ea-0d68-442d-9cf3-12226c6e3910" />


Retest:

```text
ping 192.168.20.10
```
<img width="430" height="201" alt="image" src="https://github.com/user-attachments/assets/bc9506b2-7638-4e2a-a857-3438884da8de" />



### NOC Incident Summary

| Field | Details |
|---|---|
| Incident | Inter-VLAN connectivity failure |
| Affected service | Communication between VLAN 10 and VLAN 20 |
| Root cause | VLAN 20 removed from the trunk allowed list |
| Diagnostic tool | Cisco IOS CLI |
| Diagnostic command | `show interfaces trunk` |
| Corrective action | Reconfigured allowed VLANs 10,20 |


## 11. Key Learning Outcomes

This lab demonstrates:

- Configuring and verifying multiple VLANs
- Understanding Layer 2 segmentation
- Implementing IEEE 802.1Q trunking
- Configuring router subinterfaces
- Using default gateways for remote-subnet communication
- Understanding router-on-a-stick packet forwarding
- Verifying connected routes
- Troubleshooting VLAN trunk configuration errors
- Documenting network incidents and corrective actions


## 13. How to Run the Lab

1. Install Cisco Packet Tracer.
2. Download `Lab-03-Inter-VLAN-Routing.pkt`.
3. Open the file in Packet Tracer.
4. Verify the switch VLAN and trunk configurations.
5. Verify router subinterfaces and connected routes.
6. Test same-VLAN and inter-VLAN connectivity.
7. Review the troubleshooting section to understand the intentional fault and correction.


---

**Portfolio:** Cisco Networking and Network Troubleshooting Labs

**Learning path:** Cisco Networking Academy — Network Technician Career Path

