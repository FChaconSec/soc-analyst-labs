# FSNA Basic Network Configuration Lab

## Overview
This lab focuses on configuring a basic network topology using Cisco Packet Tracer. The objective was to build foundational networking skills, including device configuration, IP addressing, and connectivity verification.

## Objectives
- Configure routers and switches using CLI
- Assign IPv4 addressing to devices
- Establish network connectivity between multiple devices
- Verify communication using ping and troubleshooting methods

## Lab Environment
- Cisco Packet Tracer
- Routers, switches, and end devices (PCs)

## Network Configuration

### VLAN Segmentation

The network was segmented using VLANs to separate different types of traffic:

- VLAN 100 – Management
- VLAN 150 – Voice
- VLAN 200 – Data

This segmentation improves security and reduces broadcast domains, which is critical in enterprise environments.

### Key Tasks Performed:
- Configured device hostnames
- Assigned IP addresses to router interfaces and PCs
- Enabled interfaces using `no shutdown.`
- Configured default gateways
- Verified connectivity using ICMP (ping)

These are fundamental tasks in network setup, as proper interface configuration and IP addressing are required before communication can occur across networks :

## Verification & Results

- Successfully established communication between devices
- Verified connectivity using:
  - `ping` between PCs
  - Router interface checks
- Confirmed correct IP addressing and routing behavior

## Skills Demonstrated
- Network configuration
- CLI navigation (Cisco IOS)
- IP addressing & subnetting basics
- Troubleshooting connectivity issues

## Screenshots

### Network Topology
<img width="957" height="357" alt="image" src="https://github.com/user-attachments/assets/26cb068f-3ff8-464f-9948-bda85bfed68c" />

### Router Interface Configuration
<img width="695" height="706" alt="image" src="https://github.com/user-attachments/assets/15f8b759-5478-4ca3-86a8-39d14a825e06" />

PC IP Configuration (DHCP)
<img width="695" height="708" alt="image" src="https://github.com/user-attachments/assets/72d421a8-9fd1-4d5b-85ac-f85aa75c7ef8" />

### End-to-End Connectivity Test
<img width="697" height="708" alt="image" src="https://github.com/user-attachments/assets/5873483e-3155-49c2-ae41-fc0d63c19101" />

Note: The initial ping request resulted in one timeout due to ARP resolution, which is expected when a device first learns the MAC address of the destination.

## Key Takeaways
This lab reinforced the importance of proper network configuration and verification. Even simple misconfigurations (such as incorrect IP addresses or interfaces not enabled) can prevent communication across the network.
This lab also demonstrates foundational networking knowledge required for SOC analysts, as understanding IP addressing, segmentation, and traffic flow is essential when analyzing network-based threats and alerts.
