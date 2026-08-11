# 🏢 Office Network with DHCP & DNS

## 📌 Overview

A small office network designed and configured using Cisco Packet Tracer.

This project builds on basic network configuration by introducing a dedicated server for **DHCP and DNS services**, allowing network devices to obtain their IP configuration automatically and use DNS for name resolution.

## 🖥️ Network Topology

The network consists of:

- 1 Router
- 1 Switch
- 1 DHCP/DNS Server
- 4 PCs

## ⚙️ Configuration

The following configurations were implemented:

- Configured the router hostname.
- Configured router passwords for basic access security.
- Configured the router interface with an IP address.
- Connected the router, switch, server, and end devices.
- Configured the server as a **DHCP server**.
- Configured the server as a **DNS server**.
- Enabled automatic IP address assignment for the PCs.
- Configured the required default gateway and DNS settings.
- Verified connectivity between network devices using `ping`.

## 🌐 DHCP Configuration

The DHCP server automatically assigns network configuration to the connected PCs, including:

- IP Address
- Subnet Mask
- Default Gateway
- DNS Server

This eliminates the need to manually configure an IP address on each PC.

## 🔎 DNS Configuration

A DNS service was configured on the server to provide name resolution within the network.

This allows devices to resolve configured domain names to their corresponding IP addresses.

## 🧪 Testing

Network connectivity was tested using the `ping` command.

The PCs were configured to obtain their IP addresses automatically from the DHCP server.

Example:

```bash
ipconfig
ping <destination-IP>
```

Successful ping responses confirmed connectivity between the configured devices.

## 🛠️ Tools & Technologies

- Cisco Packet Tracer
- IPv4
- DHCP
- DNS
- Ethernet
- Router & Switch Configuration
- IP Addressing
- Ping / Connectivity Testing

## 📂 Project Files

- `office-with-DHCP-DNS.pkt` — Cisco Packet Tracer project file.
- `topology.png` — Network topology screenshot.
- `README.md` — Project documentation.
