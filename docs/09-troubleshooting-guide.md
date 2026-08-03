# Troubleshooting Guide

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#troubleshooting-methodology"><img src="https://img.shields.io/badge/METHODOLOGY-27AE60?style=for-the-badge"></a>
<a href="#layer-1-issues"><img src="https://img.shields.io/badge/LAYER%201-8E44AD?style=for-the-badge"></a>
<a href="#layer-2-issues"><img src="https://img.shields.io/badge/LAYER%202-16A085?style=for-the-badge"></a>
<a href="#layer-3-issues"><img src="https://img.shields.io/badge/LAYER%203-2980B9?style=for-the-badge"></a>
<a href="#ospf-troubleshooting"><img src="https://img.shields.io/badge/OSPF-3498DB?style=for-the-badge"></a>
<a href="#bgp-troubleshooting"><img src="https://img.shields.io/badge/BGP-E67E22?style=for-the-badge"></a>
<a href="#hsrp-troubleshooting"><img src="https://img.shields.io/badge/HSRP-D35400?style=for-the-badge"></a>

</p>

<p align="left">

<a href="#acl-troubleshooting"><img src="https://img.shields.io/badge/ACL-C0392B?style=for-the-badge"></a>
<a href="#nat-troubleshooting"><img src="https://img.shields.io/badge/NAT-9B59B6?style=for-the-badge"></a>
<a href="#dhcp-troubleshooting"><img src="https://img.shields.io/badge/DHCP-E74C3C?style=for-the-badge"></a>
<a href="#dns-troubleshooting"><img src="https://img.shields.io/badge/DNS-2ECC71?style=for-the-badge"></a>
<a href="#management-troubleshooting"><img src="https://img.shields.io/badge/MANAGEMENT-1ABC9C?style=for-the-badge"></a>
<a href="#monitoring-troubleshooting"><img src="https://img.shields.io/badge/MONITORING-34495E?style=for-the-badge"></a>
<a href="#best-practices"><img src="https://img.shields.io/badge/BEST%20PRACTICES-7F8C8D?style=for-the-badge"></a>
<a href="#summary"><img src="https://img.shields.io/badge/SUMMARY-2C3E50?style=for-the-badge"></a>

</p>

## Overview

This document provides standardized troubleshooting procedures for diagnosing and resolving common issues within the Secure Enterprise Network Infrastructure.

The guide is intended to assist network administrators and engineers in identifying faults, verifying root causes, restoring network services, and minimizing service disruption.

The troubleshooting procedures cover Layer 1 connectivity, Layer 2 switching, Layer 3 routing, gateway redundancy, security controls, infrastructure services, and centralized management.

This document complements the Test Plan by providing operational guidance when validation tests fail or production issues occur.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Troubleshooting Methodology

All troubleshooting activities follow a structured methodology based on the OSI model and Cisco enterprise operational best practices.

### Troubleshooting Workflow

| Step | Activity | Objective |
|:----:|:---------|:----------|
| **1** | Identify the problem | Collect symptoms, user reports, alarms, and monitoring data. |
| **2** | Verify physical connectivity | Check interfaces, cables, power, and link status. |
| **3** | Isolate the affected layer | Determine whether the issue is Layer 1, Layer 2, Layer 3, security, or application related. |
| **4** | Collect diagnostic information | Review interface status, routing tables, logs, counters, and protocol states. |
| **5** | Determine the root cause | Correlate collected information to identify the underlying problem. |
| **6** | Implement corrective action | Apply the minimum required change to restore service. |
| **7** | Validate the solution | Confirm that the issue has been resolved and services operate normally. |
| **8** | Document findings | Record the incident, root cause, corrective action, and preventive recommendations. |

### Recommended Troubleshooting Order

| Priority | Layer | Examples |
|:--------:|:------|:---------|
| **1** | Physical Layer | Cable failures, interface down, duplex mismatch |
| **2** | Data Link Layer | VLAN mismatch, trunk failure, STP issues |
| **3** | Network Layer | Routing failures, OSPF, BGP, HSRP |
| **4** | Security | ACL, NAT, Firewall policies |
| **5** | Infrastructure Services | DHCP, DNS, NTP |
| **6** | Monitoring | SNMP, Syslog, LibreNMS |

### Common Diagnostic Commands

| Command | Purpose |
|:---------|:--------|
| `show ip interface brief` | Verify interface status |
| `show interfaces` | Display interface statistics |
| `show vlan brief` | Verify VLAN assignment |
| `show interfaces trunk` | Verify trunk operation |
| `show spanning-tree` | Verify STP status |
| `show ip route` | Display routing table |
| `show ip ospf neighbor` | Verify OSPF neighbors |
| `show ip ospf interface brief` | Verify OSPF interfaces |
| `show ip bgp summary` | Verify BGP session |
| `show standby brief` | Verify HSRP status |
| `show access-lists` | Verify ACL matches |
| `show ip nat translations` | Verify NAT translations |
| `show logging` | Review system logs |
| `ping` | Test Layer 3 connectivity |
| `traceroute` | Trace packet path |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Layer 1 Issues

Physical connectivity problems are the most common source of network outages. Layer 1 troubleshooting should always be completed before investigating higher-layer protocols.

### L1-001 — Interface Down

#### Symptoms

- Interface status shows **administratively down** or **down/down**
- No connectivity to neighboring device
- Ping fails immediately
- Link LEDs are off

#### Possible Causes

- Interface shutdown
- Faulty cable
- Power loss
- Incorrect interface selection
- Hardware failure

#### Diagnostic Commands

```text
show ip interface brief
show interfaces
```

#### Resolution

- Verify cable connections.
- Confirm the correct interface is connected.
- Enable the interface using `no shutdown`.
- Replace faulty cables if necessary.
- Verify device power status.

#### Prevention

- Label physical interfaces.
- Use standardized cabling.
- Document interface assignments.


### L1-002 — Interface Errors

#### Symptoms

- CRC errors increase
- Input errors increase
- Output drops
- Packet loss observed

#### Possible Causes

- Damaged cable
- Faulty NIC
- Duplex mismatch
- Speed mismatch
- Hardware issue

#### Diagnostic Commands

```text
show interfaces
```

#### Resolution

- Replace damaged cables.
- Configure matching speed and duplex settings.
- Verify interface hardware.
- Clear counters after corrective action.

#### Prevention

- Use Auto-Negotiation whenever supported.
- Inspect cabling during maintenance.
- Monitor interface error counters.

### L1-003 — Duplex or Speed Mismatch

#### Symptoms

- Slow performance
- High CRC errors
- Excessive collisions
- Intermittent connectivity

#### Possible Causes

- One side configured manually
- Auto-negotiation disabled
- Incorrect speed settings

#### Diagnostic Commands

```text
show interfaces
```

#### Resolution

- Configure both interfaces consistently.
- Enable Auto-Negotiation where appropriate.
- Verify interface capabilities.

#### Prevention

- Standardize interface configurations.
- Avoid mixed manual and automatic settings.
- Audit interface configurations periodically.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Layer 2 Issues

Layer 2 issues commonly affect endpoint connectivity, VLAN communication, and switching operations. The following procedures help isolate and resolve common switching-related faults.


### L2-001 — Incorrect VLAN Assignment

#### Symptoms

- End device cannot communicate with hosts in the same department.
- DHCP address cannot be obtained.
- Default gateway unreachable.
- Device appears in the wrong VLAN.

#### Possible Causes

- Incorrect access VLAN configuration.
- Port assigned to the wrong VLAN.
- VLAN not created on the switch.
- Access port configured as a trunk.

#### Diagnostic Commands

```text
show vlan brief
show interfaces switchport
show running-config interface
```

#### Resolution

- Verify the correct VLAN assignment.
- Configure the interface as an access port.
- Create the missing VLAN if required.
- Verify that the VLAN is active.

#### Prevention

- Maintain an up-to-date VLAN assignment table.
- Use standardized switchport templates.
- Verify VLAN assignments during deployment.

### L2-002 — Trunk Link Failure

#### Symptoms

- Multiple VLANs lose connectivity.
- Inter-switch communication fails.
- Access switches become isolated.
- Users from several departments are affected simultaneously.

#### Possible Causes

- Trunk interface administratively down.
- Incorrect encapsulation.
- Allowed VLAN mismatch.
- Native VLAN mismatch.
- Physical link failure.

#### Diagnostic Commands

```text
show interfaces trunk
show interfaces status
show running-config interface
```

#### Resolution

- Verify trunk status.
- Confirm IEEE 802.1Q encapsulation.
- Verify allowed VLAN list.
- Verify native VLAN configuration.
- Restore the physical connection if required.

#### Prevention

- Standardize trunk templates.
- Document trunk interfaces.
- Limit allowed VLANs to required networks.

### L2-003 — Native VLAN Mismatch

#### Symptoms

- CDP reports Native VLAN mismatch.
- Intermittent connectivity.
- Unexpected broadcast traffic.
- STP inconsistencies.

#### Possible Causes

- Different native VLAN configured on each end.
- Trunk configured inconsistently.
- Switch replacement using default configuration.

#### Diagnostic Commands

```text
show interfaces trunk
show cdp neighbors detail
show running-config interface
```

#### Resolution

- Configure identical native VLANs on both ends.
- Verify trunk configuration consistency.
- Confirm VLAN 999 is configured as the native VLAN.

#### Prevention

- Use an unused native VLAN.
- Disable DTP negotiation.
- Validate trunk configuration before deployment.

### L2-004 — Spanning Tree Blocking Port

#### Symptoms

- Redundant link not forwarding traffic.
- One uplink remains in Blocking or Discarding state.
- Unexpected traffic path.

#### Possible Causes

- Normal STP operation.
- Root bridge election.
- Incorrect bridge priority.

#### Diagnostic Commands

```text
show spanning-tree
show spanning-tree vlan
```

#### Resolution

- Verify the root bridge.
- Confirm expected port roles.
- Adjust bridge priorities if necessary.

#### Prevention

- Configure DIST-SW1 as the STP root bridge.
- Maintain documented STP priorities.
- Avoid unnecessary topology changes.

### L2-005 — Port Security Violation

#### Symptoms

- End device suddenly loses connectivity.
- Interface enters err-disabled or shutdown state.
- Security violation counter increases.

#### Possible Causes

- Unauthorized device connected.
- MAC address limit exceeded.
- User moved workstation without updating configuration.

#### Diagnostic Commands

```text
show port-security
show port-security interface
show interfaces status
```

#### Resolution

- Verify the connected device.
- Remove unauthorized devices.
- Clear the violation.
- Re-enable the interface if appropriate.

#### Prevention

- Configure appropriate maximum MAC addresses.
- Use sticky MAC where appropriate.
- Periodically review Port Security configuration.

### L2-006 — BPDU Guard Violation

#### Symptoms

- Access port immediately enters err-disabled state.
- Connected device loses connectivity.
- Syslog reports BPDU Guard violation.

#### Possible Causes

- Unauthorized switch connected.
- Loop created by another switch.
- Incorrect connection during maintenance.

#### Diagnostic Commands

```text
show spanning-tree inconsistentports
show interfaces status
show logging
```

#### Resolution

- Remove the unauthorized switch.
- Correct the physical connection.
- Recover the interface after resolving the issue.

#### Prevention

- Enable BPDU Guard on all access ports.
- Restrict access to network closets.
- Educate administrators on proper switch connections.

### L2-007 — Broadcast Storm

#### Symptoms

- High CPU utilization on switches.
- Slow network performance.
- Excessive broadcast traffic.
- Multiple users report connectivity issues.

#### Possible Causes

- Layer 2 switching loop.
- Faulty NIC.
- Misconfigured switch.
- Broadcast storm attack.

#### Diagnostic Commands

```text
show interfaces counters
show spanning-tree
show processes cpu
```

#### Resolution

- Identify the source of excessive broadcasts.
- Remove the looping connection.
- Verify STP operation.
- Enable Storm Control if not already configured.

#### Prevention

- Enable Rapid PVST+.
- Configure Storm Control.
- Enable BPDU Guard and PortFast.
- Follow enterprise cabling standards.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

