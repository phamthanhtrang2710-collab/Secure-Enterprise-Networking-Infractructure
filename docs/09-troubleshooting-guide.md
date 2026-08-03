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

## Layer 3 Issues

Layer 3 issues affect communication between VLANs, routed links, and external networks. The following procedures assist in identifying and resolving routing-related problems within the enterprise infrastructure.


### L3-001 — Inter-VLAN Routing Failure

#### Symptoms

- Devices in different VLANs cannot communicate.
- Same-VLAN communication operates normally.
- Default gateway is reachable.
- Cross-VLAN applications are unavailable.

#### Possible Causes

- Missing or incorrect SVI configuration.
- HSRP virtual gateway unavailable.
- Routing disabled.
- ACL blocking inter-VLAN traffic.

#### Diagnostic Commands

```text
show ip interface brief
show ip route
show standby brief
show access-lists
```

#### Resolution

- Verify VLAN interface configuration.
- Confirm HSRP virtual gateway is operational.
- Verify routing between VLANs.
- Review ACL policies.

#### Prevention

- Validate SVI configuration during deployment.
- Verify gateway redundancy after configuration changes.
- Test inter-VLAN connectivity after implementation.


### L3-002 — Default Gateway Unreachable

#### Symptoms

- Clients cannot reach external networks.
- Local VLAN communication functions normally.
- Internet access unavailable.
- Ping to gateway fails.

#### Possible Causes

- HSRP failure.
- Gateway interface shutdown.
- Incorrect default gateway configured on clients.
- Physical uplink failure.

#### Diagnostic Commands

```text
show standby brief
show ip interface brief
ping
```

#### Resolution

- Verify HSRP status.
- Confirm gateway interfaces are operational.
- Verify client gateway configuration.
- Restore failed uplinks if necessary.

#### Prevention

- Use HSRP for gateway redundancy.
- Monitor gateway availability.
- Verify DHCP default gateway option.


### L3-003 — Missing Route

#### Symptoms

- Remote subnet unreachable.
- Traceroute stops at a core router.
- Only specific networks are affected.

#### Possible Causes

- Route not advertised.
- OSPF adjacency failure.
- Incorrect static route.
- Route filtering.

#### Diagnostic Commands

```text
show ip route
show ip protocols
show ip ospf database
```

#### Resolution

- Verify route advertisement.
- Restore OSPF adjacency.
- Correct static routing if required.
- Verify route redistribution policies.

#### Prevention

- Regularly audit routing tables.
- Validate routing changes before deployment.
- Monitor routing protocol health.


### L3-004 — Packet Loss Between VLANs

#### Symptoms

- High latency.
- Intermittent communication.
- Packet loss during ping.
- Applications disconnect unexpectedly.

#### Possible Causes

- Congested interface.
- Routing instability.
- Interface errors.
- MTU mismatch.

#### Diagnostic Commands

```text
ping
traceroute
show interfaces
show ip route
```

#### Resolution

- Verify interface health.
- Investigate routing stability.
- Correct MTU inconsistencies.
- Eliminate interface errors.

#### Prevention

- Monitor interface utilization.
- Perform routine health checks.
- Maintain consistent MTU values.


### L3-005 — Asymmetric Routing

#### Symptoms

- Sessions disconnect unexpectedly.
- Firewall drops return traffic.
- One-way communication observed.
- Intermittent application failures.

#### Possible Causes

- Multiple routing paths.
- Incorrect routing metrics.
- Static route conflicts.
- Improper route redistribution.

#### Diagnostic Commands

```text
show ip route
traceroute
show ip cef
```

#### Resolution

- Verify routing path consistency.
- Remove conflicting routes.
- Adjust routing metrics if required.
- Validate forwarding decisions.

#### Prevention

- Maintain a consistent routing design.
- Avoid unnecessary static routes.
- Document routing policy changes.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## OSPF Troubleshooting

OSPF is the enterprise Interior Gateway Protocol (IGP) responsible for exchanging routing information between internal Layer 3 devices. Most OSPF issues are caused by neighbor establishment failures, configuration mismatches, or route advertisement problems.

### OSPF-001 — Neighbor Down

#### Symptoms

- OSPF neighbor not displayed.
- Missing internal routes.
- Remote VLANs unreachable.
- Routing table incomplete.

#### Possible Causes

- Interface shutdown.
- IP addressing mismatch.
- OSPF not enabled.
- Area mismatch.
- Network connectivity failure.

#### Diagnostic Commands

```text
show ip ospf neighbor
show ip ospf interface brief
show ip interface brief
show ip route
```

#### Resolution

- Verify interface status.
- Confirm IP addressing.
- Verify OSPF network statements.
- Ensure both routers belong to the same OSPF area.

#### Prevention

- Document all routed links.
- Validate OSPF configuration before deployment.
- Monitor neighbor status continuously.

### OSPF-002 — Neighbor Stuck in INIT State

#### Symptoms

- Neighbor remains in INIT state.
- Adjacency never reaches FULL.
- Routes are not exchanged.

#### Possible Causes

- One-way communication.
- ACL blocking multicast traffic.
- Incorrect interface configuration.
- Packet loss.

#### Diagnostic Commands

```text
show ip ospf neighbor
show access-lists
show interfaces
```

#### Resolution

- Verify bidirectional communication.
- Remove ACL restrictions.
- Check interface connectivity.
- Verify multicast support.

#### Prevention

- Avoid filtering OSPF multicast traffic.
- Validate ACL policies.
- Test routed links before production.

### OSPF-003 — Neighbor Stuck in EXSTART or EXCHANGE

#### Symptoms

- Neighbor repeatedly resets.
- Adjacency never reaches FULL.
- Database synchronization fails.

#### Possible Causes

- MTU mismatch.
- Router ID conflict.
- Interface instability.

#### Diagnostic Commands

```text
show ip ospf neighbor
show interfaces
show ip ospf interface
```

#### Resolution

- Verify MTU values.
- Configure unique Router IDs.
- Stabilize the physical interface.

#### Prevention

- Standardize MTU configuration.
- Assign Router IDs manually.
- Maintain consistent interface settings.

### OSPF-004 — Missing OSPF Routes

#### Symptoms

- Routing table missing enterprise networks.
- Remote VLANs unreachable.
- Neighbor relationship is FULL.

#### Possible Causes

- Network statement missing.
- Passive interface configuration.
- Incorrect wildcard mask.
- Route filtering.

#### Diagnostic Commands

```text
show ip route ospf
show ip protocols
show running-config | section router ospf
```

#### Resolution

- Verify network statements.
- Confirm wildcard masks.
- Review passive-interface configuration.
- Verify advertised interfaces.

#### Prevention

- Validate OSPF advertisements.
- Review routing table after changes.
- Use configuration templates.


### OSPF-005 — Duplicate Router ID

#### Symptoms

- Neighbor instability.
- Frequent adjacency resets.
- OSPF warnings in system logs.

#### Possible Causes

- Duplicate Router IDs.
- Router replacement using copied configuration.

#### Diagnostic Commands

```text
show ip ospf
show logging
```

#### Resolution

- Assign unique Router IDs.
- Restart the OSPF process if required.

#### Prevention

- Manually configure Router IDs.
- Maintain Router ID documentation.

### OSPF-006 — Area Mismatch

#### Symptoms

- Neighbor relationship never forms.
- Remote routes unavailable.

#### Possible Causes

- Different OSPF areas.
- Incorrect interface configuration.

#### Diagnostic Commands

```text
show ip ospf interface
show running-config | section router ospf
```

#### Resolution

- Configure matching OSPF areas.
- Verify interface assignments.

#### Prevention

- Use Area 0 consistently throughout the enterprise.
- Review routing design before implementation.

### OSPF-007 — Passive Interface Misconfiguration

#### Symptoms

- Neighbor relationship missing.
- Expected routes not advertised.

#### Possible Causes

- Routed interface configured as passive.
- Incorrect passive-interface default policy.

#### Diagnostic Commands

```text
show ip protocols
show running-config | section router ospf
```

#### Resolution

- Remove passive configuration from routed interfaces.
- Keep VLAN gateway interfaces passive as designed.

#### Prevention

- Apply passive-interface only to user-facing VLAN interfaces.
- Validate neighbor relationships after configuration changes.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## BGP Troubleshooting

Border Gateway Protocol (BGP) is implemented exclusively on **EDGE-R1** to exchange routing information with the simulated Internet Service Provider (ISP). BGP issues typically affect Internet connectivity and external route advertisement.


### BGP-001 — Neighbor in Idle State

#### Symptoms

- BGP session remains in **Idle** state.
- No external routes learned.
- Internet connectivity unavailable.

#### Possible Causes

- Neighbor IP address incorrect.
- Physical interface down.
- Routing to neighbor unavailable.
- BGP process not configured.

#### Diagnostic Commands

```text
show ip bgp summary
show ip interface brief
ping <neighbor-ip>
show running-config | section router bgp
```

#### Resolution

- Verify neighbor IP address.
- Confirm interface connectivity.
- Verify BGP configuration.
- Ensure the neighbor is reachable.

#### Prevention

- Verify Layer 3 connectivity before enabling BGP.
- Document neighbor configuration.
- Monitor BGP session status.


### BGP-002 — Neighbor in Active State

#### Symptoms

- BGP session repeatedly transitions between **Active** and **Connect**.
- Internet connectivity intermittent.
- No prefixes exchanged.

#### Possible Causes

- TCP port 179 blocked.
- Incorrect neighbor configuration.
- Firewall or ACL filtering.
- Routing path unavailable.

#### Diagnostic Commands

```text
show ip bgp summary
show access-lists
ping <neighbor-ip>
traceroute <neighbor-ip>
```

#### Resolution

- Verify neighbor configuration.
- Allow TCP port 179.
- Confirm routing path.
- Remove unnecessary ACL restrictions.

#### Prevention

- Validate connectivity before configuring BGP.
- Review firewall and ACL policies.
- Monitor BGP session logs.

### BGP-003 — AS Number Mismatch

#### Symptoms

- Neighbor relationship never establishes.
- BGP remains in Active or Idle state.
- Session resets repeatedly.

#### Possible Causes

- Incorrect local AS number.
- Incorrect remote AS number.
- Typographical configuration error.

#### Diagnostic Commands

```text
show ip bgp summary
show running-config | section router bgp
```

#### Resolution

- Verify local AS number.
- Verify remote AS number.
- Correct the BGP neighbor configuration.

#### Prevention

- Document AS assignments.
- Validate peer configuration before deployment.


### BGP-004 — Enterprise Prefix Not Advertised

#### Symptoms

- ISP cannot reach enterprise networks.
- Return traffic fails.
- Internal connectivity remains operational.

#### Possible Causes

- Missing network statement.
- Route not present in routing table.
- Incorrect prefix configured.

#### Diagnostic Commands

```text
show ip bgp
show ip route
show running-config | section router bgp
```

#### Resolution

- Verify advertised prefixes.
- Ensure the network exists in the routing table.
- Correct the network statement if necessary.

#### Prevention

- Validate advertised prefixes after deployment.
- Review BGP advertisements during maintenance.

### BGP-005 — Missing Default Route

#### Symptoms

- Internal users cannot access the Internet.
- Internal routing operates normally.
- No default route displayed in the routing table.

#### Possible Causes

- Static default route removed.
- ISP unreachable.
- BGP default route not available.

#### Diagnostic Commands

```text
show ip route
show ip bgp
show ip bgp summary
```

#### Resolution

- Verify the static default route.
- Confirm ISP connectivity.
- Restore the default route if required.

#### Prevention

- Verify default route after configuration changes.
- Monitor Internet gateway availability.
- Include default route validation in change procedures.

### BGP-006 — Route Advertisement Failure

#### Symptoms

- Internal users have Internet access.
- External networks cannot initiate return traffic.
- Enterprise prefixes missing from ISP routing table.

#### Possible Causes

- Network statement missing.
- Incorrect route advertisement policy.
- Prefix unavailable in local routing table.

#### Diagnostic Commands

```text
show ip bgp
show ip bgp neighbors
show ip route
```

#### Resolution

- Verify advertised networks.
- Confirm the prefix exists locally.
- Correct BGP advertisement configuration.

#### Prevention

- Validate route advertisements after implementation.
- Periodically review BGP configuration.
- Maintain routing documentation.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## HSRP Troubleshooting

Hot Standby Router Protocol (HSRP) provides first-hop redundancy for all enterprise VLANs. The following procedures assist in diagnosing and resolving gateway redundancy issues.


### HSRP-001 — Standby Router Does Not Become Active

#### Symptoms

- Default gateway becomes unreachable after CORE-R1 failure.
- Users lose connectivity outside their local VLAN.
- HSRP state remains unchanged.

#### Possible Causes

- HSRP not configured.
- Standby router misconfigured.
- HSRP group mismatch.
- Interface shutdown.

#### Diagnostic Commands

```text
show standby brief
show standby
show ip interface brief
```

#### Resolution

- Verify HSRP configuration on both routers.
- Confirm matching HSRP group numbers.
- Verify interface operational status.
- Restore the failed interface if necessary.

#### Prevention

- Validate HSRP operation during implementation.
- Include failover testing in maintenance procedures.
- Monitor HSRP state continuously.


### HSRP-002 — Active Router Never Preempts

#### Symptoms

- CORE-R1 recovers after failure.
- CORE-R2 continues operating as the Active router.
- Preferred gateway is not restored.

#### Possible Causes

- Preemption disabled.
- Priority configured incorrectly.
- HSRP timers inconsistent.

#### Diagnostic Commands

```text
show standby
show running-config
```

#### Resolution

- Enable HSRP preemption.
- Verify router priorities.
- Confirm timer consistency.

#### Prevention

- Enable preemption on the preferred gateway.
- Document HSRP priorities.
- Validate HSRP behavior after recovery.


### HSRP-003 — Virtual Gateway Unreachable

#### Symptoms

- Clients cannot ping the default gateway.
- DHCP configuration appears correct.
- Local VLAN communication unavailable.

#### Possible Causes

- HSRP virtual IP missing.
- VLAN interface down.
- Incorrect HSRP group.
- Interface tracking failure.

#### Diagnostic Commands

```text
show standby
show ip interface brief
ping <virtual-ip>
```

#### Resolution

- Verify virtual IP configuration.
- Confirm VLAN interface status.
- Verify HSRP group configuration.
- Restore tracked interfaces.

#### Prevention

- Verify virtual gateway after deployment.
- Include gateway validation in acceptance testing.

### HSRP-004 — Interface Tracking Triggered

#### Symptoms

- Unexpected Active/Standby transition.
- HSRP priority reduced.
- Gateway changes without router failure.

#### Possible Causes

- Routed uplink failure.
- Tracked interface shutdown.
- WAN connectivity loss.

#### Diagnostic Commands

```text
show standby
show track
show ip interface brief
```

#### Resolution

- Restore the tracked interface.
- Verify WAN connectivity.
- Confirm interface tracking configuration.

#### Prevention

- Monitor uplink interfaces.
- Verify interface tracking after maintenance.
- Use redundant uplinks where possible.


### HSRP-005 — Split Active Condition

#### Symptoms

- Both routers report Active state.
- Duplicate gateway responses.
- Intermittent packet loss.
- MAC address instability.

#### Possible Causes

- Layer 2 communication failure.
- HSRP hello packets blocked.
- VLAN trunk failure.

#### Diagnostic Commands

```text
show standby
show interfaces trunk
show spanning-tree
```

#### Resolution

- Restore Layer 2 connectivity.
- Verify trunk operation.
- Confirm HSRP hello packets are exchanged.
- Eliminate VLAN inconsistencies.

#### Prevention

- Monitor HSRP state changes.
- Verify trunk stability.
- Maintain healthy Layer 2 connectivity.

### HSRP-006 — Incorrect Active Router

#### Symptoms

- CORE-R2 operates as Active during normal conditions.
- Traffic does not follow the expected path.
- HSRP priorities appear inconsistent.

#### Possible Causes

- Incorrect priority configuration.
- Preemption disabled.
- Interface tracking reduced priority.

#### Diagnostic Commands

```text
show standby
show running-config
show track
```

#### Resolution

- Verify HSRP priorities.
- Enable preemption.
- Confirm interface tracking operation.
- Restore the preferred Active router.

#### Prevention

- Standardize HSRP configuration templates.
- Verify gateway roles after implementation.
- Document Active and Standby assignments.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## ACL Troubleshooting

Access Control Lists (ACLs) enforce enterprise security policies by permitting or denying traffic based on business requirements. Incorrect ACL configuration may unintentionally block legitimate communication or expose sensitive resources.

### ACL-001 — Legitimate Traffic Blocked

#### Symptoms

- Users cannot access authorized resources.
- Ping or application traffic fails.
- Only specific services are affected.

#### Possible Causes

- Incorrect ACL sequence.
- Incorrect source or destination address.
- Incorrect wildcard mask.
- Implicit deny statement.

#### Diagnostic Commands

```text
show access-lists
show running-config
show ip interface
```

#### Resolution

- Verify ACL entries.
- Confirm source and destination networks.
- Correct wildcard masks.
- Insert permit statements before the implicit deny.

#### Prevention

- Validate ACLs in a test environment.
- Document ACL changes.
- Follow the principle of least privilege.

### ACL-002 — Unauthorized Traffic Allowed

#### Symptoms

- Restricted VLANs can communicate.
- Sensitive resources are accessible.
- Security policy violations detected.

#### Possible Causes

- Missing deny statement.
- ACL not applied.
- Incorrect interface assignment.
- Incorrect ACL direction.

#### Diagnostic Commands

```text
show access-lists
show ip interface
show running-config
```

#### Resolution

- Apply the ACL to the correct interface.
- Verify inbound or outbound direction.
- Add missing deny entries.
- Validate traffic flow after implementation.

#### Prevention

- Review ACL policies periodically.
- Verify ACL placement before deployment.
- Perform security validation after changes.

### ACL-003 — ACL Applied to Wrong Interface

#### Symptoms

- Traffic blocked unexpectedly.
- Incorrect VLAN affected.
- Routing appears operational.

#### Possible Causes

- ACL attached to the wrong interface.
- Incorrect inbound or outbound direction.
- Configuration copied to the wrong device.

#### Diagnostic Commands

```text
show ip interface
show running-config interface
```

#### Resolution

- Remove the ACL from the incorrect interface.
- Apply the ACL to the intended interface.
- Verify traffic after correction.

#### Prevention

- Document ACL placement.
- Review interface configuration before deployment.
- Use configuration templates.

### ACL-004 — Incorrect ACL Processing Order

#### Symptoms

- Specific permit statements ignored.
- Unexpected deny behavior.
- Security policy functions inconsistently.

#### Possible Causes

- Deny statement placed before permit.
- Incorrect ACL sequence numbers.
- ACL edited without reviewing existing entries.

#### Diagnostic Commands

```text
show access-lists
```

#### Resolution

- Review ACL processing order.
- Reorder ACL entries as required.
- Verify expected traffic flow.

#### Prevention

- Follow top-down ACL design.
- Review sequence numbers before deployment.
- Test ACL logic after modifications.


### ACL-005 — Management Access Blocked

#### Symptoms

- SSH unavailable.
- SNMP polling fails.
- Network devices become unreachable for administration.

#### Possible Causes

- Management VLAN denied.
- SSH traffic blocked.
- Incorrect management ACL.

#### Diagnostic Commands

```text
show access-lists
show ip ssh
show running-config
```

#### Resolution

- Verify management ACL entries.
- Allow SSH and SNMP from authorized networks.
- Confirm Management VLAN access policy.

#### Prevention

- Test management access before applying ACLs.
- Maintain an emergency management account.
- Keep backup configurations available.


### ACL-006 — ACL Counters Not Increasing

#### Symptoms

- ACL appears unused.
- Expected matches remain at zero.
- Traffic not processed by the ACL.

#### Possible Causes

- ACL not applied.
- Incorrect interface.
- Wrong traffic direction.
- Traffic taking another routing path.

#### Diagnostic Commands

```text
show access-lists
show ip interface
show ip route
```

#### Resolution

- Verify ACL attachment.
- Confirm inbound or outbound direction.
- Generate test traffic.
- Validate routing path.

#### Prevention

- Verify ACL counters during testing.
- Include ACL validation in acceptance testing.
- Monitor ACL statistics periodically.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## NAT Troubleshooting

Network Address Translation (NAT) enables internal enterprise hosts to communicate with external networks while preserving private IPv4 addressing. In this environment, **EDGE-R1** performs Port Address Translation (PAT) for all internal enterprise VLANs.


### NAT-001 — Internet Access Failure

#### Symptoms

- Internal users cannot access Internet resources.
- Internal VLAN communication functions normally.
- DNS resolution may still succeed.
- Ping to external IP addresses fails.

#### Possible Causes

- NAT not configured.
- Default route missing.
- ISP connectivity failure.
- Incorrect NAT ACL.

#### Diagnostic Commands

```text
show ip nat translations
show ip nat statistics
show ip route
ping
```

#### Resolution

- Verify NAT configuration.
- Confirm the default route is present.
- Verify ISP connectivity.
- Validate NAT ACL entries.

#### Prevention

- Verify Internet connectivity after every NAT change.
- Include NAT validation in implementation testing.
- Monitor NAT statistics regularly.


### NAT-002 — No NAT Translations Created

#### Symptoms

- NAT translation table remains empty.
- Internet traffic fails.
- Internal routing operates normally.

#### Possible Causes

- Traffic does not match the NAT ACL.
- Inside interface incorrectly configured.
- Outside interface incorrectly configured.
- No outbound traffic generated.

#### Diagnostic Commands

```text
show ip nat translations
show ip nat statistics
show access-lists
```

#### Resolution

- Verify the NAT access list.
- Confirm inside and outside interface assignments.
- Generate test traffic.
- Verify PAT configuration.

#### Prevention

- Validate NAT after deployment.
- Periodically review translation statistics.
- Document NAT configuration.


### NAT-003 — Incorrect Inside or Outside Interface

#### Symptoms

- NAT translations fail.
- Internet connectivity unavailable.
- Translation table remains empty.

#### Possible Causes

- Interfaces configured with incorrect NAT roles.
- Interface configuration overwritten.
- Routing changes after implementation.

#### Diagnostic Commands

```text
show running-config interface
show ip nat statistics
show ip interface brief
```

#### Resolution

- Verify the inside interface configuration.
- Verify the outside interface configuration.
- Correct interface assignments.
- Test Internet connectivity.

#### Prevention

- Standardize NAT interface configuration.
- Validate interface roles after changes.
- Review interface documentation.


### NAT-004 — NAT ACL Mismatch

#### Symptoms

- Only some VLANs have Internet access.
- Other VLANs cannot reach external networks.
- Translation table contains only selected subnets.

#### Possible Causes

- Missing subnet in NAT ACL.
- Incorrect wildcard mask.
- Incorrect ACL sequence.

#### Diagnostic Commands

```text
show access-lists
show ip nat statistics
show running-config
```

#### Resolution

- Review NAT ACL entries.
- Add missing enterprise subnets.
- Correct wildcard masks.
- Generate test traffic.

#### Prevention

- Include every enterprise subnet in the NAT ACL.
- Validate Internet access from each VLAN.
- Review ACLs after network expansion.


### NAT-005 — PAT Translation Limit Reached

#### Symptoms

- New Internet sessions fail.
- Existing sessions continue operating.
- NAT statistics indicate resource exhaustion.

#### Possible Causes

- Excessive concurrent connections.
- High-volume application traffic.
- NAT resource limitations.

#### Diagnostic Commands

```text
show ip nat statistics
show ip nat translations
```

#### Resolution

- Identify excessive NAT usage.
- Clear unnecessary translations if appropriate.
- Reduce unnecessary outbound connections.
- Investigate abnormal traffic patterns.

#### Prevention

- Monitor NAT utilization.
- Investigate unusual connection spikes.
- Review Internet usage periodically.


### NAT-006 — Return Traffic Fails

#### Symptoms

- Internal users can send traffic.
- External replies never return.
- Sessions time out unexpectedly.

#### Possible Causes

- Missing return route on ISP Router.
- Incorrect default route.
- NAT translation expired.
- ACL blocking return traffic.

#### Diagnostic Commands

```text
show ip nat translations
show ip route
show access-lists
traceroute
```

#### Resolution

- Verify the ISP static route to enterprise networks.
- Confirm NAT translations exist.
- Verify return path routing.
- Review perimeter ACLs.

#### Prevention

- Validate bidirectional Internet connectivity.
- Verify return routing after deployment.
- Include end-to-end testing during implementation.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## DHCP Troubleshooting

Dynamic Host Configuration Protocol (DHCP) automatically assigns IP configuration to enterprise clients. DHCP issues typically prevent endpoints from obtaining valid network settings, resulting in limited or no network connectivity.


### DHCP-001 — Client Does Not Receive an IP Address

#### Symptoms

- Client receives an APIPA address (`169.254.x.x`).
- Network access unavailable.
- Internet connectivity unavailable.
- Ping to the default gateway fails.

#### Possible Causes

- DHCP service unavailable.
- DHCP scope exhausted.
- Incorrect VLAN assignment.
- DHCP Relay not configured.
- Physical connectivity issue.

#### Diagnostic Commands

```text
ipconfig /all
show ip interface brief
show running-config interface
ping <DHCP Server IP>
```

#### Resolution

- Verify the DHCP Server is operational.
- Confirm the client is connected to the correct VLAN.
- Verify DHCP Relay configuration if the server is located in another VLAN.
- Renew the client lease.
- Confirm physical connectivity.

#### Prevention

- Monitor DHCP service availability.
- Reserve sufficient IP addresses within each scope.
- Validate DHCP operation during deployment.


### DHCP-002 — Incorrect IP Configuration Assigned

#### Symptoms

- Client receives an unexpected IP address.
- Incorrect subnet mask.
- Incorrect default gateway.
- DNS resolution fails.

#### Possible Causes

- Incorrect DHCP scope configuration.
- Wrong DHCP options.
- Client connected to the wrong VLAN.

#### Diagnostic Commands

```text
ipconfig /all
show vlan brief
show running-config
```

#### Resolution

- Verify the DHCP scope configuration.
- Confirm DHCP options (Gateway, DNS Server, Domain Name).
- Verify VLAN assignment.
- Release and renew the DHCP lease.

#### Prevention

- Review DHCP scopes periodically.
- Maintain standardized DHCP templates.
- Validate DHCP options after configuration changes.


### DHCP-003 — DHCP Scope Exhausted

#### Symptoms

- New devices cannot obtain IP addresses.
- Existing clients continue operating.
- DHCP Server reports no available leases.

#### Possible Causes

- Address pool exhausted.
- Excessive lease duration.
- Unauthorized devices consuming addresses.

#### Diagnostic Commands

```text
ipconfig /all
show ip dhcp binding
show ip dhcp pool
```
```
Get-DhcpServerv4Scope
Get-DhcpServerv4ScopeStatistics
Get-DhcpServerv4Lease
Get-DhcpServerv4ExclusionRange
```
#### Resolution

- Expand the DHCP scope if appropriate.
- Reduce lease duration.
- Remove stale DHCP leases.
- Investigate unauthorized clients.

#### Prevention

- Monitor DHCP utilization.
- Reserve adequate address space.
- Review lease statistics regularly.


### DHCP-004 — DHCP Relay Failure

#### Symptoms

- Clients in remote VLANs cannot obtain IP addresses.
- Clients in the server VLAN receive addresses normally.
- DHCP Server is reachable by ping.

#### Possible Causes

- Missing IP Helper Address.
- Incorrect DHCP Server IP.
- ACL blocking DHCP traffic.
- Routing issue.

#### Diagnostic Commands

```text
show running-config interface
show ip interface
ping <DHCP Server IP>
```

#### Resolution

- Verify the IP Helper Address configuration.
- Confirm DHCP Server IP address.
- Verify routing between VLANs.
- Review ACL policies affecting UDP ports 67 and 68.

#### Prevention

- Document DHCP Relay configuration.
- Validate DHCP across every VLAN.
- Include DHCP Relay verification in implementation testing.


### DHCP-005 — Client Cannot Renew Lease

#### Symptoms

- Existing client loses network connectivity.
- Lease renewal fails.
- Intermittent IP address assignment.

#### Possible Causes

- DHCP Server unavailable.
- Network interruption.
- Relay configuration failure.
- Scope unavailable.

#### Diagnostic Commands

```text
ipconfig /renew
ipconfig /all
ping <DHCP Server IP>
```

#### Resolution

- Restore DHCP Server availability.
- Verify Layer 3 connectivity.
- Confirm DHCP Relay operation.
- Renew the lease after connectivity is restored.

#### Prevention

- Monitor DHCP Server health.
- Verify lease renewal periodically.
- Include DHCP renewal testing during maintenance.


### DHCP-006 — Duplicate IP Address Detected

#### Symptoms

- IP conflict warning displayed.
- Intermittent connectivity.
- Devices disconnect unexpectedly.

#### Possible Causes

- Static IP overlaps the DHCP scope.
- Duplicate manual configuration.
- Unauthorized device using the same IP.

#### Diagnostic Commands

```text
ipconfig /all
arp -a
show ip dhcp binding
```
```
Get-DhcpServerv4Scope
Get-DhcpServerv4ScopeStatistics
Get-DhcpServerv4Lease
Get-DhcpServerv4ExclusionRange
```
#### Resolution

- Identify the conflicting device.
- Correct the static IP configuration.
- Adjust DHCP exclusions if necessary.
- Renew the DHCP lease.

#### Prevention

- Reserve static addresses outside DHCP scopes.
- Document IP allocations.
- Regularly audit address usage.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## DNS Troubleshooting

The Domain Name System (DNS) provides centralized name resolution for internal enterprise resources and external Internet destinations. DNS-related failures may prevent users from accessing services by hostname even when IP connectivity remains operational.

### DNS-001 — Internal Name Resolution Failure

#### Symptoms

- Internal resources cannot be reached by hostname.
- Access by IP address succeeds.
- `nslookup` returns a timeout or non-existent domain response.
- Domain-based services are unavailable.

#### Possible Causes

- DNS Server unavailable.
- DNS service stopped.
- Client configured with an incorrect DNS Server.
- Internal DNS record missing.
- ACL blocking DNS traffic.

#### Diagnostic Commands

```text
ipconfig /all
nslookup win-srv01.verra.local
nslookup lnx-srv01.verra.local
ping 10.10.50.10
```

#### Resolution

- Verify that WIN-SRV01 is reachable.
- Confirm that the DNS Server service is running.
- Verify that clients use `10.10.50.10` as their DNS Server.
- Review DNS records in the internal zone.
- Confirm that UDP and TCP port 53 traffic is permitted.

#### Prevention

- Monitor DNS service availability.
- Maintain accurate internal DNS records.
- Validate DHCP Option 006 after changes.
- Document DNS zone and record assignments.

### DNS-002 — Incorrect DNS Server Assigned

#### Symptoms

- Client receives a valid IP address.
- IP connectivity operates normally.
- Internal hostname resolution fails.
- `ipconfig /all` displays an unexpected DNS Server.

#### Possible Causes

- Incorrect DHCP Option 006.
- Manually configured DNS address.
- Client connected to an incorrect VLAN or DHCP scope.

#### Diagnostic Commands

```text
ipconfig /all
nslookup
Get-DhcpServerv4OptionValue
```

#### Resolution

- Correct DHCP Option 006.
- Remove incorrect manual DNS configuration.
- Release and renew the DHCP lease.
- Verify the assigned DHCP scope.

#### Prevention

- Centralize DNS assignment through DHCP.
- Audit DHCP options regularly.
- Avoid unnecessary manual DNS configuration.

### DNS-003 — Internal DNS Record Missing

#### Symptoms

- One internal hostname fails to resolve.
- Other internal records resolve successfully.
- The destination server remains reachable by IP address.

#### Possible Causes

- Missing A record.
- Incorrect hostname or IP address.
- Record created in the wrong DNS zone.
- Dynamic DNS registration failure.

#### Diagnostic Commands

```text
nslookup <hostname>
Resolve-DnsName <hostname>
Get-DnsServerResourceRecord -ZoneName "verra.local"
```

#### Resolution

- Create the missing A record.
- Correct the hostname or IP address.
- Verify that the record exists in `verra.local`.
- Re-register the host in DNS if appropriate.

#### Prevention

- Validate DNS records after server deployment.
- Maintain a documented hostname and IP inventory.
- Review stale and missing records periodically.

### DNS-004 — External Name Resolution Failure

#### Symptoms

- Internal DNS names resolve successfully.
- Public Internet names fail to resolve.
- External IP addresses remain reachable.
- Web browsing by hostname fails.

#### Possible Causes

- DNS Forwarder unavailable.
- Incorrect Forwarder configuration.
- Internet connectivity failure.
- Firewall or ACL blocking outbound DNS traffic.

#### Diagnostic Commands

```text
nslookup www.cisco.com
Resolve-DnsName www.cisco.com
ping 8.8.8.8
tracert 8.8.8.8
```

#### Resolution

- Verify Internet connectivity.
- Review DNS Forwarder configuration.
- Confirm access to the configured public DNS servers.
- Allow outbound UDP and TCP port 53 where required.

#### Prevention

- Configure multiple DNS Forwarders.
- Monitor Forwarder availability.
- Include external resolution in routine testing.

### DNS-005 — DNS Forwarder Failure

#### Symptoms

- Internal name resolution operates normally.
- External DNS queries time out.
- DNS Server itself remains reachable.

#### Possible Causes

- Public DNS Forwarder unavailable.
- Incorrect Forwarder IP address.
- Perimeter ACL blocking DNS.
- ISP connectivity issue.

#### Diagnostic Commands

```text
Get-DnsServerForwarder
Resolve-DnsName www.cisco.com -Server 8.8.8.8
nslookup www.cisco.com 8.8.8.8
```

#### Resolution

- Verify Forwarder addresses.
- Remove unavailable Forwarders.
- Add a secondary Forwarder.
- Confirm outbound DNS traffic is allowed.

#### Prevention

- Use more than one trusted DNS Forwarder.
- Periodically test each configured Forwarder.
- Monitor external DNS query failures.

### DNS-006 — DNS Service Unavailable

#### Symptoms

- All internal and external DNS lookups fail.
- DNS Server is unreachable or the service is stopped.
- Applications relying on hostname resolution fail.

#### Possible Causes

- WIN-SRV01 offline.
- DNS Server service stopped.
- Server interface or VLAN failure.
- ACL blocking access to the DNS Server.

#### Diagnostic Commands

```text
ping 10.10.50.10
Get-Service DNS
Get-DnsServer
nslookup
```

#### Resolution

- Restore connectivity to WIN-SRV01.
- Start or restart the DNS Server service.
- Verify Server VLAN connectivity.
- Review service-specific ACL entries.

#### Prevention

- Monitor DNS Server availability.
- Configure automated service health checks.
- Back up DNS zones and server configuration.
- Plan secondary DNS as a future enhancement.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Management Troubleshooting

Management troubleshooting focuses on secure administrative access to routers, switches, and servers through the dedicated Management VLAN.

### MGT-001 — SSH Connection Failure

#### Symptoms

- SSH connection times out or is refused.
- Device responds to ping but cannot be managed remotely.
- Telnet is unavailable as designed.

#### Possible Causes

- SSH service not enabled.
- RSA keys missing.
- Incorrect username or credentials.
- VTY lines improperly configured.
- Management ACL blocking SSH.
- Incorrect source VLAN.

#### Diagnostic Commands

```text
show ip ssh
show users
show login
show running-config | section line vty
show access-lists
```

#### Resolution

- Verify that SSH Version 2 is enabled.
- Confirm RSA keys are present.
- Verify local user credentials.
- Review VTY transport and login configuration.
- Allow SSH from authorized management networks.

#### Prevention

- Validate SSH after every management change.
- Maintain a secured administrative account.
- Back up device configurations before modifying VTY access.

### MGT-002 — Management VLAN Unreachable

#### Symptoms

- Multiple devices cannot be managed.
- Management IP addresses fail to respond.
- Production user traffic may continue normally.

#### Possible Causes

- VLAN 99 missing from a trunk.
- Management SVI down.
- Incorrect switch default gateway.
- ACL blocking VLAN 99.
- Trunk or Layer 2 failure.

#### Diagnostic Commands

```text
show vlan brief
show interfaces trunk
show ip interface brief
show access-lists
show spanning-tree vlan 99
```

#### Resolution

- Verify that VLAN 99 exists.
- Confirm VLAN 99 is allowed on required trunks.
- Restore the management SVI.
- Verify management addressing and default gateway settings.
- Review ACL policies.

#### Prevention

- Include VLAN 99 in trunk validation.
- Monitor management interface reachability.
- Document all management IP addresses.

### MGT-003 — Authentication Failure

#### Symptoms

- SSH session reaches the device but login fails.
- Valid administrator credentials are rejected.
- Login failure messages appear in Syslog.

#### Possible Causes

- Incorrect username or password.
- Local AAA configuration error.
- Privilege level misconfiguration.
- Account removed or disabled.

#### Diagnostic Commands

```text
show running-config | include username
show aaa servers
show login failures
show logging
```

#### Resolution

- Verify the administrator account.
- Correct local AAA configuration.
- Confirm the required privilege level.
- Use console recovery if remote access is unavailable.

#### Prevention

- Maintain at least one tested backup administrator account.
- Document AAA changes.
- Test authentication before ending a maintenance session.

### MGT-004 — SNMP Management Access Failure

#### Symptoms

- Monitoring platform cannot poll a device.
- Device remains reachable through ping and SSH.
- SNMP timeout or authentication errors are recorded.

#### Possible Causes

- SNMP credentials mismatch.
- SNMP access ACL blocking the monitoring server.
- Incorrect SNMP version.
- SNMP configuration missing.

#### Diagnostic Commands

```text
show snmp
show access-lists
show running-config | section snmp
```

#### Resolution

- Verify the configured SNMP version.
- Correct authentication and privacy settings.
- Permit the monitoring server in the SNMP ACL.
- Confirm reachability between the platform and managed device.

#### Prevention

- Standardize SNMP templates.
- Store SNMP credentials securely.
- Test SNMP polling after configuration changes.

### MGT-005 — Network Device Time Incorrect

#### Symptoms

- Device timestamps differ.
- Syslog messages appear out of order.
- Authentication or certificate-related events may fail.
- Troubleshooting timelines become inaccurate.

#### Possible Causes

- NTP Server unreachable.
- Incorrect NTP Server IP.
- NTP not configured.
- ACL blocking UDP port 123.

#### Diagnostic Commands

```text
show clock
show ntp associations
show ntp status
ping 10.10.50.10
```

#### Resolution

- Verify reachability to WIN-SRV01.
- Correct the NTP Server configuration.
- Permit UDP port 123.
- Confirm device timezone settings.

#### Prevention

- Monitor NTP synchronization.
- Use a consistent enterprise time source.
- Validate timestamps during acceptance testing.

### MGT-006 — Device Configuration Changes Not Saved

#### Symptoms

- Configuration works temporarily.
- Changes disappear after reload.
- Device returns to an earlier configuration.

#### Possible Causes

- Running configuration not copied to startup configuration.
- Save operation failed.
- Incorrect configuration register.
- Device storage issue.

#### Diagnostic Commands

```text
show running-config
show startup-config
show version
dir
```

#### Resolution

- Save the configuration to startup configuration.
- Verify configuration register settings.
- Confirm available storage.
- Back up the configuration externally.

#### Prevention

- Include configuration save in every change procedure.
- Use automated configuration backups.
- Compare running and startup configurations after changes.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Troubleshooting

Monitoring troubleshooting covers SNMP polling, centralized Syslog collection, device discovery, health checks, and dashboard visibility.

### MON-001 — Device Missing from Monitoring Platform

#### Symptoms

- Device does not appear in LibreNMS or Zabbix.
- Ping may succeed from the monitoring server.
- No device statistics are displayed.

#### Possible Causes

- Device not added correctly.
- SNMP unavailable.
- Incorrect management IP.
- ACL blocking monitoring traffic.
- Routing issue between the monitoring server and VLAN 99.

#### Diagnostic Commands

```text
ping <Management IP>
traceroute <Management IP>
show snmp
show access-lists
```

#### Resolution

- Verify the configured management IP.
- Add or rediscover the device.
- Confirm SNMP operation.
- Review routing and ACL policies.
- Verify monitoring credentials.

#### Prevention

- Maintain an accurate device inventory.
- Add monitoring validation to device onboarding.
- Standardize monitoring configuration.

### MON-002 — SNMP Polling Failure

#### Symptoms

- Device is marked unavailable.
- Graphs stop updating.
- SNMP timeout alerts appear.

#### Possible Causes

- SNMP credentials changed.
- SNMP service disabled.
- ACL blocking the monitoring server.
- Version mismatch.
- Network path failure.

#### Diagnostic Commands

```text
show snmp
show access-lists
ping <Monitoring Server IP>
```

From the monitoring server:

```text
snmpwalk -v3 -l authPriv -u <username> <device-ip>
```

#### Resolution

- Confirm the configured SNMP version.
- Correct SNMP credentials.
- Permit the monitoring server through the management ACL.
- Verify Layer 3 reachability.

#### Prevention

- Store credentials securely.
- Monitor SNMP authentication failures.
- Test polling after security changes.

### MON-003 — Syslog Messages Not Received

#### Symptoms

- Device events do not appear on LNX-SRV01.
- Local device logging is operational.
- Monitoring platform lacks event information.

#### Possible Causes

- Incorrect Syslog Server address.
- UDP port 514 blocked.
- Syslog service stopped.
- Device logging level too restrictive.
- Routing failure.

#### Diagnostic Commands

```text
show logging
show running-config | include logging
ping 10.10.50.20
```

On Ubuntu Server:

```text
sudo systemctl status rsyslog
sudo journalctl -u rsyslog
sudo ss -lunp | grep 514
```

#### Resolution

- Correct the Syslog Server address.
- Start or restart the Syslog service.
- Permit Syslog traffic through ACLs.
- Configure an appropriate logging severity.
- Verify routing to LNX-SRV01.

#### Prevention

- Monitor Syslog service health.
- Generate periodic test events.
- Back up centralized logs.

### MON-004 — Monitoring Dashboard Shows Stale Data

#### Symptoms

- Graphs are not current.
- Device status remains unchanged after an event.
- Polling timestamps are old.

#### Possible Causes

- Poller stopped.
- Database or storage problem.
- High monitoring server resource utilization.
- SNMP response delay.

#### Diagnostic Commands

On the monitoring server:

```text
systemctl status cron
systemctl status mariadb
df -h
free -m
top
```

#### Resolution

- Restore the monitoring poller.
- Verify database availability.
- Free disk space if required.
- Investigate monitoring server resource usage.
- Confirm SNMP response times.

#### Prevention

- Monitor the monitoring server itself.
- Configure disk and database alerts.
- Review polling performance regularly.

### MON-005 — False Device-Down Alert

#### Symptoms

- Monitoring platform reports a device as down.
- Device remains reachable from another system.
- Alert clears without intervention.

#### Possible Causes

- Temporary packet loss.
- ICMP blocked.
- Polling timeout too short.
- Monitoring server connectivity issue.

#### Diagnostic Commands

```text
ping <device-ip>
traceroute <device-ip>
show interfaces
show access-lists
```

#### Resolution

- Verify reachability from the monitoring server.
- Review interface errors and packet loss.
- Adjust polling timeout where justified.
- Confirm ICMP and SNMP access policies.

#### Prevention

- Use multiple monitoring methods.
- Avoid overly aggressive alert thresholds.
- Monitor the path between the platform and managed devices.

### MON-006 — Critical Events Not Generating Alerts

#### Symptoms

- Device failure appears on the dashboard.
- No email or dashboard notification is generated.
- Alert rules appear inactive.

#### Possible Causes

- Alert rule disabled.
- Notification transport not configured.
- Threshold incorrect.
- Event severity does not match the rule.

#### Diagnostic Commands

Review the monitoring platform:

```text
Alert Rules
Notification Transports
Event Logs
Polling Logs
```

#### Resolution

- Enable the required alert rule.
- Correct threshold and severity conditions.
- Configure the notification transport.
- Generate a controlled test event.

#### Prevention

- Test alerting periodically.
- Document alert thresholds.
- Review alert rules after infrastructure changes.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Best Practices

The following practices improve troubleshooting efficiency, reduce operational risk, and support consistent incident resolution.

### Troubleshooting Best Practices

| Best Practice | Implementation |
|:--------------|:---------------|
| **Start with Layer 1** | Verify power, cabling, and interface status before investigating higher layers. |
| **Follow the Packet Path** | Test connectivity from the source toward the destination one hop at a time. |
| **Change One Variable at a Time** | Avoid multiple simultaneous changes that make root-cause identification difficult. |
| **Collect Evidence First** | Capture logs, counters, protocol states, and configurations before applying changes. |
| **Use Known-Good Baselines** | Compare the affected device against documented standard configurations. |
| **Verify Both Directions** | Confirm forward and return routing paths. |
| **Review Recent Changes** | Determine whether an implementation or maintenance activity preceded the incident. |
| **Back Up Before Changes** | Save the current configuration before corrective action. |
| **Validate After Resolution** | Re-run relevant Test Plan cases after restoring service. |
| **Document the Incident** | Record symptoms, root cause, resolution, and prevention recommendations. |

### Change Safety Guidelines

| Guideline | Requirement |
|:----------|:------------|
| Configuration Backup | Required before major changes |
| Maintenance Window | Required for high-risk changes |
| Rollback Plan | Defined before implementation |
| Peer Review | Recommended for routing and security changes |
| Post-Change Validation | Mandatory |
| Documentation Update | Required when the design changes |

### Escalation Guidelines

| Escalation Level | Condition | Responsible Team |
|:-----------------|:----------|:-----------------|
| **Level 1** | Basic endpoint, cabling, or access-port issue | Help Desk / NOC |
| **Level 2** | VLAN, trunk, routing, DHCP, DNS, or monitoring issue | Network Administrator |
| **Level 3** | Complex OSPF, BGP, HSRP, security, or repeated outage | Senior Network Engineer |
| **External** | ISP circuit, platform defect, or vendor-specific software issue | ISP / Vendor Support |

### Common Verification Commands

| Category | Commands |
|:---------|:---------|
| Interface Status | `show ip interface brief`, `show interfaces` |
| VLAN | `show vlan brief` |
| Trunk | `show interfaces trunk` |
| Spanning Tree | `show spanning-tree` |
| Port Security | `show port-security` |
| Routing Table | `show ip route` |
| OSPF | `show ip ospf neighbor`, `show ip protocols` |
| BGP | `show ip bgp summary`, `show ip bgp` |
| HSRP | `show standby brief`, `show track` |
| ACL | `show access-lists`, `show ip interface` |
| NAT | `show ip nat translations`, `show ip nat statistics` |
| DHCP Client | `ipconfig /all`, `ipconfig /renew` |
| Windows DHCP | `Get-DhcpServerv4Scope`, `Get-DhcpServerv4Lease` |
| DNS | `nslookup`, `Resolve-DnsName` |
| SSH | `show ip ssh`, `show users` |
| NTP | `show ntp status`, `show ntp associations` |
| Syslog | `show logging` |
| SNMP | `show snmp` |
| Connectivity | `ping`, `traceroute` |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Summary

This Troubleshooting Guide provides a structured operational reference for diagnosing and resolving common failures within the Secure Enterprise Network Infrastructure.

The guide follows a layered troubleshooting approach that begins with physical connectivity and progresses through switching, routing, gateway redundancy, security controls, infrastructure services, management access, and monitoring.

By combining symptom identification, diagnostic commands, corrective actions, preventive controls, and escalation procedures, the document supports consistent incident response while minimizing service disruption and reducing the risk of repeated failures.

The procedures should be used together with the approved High-Level Design, Low-Level Design, Test Plan, IP Addressing Plan, VLAN Design, Routing Design, Security Design, and device configuration backups.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Glossary

| Acronym | Definition |
|:--------|:-----------|
| **AAA** | Authentication, Authorization, and Accounting |
| **ACL** | Access Control List |
| **APIPA** | Automatic Private IP Addressing |
| **BGP** | Border Gateway Protocol |
| **BPDU** | Bridge Protocol Data Unit |
| **DHCP** | Dynamic Host Configuration Protocol |
| **DNS** | Domain Name System |
| **DTP** | Dynamic Trunking Protocol |
| **HSRP** | Hot Standby Router Protocol |
| **ICMP** | Internet Control Message Protocol |
| **NAT** | Network Address Translation |
| **NTP** | Network Time Protocol |
| **OSPF** | Open Shortest Path First |
| **PAT** | Port Address Translation |
| **SNMP** | Simple Network Management Protocol |
| **SSH** | Secure Shell |
| **STP** | Spanning Tree Protocol |
| **Syslog** | System Logging Protocol |
| **VLAN** | Virtual Local Area Network |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
