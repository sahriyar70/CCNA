# Routing Protocols & Configuration Guide (Cisco)

This guide covers the fundamentals of IP Routing, IGP, EGP, ASN, Static Routing, Dynamic Routing, OSPF, EIGRP, RIP, IS-IS, BGP, and essential Cisco IOS commands.

---

## 1. What is Routing?

**Routing** is the process of selecting the best path to forward packets from a source network to a destination network.

A router uses a **Routing Table** to determine where packets should be forwarded.

### Routing Table

```bash
show ip route
```

Displays the IPv4 routing table.

| Code | Meaning |
|---|---|
| C | Connected |
| L | Local |
| S | Static |
| R | RIP |
| D | EIGRP |
| O | OSPF |
| B | BGP |
| i | IS-IS |

---

## 2. Types of Routing

### Static Routing

Routes are manually configured by the network administrator.

**Purpose:** Small networks, specific routes, and default routes.

```bash
ip route <network> <subnet-mask> <next-hop>
```

Example:

```bash
R1(config)# ip route 192.168.20.0 255.255.255.0 10.0.0.2
```

Routes traffic for `192.168.20.0/24` through next-hop `10.0.0.2`.

### Default Routing

Used when no specific route exists for the destination.

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

This configures the Gateway of Last Resort.

### Dynamic Routing

Routers automatically learn and exchange routes using routing protocols.

**Purpose:** Large networks, automatic route updates, and network adaptability.

---

## 3. IGP (Interior Gateway Protocol)

**IGP** is used to exchange routing information within a single Autonomous System (AS).

An AS is a network or group of networks managed under a common routing policy.

### Types of IGP

| Protocol | Full Form | Main Function |
|---|---|---|
| RIP | Routing Information Protocol | Simple distance-vector routing |
| OSPF | Open Shortest Path First | Link-state routing |
| EIGRP | Enhanced Interior Gateway Routing Protocol | Advanced distance-vector routing |
| IS-IS | Intermediate System to Intermediate System | Link-state routing |

### RIP

RIP selects routes based on **Hop Count**.

- Maximum usable hop count: 15
- 16 hops means unreachable
- Suitable for small networks

```bash
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 192.168.10.0
R1(config-router)# no auto-summary
```

Verify:

```bash
show ip protocols
show ip route rip
```

---

## 4. OSPF (Open Shortest Path First)

OSPF is a **Link-State IGP** that uses the Shortest Path First (SPF) algorithm to calculate routes.

### Key Features

- Uses Cost as its routing metric
- Supports Area-based network design
- Area 0 is the Backbone Area
- Converges faster than RIP in many network scenarios

### OSPF Configuration

```bash
R1(config)# router ospf 1
R1(config-router)# router-id 1.1.1.1
R1(config-router)# network 192.168.10.0 0.0.0.255 area 0
```

**Explanation:**
- `1` = OSPF Process ID
- `1.1.1.1` = Router ID
- `0.0.0.255` = Wildcard Mask
- `area 0` = Backbone Area

### OSPF Verification

```bash
show ip ospf neighbor
show ip ospf
show ip route ospf
```

---

## 5. EIGRP (Enhanced Interior Gateway Routing Protocol)

EIGRP is an advanced distance-vector routing protocol that uses the **DUAL algorithm** to calculate loop-free paths.

### Key Features

- Uses Bandwidth and Delay by default
- Supports fast convergence
- Supports unequal-cost load balancing
- Uses Autonomous System numbers in classic configuration

### EIGRP Configuration

```bash
R1(config)# router eigrp 100
R1(config-router)# network 192.168.10.0 0.0.0.255
R1(config-router)# no auto-summary
```

Here, `100` is the EIGRP Autonomous System number.

### EIGRP Verification

```bash
show ip eigrp neighbors
show ip eigrp topology
show ip route eigrp
```

---

## 6. IS-IS (Intermediate System to Intermediate System)

IS-IS is a Link-State routing protocol commonly used in large service-provider networks.

### Key Features

- Uses the SPF algorithm
- Supports hierarchical routing
- Level 1: Routing within an area
- Level 2: Routing between areas

### Basic Configuration

```bash
R1(config)# router isis
R1(config-router)# net 49.0001.0000.0000.0001.00
```

Enable IS-IS on an interface:

```bash
R1(config)# interface g0/0
R1(config-if)# ip router isis
```

### Verification

```bash
show isis neighbors
show isis database
```

---

## 7. EGP (Exterior Gateway Protocol)

**EGP** is used to exchange routing information between different Autonomous Systems.

The modern Internet primarily uses **BGP (Border Gateway Protocol)**.

### IGP vs EGP

| Feature | IGP | EGP |
|---|---|---|
| Scope | Within an AS | Between ASes |
| Main Purpose | Internal routing | Inter-AS routing |
| Examples | OSPF, RIP, EIGRP, IS-IS | BGP |

---

## 8. ASN (Autonomous System Number)

An **ASN** is a unique number assigned to an Autonomous System for routing identification.

### ASN Types

| Type | Range |
|---|---|
| Public 16-bit ASN | 1–64511 |
| Private 16-bit ASN | 64512–65534 |
| Public 32-bit ASN | 1–4294967295 |

Private ASN ranges are reserved for private use within applicable ranges.

---

## 9. BGP (Border Gateway Protocol)

BGP is a **Path-Vector EGP** used to exchange routing information between Autonomous Systems.

It is the primary routing protocol of the Internet.

### Key Features

- Uses TCP port 179
- Uses AS-PATH to prevent routing loops
- Supports policy-based routing decisions
- eBGP: Between different ASes
- iBGP: Within the same AS

### Basic BGP Configuration

Example: R1 belongs to AS 65001 and its neighbor belongs to AS 65002.

```bash
R1(config)# router bgp 65001
R1(config-router)# neighbor 10.0.0.2 remote-as 65002
R1(config-router)# network 192.168.10.0 mask 255.255.255.0
```

**Note:** The network being advertised must exist in the routing table with the exact prefix.

### BGP Verification

```bash
show ip bgp summary
show ip bgp
show ip route bgp
```

---

## 10. Administrative Distance (AD)

Administrative Distance determines which routing source is preferred when multiple sources provide a route to the same destination prefix.

**Lower AD = More preferred**

| Route Source | Default AD |
|---|---:|
| Connected | 0 |
| Static | 1 |
| eBGP | 20 |
| EIGRP | 90 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| iBGP | 200 |

---

## 11. Route Redistribution

**Redistribution** allows routes learned from one routing protocol to be introduced into another routing protocol.

Example: Redistribute OSPF routes into EIGRP.

```bash
R1(config)# router eigrp 100
R1(config-router)# redistribute ospf 1
```

Redistribution may require metric configuration and route filtering to prevent routing loops.

---

## 12. Essential Routing Commands

| Command | Function |
|---|---|
| `show ip route` | Display routing table |
| `show ip protocols` | Display active routing protocols |
| `show ip interface brief` | Check interface status and IPs |
| `show running-config` | Display current configuration |
| `show ip route static` | Display static routes |
| `show ip ospf neighbor` | Check OSPF neighbors |
| `show ip eigrp neighbors` | Check EIGRP neighbors |
| `show ip bgp summary` | Check BGP sessions |
| `ping <IP>` | Test connectivity |
| `traceroute <IP>` | Trace packet path |
| `copy running-config startup-config` | Save configuration |

---

## Summary

- **Routing:** Forwarding packets using the best available path.
- **Static Routing:** Manually configured routes.
- **Dynamic Routing:** Automatically learns and exchanges routes.
- **IGP:** Routing within an Autonomous System.
- **EGP:** Routing between Autonomous Systems.
- **OSPF:** Link-state routing using Cost and Areas.
- **EIGRP:** Advanced distance-vector routing using DUAL.
- **RIP:** Distance-vector routing using Hop Count.
- **IS-IS:** Link-state routing using SPF and hierarchical levels.
- **BGP:** Path-vector routing used between Autonomous Systems.
- **ASN:** Identifies an Autonomous System.

### References

- [Cisco IP Routing Configuration Guide](https://www.cisco.com/c/en/us/td/docs/routers/ios-xe/ip-routing/b-ip-routing/m_iri-ip-prot-indep-0.html)
- [Cisco Basic Router Configuration](https://www.cisco.com/en/US/docs/routers/access/800/850/software/configuration/guide/routconf.html)

