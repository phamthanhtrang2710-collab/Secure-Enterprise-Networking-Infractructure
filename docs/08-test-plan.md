
# Test Plan

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#test-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-27AE60?style=for-the-badge"></a>
<a href="#test-environment"><img src="https://img.shields.io/badge/ENVIRONMENT-8E44AD?style=for-the-badge"></a>
<a href="#prerequisites"><img src="https://img.shields.io/badge/PREREQUISITES-16A085?style=for-the-badge"></a>
<a href="#test-categories"><img src="https://img.shields.io/badge/CATEGORIES-2980B9?style=for-the-badge"></a>
<a href="#connectivity-testing"><img src="https://img.shields.io/badge/CONNECTIVITY-3498DB?style=for-the-badge"></a>
<a href="#layer-2-testing"><img src="https://img.shields.io/badge/LAYER%202-E67E22?style=for-the-badge"></a>
<a href="#layer-3-routing-testing"><img src="https://img.shields.io/badge/LAYER%203-D35400?style=for-the-badge"></a>
<a href="#hsrp-testing"><img src="https://img.shields.io/badge/HSRP-C0392B?style=for-the-badge"></a>
<a href="#acl-verification"><img src="https://img.shields.io/badge/ACL-9B59B6?style=for-the-badge"></a>
<a href="#nat-verification"><img src="https://img.shields.io/badge/NAT-E74C3C?style=for-the-badge"></a>
<a href="#dhcp-testing"><img src="https://img.shields.io/badge/DHCP-1ABC9C?style=for-the-badge"></a>
<a href="#dns-testing"><img src="https://img.shields.io/badge/DNS-2ECC71?style=for-the-badge"></a>
<a href="#internet-connectivity-testing"><img src="https://img.shields.io/badge/INTERNET-34495E?style=for-the-badge"></a>
<a href="#management-access-testing"><img src="https://img.shields.io/badge/MANAGEMENT-7F8C8D?style=for-the-badge"></a>
<a href="#monitoring-verification"><img src="https://img.shields.io/badge/MONITORING-2C3E50?style=for-the-badge"></a>
<a href="#security-verification"><img src="https://img.shields.io/badge/SECURITY-95A5A6?style=for-the-badge"></a>
<a href="#failure-and-recovery-testing"><img src="https://img.shields.io/badge/FAILOVER-8E44AD?style=for-the-badge"></a>
<a href="#performance-validation"><img src="https://img.shields.io/badge/PERFORMANCE-27AE60?style=for-the-badge"></a>
<a href="#acceptance-criteria"><img src="https://img.shields.io/badge/ACCEPTANCE-0B8FD3?style=for-the-badge"></a>
<a href="#summary"><img src="https://img.shields.io/badge/SUMMARY-2C3E50?style=for-the-badge"></a>

</p>

# Overview

This document defines the testing methodology used to validate the Secure Enterprise Network Infrastructure after implementation.

The objective of this document is to verify that all network services, routing functions, security controls, management services, and high availability mechanisms operate according to the approved High-Level Design (HLD) and Low-Level Design (LLD).

Testing follows a structured validation process covering Layer 2 switching, Layer 3 routing, infrastructure services, security controls, redundancy mechanisms, monitoring, and end-to-end connectivity.

Each validation test includes expected results to ensure consistent verification and simplified troubleshooting.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Test Objectives

| ID | Objective |
|:---:|:----------|
| **TEST-OBJ-001** | Verify Layer 2 switching functionality. |
| **TEST-OBJ-002** | Validate Layer 3 routing between enterprise VLANs. |
| **TEST-OBJ-003** | Verify gateway redundancy using HSRP. |
| **TEST-OBJ-004** | Validate Internet connectivity through the Edge Router. |
| **TEST-OBJ-005** | Confirm ACL enforcement and security policies. |
| **TEST-OBJ-006** | Verify DHCP, DNS, NTP, Syslog, and SNMP services. |
| **TEST-OBJ-007** | Validate NAT functionality for Internet access. |
| **TEST-OBJ-008** | Verify monitoring and centralized management. |
| **TEST-OBJ-009** | Validate failover and recovery behavior. |
| **TEST-OBJ-010** | Ensure the network satisfies all acceptance criteria. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Test Environment

| Component | Description |
|:----------|:------------|
| Test Environment | Enterprise Lab |
| Platform | Cisco Packet Tracer |
| Routing | OSPF + BGP |
| Gateway Redundancy | HSRP |
| Security | ACL, NAT, SSH |
| Monitoring | Syslog, SNMP, LibreNMS / Zabbix |
| Servers | Windows Server 2022, Ubuntu Server 24.04 |

### Devices Included

| Device |
|:-------|
| EDGE-R1 |
| CORE-R1 |
| CORE-R2 |
| DIST-SW1 |
| HR-SW01 |
| IT-SW01 |
| FIN-SW01 |
| SALES-SW01 |
| SRV-SW01 |
| WIN-SRV01 |
| LNX-SRV01 |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Prerequisites

The following conditions shall be completed before validation begins.

| Requirement | Status |
|:------------|:------:|
| Device configurations completed | ✓ |
| VLAN deployment completed | ✓ |
| Routing configured | ✓ |
| HSRP operational | ✓ |
| ACL deployment completed | ✓ |
| NAT configured | ✓ |
| DHCP operational | ✓ |
| DNS operational | ✓ |
| Monitoring operational | ✓ |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Test Categories

| Category | Purpose |
|:----------|:--------|
| Connectivity | End-to-end communication |
| Layer 2 | VLANs, trunks, STP |
| Layer 3 | Routing |
| High Availability | HSRP |
| Security | ACL, NAT, Firewall |
| Infrastructure Services | DHCP, DNS |
| Monitoring | Syslog, SNMP |
| Recovery | Failover |
| Performance | Basic validation |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Connectivity Testing

Connectivity testing validates end-to-end communication between enterprise devices before individual network services are verified.

### TEST-CON-001 — Device Reachability

#### Objective

Verify that all enterprise infrastructure devices are reachable through the management network.

#### Preconditions

- All devices are powered on.
- Management VLAN (VLAN 99) is operational.
- IP addressing has been configured.

#### Test Procedure

| Step | Action |
|:----:|:-------|
| 1 | Connect to a management workstation. |
| 2 | Ping every network device. |
| 3 | Record any packet loss or unreachable devices. |

#### Expected Result

- All infrastructure devices respond successfully.
- No packet loss is observed.

### Verification Commands

```
ping 10.10.99.2
ping 10.10.99.3
ping 10.10.99.4
ping 10.10.99.10
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-CON-002 — End-to-End Connectivity

#### Objective

Verify communication between enterprise VLANs according to the approved security policy.

#### Preconditions

- OSPF operational.
- HSRP operational.
- ACLs applied.

#### Test Procedure

| Step | Action |
|:----:|:-------|
| 1 | Ping between authorized VLANs. |
| 2 | Verify unauthorized communication is denied. |
| 3 | Record successful and failed traffic. |

#### Expected Result

- Authorized traffic succeeds.
- Unauthorized traffic is blocked.

#### Verification Commands

```
ping
traceroute
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Layer 2 Testing

### TEST-L2-001 — VLAN Verification

#### Objective

Verify VLAN creation and assignments.

#### Procedure

| Step | Action |
|:----:|:-------|
| 1 | Display VLAN database. |
| 2 | Verify VLAN IDs and names. |
| 3 | Verify access ports belong to correct VLANs. |

#### Expected Result

- VLANs 10,20,30,40,50,99,999 exist.
- Ports belong to correct VLAN.

#### Verification Commands

```
show vlan brief
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |


### TEST-L2-002 — Trunk Verification

#### Objective

Verify IEEE 802.1Q trunk operation.

#### Expected Result

- Trunks operational.
- Native VLAN 999.
- Allowed VLAN list correct.

#### Verification Commands

```
show interfaces trunk
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-L2-003 — STP Verification

#### Objective

Verify Rapid PVST+ operation.

#### Expected Result

- DIST-SW1 is Root Bridge.
- No loops detected.

#### Verification Commands

```
show spanning-tree
show spanning-tree summary
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |


### TEST-L2-004 — Port Security

#### Objective

Verify Port Security.

#### Expected Result

- Unauthorized MAC addresses blocked.

#### Verification Commands

```
show port-security
show port-security interface
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Layer 3 Routing Testing

### TEST-L3-001 — OSPF Neighbor

#### Objective

Verify all OSPF adjacencies.

#### Expected Result

- Neighbor state FULL.

#### Verification Commands

```
show ip ospf neighbor
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |


### TEST-L3-002 — Routing Table

#### Objective

Verify enterprise routes.

#### Expected Result

- All VLAN routes installed.

#### Verification Commands

```
show ip route
show ip route ospf
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-L3-003 — Default Route

#### Objective

Verify Internet default route.

#### Expected Result

- Default route present.

#### Verification Commands

```
show ip route
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |


### TEST-L3-004 — BGP Session

#### Objective

Verify BGP peering.

#### Expected Result

- Neighbor state Established.

#### Verification Commands

```
show ip bgp summary
show ip bgp
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## HSRP Testing


### TEST-HSRP-001 — Gateway Status

#### Objective

Verify Active/Standby operation.

#### Expected Result

CORE-R1 Active

CORE-R2 Standby

#### Verification Commands

```
show standby brief
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-HSRP-002 — Gateway Failover

#### Objective

Verify gateway redundancy.

#### Procedure

Shutdown CORE-R1 active interface.

#### Expected Result

CORE-R2 becomes Active.

#### Verification Commands

```
show standby
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## ACL Verification

### TEST-ACL-001 — HR Access Restriction

#### Objective

Verify that Human Resources users cannot directly access the Finance VLAN.

#### Preconditions

- Inter-VLAN routing operational.
- ACLs applied.

#### Test Procedure

| Step | Action |
|:----:|:-------|
| 1 | Connect a test PC in VLAN 10 (HR). |
| 2 | Ping a Finance host. |
| 3 | Attempt to access Finance services. |

#### Expected Result

- Traffic is denied.
- ICMP requests fail.
- No unauthorized access is permitted.

#### Verification Commands

```
show access-lists
show ip interface
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

## TEST-ACL-002 — IT Administrative Access

### Objective

Verify that the IT VLAN has administrative connectivity to enterprise devices.

### Expected Result

- SSH access succeeds.
- Management interfaces are reachable.

### Verification Commands

```
show access-lists
ssh
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |


### TEST-ACL-003 — Management VLAN Isolation

#### Objective

Verify that users cannot directly access the Management VLAN.

#### Expected Result

- User VLAN traffic denied.
- Management VLAN reachable only from authorized administrators.

#### Verification Commands

```
show access-lists
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## NAT Verification

### TEST-NAT-001 — PAT Translation

#### Objective

Verify Port Address Translation on EDGE-R1.

#### Test Procedure

| Step | Action |
|:----:|:-------|
| 1 | Generate Internet traffic from an internal PC. |
| 2 | Verify NAT translations. |

#### Expected Result

- Private IP translated to public IP.
- Internet access successful.

#### Verification Commands

```
show ip nat translations
show ip nat statistics
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-NAT-002 — Outside Access Protection

#### Objective

Verify that unsolicited inbound connections are blocked.

#### Expected Result

- No direct access from the Internet.
- Existing sessions continue normally.

#### Verification Commands

```
show access-lists
show ip nat translations
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## DHCP Testing

### TEST-DHCP-001 — IP Address Assignment

#### Objective

Verify automatic IPv4 address assignment.

#### Expected Result

- Client receives valid IP address.
- Gateway and DNS assigned correctly.

#### Verification Commands

```
ipconfig /all
show ip dhcp binding
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-DHCP-002 — Lease Renewal

#### Objective

Verify DHCP lease renewal.

#### Expected Result

- Lease renewed successfully.
- IP remains valid.

#### Verification Commands

```
ipconfig /renew
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## DNS Testing

### TEST-DNS-001 — Internal Name Resolution

#### Objective

Verify resolution of enterprise hostnames.

#### Expected Result

- Internal DNS records resolve successfully.

#### Verification Commands

```
nslookup win-srv01.verra.local
nslookup lnx-srv01.verra.local
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-DNS-002 — External Name Resolution

#### Objective

Verify Internet DNS forwarding.

#### Expected Result

- Public domain names resolve successfully.

#### Verification Commands

```
nslookup www.cisco.com
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Internet Connectivity Testing


### TEST-INT-001 — Internet Reachability

#### Objective

Verify outbound Internet connectivity.

#### Expected Result

- Internet hosts reachable.
- Stable routing path.

#### Verification Commands

```
ping
traceroute
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |


### TEST-INT-002 — Default Route Validation

#### Objective

Verify Internet traffic uses EDGE-R1.

#### Expected Result

- Default gateway forwards traffic correctly.

#### Verification Commands

```
show ip route
traceroute
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Management Access Testing

### TEST-MGT-001 — SSH Access

#### Objective

Verify secure remote administration.

#### Expected Result

- SSH login successful.
- Telnet unavailable.

#### Verification Commands

```
show ip ssh
show users
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-MGT-002 — Management VLAN Access

#### Objective

Verify only authorized administrators access infrastructure devices.

#### Expected Result

- Authorized access permitted.
- Unauthorized access denied.

#### Verification Commands

```
show access-lists
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Verification

### TEST-MON-001 — Syslog

#### Objective

Verify centralized logging.

#### Expected Result

- Infrastructure logs received by Syslog Server.

#### Verification Commands

```
show logging
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |


### TEST-MON-002 — SNMP

#### Objective

Verify infrastructure monitoring.

#### Expected Result

- Devices appear in monitoring platform.
- Interface statistics collected.

#### Verification Commands

```
show snmp
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Security Verification

### TEST-SEC-001 — Port Security

#### Expected Result

Unauthorized devices are blocked.

#### Verification Commands

```
show port-security
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-SEC-002 — BPDU Guard

#### Expected Result

Rogue switch causes port shutdown.

#### Verification Commands

```
show spanning-tree summary
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-SEC-003 — Disabled Telnet

#### Expected Result

Telnet unavailable.

#### Verification Commands

```
show running-config
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Failure and Recovery Testing

### TEST-FAIL-001 — HSRP Failover

#### Failure Scenario

Shutdown CORE-R1.

#### Expected Result

CORE-R2 becomes Active.

#### Verification Commands

```
show standby brief
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |


### TEST-FAIL-002 — OSPF Recovery

#### Failure Scenario

Disconnect one routed link.

#### Expected Result

OSPF reconverges automatically using the remaining available path.

#### Verification Commands

```
show ip ospf neighbor
show ip route
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

### TEST-FAIL-003 — BGP Recovery

#### Failure Scenario

Restore ISP connectivity.

#### Expected Result

BGP session returns to Established state.

#### Verification Commands

```
show ip bgp summary
```

| Status |
|:------:|
| ☐ PASS ☐ FAIL |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Performance Validation

#### Performance Checklist

| Validation Item | Expected Result |
|:----------------|:----------------|
| CPU Utilization | Normal |
| Memory Utilization | Normal |
| Interface Errors | None |
| OSPF Convergence | Successful |
| HSRP Failover | < 5 Seconds |
| Packet Loss | 0% During Normal Operation |

#### Verification Commands

```
show processes cpu
show memory
show interfaces
```

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Acceptance Criteria

| Category | Requirement |
|:---------|:------------|
| Layer 2 | All VLANs and trunks operational |
| Layer 3 | OSPF neighbors FULL |
| Internet | Connectivity operational |
| HSRP | Active/Standby functioning correctly |
| ACL | Business policies enforced |
| NAT | PAT operational |
| DHCP | Address assignment successful |
| DNS | Internal and external resolution successful |
| Monitoring | Syslog and SNMP operational |
| Security | Management access protected |
| Failover | Automatic recovery successful |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Summary

The validation procedures defined in this document provide a structured approach for verifying the functionality, performance, security, and resilience of the Secure Enterprise Network Infrastructure.

Successful completion of all validation tests demonstrates that the implemented network aligns with the approved High-Level Design (HLD), Low-Level Design (LLD), security architecture, and operational requirements.

The testing methodology follows enterprise validation practices by verifying Layer 2 switching, Layer 3 routing, gateway redundancy, infrastructure services, security controls, monitoring, and failover behavior before the network is considered production-ready.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

# Glossary

| Acronym | Definition |
|:--------:|:-----------|
| ACL | Access Control List |
| BGP | Border Gateway Protocol |
| DHCP | Dynamic Host Configuration Protocol |
| DNS | Domain Name System |
| HLD | High-Level Design |
| HSRP | Hot Standby Router Protocol |
| LLD | Low-Level Design |
| NAT | Network Address Translation |
| OSPF | Open Shortest Path First |
| PAT | Port Address Translation |
| SNMP | Simple Network Management Protocol |
| SSH | Secure Shell |
| STP | Spanning Tree Protocol |
| Syslog | System Logging Protocol |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

