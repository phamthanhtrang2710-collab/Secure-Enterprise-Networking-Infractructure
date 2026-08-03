# Implementation Guide

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#implementation-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-27AE60?style=for-the-badge"></a>
<a href="#implementation-scope"><img src="https://img.shields.io/badge/SCOPE-8E44AD?style=for-the-badge"></a>
<a href="#implementation-principles"><img src="https://img.shields.io/badge/PRINCIPLES-16A085?style=for-the-badge"></a>
<a href="#implementation-prerequisites"><img src="https://img.shields.io/badge/PREREQUISITES-2980B9?style=for-the-badge"></a>
<a href="#implementation-environment"><img src="https://img.shields.io/badge/ENVIRONMENT-3498DB?style=for-the-badge"></a>
<a href="#implementation-phases"><img src="https://img.shields.io/badge/PHASES-E67E22?style=for-the-badge"></a>
<a href="#phase-1--device-preparation"><img src="https://img.shields.io/badge/PHASE%201-D35400?style=for-the-badge"></a>
<a href="#phase-2--layer-2-implementation"><img src="https://img.shields.io/badge/PHASE%202-C0392B?style=for-the-badge"></a>
<a href="#phase-3--layer-3-and-hsrp-implementation"><img src="https://img.shields.io/badge/PHASE%203-9B59B6?style=for-the-badge"></a>
<a href="#phase-4--routing-implementation"><img src="https://img.shields.io/badge/PHASE%204-E74C3C?style=for-the-badge"></a>
<a href="#phase-5--security-and-nat-implementation"><img src="https://img.shields.io/badge/PHASE%205-1ABC9C?style=for-the-badge"></a>
<a href="#phase-6--infrastructure-services"><img src="https://img.shields.io/badge/PHASE%206-2ECC71?style=for-the-badge"></a>
<a href="#phase-7--monitoring-and-logging"><img src="https://img.shields.io/badge/PHASE%207-34495E?style=for-the-badge"></a>
<a href="#phase-8--backup-automation"><img src="https://img.shields.io/badge/PHASE%208-7F8C8D?style=for-the-badge"></a>
<a href="#phase-9--validation-and-acceptance"><img src="https://img.shields.io/badge/PHASE%209-95A5A6?style=for-the-badge"></a>
<a href="#change-control"><img src="https://img.shields.io/badge/CHANGE%20CONTROL-8E44AD?style=for-the-badge"></a>
<a href="#rollback-procedure"><img src="https://img.shields.io/badge/ROLLBACK-27AE60?style=for-the-badge"></a>
<a href="#implementation-evidence"><img src="https://img.shields.io/badge/EVIDENCE-0B8FD3?style=for-the-badge"></a>
<a href="#handover-and-operational-readiness"><img src="https://img.shields.io/badge/HANDOVER-C0392B?style=for-the-badge"></a>
<a href="#implementation-checklist"><img src="https://img.shields.io/badge/CHECKLIST-E67E22?style=for-the-badge"></a>
<a href="#summary"><img src="https://img.shields.io/badge/SUMMARY-2C3E50?style=for-the-badge"></a>

</p>

## Overview

This Implementation Guide defines the step-by-step deployment process for the Secure Enterprise Network Infrastructure project.

The document translates the approved High-Level Design and Low-Level Design into an ordered implementation procedure covering network device preparation, VLAN deployment, Layer 2 switching, inter-VLAN routing, HSRP, OSPF, BGP, NAT, access control, infrastructure services, monitoring, centralized logging, and configuration backup automation.

The implementation follows a phased approach to ensure that technical dependencies are satisfied before higher-level services are introduced.

Each phase includes prerequisites, implementation activities, validation requirements, expected results, and rollback considerations.

Detailed device-level configurations should be stored separately within the project configuration directory. This document focuses on implementation workflow and deployment control rather than reproducing complete device configurations.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Objectives

| ID | Implementation Objective |
|:---:|:-------------------------|
| **IMP-OBJ-001** | Deploy the approved enterprise network architecture in a controlled and repeatable manner. |
| **IMP-OBJ-002** | Implement Layer 2 and Layer 3 services according to the approved LLD. |
| **IMP-OBJ-003** | Maintain consistency with the IP Addressing Plan and VLAN Design. |
| **IMP-OBJ-004** | Establish resilient default gateway services using HSRP. |
| **IMP-OBJ-005** | Establish internal and external routing using OSPF and BGP. |
| **IMP-OBJ-006** | Enforce segmentation and least-privilege communication through ACLs. |
| **IMP-OBJ-007** | Provide secure Internet access through NAT and perimeter filtering. |
| **IMP-OBJ-008** | Deploy centralized DHCP, DNS, NTP, Syslog, and monitoring services. |
| **IMP-OBJ-009** | Automate network device configuration backups using Python and Netmiko. |
| **IMP-OBJ-010** | Validate each implementation phase before proceeding to dependent services. |
| **IMP-OBJ-011** | Maintain recoverable pre-change and post-change configuration states. |
| **IMP-OBJ-012** | Produce sufficient implementation evidence for technical review and portfolio presentation. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Scope

### In-Scope Implementation

| Area | Implementation Coverage |
|:-----|:------------------------|
| Device Preparation | Hostnames, management addressing, local authentication, SSH, and basic hardening |
| VLAN Deployment | VLANs 10, 20, 30, 40, 50, 99, and 999 |
| Access Ports | Departmental and server access-port assignments |
| Trunking | Core-to-Distribution and Distribution-to-Access 802.1Q trunks |
| Spanning Tree | Rapid PVST+, root bridge placement, PortFast, and BPDU Guard |
| Layer 2 Security | Port Security, unused-port shutdown, native VLAN, and DTP control |
| Inter-VLAN Routing | Router subinterfaces or VLAN gateway interfaces on CORE-R1 and CORE-R2 |
| High Availability | HSRPv2 for enterprise VLAN gateways |
| Internal Routing | OSPFv2 Area 0 |
| External Routing | eBGP between EDGE-R1 and the ISP Router |
| Default Routing | Static default route from EDGE-R1 toward the ISP |
| NAT | PAT overload on EDGE-R1 |
| Access Control | Inter-VLAN ACLs and management-plane filtering |
| DHCP | Windows Server DHCP scopes and Cisco DHCP Relay |
| DNS | AD-integrated internal DNS and external Forwarders |
| NTP | Centralized enterprise time synchronization |
| Monitoring | LibreNMS device monitoring |
| Logging | Centralized Syslog on LNX-SRV01 |
| Automation | Python and Netmiko configuration backups |
| Validation | Functional, security, failover, monitoring, and recovery tests |

### Out-of-Scope Implementation

| Area | Reason |
|:-----|:-------|
| IPv6 | Reserved for future expansion |
| MPLS | Not required for the simulated campus environment |
| SD-WAN | Outside the initial implementation scope |
| Wireless Controller | Not included in the current architecture |
| Production Firewall Appliance | Perimeter control is simulated through EDGE-R1 ACLs and NAT |
| Cloud Integration | AWS and Azure connectivity are future enhancements |
| Dual ISP | The current design uses a single ISP |
| Full SIEM Deployment | Centralized Syslog is implemented without an enterprise SIEM |
| Geographic Disaster Recovery | Not available in the simulated lab |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Principles

| Principle | Implementation Requirement |
|:----------|:---------------------------|
| **Design Compliance** | All changes must follow the approved HLD and LLD. |
| **Dependency Order** | Layer 2 must be operational before Layer 3 and infrastructure services. |
| **Incremental Deployment** | Configuration is introduced in controlled phases. |
| **Pre-Change Protection** | A backup must be created before significant changes. |
| **Immediate Validation** | Each change is validated before proceeding. |
| **Least Privilege** | Only required access is permitted. |
| **Secure Management** | SSH replaces insecure remote management protocols. |
| **Configuration Consistency** | Standard templates are used for similar devices. |
| **Evidence Collection** | Relevant outputs and screenshots are collected during implementation. |
| **Rollback Readiness** | Every major implementation phase includes a recovery method. |
| **Documentation Accuracy** | Any deviation from the approved design must be recorded. |
| **Secret Protection** | Credentials and unredacted configurations must not be published. |

### Implementation Rule

> Do not proceed to the next phase when a critical validation item from the current phase has failed.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Prerequisites

### Documentation Prerequisites

| Document | Required Status |
|:---------|:----------------|
| Business Requirements Document | Approved for implementation |
| High-Level Design | Completed |
| Low-Level Design | Completed |
| IP Addressing Plan | Completed and verified |
| VLAN Design | Completed and verified |
| Routing Design | Completed and verified |
| Security Design | Completed and verified |
| Test Plan | Available |
| Troubleshooting Guide | Available |
| Monitoring Guide | Available |
| Backup and Recovery Guide | Available |

### Technical Prerequisites

| Requirement | Verification |
|:------------|:-------------|
| Network Devices | Required routers and switches available |
| Device Images | Compatible Cisco IOS or IOS XE images installed |
| Servers | WIN-SRV01 and LNX-SRV01 available |
| Management Workstation | Connected to an authorized management network |
| Console Access | Available for initial configuration and recovery |
| IP Addressing | Reserved addresses confirmed |
| Credentials | Administrative credentials available securely |
| Time Source | NTP source selected |
| Lab Platform | Packet Tracer, GNS3, EVE-NG, or physical equipment ready |
| Evidence Directory | Screenshot and command-output folders created |
| Backup Directory | Protected backup repository created |

### Pre-Implementation Validation

| ID | Validation Item | Expected Result | Status |
|:--:|:----------------|:----------------|:------:|
| **PRE-001** | Device inventory reviewed | All required devices listed | ☐ |
| **PRE-002** | Interface mapping reviewed | Interfaces match the LLD | ☐ |
| **PRE-003** | IP addressing reviewed | No duplicate IP addresses | ☐ |
| **PRE-004** | VLAN assignments reviewed | VLAN mapping approved | ☐ |
| **PRE-005** | Server requirements reviewed | Servers have sufficient resources | ☐ |
| **PRE-006** | Initial device access tested | Console access successful | ☐ |
| **PRE-007** | Existing configurations captured | Baseline available | ☐ |
| **PRE-008** | Rollback plan reviewed | Recovery actions understood | ☐ |
| **PRE-009** | Test cases identified | Required validation tests selected | ☐ |
| **PRE-010** | Maintenance window confirmed | Implementation authorized | ☐ |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Environment

### Network Infrastructure

| Hostname | Platform | Implementation Role |
|:---------|:---------|:--------------------|
| **EDGE-R1** | Cisco ISR 4331 | BGP, NAT, perimeter filtering, default routing |
| **CORE-R1** | Cisco ISR 4331 | Primary inter-VLAN gateway, HSRP Active, OSPF |
| **CORE-R2** | Cisco ISR 4331 | Secondary inter-VLAN gateway, HSRP Standby, OSPF |
| **DIST-SW1** | Cisco Catalyst 3560 | Layer 2 VLAN and trunk aggregation |
| **HR-SW01** | Cisco Catalyst 2960 | HR access connectivity |
| **IT-SW01** | Cisco Catalyst 2960 | IT access connectivity |
| **FIN-SW01** | Cisco Catalyst 2960 | Finance access connectivity |
| **SALES-SW01** | Cisco Catalyst 2960 | Sales access connectivity |
| **SRV-SW01** | Cisco Catalyst 2960 | Server access connectivity |
| **ISP-R1** | Simulated ISP Router | External BGP peer and Internet simulation |

### Server Infrastructure

| Hostname | Platform | Services |
|:---------|:---------|:---------|
| **WIN-SRV01** | Windows Server 2022 | AD DS, DHCP, DNS, NTP |
| **LNX-SRV01** | Ubuntu Server 24.04 LTS | LibreNMS, Syslog, web services, automation |

### Key Addressing

| Component | Address |
|:----------|:--------|
| EDGE-R1 Management | `10.10.99.4` |
| CORE-R1 Management | `10.10.99.2` |
| CORE-R2 Management | `10.10.99.3` |
| DIST-SW1 Management | `10.10.99.10` |
| WIN-SRV01 | `10.10.50.10` |
| LNX-SRV01 | `10.10.50.20` |
| EDGE-R1 ISP Interface | `203.0.113.1/30` |
| ISP-R1 Enterprise Interface | `203.0.113.2/30` |
| EDGE-R1 to CORE-R1 | `10.10.250.0/30` |
| EDGE-R1 to CORE-R2 | `10.10.250.4/30` |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Phases

| Phase | Implementation Area | Dependency |
|:----:|:--------------------|:-----------|
| **1** | Device Preparation | Physical or virtual devices available |
| **2** | Layer 2 Implementation | Device preparation complete |
| **3** | Layer 3 and HSRP | VLANs and trunks operational |
| **4** | Routing Implementation | Layer 3 interfaces operational |
| **5** | Security and NAT | Routing operational |
| **6** | Infrastructure Services | Inter-VLAN connectivity operational |
| **7** | Monitoring and Logging | Management and server connectivity operational |
| **8** | Backup Automation | SSH management operational |
| **9** | Validation and Acceptance | All previous phases complete |

### Implementation Decision Gates

| Gate | Requirement |
|:-----|:------------|
| **Gate 1** | Devices accessible and baseline configuration verified |
| **Gate 2** | VLAN and trunk verification completed |
| **Gate 3** | HSRP gateways reachable |
| **Gate 4** | OSPF, BGP, and default routing operational |
| **Gate 5** | ACL and NAT tests successful |
| **Gate 6** | DHCP, DNS, NTP, and server services operational |
| **Gate 7** | LibreNMS and Syslog receiving data |
| **Gate 8** | Automated backups completed |
| **Gate 9** | All acceptance tests passed |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 1 — Device Preparation

### Objective

Prepare all network devices for secure configuration, management, monitoring, and subsequent service deployment.

### Implementation Activities

| Step | Activity |
|:---:|:---------|
| **1.1** | Establish console access to each network device. |
| **1.2** | Remove obsolete lab configuration where appropriate. |
| **1.3** | Configure the approved hostname. |
| **1.4** | Disable DNS lookup where unintended command delays must be prevented. |
| **1.5** | Configure an enable secret. |
| **1.6** | Configure an authorized local administrator account. |
| **1.7** | Configure service password encryption where supported. |
| **1.8** | Configure a legal login banner. |
| **1.9** | Configure the domain name required for SSH. |
| **1.10** | Generate RSA keys and enable SSH Version 2. |
| **1.11** | Restrict VTY lines to SSH. |
| **1.12** | Configure console and session timeouts. |
| **1.13** | Configure management IP addressing. |
| **1.14** | Configure device descriptions and interface descriptions. |
| **1.15** | Configure NTP and Syslog destination settings where connectivity permits. |
| **1.16** | Save the baseline configuration. |
| **1.17** | Capture the initial configuration backup. |

### Baseline Device Configuration Requirements

| Item | Requirement |
|:-----|:------------|
| Hostname | Matches Device Inventory |
| Remote Management | SSH Version 2 |
| Telnet | Disabled |
| Authentication | Local AAA initially |
| Management Network | VLAN 99 |
| Logging | LNX-SRV01 |
| NTP | WIN-SRV01 |
| Unused Services | Disabled where practical |
| Passwords | Protected and excluded from GitHub |
| Configuration Backup | Required |

### Phase 1 Verification

```text
show running-config
show ip interface brief
show ip ssh
show users
show clock
show logging
```

### Expected Result

- Every device has the correct hostname.
- Management addressing matches the IP plan.
- SSH is enabled.
- Telnet is unavailable.
- Administrative login succeeds.
- Baseline configuration is saved and backed up.

### Rollback

- Restore the initial baseline configuration.
- Use console access if remote management fails.
- Remove incorrect management addressing before reapplying the approved configuration.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 2 — Layer 2 Implementation

### Objective

Deploy departmental VLAN segmentation, switch access ports, trunk connectivity, spanning tree, and Layer 2 security controls.

### VLAN Creation

| VLAN | Name | Purpose |
|:----:|:-----|:--------|
| **10** | HR | Human Resources |
| **20** | IT | Information Technology |
| **30** | FINANCE | Finance Department |
| **40** | SALES | Sales Department |
| **50** | SERVERS | Enterprise Servers |
| **99** | MANAGEMENT | Infrastructure Management |
| **999** | NATIVE-BLACKHOLE | Unused Native VLAN |

### Access-Port Implementation

| Switch | Assigned VLAN | Endpoint Type |
|:-------|:-------------:|:--------------|
| HR-SW01 | 10 | HR workstations |
| IT-SW01 | 20 | IT workstations |
| FIN-SW01 | 30 | Finance workstations |
| SALES-SW01 | 40 | Sales workstations |
| SRV-SW01 | 50 | Windows and Linux servers |

### Trunk Implementation

| Link | Allowed VLANs | Native VLAN |
|:-----|:--------------|:------------|
| CORE-R1 ↔ DIST-SW1 | 10,20,30,40,50,99 | 999 |
| CORE-R2 ↔ DIST-SW1 | 10,20,30,40,50,99 | 999 |
| DIST-SW1 ↔ HR-SW01 | 10,99 | 999 |
| DIST-SW1 ↔ IT-SW01 | 20,99 | 999 |
| DIST-SW1 ↔ FIN-SW01 | 30,99 | 999 |
| DIST-SW1 ↔ SALES-SW01 | 40,99 | 999 |
| DIST-SW1 ↔ SRV-SW01 | 50,99 | 999 |

### Layer 2 Security Activities

| Control | Implementation |
|:--------|:---------------|
| DTP | Disabled on static trunks |
| Native VLAN | VLAN 999 |
| Allowed VLANs | Explicitly limited |
| PortFast | Enabled on endpoint-facing ports |
| BPDU Guard | Enabled on endpoint-facing ports |
| Port Security | Enabled on user-facing access ports |
| Unused Ports | Assigned to VLAN 999 and shut down |
| Storm Control | Enabled where platform support permits |

### Spanning Tree Implementation

| Item | Implementation |
|:-----|:---------------|
| STP Mode | Rapid PVST+ |
| Root Bridge | DIST-SW1 |
| Root Scope | VLANs 10, 20, 30, 40, 50, and 99 |
| Access Port Protection | PortFast and BPDU Guard |
| Secondary Root | Not implemented in the current single-distribution design |

### Phase 2 Verification

```text
show vlan brief
show interfaces trunk
show interfaces switchport
show spanning-tree
show spanning-tree root
show port-security
show interfaces status
show cdp neighbors
```

### Expected Result

- All required VLANs exist.
- Access ports belong to the correct VLANs.
- Trunks use VLAN 999 as the native VLAN.
- Only required VLANs are allowed on each trunk.
- DIST-SW1 is the STP root.
- Access-layer security features are active.

### Rollback

- Restore the previous VLAN and trunk configuration.
- Remove an incorrectly introduced VLAN.
- Return affected switchports to the last known good assignment.
- Disable Port Security temporarily only when required for controlled recovery.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 3 — Layer 3 and HSRP Implementation

### Objective

Provide redundant Layer 3 default gateway services for enterprise VLANs using CORE-R1 and CORE-R2.

### Gateway Addressing

| VLAN | Virtual IP | CORE-R1 | CORE-R2 |
|:----:|:-----------|:--------|:--------|
| **10** | `10.10.10.1` | `10.10.10.2` | `10.10.10.3` |
| **20** | `10.10.20.1` | `10.10.20.2` | `10.10.20.3` |
| **30** | `10.10.30.1` | `10.10.30.2` | `10.10.30.3` |
| **40** | `10.10.40.1` | `10.10.40.2` | `10.10.40.3` |
| **50** | `10.10.50.1` | `10.10.50.2` | `10.10.50.3` |
| **99** | `10.10.99.1` | `10.10.99.2` | `10.10.99.3` |

### HSRP Parameters

| Parameter | CORE-R1 | CORE-R2 |
|:----------|:--------|:--------|
| HSRP Version | Version 2 | Version 2 |
| Priority | 110 | 100 |
| Preemption | Enabled | Enabled |
| Preferred Role | Active | Standby |
| Interface Tracking | EDGE uplink | EDGE uplink |

### Implementation Activities

| Step | Activity |
|:---:|:---------|
| **3.1** | Configure VLAN gateway interfaces or router subinterfaces. |
| **3.2** | Assign physical gateway IP addresses. |
| **3.3** | Configure HSRPv2 groups. |
| **3.4** | Configure one virtual IP per VLAN. |
| **3.5** | Configure CORE-R1 priority as 110. |
| **3.6** | Configure CORE-R2 priority as 100. |
| **3.7** | Enable preemption. |
| **3.8** | Configure interface tracking. |
| **3.9** | Confirm the HSRP virtual MAC is learned correctly. |
| **3.10** | Test gateway reachability from each VLAN. |

### Phase 3 Verification

```text
show ip interface brief
show standby brief
show standby
show track
show arp
ping 10.10.10.1
ping 10.10.20.1
ping 10.10.30.1
ping 10.10.40.1
ping 10.10.50.1
ping 10.10.99.1
```

### Failover Test

1. Confirm CORE-R1 is Active.
2. Generate continuous traffic through the HSRP gateway.
3. Shut down the tracked CORE-R1 uplink or simulate CORE-R1 failure.
4. Confirm CORE-R2 becomes Active.
5. Confirm traffic resumes through CORE-R2.
6. Restore CORE-R1.
7. Confirm preemption returns the Active role to CORE-R1.

### Expected Result

- CORE-R1 is the preferred Active gateway.
- CORE-R2 remains Standby.
- Endpoints use the HSRP virtual IP.
- CORE-R2 assumes the Active role during failure.
- CORE-R1 resumes the Active role after recovery.

### Rollback

- Remove incorrect HSRP groups.
- Restore previous gateway configuration.
- Disable interface tracking if it produces unintended failover.
- Use one physical gateway temporarily during controlled recovery if necessary.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 4 — Routing Implementation

### Objective

Establish internal route exchange through OSPF, external ISP connectivity through eBGP, and deterministic Internet forwarding through a static default route.

### Routed Links

| Link | EDGE-R1 | Remote Device |
|:-----|:--------|:--------------|
| EDGE-R1 ↔ CORE-R1 | `10.10.250.1/30` | CORE-R1 `10.10.250.2/30` |
| EDGE-R1 ↔ CORE-R2 | `10.10.250.5/30` | CORE-R2 `10.10.250.6/30` |
| EDGE-R1 ↔ ISP-R1 | `203.0.113.1/30` | ISP-R1 `203.0.113.2/30` |

### OSPF Implementation

| Item | Value |
|:-----|:------|
| Process ID | 1 |
| Area | 0 |
| EDGE-R1 Router ID | 1.1.1.1 |
| CORE-R1 Router ID | 2.2.2.2 |
| CORE-R2 Router ID | 3.3.3.3 |
| VLAN Interfaces | Passive |
| Routed EDGE Links | Non-passive |
| Authentication | Future enhancement |

### OSPF Implementation Activities

| Step | Activity |
|:---:|:---------|
| **4.1** | Configure OSPF process 1. |
| **4.2** | Assign unique Router IDs. |
| **4.3** | Configure passive-interface by default where appropriate. |
| **4.4** | Remove passive status from routed OSPF links. |
| **4.5** | Advertise enterprise VLAN networks. |
| **4.6** | Advertise routed EDGE-to-CORE networks. |
| **4.7** | Confirm OSPF neighbor formation. |
| **4.8** | Confirm enterprise routes appear on EDGE-R1. |
| **4.9** | Configure default route origination after the static default route is available. |

### BGP Implementation

| Item | Value |
|:-----|:------|
| Enterprise AS | 65001 |
| ISP AS | 65000 |
| Enterprise Peer | `203.0.113.2` |
| ISP Peer | `203.0.113.1` |
| Enterprise Prefix | `10.10.0.0/16` |
| Default Route | Static on EDGE-R1 |

### Static Routing

| Device | Destination | Next Hop | Administrative Distance |
|:-------|:------------|:---------|:------------------------|
| EDGE-R1 | `0.0.0.0/0` | `203.0.113.2` | 1 |
| ISP-R1 | `10.10.0.0/16` | `203.0.113.1` | 1 |

### Phase 4 Verification

```text
show ip interface brief
show ip route
show ip route ospf
show ip protocols
show ip ospf neighbor
show ip ospf interface brief
show ip ospf database
show ip bgp summary
show ip bgp
show ip route bgp
ping 203.0.113.2
traceroute 203.0.113.2
```

### Expected Result

- OSPF neighbors reach FULL state.
- EDGE-R1 learns enterprise routes.
- CORE-R1 and CORE-R2 learn the default route through OSPF.
- BGP reaches the Established state.
- ISP-R1 has a route toward the enterprise.
- Enterprise traffic reaches the ISP simulation.

### Rollback

- Restore the previous OSPF section.
- Remove incorrect network statements.
- Restore the static default route.
- Remove and reconfigure an incorrect BGP neighbor.
- Use static routing temporarily when dynamic routing cannot be restored immediately.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 5 — Security and NAT Implementation

### Objective

Implement controlled inter-VLAN access, secure management-plane access, perimeter filtering, and PAT-based Internet connectivity.

### ACL Implementation Order

1. Confirm required traffic flows.
2. Define source and destination networks.
3. Identify required protocols and ports.
4. Create explicit permit statements.
5. Add explicit deny statements where appropriate.
6. Include logging selectively.
7. Apply the ACL to the correct interface and direction.
8. Validate permitted and denied traffic.
9. Review ACL counters.

### Access Policy Summary

| Source | Destination | Service | Action |
|:-------|:------------|:--------|:------:|
| IT VLAN | Enterprise VLANs | Administrative services | Allow |
| Management VLAN | Network devices | SSH and SNMP | Allow |
| HR VLAN | Finance VLAN | Any | Deny |
| Finance VLAN | Server VLAN | Approved services | Allow |
| Sales VLAN | Server VLAN | HTTPS and DNS | Allow |
| HR VLAN | Server VLAN | DHCP, DNS, and HTTPS | Allow |
| User VLANs | Management VLAN | Any | Deny |
| Authorized VLANs | Internet | Approved outbound services | Allow |
| Outside | Inside | Unsolicited traffic | Deny |

### NAT Implementation

| Item | Configuration |
|:-----|:--------------|
| NAT Device | EDGE-R1 |
| NAT Type | PAT overload |
| Inside Networks | `10.10.0.0/16` |
| Inside Interfaces | EDGE-R1 interfaces facing CORE-R1 and CORE-R2 |
| Outside Interface | EDGE-R1 G0/0 |
| Global Address | EDGE-R1 outside-interface address |

### Security Hardening Activities

| Control | Implementation |
|:--------|:---------------|
| Telnet | Disabled |
| SSH | Version 2 |
| Management ACL | Restricts VTY access |
| SNMP | SNMPv3 preferred |
| Unused Ports | Disabled |
| Port Security | Enabled |
| DTP | Disabled |
| Native VLAN | VLAN 999 |
| Syslog | Centralized |
| NTP | Centralized |
| Password Publication | Prohibited |

### Phase 5 Verification

```text
show access-lists
show ip interface
show running-config | section access-list
show ip nat translations
show ip nat statistics
show ip ssh
show users
show port-security
show logging
ping
traceroute
```

### Required Security Tests

| Test | Expected Result |
|:-----|:----------------|
| HR to Finance | Denied |
| IT to managed devices | Allowed |
| User VLAN to Management VLAN | Denied |
| Finance to approved server service | Allowed |
| User VLAN to Internet | Allowed through PAT |
| Outside unsolicited connection | Denied |
| Telnet connection | Rejected |
| SSH from authorized source | Allowed |
| SSH from unauthorized source | Denied |

### Rollback

- Remove the newly applied ACL from the interface.
- Restore the pre-change ACL configuration.
- Restore previous NAT inside and outside roles.
- Restore the previous NAT ACL.
- Preserve console access before changing management restrictions.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 6 — Infrastructure Services

### Objective

Deploy centralized DHCP, DNS, NTP, and required server connectivity.

### Windows Server Preparation

| Item | Configuration |
|:-----|:--------------|
| Hostname | WIN-SRV01 |
| IP Address | `10.10.50.10/24` |
| Default Gateway | `10.10.50.1` |
| Preferred DNS | `10.10.50.10` |
| Operating System | Windows Server 2022 |
| Roles | AD DS, DHCP, DNS |
| NTP Role | Internal enterprise time source |

### DHCP Scope Implementation

| VLAN | Scope | Gateway | DNS Server |
|:----:|:------|:--------|:-----------|
| 10 | `10.10.10.50–10.10.10.200` | `10.10.10.1` | `10.10.50.10` |
| 20 | `10.10.20.50–10.10.20.200` | `10.10.20.1` | `10.10.50.10` |
| 30 | `10.10.30.50–10.10.30.200` | `10.10.30.1` | `10.10.50.10` |
| 40 | `10.10.40.50–10.10.40.200` | `10.10.40.1` | `10.10.50.10` |

### DHCP Relay

Configure the DHCP Relay destination on the VLAN gateway interfaces:

```text
10.10.50.10
```

The relay should be configured on user VLAN gateway interfaces that do not contain the DHCP Server.

### DNS Implementation

| Item | Configuration |
|:-----|:--------------|
| Internal Zone | `verra.local` |
| Zone Type | AD-integrated |
| Dynamic Updates | Secure only |
| Forwarders | Approved external DNS resolvers |
| Primary Server | WIN-SRV01 |

### Required DNS Records

| Hostname | IP Address |
|:---------|:-----------|
| win-srv01.verra.local | `10.10.50.10` |
| lnx-srv01.verra.local | `10.10.50.20` |
| edge-r1.verra.local | `10.10.99.4` |
| core-r1.verra.local | `10.10.99.2` |
| core-r2.verra.local | `10.10.99.3` |
| dist-sw1.verra.local | `10.10.99.10` |

### Phase 6 Verification

On a Windows client:

```text
ipconfig /release
ipconfig /renew
ipconfig /all
nslookup win-srv01.verra.local
nslookup www.cisco.com
ping 10.10.50.10
```

On Cisco devices:

```text
show ip interface
show access-lists
show clock
show ntp status
show ntp associations
```

On WIN-SRV01:

```powershell
Get-DhcpServerv4Scope
Get-DhcpServerv4ScopeStatistics
Get-DhcpServerv4Lease
Get-DhcpServerv4OptionValue
Get-DnsServerZone
Get-DnsServerResourceRecord -ZoneName "verra.local"
```

### Expected Result

- Clients obtain the correct IP configuration.
- HSRP virtual IP is assigned as the default gateway.
- WIN-SRV01 is assigned as the DNS Server.
- Internal and external DNS resolution succeeds.
- Network device clocks synchronize with the approved time source.

### Rollback

- Disable an incorrect DHCP scope.
- Remove an incorrect relay address.
- Restore the previous DHCP export.
- Restore DNS records or zone data.
- Revert NTP configuration to the last known good state.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 7 — Monitoring and Logging

### Objective

Deploy LibreNMS monitoring and centralized Syslog collection on LNX-SRV01.

### Linux Server Preparation

| Item | Configuration |
|:-----|:--------------|
| Hostname | LNX-SRV01 |
| IP Address | `10.10.50.20/24` |
| Default Gateway | `10.10.50.1` |
| DNS Server | `10.10.50.10` |
| Platform | Ubuntu Server 24.04 LTS |
| Services | LibreNMS, MariaDB, Apache, Rsyslog |

### Monitoring Implementation Activities

| Step | Activity |
|:---:|:---------|
| **7.1** | Verify LNX-SRV01 network connectivity. |
| **7.2** | Install LibreNMS prerequisites. |
| **7.3** | Install and configure MariaDB. |
| **7.4** | Install and configure LibreNMS. |
| **7.5** | Configure secure dashboard access. |
| **7.6** | Configure SNMPv3 on supported devices. |
| **7.7** | Use restricted SNMPv2c only when required by the lab platform. |
| **7.8** | Add devices using Management VLAN addresses. |
| **7.9** | Configure device groups and locations. |
| **7.10** | Configure operational alert rules. |
| **7.11** | Configure centralized Syslog. |
| **7.12** | Validate timestamp consistency through NTP. |

### Device Onboarding Order

1. LNX-SRV01
2. WIN-SRV01
3. EDGE-R1
4. CORE-R1
5. CORE-R2
6. DIST-SW1
7. Access switches

### Phase 7 Verification

On Cisco devices:

```text
show snmp
show logging
show clock
show ntp status
```

On LNX-SRV01:

```text
sudo systemctl status rsyslog
sudo systemctl status mariadb
sudo systemctl status apache2
sudo ss -lunp | grep 514
df -h
free -m
```

### Controlled Alert Tests

| Test | Expected Result |
|:-----|:----------------|
| Shut down a monitored interface | Interface-down alert generated |
| Temporarily stop SNMP polling | Polling failure detected |
| Generate a login failure | Syslog event recorded |
| Simulate OSPF neighbor loss | Routing event recorded |
| Trigger HSRP transition | State change recorded |
| Restore the component | Recovery reflected in LibreNMS |

### Expected Result

- All in-scope devices appear in LibreNMS.
- ICMP and SNMP polling succeed.
- Interface metrics are visible.
- Syslog events reach LNX-SRV01.
- Controlled failures generate alerts.
- Recovery events clear appropriately.

### Rollback

- Restore the previous SNMP configuration.
- Remove an incorrectly onboarded device.
- Restore LibreNMS and Rsyslog configuration files.
- Restore the LibreNMS database from a validated backup when necessary.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 8 — Backup Automation

### Objective

Automate daily network device configuration backups through Python and Netmiko.

### Automation Host

| Item | Value |
|:-----|:------|
| Host | LNX-SRV01 |
| Language | Python 3 |
| Library | Netmiko |
| Transport | SSH |
| Backup Location | `/opt/network-backups/` |
| Schedule | Daily through cron |
| Credential Storage | Protected environment or secrets file |

### Automation Implementation Activities

| Step | Activity |
|:---:|:---------|
| **8.1** | Install Python 3 and required packages. |
| **8.2** | Install Netmiko in a virtual environment. |
| **8.3** | Create the protected directory structure. |
| **8.4** | Create a sanitized device inventory. |
| **8.5** | Configure protected credential input. |
| **8.6** | Implement SSH connection handling. |
| **8.7** | Collect running and startup configurations. |
| **8.8** | Save timestamped configuration files. |
| **8.9** | Validate hostname and file content. |
| **8.10** | Record success and failure logs. |
| **8.11** | Configure retention handling. |
| **8.12** | Schedule the script through cron. |
| **8.13** | Test failure reporting. |

### Required Backup Files

```text
<HOSTNAME>_daily_<YYYYMMDD>_<HHMMSS>.cfg
```

### Phase 8 Verification

```bash
ls -lah /opt/network-backups/configs/
find /opt/network-backups/configs/ -type f
tail -n 50 /opt/network-backups/logs/backup.log
sha256sum <backup-file>
```

### Verification Requirements

| Item | Expected Result |
|:-----|:----------------|
| Device Connection | SSH succeeds |
| Running Configuration | Collected |
| Startup Configuration | Collected |
| File Naming | Follows standard |
| File Content | Non-empty |
| Hostname Validation | Correct |
| Logging | Success or failure recorded |
| Schedule | Cron job executes |
| Secret Protection | No credentials published |

### Rollback

- Disable the scheduled cron job.
- Restore the previous approved script version.
- Restore inventory and configuration files.
- Retain failed logs for troubleshooting.
- Perform a manual backup until automation is restored.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Phase 9 — Validation and Acceptance

### Objective

Confirm that the deployed infrastructure satisfies the approved requirements and is ready for operational use.

### Validation Categories

| Category | Validation Coverage |
|:---------|:--------------------|
| Physical Connectivity | Interfaces and links |
| Layer 2 | VLANs, trunks, STP, and Port Security |
| Layer 3 | Gateway and inter-VLAN routing |
| High Availability | HSRP failover |
| Internal Routing | OSPF |
| External Routing | BGP and default route |
| Security | ACLs, management restrictions, and hardening |
| Internet Access | NAT and perimeter policy |
| Infrastructure Services | DHCP, DNS, NTP |
| Monitoring | LibreNMS and Syslog |
| Automation | Configuration backups |
| Recovery | Selected restoration test |

### Final Verification Commands

```text
show ip interface brief
show interfaces
show vlan brief
show interfaces trunk
show spanning-tree
show port-security
show standby brief
show track
show ip route
show ip route ospf
show ip ospf neighbor
show ip bgp summary
show access-lists
show ip nat translations
show ip nat statistics
show ip ssh
show snmp
show logging
show ntp associations
ping
traceroute
```

### Acceptance Checklist

| ID | Acceptance Item | Expected Result | Status |
|:--:|:----------------|:----------------|:------:|
| **IMP-ACC-001** | Device management | SSH operational | ☐ |
| **IMP-ACC-002** | VLAN deployment | VLANs operational | ☐ |
| **IMP-ACC-003** | Trunk connectivity | Required VLANs forwarded | ☐ |
| **IMP-ACC-004** | STP | DIST-SW1 is root | ☐ |
| **IMP-ACC-005** | HSRP | Active and Standby roles correct | ☐ |
| **IMP-ACC-006** | Inter-VLAN routing | Authorized traffic succeeds | ☐ |
| **IMP-ACC-007** | OSPF | Neighbors FULL | ☐ |
| **IMP-ACC-008** | BGP | Peer Established | ☐ |
| **IMP-ACC-009** | Default route | Installed and advertised internally | ☐ |
| **IMP-ACC-010** | ACL policy | Permit and deny behavior correct | ☐ |
| **IMP-ACC-011** | NAT | PAT translations created | ☐ |
| **IMP-ACC-012** | DHCP | Correct leases assigned | ☐ |
| **IMP-ACC-013** | DNS | Internal and external resolution succeeds | ☐ |
| **IMP-ACC-014** | NTP | Devices synchronized | ☐ |
| **IMP-ACC-015** | Syslog | Events collected centrally | ☐ |
| **IMP-ACC-016** | LibreNMS | Devices monitored | ☐ |
| **IMP-ACC-017** | Backup automation | Backup run succeeds | ☐ |
| **IMP-ACC-018** | Recovery test | Selected recovery succeeds | ☐ |
| **IMP-ACC-019** | Documentation | Updated and complete | ☐ |
| **IMP-ACC-020** | Critical defects | None unresolved | ☐ |

### Acceptance Requirement

The implementation is accepted only when:

- All critical test cases pass.
- No unresolved critical security issues remain.
- Monitoring and centralized logging are operational.
- Backups are available and verified.
- Recovery procedures have been demonstrated.
- Documentation reflects the implemented environment.
- Required evidence has been captured.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Change Control

All implementation changes must follow a controlled change process.

### Change Record Requirements

| Field | Description |
|:------|:------------|
| Change ID | Unique change reference |
| Change Description | Summary of the proposed change |
| Business Reason | Justification |
| Affected Devices | Devices and services impacted |
| Risk Level | Low, Medium, High, or Critical |
| Implementation Plan | Ordered deployment steps |
| Validation Plan | Required post-change tests |
| Rollback Plan | Recovery actions |
| Maintenance Window | Approved implementation period |
| Approver | Authorized reviewer |
| Result | Successful, partial, failed, or rolled back |

### Change Workflow

```text
Change Requested
       │
       ▼
Technical Review
       │
       ▼
Risk Assessment
       │
       ▼
Approval
       │
       ▼
Pre-Change Backup
       │
       ▼
Implementation
       │
       ▼
Validation
       │
       ├── Successful ──► Documentation and Closure
       │
       └── Failed ──────► Rollback and Investigation
```

### Emergency Changes

Emergency changes may proceed without the complete standard approval cycle only when immediate action is required to restore critical services or reduce an active security risk.

Emergency changes must still include:

- Incident reference
- Authorized technical owner
- Backup where possible
- Minimum required change
- Post-change validation
- Retrospective approval
- Complete documentation

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Rollback Procedure

### Rollback Triggers

Rollback should begin when:

- A critical service becomes unavailable.
- Validation tests fail.
- Connectivity is degraded beyond the approved impact.
- Management access is lost.
- Security policy behaves incorrectly.
- Routing becomes unstable.
- The maintenance window is exceeded.
- Recovery risk becomes greater than the benefit of continuing.

### Standard Rollback Process

| Step | Activity |
|:---:|:---------|
| **1** | Stop further implementation activity. |
| **2** | Record the current failed state. |
| **3** | Notify the implementation owner. |
| **4** | Select the validated pre-change backup. |
| **5** | Remove or reverse the failed configuration. |
| **6** | Restore the last known good configuration. |
| **7** | Verify device and service recovery. |
| **8** | Confirm monitoring status. |
| **9** | Record the rollback result. |
| **10** | Conduct root-cause analysis before retrying. |

### Rollback Verification

```text
show ip interface brief
show vlan brief
show interfaces trunk
show standby brief
show ip route
show ip ospf neighbor
show ip bgp summary
show access-lists
show ip nat translations
show logging
ping
traceroute
```

### Rollback Completion Criteria

- Services have returned to the pre-change operational state.
- Monitoring alerts have cleared.
- Security controls remain active.
- The restored configuration is saved.
- A post-rollback backup has been created.
- The failed change is documented.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Evidence

Implementation evidence demonstrates that the solution was deployed and validated successfully.

### Required Evidence

| Evidence Category | Examples |
|:------------------|:---------|
| Device Preparation | Hostnames, SSH status, management addressing |
| VLAN | `show vlan brief` |
| Trunking | `show interfaces trunk` |
| STP | `show spanning-tree root` |
| Port Security | `show port-security` |
| HSRP | `show standby brief` |
| OSPF | `show ip ospf neighbor` |
| BGP | `show ip bgp summary` |
| Routing | `show ip route` |
| ACL | ACL counters and traffic tests |
| NAT | NAT translation output |
| DHCP | Client lease and scope information |
| DNS | Internal and external query output |
| NTP | Synchronization output |
| Monitoring | LibreNMS dashboard screenshots |
| Syslog | Centralized event evidence |
| Automation | Backup files and execution logs |
| Recovery | Successful recovery-test evidence |

### Evidence Directory Structure

```text
evidence/
├── 01-device-preparation/
├── 02-layer2/
├── 03-layer3-hsrp/
├── 04-routing/
├── 05-security-nat/
├── 06-services/
├── 07-monitoring/
├── 08-automation/
├── 09-validation/
└── 10-recovery/
```

### Screenshot Standards

- Use meaningful file names.
- Capture only the relevant output.
- Ensure the hostname is visible.
- Redact passwords, secrets, keys, and tokens.
- Avoid publishing unredacted running configurations.
- Include the related test or implementation ID where practical.
- Do not modify evidence in a way that changes its technical meaning.

### Example Naming

```text
IMP-PHASE4_CORE-R1_OSPF-NEIGHBOR-FULL.png
IMP-PHASE5_EDGE-R1_NAT-TRANSLATIONS.png
IMP-PHASE7_LIBRENMS_DEVICE-HEALTH.png
IMP-PHASE8_BACKUP-SUCCESS-REPORT.png
```

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Handover and Operational Readiness

### Handover Requirements

| Item | Requirement |
|:-----|:------------|
| Network Documentation | Updated and available |
| Device Inventory | Complete |
| IP Addressing Plan | Matches implementation |
| Configuration Files | Backed up and protected |
| Monitoring | All devices onboarded |
| Logging | Central collection operational |
| Backup Automation | Scheduled and verified |
| Test Results | Completed |
| Known Issues | Documented |
| Operations Runbook | Available |
| Troubleshooting Guide | Available |
| Recovery Guide | Available |

### Operational Readiness Review

| Review Area | Acceptance Requirement |
|:------------|:-----------------------|
| Availability | Critical services operational |
| Security | Required controls enabled |
| Monitoring | Devices and services visible |
| Backup | Successful backup available |
| Recoverability | Recovery process tested |
| Documentation | Current and accurate |
| Administration | Secure access confirmed |
| Incident Handling | Escalation procedures available |

### Known-Issue Register

Any unresolved non-critical issue must include:

- Issue ID
- Description
- Operational impact
- Workaround
- Risk level
- Responsible owner
- Planned remediation date
- Acceptance approval

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Checklist

### Pre-Implementation

| Task | Status |
|:-----|:------:|
| Review HLD and LLD | ☐ |
| Review interface mapping | ☐ |
| Review IP addressing | ☐ |
| Confirm device access | ☐ |
| Confirm server availability | ☐ |
| Capture baseline backups | ☐ |
| Prepare evidence directories | ☐ |
| Confirm rollback procedure | ☐ |
| Confirm maintenance authorization | ☐ |

### Network Implementation

| Task | Status |
|:-----|:------:|
| Configure device baselines | ☐ |
| Configure management addresses | ☐ |
| Configure SSH | ☐ |
| Create VLANs | ☐ |
| Configure access ports | ☐ |
| Configure trunks | ☐ |
| Configure Rapid PVST+ | ☐ |
| Configure Port Security | ☐ |
| Configure VLAN gateways | ☐ |
| Configure HSRP | ☐ |
| Configure OSPF | ☐ |
| Configure BGP | ☐ |
| Configure static routes | ☐ |
| Configure ACLs | ☐ |
| Configure NAT | ☐ |

### Services and Operations

| Task | Status |
|:-----|:------:|
| Deploy DHCP scopes | ☐ |
| Configure DHCP Relay | ☐ |
| Configure DNS | ☐ |
| Configure NTP | ☐ |
| Deploy LibreNMS | ☐ |
| Configure SNMP | ☐ |
| Configure Syslog | ☐ |
| Deploy backup automation | ☐ |
| Configure scheduled backups | ☐ |

### Validation and Handover

| Task | Status |
|:-----|:------:|
| Perform connectivity testing | ☐ |
| Perform HSRP failover testing | ☐ |
| Verify OSPF and BGP | ☐ |
| Verify ACL policy | ☐ |
| Verify NAT | ☐ |
| Verify DHCP and DNS | ☐ |
| Verify monitoring and Syslog | ☐ |
| Verify backup automation | ☐ |
| Perform recovery test | ☐ |
| Capture implementation evidence | ☐ |
| Update documentation | ☐ |
| Complete operational handover | ☐ |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Summary

This Implementation Guide defines the controlled deployment process for the Secure Enterprise Network Infrastructure.

The implementation is divided into dependency-based phases beginning with device preparation and progressing through Layer 2 switching, Layer 3 gateway services, HSRP, OSPF, BGP, ACLs, NAT, infrastructure services, monitoring, centralized logging, and backup automation.

Each phase includes validation and rollback requirements to reduce implementation risk and ensure that configuration problems are identified before dependent services are introduced.

The guide also defines change control, evidence collection, operational handover, and acceptance requirements to support a repeatable and auditable implementation process.

When combined with the High-Level Design, Low-Level Design, Test Plan, Troubleshooting Guide, Monitoring Guide, Backup and Recovery Guide, and Operations Runbook, this document provides a complete transition from approved architecture to operational enterprise infrastructure.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Glossary

| Acronym | Definition |
|:--------|:-----------|
| **AAA** | Authentication, Authorization, and Accounting |
| **ACL** | Access Control List |
| **AD DS** | Active Directory Domain Services |
| **BGP** | Border Gateway Protocol |
| **DHCP** | Dynamic Host Configuration Protocol |
| **DNS** | Domain Name System |
| **DTP** | Dynamic Trunking Protocol |
| **HSRP** | Hot Standby Router Protocol |
| **HLD** | High-Level Design |
| **LLD** | Low-Level Design |
| **NAT** | Network Address Translation |
| **NTP** | Network Time Protocol |
| **OSPF** | Open Shortest Path First |
| **PAT** | Port Address Translation |
| **RCA** | Root Cause Analysis |
| **SNMP** | Simple Network Management Protocol |
| **SSH** | Secure Shell |
| **STP** | Spanning Tree Protocol |
| **Syslog** | System Logging Protocol |
| **VLAN** | Virtual Local Area Network |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
