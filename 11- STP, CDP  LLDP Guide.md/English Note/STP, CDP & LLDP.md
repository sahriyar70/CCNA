# Cisco Switch: STP, CDP & LLDP Guide

## 1. STP (Spanning Tree Protocol)

**STP** is a Layer 2 protocol that prevents switching loops caused by redundant connections between switches.

### STP Port States

| State      | Function                                              |
| ---------- | ----------------------------------------------------- |
| Blocking   | Does not forward regular data; processes BPDUs        |
| Listening  | Processes BPDUs and helps prevent loops               |
| Learning   | Learns MAC addresses and builds the MAC address table |
| Forwarding | Forwards normal network traffic                       |
| Disabled   | The port does not participate in STP                  |

**Note:** These five states belong to Legacy STP. RSTP uses three states: `Discarding`, `Learning`, and `Forwarding`.

### STP Commands

**View STP information for all VLANs:**

```text
Switch# show spanning-tree
```

**View STP information for a specific VLAN:**

```text
Switch# show spanning-tree vlan 1
```

**How to interpret the output:**

* `FWD` — Forwarding state
* `BLK` — Blocking state
* `Root ID` — Information about the Root Bridge
* `Bridge ID` — Information about the local switch's Bridge ID

---

## 2. CDP (Cisco Discovery Protocol)

**CDP** is a Cisco-proprietary Layer 2 protocol that identifies directly connected neighboring devices.

### Information Available Through CDP

* Device ID / Hostname
* Local Interface and Remote Port
* Device Platform
* IP Address
* Device Capabilities

### CDP Commands

**Enable CDP globally:**

```text
Switch(config)# cdp run
```

**View neighboring devices:**

```text
Switch# show cdp neighbors
```

**View detailed neighbor information:**

```text
Switch# show cdp neighbors detail
```

**Disable CDP on a specific interface:**

```text
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# no cdp enable
```

**Note:** CDP is commonly enabled by default on Cisco devices, but this may vary depending on the configuration.

---

## 3. LLDP (Link Layer Discovery Protocol)

**LLDP** is a vendor-neutral Layer 2 discovery protocol standardized by IEEE 802.1AB. It helps discover neighboring network devices from different manufacturers.

### LLDP Commands

**Enable LLDP globally:**

```text
Switch(config)# lldp run
```

**View neighboring devices:**

```text
Switch# show lldp neighbors
```

**View detailed neighbor information:**

```text
Switch# show lldp neighbors detail
```

**Disable LLDP on a specific interface:**

```text
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# no lldp transmit
Switch(config-if)# no lldp receive
```

* `no lldp transmit` — Disables sending LLDP advertisements.
* `no lldp receive` — Disables receiving LLDP advertisements.

**Note:** LLDP is commonly disabled by default on Cisco devices, but this may vary depending on the device and configuration.

---

## 4. CDP vs LLDP

| Feature       | CDP                      | LLDP                          |
| ------------- | ------------------------ | ----------------------------- |
| Full Form     | Cisco Discovery Protocol | Link Layer Discovery Protocol |
| Type          | Proprietary              | Open Standard                 |
| Standard      | Cisco-specific           | IEEE 802.1AB                  |
| Compatibility | Primarily Cisco devices  | Devices from multiple vendors |
| Purpose       | Neighbor Discovery       | Neighbor Discovery            |

---

## 5. Quick Verification Commands

| Command                      | Purpose                         |
| ---------------------------- | ------------------------------- |
| `show spanning-tree`         | View STP status                 |
| `show spanning-tree vlan 1`  | View STP information for VLAN 1 |
| `show cdp neighbors`         | View CDP neighbors              |
| `show cdp neighbors detail`  | View detailed CDP information   |
| `show lldp neighbors`        | View LLDP neighbors             |
| `show lldp neighbors detail` | View detailed LLDP information  |
| `show running-config`        | View the current configuration  |

**Important:** Not all commands are supported on every Cisco model or IOS version. Always verify the current configuration before making changes to a lab or production network.
