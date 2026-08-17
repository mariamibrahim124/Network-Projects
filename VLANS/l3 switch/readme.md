# VLANs and Inter-VLAN Routing using a Layer 3 Switch

## Overview

This project demonstrates a multi-switch network using **VLAN segmentation, trunking, Inter-VLAN Routing, Layer 3 Switching, and routing to remote networks**.

Unlike the Router-on-a-Stick approach, the Inter-VLAN Routing process in this project is performed directly by a **Layer 3 switch**.

The Layer 3 switch operates as both:

* A switch for Layer 2 VLAN connectivity.
* A router for Layer 3 communication between VLANs and remote networks.

The project was designed and tested using **Cisco Packet Tracer**.

---

## Objectives

* Create and configure VLANs.
* Assign end devices to different VLANs.
* Configure access ports.
* Configure trunk links between switches.
* Configure a Layer 3 switch for Inter-VLAN Routing.
* Configure Switch Virtual Interfaces (SVIs).
* Enable Layer 3 routing using `ip routing`.
* Configure a routed port toward the external router.
* Configure routing to remote networks.
* Verify the routing table.
* Test connectivity between local VLANs and remote networks.

---

# VLAN and IP Addressing

| VLAN        | Network         | Example Hosts               |
| ----------- | --------------- | --------------------------- |
| VLAN 10     | 192.168.10.0/24 | 192.168.10.3                |
| VLAN 20     | 192.168.20.0/24 | 192.168.20.2, 192.168.20.23 |
| Routed Link | 192.168.30.0/24 | Layer 3 Switch ↔ Router     |

The VLAN networks are directly connected to the Layer 3 switch through their SVIs.

---

# Key Networking Concepts

## VLANs

VLANs logically divide a switched network into separate broadcast domains.

In this project:

```text
VLAN 10 → 192.168.10.0/24
VLAN 20 → 192.168.20.0/24
```

---

## Trunking

Trunk links are used between the Layer 2 switches and the Layer 3 switch so that multiple VLANs can traverse the same physical connection.

```bash
switchport mode trunk
```

---

## SVI — Switch Virtual Interface

A Layer 3 switch can provide a Layer 3 gateway for each VLAN using an SVI.

For example:

```text
interface vlan 10
→ Gateway for VLAN 10

interface vlan 20
→ Gateway for VLAN 20
```

---

# Layer 3 Switch Configuration

## 1. Create VLANs

```bash
enable
configure terminal
vlan 10
vlan 20
```

---

## 2. Configure Access Ports

Example for VLAN 10:

```bash
interface fa0/1
switchport mode access
switchport access vlan 10
```

Example for VLAN 20:

```bash
interface fa0/2
switchport mode access
switchport access vlan 20
```

---

## 3. Configure Trunk Ports

```bash
interface fa0/1
switchport mode trunk
```

Trunking allows VLAN traffic to travel between the access switches and the Layer 3 switch.

---

# Inter-VLAN Routing using SVIs

The Layer 3 switch provides a gateway for each VLAN.

## VLAN 10 SVI

```bash
interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown
```

## VLAN 20 SVI

```bash
interface vlan 20
ip address 192.168.20.1 255.255.255.0
no shutdown
```

---

## Enable Layer 3 Routing

The Layer 3 switch must be configured to perform routing:

```bash
ip routing
```

After enabling routing, the switch can route traffic between:

```text
192.168.10.0/24
        ↕
192.168.20.0/24
```

This means the Layer 3 switch performs **Inter-VLAN Routing without requiring a separate router for the local VLANs**.

---

# Routed Port toward the Router

The connection between the Layer 3 switch and the router is configured as a **Layer 3 routed interface** rather than a normal switchport.

```bash
interface fa0/3
no switchport
ip address 192.168.30.1 255.255.255.0
no shutdown
```

The router can use an IP address from the same routed network, for example:

```bash
interface g0/0
ip address 192.168.30.2 255.255.255.0
no shutdown
```

The exact interface names/IPs should match the Packet Tracer topology.

---

# Routing to Remote Networks

One of the important parts of this project is that the Layer 3 switch does not only route between its directly connected VLANs.

It also needs to **learn routes to networks that are not directly connected to it**.
Ex. the switch doesn't see 8.8.8.8 network and the router doesn't see 192.168.10.0 and 192.168.20.0 so we should use routing method.

---

## Default Static Route

To reach remote networks, the Layer 3 switch uses a default static route pointing to the router:

```
ip route 0.0.0.0 0.0.0.0 192.168.30.2
```

* `0.0.0.0/0` → Any unknown destination.
* `192.168.30.2` → Router next-hop.\

The router is also configured with static routes to reach the VLAN networks behind the Layer 3 switch:

```
ip route 192.168.10.0 255.255.255.0 192.168.30.1 (next-hop)
ip route 192.168.20.0 255.255.255.0 192.168.30.1
```

Verify with:

```
show ip route
```

---

# Verification Commands

## Verify VLANs

```bash
show vlan brief
```

## Verify Trunk Links

```bash
show interfaces trunk
```

## Verify SVIs

```bash
show ip interface brief
```

This is especially useful for checking:

```text
Vlan10
Vlan20
```

and their IP addresses/status.

---

## Verify Routing

```bash
show ip route
```

This is one of the most important commands in this project.

It can be used to verify:

* Connected VLAN networks.
* Routed networks.
* Static routes.
* Remote networks.

---

## Verify Running Configuration

```bash
show running-config
```

Used to inspect the complete active configuration.

---

## Test Network Connectivity

from vlan 10 (pc 192.168.10.2):

    ping 192.168.10.3                same vlan

    ping 192.168.20.3                different vlan

    ping 8.8.8.8                          remote network

---

# Important Commands Quick Reference

| Command                     | Purpose                                       |
| --------------------------- | --------------------------------------------- |
| `vlan 10`                   | Create VLAN 10                                |
| `switchport mode access`    | Configure an access port                      |
| `switchport access vlan 10` | Assign a port to VLAN 10                      |
| `switchport mode trunk`     | Configure a trunk                             |
| `interface vlan 10`         | Create/configure VLAN 10 SVI                  |
| `ip address ...`            | Assign an IP address                          |
| `ip routing`                | Enable Layer 3 routing                        |
| `no switchport`             | Convert a switch interface into a routed port |
| `ip route ...`              | Configure a static route                      |
| `show vlan brief`           | Verify VLANs                                  |
| `show interfaces trunk`     | Verify trunking                               |
| `show ip interface brief`   | Verify interfaces and IP addresses            |
| `show ip route`             | Verify the routing table                      |
| `show running-config`       | View the active configuration                 |
| `ping`                      | Test connectivity                             |
