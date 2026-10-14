<h1 align="center">Dynamic Routing Using RIPv2</h1>

<p align="center">
  A Cisco Packet Tracer lab that connects two LANs through two routers using the RIP version 2 dynamic routing protocol.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white" alt="Cisco Packet Tracer"/>
  <img src="https://img.shields.io/badge/Routing-RIPv2-orange?style=for-the-badge" alt="RIPv2"/>
  <img src="https://img.shields.io/badge/Level-CCNA-green?style=for-the-badge" alt="CCNA"/>
</p>

---

## Table of Contents

- [Overview](#overview)
- [Network Topology](#network-topology)
- [IP Addressing Table](#ip-addressing-table)
- [Configuration](#configuration)
- [Verification and Testing](#verification-and-testing)
- [Screenshots](#screenshots)
- [Repository Structure](#repository-structure)
- [How to Run](#how-to-run)
- [Skills Demonstrated](#skills-demonstrated)
- [Author](#author)

---

## Overview

This project simulates a small enterprise network with two LANs, **HQ** and **BRANCH**, each behind its own router. Instead of manually defining routes, **RIPv2** is configured so the routers automatically exchange routing information and learn the remote LAN.

**Objectives**

- Implement a two-LAN topology with a point-to-point link between routers.
- Configure IPv4 addressing and default gateways.
- Enable RIPv2 with `no auto-summary` on both routers.
- Verify dynamic route learning and end-to-end connectivity.

**Why RIPv2?**

- Supports **VLSM and CIDR** (sends subnet masks in updates, unlike RIPv1).
- Uses **multicast (224.0.0.9)** instead of broadcast.
- Hop-count metric with a maximum of 15 hops.
- Administrative distance of 120.

---

## Network Topology

| Component   | Quantity | Model           |
|-------------|----------|-----------------|
| Routers     | 2        | Cisco 2911      |
| Switches    | 2        | Cisco 2960-24TT |
| End Devices | 4        | Laptop-PT       |

```
 [Laptop0]--+                                        +--[Laptop2]
            |                                        |
         [Switch0]---[HQ]===============[BRANCH]---[Switch1]
            |      Gig0/0  Gig0/1   Gig0/0  Gig0/1    |
 [Laptop1]--+                                        +--[Laptop3]

     LAN 1 (192.168.1.0/24)   2.2.2.0/30   LAN 2 (192.168.2.0/24)
```

---

## IP Addressing Table

| Device  | Interface | IP Address  | Subnet Mask     | Default Gateway |
|---------|-----------|-------------|-----------------|-----------------|
| HQ      | Gig0/0    | 192.168.1.1 | 255.255.255.0   | N/A             |
| HQ      | Gig0/1    | 2.2.2.1     | 255.255.255.252 | N/A             |
| BRANCH  | Gig0/1    | 192.168.2.1 | 255.255.255.0   | N/A             |
| BRANCH  | Gig0/0    | 2.2.2.2     | 255.255.255.252 | N/A             |
| Laptop0 | Fa0       | 192.168.1.2 | 255.255.255.0   | 192.168.1.1     |
| Laptop1 | Fa0       | 192.168.1.3 | 255.255.255.0   | 192.168.1.1     |
| Laptop2 | Fa0       | 192.168.2.2 | 255.255.255.0   | 192.168.2.1     |
| Laptop3 | Fa0       | 192.168.2.3 | 255.255.255.0   | 192.168.2.1     |

---

## Configuration

### Interface Configuration

**HQ**
```
HQ(config)# interface gigabitEthernet 0/0
HQ(config-if)# ip address 192.168.1.1 255.255.255.0
HQ(config-if)# no shutdown
HQ(config-if)# exit
HQ(config)# interface gigabitEthernet 0/1
HQ(config-if)# ip address 2.2.2.1 255.255.255.252
HQ(config-if)# no shutdown
```

**BRANCH**
```
BRANCH(config)# interface gigabitEthernet 0/1
BRANCH(config-if)# ip address 192.168.2.1 255.255.255.0
BRANCH(config-if)# no shutdown
BRANCH(config-if)# exit
BRANCH(config)# interface gigabitEthernet 0/0
BRANCH(config-if)# ip address 2.2.2.2 255.255.255.252
BRANCH(config-if)# no shutdown
```

### RIPv2 Configuration

**HQ**
```
HQ(config)# router rip
HQ(config-router)# version 2
HQ(config-router)# no auto-summary
HQ(config-router)# network 192.168.1.0
HQ(config-router)# network 2.0.0.0
```

**BRANCH**
```
BRANCH(config)# router rip
BRANCH(config-router)# version 2
BRANCH(config-router)# no auto-summary
BRANCH(config-router)# network 192.168.2.0
BRANCH(config-router)# network 2.0.0.0
```

> RIP uses classful `network` statements, so `2.2.2.0` is automatically converted to `2.0.0.0`.

### Steps Performed

1. Assigned IP addresses to laptops and router interfaces.
2. Enabled router interfaces with `no shutdown`.
3. Enabled RIPv2 on both routers and advertised the directly connected networks.
4. Disabled auto-summarization with `no auto-summary`.
5. Verified learned routes and tested connectivity with `ping`.

---

## Verification and Testing

| Command | Purpose |
|---------|---------|
| `show ip route` | Confirm RIP routes (marked with `R`) are installed |
| `show ip protocols` | Verify RIPv2 is running and the advertised networks |
| `show ip rip database` | View the routes stored in the RIP database |
| `ping <destination-ip>` | Test end-to-end connectivity between LANs |
| `tracert <destination-ip>` | Trace the path between the two LANs |

**Result:** Each router learns the remote LAN dynamically via RIPv2, and laptops in LAN 1 successfully communicate with laptops in LAN 2 and vice versa.

---

## Screenshots

<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/f464c505-53fb-46a2-a666-1ba0f1b0b11d" alt="Full network topology" width="100%"/>
      <br/><sub><b>Network Topology</b></sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/1021a590-f113-4f5f-96d3-ba9ed2a530b2" alt="HQ routing table" width="100%"/>
      <br/><sub><b>HQ Routing Table (show ip route)</b></sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/0163cd46-dcaa-49a4-a0d1-ab4d5c09006d" alt="BRANCH routing table" width="100%"/>
      <br/><sub><b>BRANCH Routing Table (show ip route)</b></sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/15117ee0-71f4-482b-9a09-a6f092cc4d2a" alt="Ping test from Laptop0 to Laptop2" width="100%"/>
      <br/><sub><b>Ping Test: Laptop0 to Laptop2</b></sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/1d072ffe-9d1b-4617-8fba-ad9699ea70e4" alt="Ping test from Laptop3 to Laptop1" width="100%"/>
      <br/><sub><b>Ping Test: Laptop3 to Laptop1</b></sub>
    </td>
  </tr>
</table>

---

## Repository Structure

```
Dynamic-Routing-Using-RIPv2/
├── Dynamic-Routing-RIPv2.pkt     # Packet Tracer project file
└── README.md
```

---

## How to Run

1. Install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer).
2. Download or clone this repository.
3. Open `Dynamic-Routing-RIPv2.pkt` in Packet Tracer.
4. Open the CLI on each router and run `show ip route` and `show ip protocols`.
5. Use the Laptop Command Prompt to run `ping` tests between the LANs.

---

## Skills Demonstrated

- IPv4 Addressing and Subnetting
- Dynamic Routing with RIPv2
- Route Summarization Control (`no auto-summary`)
- Cisco IOS CLI
- ICMP Ping and Network Troubleshooting
- Network Documentation

---

## Author

<table>
  <tr>
    <td align="center">
      <a href="https://github.com/mohamedmousa2003">
        <img src="https://github.com/mohamedmousa2003.png" width="90" alt="Mohamed Moussa"/><br/>
        <sub><b>Mohamed Moussa</b></sub>
      </a>
    </td>
  </tr>
</table>
