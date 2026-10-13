# Spanning Tree Protocol (STP) — CCNA Notes

## 1. What is STP?

**Spanning Tree Protocol (STP)** is a Layer 2 protocol that prevents switching loops in Ethernet networks by blocking redundant paths.

**Standard:** IEEE 802.1D

## 2. Why Is STP Needed?

Without STP, switching loops can cause:

* Broadcast storms
* MAC address table instability (MAC flapping)
* Duplicate frames
* Network congestion and outages

## 3. How STP Works

1. Switches exchange BPDUs.
2. A Root Bridge is elected.
3. Each non-root switch selects a Root Port.
4. Designated Ports are selected for network segments.
5. Redundant paths are prevented from forwarding to avoid loops.
6. An alternative path may become active when a link fails.

## 4. BPDU (Bridge Protocol Data Unit)

BPDUs are Layer 2 control frames used by switches to exchange spanning-tree information.

They help elect the Root Bridge, calculate paths, and determine port roles.

## 5. Root Bridge Election

The switch with the **lowest Bridge ID** becomes the Root Bridge.

Bridge ID includes:

* Bridge Priority
* Extended System ID
* MAC Address

Lower priority is preferred. If the relevant priority values are equal, the lower MAC address generally wins.

## 6. STP Port Roles

| Port Role            | Function                                                |
| -------------------- | ------------------------------------------------------- |
| Root Port (RP)       | Best path toward the Root Bridge                        |
| Designated Port (DP) | Forwarding port for a network segment                   |
| Alternate Port       | Backup path toward the Root Bridge                      |
| Backup Port          | Backup for a Designated Port on the same shared segment |

## 7. STP Port States

Classic STP has five states:

| State      | Function                                  |
| ---------- | ----------------------------------------- |
| Disabled   | Not participating in normal STP operation |
| Blocking   | Does not forward user traffic             |
| Listening  | Processes STP information                 |
| Learning   | Learns MAC addresses                      |
| Forwarding | Forwards traffic and learns MAC addresses |

**RSTP** uses three states: Discarding, Learning, and Forwarding.

## 8. STP Timers

| Timer         |    Default |
| ------------- | ---------: |
| Hello Time    |  2 seconds |
| Forward Delay | 15 seconds |
| Max Age       | 20 seconds |

These are typical classic STP defaults.

## 9. Types of STP

* **STP (IEEE 802.1D):** Traditional loop prevention.
* **RSTP:** Faster convergence.
* **PVST+:** Separate spanning-tree instance per VLAN.
* **Rapid PVST+:** Rapid spanning tree per VLAN.
* **MSTP:** Maps multiple VLANs to spanning-tree instances.

## 10. Cisco STP Commands

### Check STP Information

```cisco
show spanning-tree
show spanning-tree vlan 10
show spanning-tree summary
show spanning-tree root
```

### Configure STP Mode

```cisco
configure terminal
spanning-tree mode rapid-pvst
```

### Configure Root Bridge

```cisco
spanning-tree vlan 10 root primary
```

### Configure Secondary Root

```cisco
spanning-tree vlan 10 root secondary
```

### Set Bridge Priority

```cisco
spanning-tree vlan 10 priority 4096
```

### Save Configuration

```cisco
end
copy running-config startup-config
```

## 11. PortFast and BPDU Guard

**PortFast:** Allows eligible end-device-facing ports to transition rapidly to forwarding.

```cisco
interface gigabitEthernet0/10
spanning-tree portfast
```

**BPDU Guard:** Protects edge ports by placing a port into an error-disabled state when a BPDU is received.

```cisco
interface gigabitEthernet0/10
spanning-tree bpduguard enable
```

Use PortFast on appropriate edge ports, not ordinary switch-to-switch links.

## 12. Root Guard and Loop Guard

**Root Guard:** Helps prevent an unexpected superior BPDU from changing the intended Root Bridge.

```cisco
interface gigabitEthernet0/2
spanning-tree guard root
```

**Loop Guard:** Helps prevent certain ports from incorrectly transitioning to forwarding when expected BPDUs stop arriving.

```cisco
spanning-tree loopguard default
```

Check feature support on your switch model and IOS version.

## 13. STP and EtherChannel

EtherChannel combines multiple physical links into one logical Port-Channel. STP generally treats a correctly formed Layer 2 EtherChannel as one logical port.

```cisco
show etherchannel summary
show spanning-tree
```

## 14. Troubleshooting Commands

```cisco
show spanning-tree
show spanning-tree vlan 10
show interfaces status
show mac address-table
show logging
show running-config
```

Check the Root Bridge, port roles, link status, VLAN settings, and configuration mismatches.

## 15. Security Best Practices

* Configure the intended Root Bridge.
* Use PortFast and BPDU Guard on appropriate edge ports.
* Use Root Guard on suitable designated ports.
* Disable unused switch ports.
* Monitor topology changes and MAC flapping.
* Back up configurations before making changes.

## Quick Revision

* **STP:** Prevents Layer 2 loops.
* **Root Bridge:** The switch with the lowest Bridge ID.
* **BPDU:** STP control frame.
* **Root Port:** Best path toward the Root Bridge.
* **Designated Port:** Forwarding port for a segment.
* **RSTP:** Provides faster convergence.
* **PortFast:** Speeds up edge-port transition.
* **BPDU Guard:** Protects edge ports against unexpected BPDUs.

## Official References

* [Cisco Spanning Tree Configuration Guide](https://www.cisco.com/c/en/us/td/docs/switches/lan/c9000/lyr2-fwd/stp/stp-configuration-guide/m-stp.html)
* [Cisco PortFast and BPDU Guard](https://www.cisco.com/c/en/us/support/docs/lan-switching/spanning-tree-protocol/10586-65.html)
