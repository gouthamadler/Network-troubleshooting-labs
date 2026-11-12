
# Lab 01 — Basic LAN Connectivity

## Overview

A Cisco Packet Tracer lab demonstrating basic Ethernet
switching, static IPv4 addressing, ARP resolution, MAC
address learning, and end-to-end LAN connectivity.

## Objectives

- Build a LAN with one Cisco 2960 switch and two PCs.
- Configure static IPv4 addresses on both PCs.
- Establish connectivity using copper straight-through cables.
- Verify connectivity using ICMP ping.
- Observe ARP and dynamic MAC address learning.
- Practice Cisco IOS verification commands.

## Tools and Technologies

- Cisco Packet Tracer
- Cisco Catalyst 2960 switch (simulated)
- IPv4 / Ethernet / ARP / ICMP
- Cisco IOS CLI

## Network Topology

<img width="712" height="369" alt="Lab 01 Network topology" src="https://github.com/user-attachments/assets/144b477b-0f4e-4048-920c-8a6a39b9cec8" />


### Connections

| Device | Interface | Connected To |
|---|---|---|
| PC0 | FastEthernet0 | SW1 Fa0/1 |
| PC1 | FastEthernet0 | SW1 Fa0/2 |

### IP Addressing

| Device | IPv4 Address | Subnet Mask | Gateway |
|---|---|---|---|
| PC0 | 192.168.10.10 | 255.255.255.0 | Not required |
| PC1 | 192.168.10.20 | 255.255.255.0 | Not required |

Network: `192.168.10.0/24`

Both PCs operate in the same subnet and default VLAN.

## Configuration

1. Added one Cisco 2960 switch and two PCs.
2. Connected the PCs using straight-through cables.
3. Assigned static IPv4 addresses.
4. Kept both switch access ports in VLAN 1.
5. Saved the network as a Packet Tracer `.pkt` file.

## Verification

### PC1 Connectivity

Commands:

```text
ipconfig
ping 192.168.10.20
arp -a
```

Ping result: [Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)]

<img width="724" height="820" alt="Screenshot 2026-11-12 161715" src="https://github.com/user-attachments/assets/72639cd4-ae31-4acf-801e-9b4ac3655a59" />


### Switch Verification

Commands:

```text
enable
show mac address-table
show interfaces status
show vlan brief
```

Observed MAC addresses: [ 0001.432b.3dd1    DYNAMIC     Fa0/1 ; 0001.c937.5bdb    DYNAMIC     Fa0/2]

<img width="349" height="144" alt="Screenshot 2026-11-12 162227" src="https://github.com/user-attachments/assets/207788c4-ba80-4b9a-8427-43a50baf6da5" />
<img width="585" height="304" alt="Screenshot 2026-11-12 162245" src="https://github.com/user-attachments/assets/0137165c-941f-4ff0-b668-df186685ef91" />
<img width="606" height="197" alt="Screenshot 2026-11-12 162306" src="https://github.com/user-attachments/assets/0b3b521b-ebb6-4142-bfff-baa3e9f5fdd6" />




## Technical Explanation

When PC0 communicates with PC1:

1. PC0 determines that PC1 is in the local IPv4 subnet.
2. PC0 uses ARP to discover PC1's MAC address if needed.
3. SW0 learns source MAC addresses on incoming frames.
4. SW0 forwards known unicast frames to the correct port.
5. PC1 responds to PC0's ICMP Echo Request.

No router or default gateway is necessary for this
same-subnet communication.

## Troubleshooting Notes

Record any actual problems encountered and how they
were resolved.

If no faults occurred, state:
"No faults were encountered during the final verification."

## Learning Outcomes

This lab reinforced:

- IPv4 addressing and subnet membership
- Ethernet switching
- ARP operation
- MAC address table learning
- Cisco IOS verification commands
- Basic network connectivity testing


## How to Run

1. Install Cisco Packet Tracer.
2. Download the `.pkt` project file.
3. Open it using Packet Tracer.
4. Open the PCs' command prompts to test connectivity.
5. Access SW0's CLI to examine the MAC address table.



