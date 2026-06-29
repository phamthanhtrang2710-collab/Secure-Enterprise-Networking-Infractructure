# Business Requirements Document

| Item | Details |
|------|---------|
| Project | Secure Enterprise Network Infrastructure |
| Company | NorthWind Technology Inc. |
| Document Version | 1.0 |
| Author | Thi Thanh Trang Pham |
| Date | July 2026 |
| Status | Draft |

<p align="center">
  <a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
  <a href="#business-background"><img src="https://img.shields.io/badge/BUSINESS%20BACKGROUND-5FAF00?style=for-the-badge"></a>
  <a href="#business-challenges"><img src="https://img.shields.io/badge/CHALLENGES-EA4B4B?style=for-the-badge"></a>
  <a href="#project-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-F2762E?style=for-the-badge"></a>
  <a href="#project-scope"><img src="https://img.shields.io/badge/SCOPE-8A0FAE?style=for-the-badge"></a>
  <a href="#stakeholders"><img src="https://img.shields.io/badge/STAKEHOLDERS-8A2BE2?style=for-the-badge"></a>
  <a href="#functional-requirements"><img src="https://img.shields.io/badge/FUNCTIONAL%20REQUIREMENTS-22B800?style=for-the-badge"></a>
</p>

<p align="center">
  <a href="#non-functional-requirements"><img src="https://img.shields.io/badge/NON--FUNCTIONAL-A6A6A6?style=for-the-badge"></a>
  <a href="#security-requirements"><img src="https://img.shields.io/badge/SECURITY-F26B2D?style=for-the-badge"></a>
</p>

## Overview
Verra Technology Inc. is a growing technology company with approximately 250 employees working in various departments. Their current network infrastructure lacks partitioning, redundancy, centralized management, and monitoring capabilities.

This project proposes the design and implementation of a secure, scalable, and highly available enterprise network to support current operations and future business growth.

## Business Background
<table>
<tr>
<td valign="top" width="50%">

### Current Environment

| Item | Current State |
|------|---------------|
| Employees | 250 |
| Office | Head Office & Branch Office |
| Internet | Single ISP |
| Server | Windows & Linux |
| Remote Workers | Yes |

</td>

<td valign="top" width="50%">

### Company Department

| Department | Description |
|------|--------|
| HR | Human Resources |
| Finance | Accounting & Payroll |
| IT | Infrastructure & Support |
| Sales | Customer Management |
| Marketing | Digital Marketing |
| Operations | Internal Operations |


</td>
</tr>
</table>

## Current Business Challenges

| ID | Current Problem | Business Impact |
|:--:|-----------------|-----------------|
| **BC-001** | Flat Network Architecture | Excessive broadcast traffic and reduced network performance |
| **BC-002** | No VLAN Segmentation | Increased security risks and unrestricted lateral movement |
| **BC-003** | No Redundant Gateway | Single Point of Failure (SPOF) affecting network availability |
| **BC-004** | No Centralized Monitoring | Slow fault detection and difficult troubleshooting |
| **BC-005** | No Configuration Backup | Long recovery time after device failure or misconfiguration 

## Project Objectives

| ID | Objective |
|:---:|:----------|
| **OBJ-001** | Design a scalable enterprise network architecture. |
| **OBJ-002** | Segment business departments using VLANs. |
| **OBJ-003** | Implement dynamic routing with OSPF and BGP. |
| **OBJ-004** | Provide gateway redundancy using HSRP. |
| **OBJ-005** | Secure inter-VLAN traffic with ACLs. |
| **OBJ-006** | Deploy centralized network services (DHCP, DNS, Syslog, SNMP). |
| **OBJ-007** | Monitor network health using LibreNMS or Zabbix. |
| **OBJ-008** | Automate configuration backup with Python or Ansible. |
| **OBJ-009** | Validate the infrastructure through comprehensive testing. |
| **OBJ-010** | Produce enterprise-grade technical documentation. |

## Project Scope

<table>
<tr>
<td width="60%" valign="top">

### In Scope

- [x] VLAN Segmentation  
- [x] OSPF Routing  
- [x] BGP Edge Connectivity  
- [x] HSRP Gateway Redundancy  
- [x] Access Control Lists (ACL)  
- [x] NAT  
- [x] DHCP  
- [x] DNS  
- [x] Secure SSH Management  
- [x] Network Monitoring  
- [x] Configuration Backup Automation  

</td>
<td width="60%" valign="top">

### Out of Scope

- [ ] IPv6 Deployment  
- [ ] MPLS  
- [ ] SD-WAN  
- [ ] Wireless Controller  
- [ ] Cloud Infrastructure  

</td>
</tr>
</table>

## Stakeholders

| Role | Responsibility |
|:------|:---------------|
| **Chief Executive Officer (CEO)** | Sponsors the project and approves the overall business direction. |
| **IT Manager** | Reviews and approves the network architecture and implementation plan. |
| **Network Engineer** | Designs, configures, tests, and documents the enterprise network infrastructure. |
| **System Administrator** | Deploys and maintains Windows/Linux servers and network services. |
| **Security Administrator** | Implements security policies, ACLs, SSH hardening, and access controls. |
| **Help Desk Team** | Performs first-level support and troubleshooting after deployment. |
| **Employees** | Use network services and business applications. |

## Functional Requirements

| ID | Requirement |
|:---:|:------------|
| **FR-001** | Automatically assign IP addresses using DHCP. |
| **FR-002** | Provide secure Internet connectivity for all departments. |
| **FR-003** | Implement dynamic routing using OSPF. |
| **FR-004** | Establish WAN connectivity through BGP. |
| **FR-005** | Provide gateway redundancy using HSRP. |
| **FR-006** | Control inter-VLAN traffic using Access Control Lists (ACLs). |
| **FR-007** | Provide internal DNS name resolution. |
| **FR-008** | Synchronize network devices using NTP. |
| **FR-009** | Collect logs through a centralized Syslog server. |
| **FR-010** | Monitor network devices using SNMP and LibreNMS. |
| **FR-011** | Enable secure remote management via SSH. |
| **FR-012** | Automatically back up device configurations using Python automation. |

## Non-Functional Requirements

| ID | Requirement |
|:---:|:------------|
| **NFR-001** | Achieve at least **99.9%** network availability. |
| **NFR-002** | Ensure low-latency communication across all VLANs. |
| **NFR-003** | Maintain modular and scalable network architecture. |
| **NFR-004** | Support future business expansion without major redesign. |
| **NFR-005** | Produce comprehensive technical documentation. |
| **NFR-006** | Perform automated daily configuration backups. |
| **NFR-007** | Provide centralized monitoring and alerting. |
| **NFR-008** | Ensure configuration consistency across all network devices. |
| **NFR-009** | Support rapid troubleshooting and disaster recovery. |
| **NFR-010** | Follow enterprise networking best practices throughout the implementation. |

## Security Requirements

| ID | Requirement |
|:---:|:------------|
| **SEC-001** | Disable Telnet and allow SSH only for remote administration. |
| **SEC-002** | Configure SSH Version 2 with encrypted management access. |
| **SEC-003** | Implement Access Control Lists (ACLs) to restrict unauthorized traffic. |
| **SEC-004** | Apply the Principle of Least Privilege to all user accounts. |
| **SEC-005** | Protect device passwords using strong encryption. |
| **SEC-006** | Secure routing protocols with authentication where applicable. |
| **SEC-007** | Disable unused switch ports and services. |
| **SEC-008** | Configure Port Security on access switches. |
| **SEC-009** | Restrict management access to authorized administrator VLANs only. |
| **SEC-010** | Log security events to a centralized Syslog server for auditing. |

## Assumptions

| ID | Assumption |
|:---:|:-----------|
| **ASM-001** | Internet connectivity from the ISP is available and stable throughout the project. |
| **ASM-002** | All network devices support the required enterprise features, including VLANs, OSPF, BGP, HSRP, ACLs, SSH, and SNMP. |
| **ASM-003** | Windows and Linux servers have sufficient CPU, memory, and storage resources to host the required services. |
| **ASM-004** | The organization owns the necessary public IP addresses for Internet connectivity. |
| **ASM-005** | All networking equipment is operational and running supported software versions. |
| **ASM-006** | Required software licenses and hardware resources are available before deployment. |
| **ASM-007** | The implementation is performed during an approved maintenance window to minimize business disruption. |
| **ASM-008** | Qualified IT personnel are available to deploy, validate, and maintain the infrastructure. |
| **ASM-009** | End users have appropriate permissions to access only the resources required for their roles. |
| **ASM-010** | Configuration backups and project documentation will be maintained after deployment. |

## Constraints

| ID | Constraint | Description |
|:---:|:-----------|:------------|
| **CON-001** | Budget | The project is implemented using existing lab equipment and software. |
| **CON-002** | Hardware | Cisco ISR routers and Catalyst switches are used throughout the project. |
| **CON-003** | Timeline | The implementation must be completed within four weeks. |
| **CON-004** | Environment | The solution is deployed in a simulated enterprise lab environment. |
| **CON-005** | Software | Only approved networking software and tools may be used. |
| **CON-006** | Resources | Limited engineering resources are available during implementation. |
| **CON-007** | Maintenance Window | Configuration changes must be performed during scheduled maintenance periods. |
| **CON-008** | Compatibility | The design must remain compatible with existing business services. |

## Risks

| ID | Risk | Mitigation |
|:---:|:-----|:-----------|
| **RSK-001** | Router failure | Implement HSRP gateway redundancy. |
| **RSK-002** | Switch failure | Maintain backup configurations and spare hardware. |
| **RSK-003** | Human configuration errors | Follow documented implementation procedures and peer reviews. |
| **RSK-004** | Configuration loss | Perform automated daily configuration backups. |
| **RSK-005** | Unauthorized access | Secure management access using SSH and ACLs. |
| **RSK-006** | Routing misconfiguration | Validate OSPF and BGP configurations before deployment. |
| **RSK-007** | Network downtime | Schedule implementation during maintenance windows. |
| **RSK-008** | Security vulnerabilities | Apply security hardening and regular configuration audits. |

## Success Criteria

| ID | Success Criteria | Status |
|:---:|:-----------------|:------:|
| **SC-001** | OSPF neighbor relationships are fully established. | ☐ |
| **SC-002** | BGP connectivity to the ISP is operational. | ☐ |
| **SC-003** | HSRP failover functions correctly. | ☐ |
| **SC-004** | Inter-VLAN communication follows ACL policies. | ☐ |
| **SC-005** | DHCP assigns IP addresses successfully. | ☐ |
| **SC-006** | DNS resolution is operational. | ☐ |
| **SC-007** | Internet access is available from all authorized VLANs. | ☐ |
| **SC-008** | Network monitoring dashboard is online. | ☐ |
| **SC-009** | Automated configuration backups execute successfully. | ☐ |
| **SC-010** | All verification tests pass successfully. | ☐ |

## Project Deliverables

| ID | Deliverable | Status |
|:---:|:------------|:------:|
| **DEL-001** | High-Level Design (HLD) Document | ☐ |
| **DEL-002** | Low-Level Design (LLD) Document | ☐ |
| **DEL-003** | Enterprise Network Diagram | ☐ |
| **DEL-004** | IP Addressing Plan | ☐ |
| **DEL-005** | VLAN Design Document | ☐ |
| **DEL-006** | Device Configuration Files | ☐ |
| **DEL-007** | Security Configuration Guide | ☐ |
| **DEL-008** | Network Verification Report | ☐ |
| **DEL-009** | Test Plan and Test Results | ☐ |
| **DEL-010** | Troubleshooting Guide | ☐ |
| **DEL-011** | Python Automation Scripts | ☐ |
| **DEL-012** | Project README Documentation | ☐ |
