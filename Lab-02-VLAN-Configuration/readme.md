
# Lab 02 — VLAN Configuration and Segmentation

## Overview

This Cisco Packet Tracer lab demonstrates VLAN
configuration and Layer 2 segmentation using a
Cisco 2960 switch and four PCs.

Two departments are configured:

- Sales — VLAN 10
- HR — VLAN 20

## Objectives

- Create VLAN 10 and VLAN 20.
- Configure switch access ports.
- Verify VLAN membership.
- Test same-VLAN connectivity.
- Verify isolation between VLANs without routing.
- Troubleshoot an incorrect access VLAN assignment.

## Tools and Technologies

- Cisco Packet Tracer
- Cisco Catalyst 2960 (simulated)
- Cisco IOS
- Ethernet and IPv4
- VLANs and MAC address learning
- ICMP troubleshooting

## Network Topology

<img width="437" height="397" alt="topology png" src="https://github.com/user-attachments/assets/3b394662-a9bf-4e8d-919e-01bf031d5d09" />

## IP Addressing Plan

| Device | IPv4 Address | Mask | VLAN | Switch Port |
|---|---|---|---|---|
| PC1 | 192.168.10.10 | /24 | 10 | Fa0/1 |
| PC2 | 192.168.10.20 | /24 | 10 | Fa0/2 |
| PC3 | 192.168.20.10 | /24 | 20 | Fa0/3 |
| PC4 | 192.168.20.20 | /24 | 20 | Fa0/4 |

No default gateways were configured because
this lab does not include inter-VLAN routing.

## VLAN Configuration

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

interface range fastEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 10
exit

interface range fastEthernet 0/3 - 4
 switchport mode access
 switchport access vlan 20
exit

end
copy running-config startup-config
```

## VLAN Verification

Command:

```cisco
show vlan brief
```

Expected assignments:

- VLAN 10: Fa0/1, Fa0/2
- VLAN 20: Fa0/3, Fa0/4

<img width="610" height="231" alt="vlan-configuration png" src="https://github.com/user-attachments/assets/5db9ddcf-0afc-4686-9fa0-219ba8b3e5d2" />

## Connectivity Tests

| Source | Destination | Expected | Actual |
|---|---|---|---|
| PC1 | PC2 | Success | Success |
<img width="458" height="360" alt="Screenshot 2026-11-12 213456" src="https://github.com/user-attachments/assets/9a855579-a2a4-485c-a534-7e6c7a3f2cb8" />

------------------------------------------------------------------------------------------------------------------------------

| Source | Destination | Expected | Actual |
|---|---|---|---|
| PC3 | PC4 | Success | Success |

<img width="487" height="342" alt="Screenshot 2026-11-12 213541" src="https://github.com/user-attachments/assets/93fb4b92-68ec-4372-909d-5df19a3df3df" />

------------------------------------------------------------------------------------------------------------------------------


| Source | Destination | Expected | Actual |
|---|---|---|---|
| PC1 | PC3 | Failure | Failure |

<img width="483" height="504" alt="Screenshot 2026-11-12 213654" src="https://github.com/user-attachments/assets/c12cfb99-e206-436a-bfac-191732ea35d7" />

------------------------------------------------------------------------------------------------------------------------------


| Source | Destination | Expected | Actual |
|---|---|---|---|
| PC2 | PC4 | Failure | Failure |

<img width="451" height="312" alt="Screenshot 2026-11-12 213841" src="https://github.com/user-attachments/assets/66472d1a-a85c-4275-b2b0-db2afd59c18a" />

------------------------------------------------------------------------------------------------------------------------------





## MAC Address Learning

Verification command:

```cisco
show mac address-table dynamic
```

The switch learns MAC addresses associated
with the VLAN and ingress switch port.

<img width="386" height="151" alt="Screenshot 2026-11-12 212211" src="https://github.com/user-attachments/assets/59e547f6-76a4-4c6a-a9d8-c6371f585d6a" />


## Troubleshooting Scenario

### Problem

Scenario: PC2 suddenly cannot communicate with PC1, even though both have correct IPv4 addresses.
We will deliberately introduce a configuration mistake.
On SW1:

configure terminal

interface fastEthernet 0/2
 switchport access vlan 20
exit

end

<img width="505" height="155" alt="Screenshot 2026-11-12 220002" src="https://github.com/user-attachments/assets/79aacce7-0cc0-4a90-a790-78c0556e8977" />

Now PC2 is assigned to VLAN 20, even though its IPv4 address is still in the 192.168.10.0/24 subnet.

Run from PC1: ping 192.168.10.20
<img width="444" height="183" alt="Screenshot 2026-11-12 220210" src="https://github.com/user-attachments/assets/5e804d03-551b-4fed-b1a6-937543a57e4e" />



### Diagnostic Commands

```cisco
show vlan brief
show interfaces fastEthernet 0/2 switchport
```
<img width="614" height="329" alt="Screenshot 2026-11-12 220459" src="https://github.com/user-attachments/assets/cea6297f-6263-4515-bc68-62045e560b00" />

### Root Cause

Fa0/2 was assigned to VLAN 20 rather than VLAN 10.

### Corrective Action

```cisco
configure terminal
interface fastEthernet 0/2
 switchport access vlan 10
end
```
<img width="450" height="87" alt="Screenshot 2026-11-12 220628" src="https://github.com/user-attachments/assets/bc5e9e9c-ae28-4af5-ac45-b25f918dd2e5" />


### Verification
Repeat the ping from PC1.

<img width="421" height="180" alt="Screenshot 2026-11-12 220755" src="https://github.com/user-attachments/assets/2a263d20-cfa0-4b36-aee3-8a6bb8a9b2d2" />



## Technical Explanation

A VLAN represents a separate Layer 2 broadcast
domain.

Devices in the same VLAN and IP subnet can
communicate through the switch.

Different VLANs require Layer 3 forwarding
for inter-VLAN communication.


## Learning Outcomes

- VLAN creation and access-port configuration
- VLAN-based Layer 2 segmentation
- Cisco IOS verification commands
- MAC address learning
- Fault isolation and configuration correction

## Status

Completed

