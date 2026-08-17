# VLANs and Inter-VLAN Routing using Router-on-a-Stick

## Overview

This project demonstrates how to design and configure a small multi-switch network using **VLANs, trunking, and Inter-VLAN Routing**.

The network contains multiple switches and end devices divided into two VLANs. Since devices in different VLANs belong to different broadcast domains, a router is used to provide communication between them using the **Router-on-a-Stick** technique.

The project was implemented and tested using **Cisco Packet Tracer**.

---

## Objectives

* Create and configure VLANs.
* Assign end devices to the appropriate VLANs.
* Configure switch access ports.
* Configure trunk links between switches.
* Extend VLANs across multiple switches.
* Configure Inter-VLAN Routing using Router-on-a-Stick.
* Configure router subinterfaces using 802.1Q encapsulation.
* Verify VLAN and trunk configurations.
* Test connectivity between devices in different VLANs.

---

## VLAN and IP Addressing

| VLAN    | Network         | Example Hosts               |
| ------- | --------------- | --------------------------- |
| VLAN 10 | 192.168.10.0/24 | 192.168.10.2, 192.168.10.3  |
| VLAN 20 | 192.168.20.0/24 | 192.168.20.2, 192.168.20.23 |

The VLANs represent separate Layer 2 broadcast domains.

Devices within the same VLAN can communicate at Layer 2, while communication between VLANs requires Layer 3 routing.

---

## Key Networking Concepts

### VLAN

A VLAN logically separates devices into different broadcast domains on the same physical switching infrastructure.

### Access Port

An access port carries traffic for a single VLAN and is normally used to connect end devices such as PCs.

### Trunk Port

A trunk port carries traffic from multiple VLANs over a single physical link using VLAN tagging.

### Inter-VLAN Routing

Inter-VLAN Routing allows devices in different VLANs to communicate through a Layer 3 device.

### Router-on-a-Stick

Router-on-a-Stick uses a single physical router interface divided into multiple **subinterfaces**.

Each subinterface represents a VLAN and is configured with:

* An IP address.
* 802.1Q encapsulation.
* The corresponding VLAN ID.

---

# Configuration

## 1. Create VLANs

On the switches:

```bash
enable
configure terminal
vlan 10
vlan 20
```

---

## 2. Configure Access Ports

For a device belonging to VLAN 10:

```bash
interface fa0/1
switchport mode access
switchport access vlan 10
```

For a device belonging to VLAN 20:

```bash
interface fa0/2
switchport mode access
switchport access vlan 20
```

---

## 3. Configure Trunk Ports

Trunking is required between switches so that VLAN 10 and VLAN 20 can travel across the links.

```bash
interface fa0/3
switchport mode trunk
```

The same concept is applied to the trunk connection between the switch and the router.

---

# Router-on-a-Stick Configuration

The router's physical interface is divided into subinterfaces.

## VLAN 10 Subinterface

```bash
interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
```

## VLAN 20 Subinterface

```bash
interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
```

The physical interface must also be enabled:

```bash
interface g0/0
no shutdown
```

The router now acts as the default gateway for both VLANs.

```text
VLAN 10 → 192.168.10.1
VLAN 20 → 192.168.20.1
```

---

# Default Gateway

Each PC must use the IP address of its VLAN's router subinterface as its default gateway.

| VLAN    | Default Gateway |
| ------- | --------------- |
| VLAN 10 | 192.168.10.1    |
| VLAN 20 | 192.168.20.1    |

This allows the PCs to send traffic outside their local subnet.

---

# Verification Commands

## Verify VLANs

```bash
show vlan brief
```

Used to verify:

* VLAN creation.
* VLAN IDs.
* Access port assignments.

## Verify Trunking

```bash
show interfaces trunk
```

Used to verify:

* Trunk ports.
* Trunk status.
* VLANs carried over trunk links.

## Verify IP Interfaces

```bash
show ip interface brief
```

Used to quickly check interface status and IP addressing.

## Verify Router Configuration

```bash
show running-config
```

Used to inspect the active configuration, including router subinterfaces and encapsulation.

## Check Routing Table

```bash
show ip route
```

Used to verify the networks known by the router.

---

# Connectivity Testing

The network was tested using ICMP/Ping.

### Same VLAN

Devices in the same VLAN should be able to communicate directly through Layer 2 switching.

Example:

```bash
ping 192.168.10.3
```

### Different VLANs

A device in VLAN 10 should be able to communicate with a device in VLAN 20 through the router.

Example:

```bash
ping 192.168.20.23
```

This verifies that **Inter-VLAN Routing** is working correctly.

---

# Important Commands Quick Reference

| Command                     | Purpose                                 |
| --------------------------- | --------------------------------------- |
| `vlan 10`                   | Create VLAN 10                          |
| `switchport mode access`    | Configure an access port                |
| `switchport access vlan 10` | Assign a port to VLAN 10                |
| `switchport mode trunk`     | Configure a trunk port                  |
| `interface g0/0.10`         | Create VLAN 10 router subinterface      |
| `encapsulation dot1Q 10`    | Associate the subinterface with VLAN 10 |
| `ip address ...`            | Assign an IP address                    |
| `no shutdown`               | Enable an interface                     |
| `show vlan brief`           | Verify VLANs                            |
| `show interfaces trunk`     | Verify trunking                         |
| `show ip interface brief`   | Verify interface status/IPs             |
| `show ip route`             | Verify routing table                    |
| `ping`                      | Test connectivity                       |

---
