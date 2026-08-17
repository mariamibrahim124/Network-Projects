# Inter-VLAN Routing Comparison

This comparison highlights the key differences between two approaches to **Inter-VLAN Routing** implemented in this repository:

* Router-on-a-Stick
* Layer 3 Switch with SVIs

| Feature              | Router-on-a-Stick                    | Layer 3 Switch                     |
| -------------------- | ------------------------------------ | ---------------------------------- |
| Inter-VLAN Routing   | Router                               | Layer 3 Switch                     |
| VLAN Gateway         | Router Subinterface                  | SVI                                |
| 802.1Q Subinterfaces | Required                             | Not required for SVIs              |
| `ip routing`         | Not required on L2 switch            | Required                           |
| Routed Port          | Usually not required for local VLANs | Used toward router/other L3 device |
| Scalability          | Suitable for small networks          | Better suited for larger networks  |
| Main Technology      | Router Subinterfaces                 | SVIs + Layer 3 Routing             |

## Router-on-a-Stick

Router-on-a-Stick uses a single physical router interface divided into multiple **subinterfaces**, with each subinterface acting as the default gateway for a VLAN.

```text
VLAN 10 ──┐
          ├── Switch ── Trunk ── Router
VLAN 20 ──┘
```

The router performs the Inter-VLAN Routing using **802.1Q encapsulation**.

### Key Commands

```bash
interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
```

---

## Layer 3 Switch

A Layer 3 switch performs Inter-VLAN Routing using **Switch Virtual Interfaces (SVIs)**.

```text
VLAN 10 ──┐
          ├── Layer 3 Switch ── Router
VLAN 20 ──┘
```

Each VLAN has an SVI that acts as its default gateway.

### Key Commands

```bash
ip routing

interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown

interface vlan 20
ip address 192.168.20.1 255.255.255.0
no shutdown
```

The Layer 3 switch can also use routing to reach networks beyond its directly connected VLANs.

---

## Summary

**Router-on-a-Stick** is a simple approach suitable for smaller networks, while a **Layer 3 Switch** provides routing directly at the switching layer and is more scalable for larger network environments.

Both approaches provide **Inter-VLAN Routing**, but they use different Layer 3 technologies to achieve it.
