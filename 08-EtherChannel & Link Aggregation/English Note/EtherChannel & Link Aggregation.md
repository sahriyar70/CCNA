# EtherChannel — CCNA Notes

## 1. Definition

**EtherChannel** is a Cisco technology that combines multiple physical Ethernet links into one logical link to increase bandwidth and provide redundancy.

## 2. Advantages

* Increases available bandwidth.
* Provides link redundancy.
* Supports load balancing.
* Reduces the impact of individual link failures.
* STP treats a properly configured Layer 2 EtherChannel as one logical link.

## 3. EtherChannel Protocols

| Protocol | Description                                              |
| -------- | -------------------------------------------------------- |
| PAgP     | Cisco proprietary negotiation protocol                   |
| LACP     | IEEE standard (802.1AX; historically 802.3ad)            |
| Static   | Manually configured using `mode on`, without negotiation |

## 4. Negotiation Modes

### PAgP

* `desirable` — Actively negotiates.
* `auto` — Passively waits for negotiation.

### LACP

* `active` — Actively negotiates.
* `passive` — Responds to negotiation.

**Note:** `on` mode does not negotiate and must be configured consistently on both ends.

## 5. Configuration Commands

### LACP Configuration

**Switch 1:**

```cisco
enable
configure terminal
interface range gigabitEthernet0/1-2
channel-group 1 mode active
exit
interface port-channel 1
switchport mode trunk
end
copy running-config startup-config
```

**Switch 2:**

```cisco
enable
configure terminal
interface range gigabitEthernet0/1-2
channel-group 1 mode active
exit
interface port-channel 1
switchport mode trunk
end
copy running-config startup-config
```

*Use matching interface numbers and compatible settings on both switches. Configure trunking only when required by your topology.*

## 6. Verification Commands

```cisco
show etherchannel summary
show etherchannel detail
show interfaces port-channel 1
show lacp neighbor
show running-config
```

## 7. Common EtherChannel Requirements

* Member links should have compatible speed and duplex settings.
* Switchport mode must match.
* Access VLAN or trunk settings must be compatible.
* Allowed VLAN lists should be compatible on trunk links.
* Both ends must use compatible negotiation settings.
* A physical interface should not belong to multiple EtherChannels.

## 8. Troubleshooting

| Problem                 | Possible Cause                                 |
| ----------------------- | ---------------------------------------------- |
| Channel does not form   | Protocol or mode mismatch                      |
| Port is suspended       | Incompatible member-port configuration         |
| VLAN traffic fails      | Trunk or VLAN mismatch                         |
| Only one link is active | Configuration issue or load-balancing behavior |

Check the configuration on both switches before making changes.

## 9. Quick Revision

* **EtherChannel:** Multiple physical links combined into one logical link.
* **LACP:** Open standard.
* **PAgP:** Cisco proprietary.
* **Active:** Actively negotiates LACP.
* **Passive:** Responds to LACP negotiation.
* **Desirable:** Actively negotiates PAgP.
* **Auto:** Passively negotiates PAgP.
* **Port-Channel:** Logical interface representing the bundled links.
* **STP:** Treats a properly formed Layer 2 EtherChannel as one logical link.
