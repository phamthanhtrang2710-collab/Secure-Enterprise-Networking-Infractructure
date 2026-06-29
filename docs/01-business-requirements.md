# Business Requirements Document

| Item | Details |
|------|---------|
| Project | Secure Enterprise Network Infrastructure |
| Company | Verra Technology Inc. |
| Document Version | 1.0 |
| Author | Thi Thanh Trang Pham |
| Date | July 2026 |
| Status | Draft |

## Document Revision History

| Version | Date | Author | Description |
|----------|------|--------|-------------|
| 1.0 | July 2026 | Thi Thanh Trang Pham | Initial release |

## Document Approval

| Role | Name | Status |
|------|------|--------|
| Project Sponsor | CEO | Pending |
| Network Architect | Thi Thanh Trang Pham | Approved |
| Technical Reviewer | Pending | Pending |


## Contents
<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#business-background"><img src="https://img.shields.io/badge/BUSINESS%20BACKGROUND-65B300?style=for-the-badge"></a>
<a href="#business-challenges"><img src="https://img.shields.io/badge/CHALLENGES-E74C3C?style=for-the-badge"></a>
<a href="#project-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-F39C12?style=for-the-badge"></a>
<a href="#project-scope"><img src="https://img.shields.io/badge/SCOPE-8E44AD?style=for-the-badge"></a>
<a href="#stakeholders"><img src="https://img.shields.io/badge/STAKEHOLDERS-8E44AD?style=for-the-badge"></a>
<a href="#functional-requirements"><img src="https://img.shields.io/badge/FUNCTIONAL%20REQUIREMENTS-2ECC71?style=for-the-badge"></a>

</p>

<p align="left">

<a href="#non-functional-requirements"><img src="https://img.shields.io/badge/NON--FUNCTIONAL%20REQUIREMENTS-95A5A6?style=for-the-badge"></a>
<a href="#security-baseline"><img src="https://img.shields.io/badge/SECURITY%20BASELINE-E67E22?style=for-the-badge"></a>
<a href="#assumptions"><img src="https://img.shields.io/badge/ASSUMPTIONS-16A085?style=for-the-badge"></a>
<a href="#constraints"><img src="https://img.shields.io/badge/CONSTRAINTS-C0392B?style=for-the-badge"></a>
<a href="#risks"><img src="https://img.shields.io/badge/RISKS-D35400?style=for-the-badge"></a>
<a href="#success-criteria"><img src="https://img.shields.io/badge/SUCCESS%20CRITERIA-27AE60?style=for-the-badge"></a>
</p>

## Overview
This Business Requirements Document (BRD) defines the business objectives, technical requirements, project scope, security expectations, constraints, risks, and success criteria for the design and implementation of a Secure Enterprise Network Infrastructure for Verra Technology Inc.

The proposed solution aims to provide a secure, scalable, highly available, and easily manageable enterprise network that supports current business operations while meeting the organization's future growth needs.

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

### Business Drivers

| Driver | Description |
|---------|-------------|
| Business Growth | Support future expansion without major network redesign. |
| Security | Protect sensitive business data through network segmentation and access control. |
| High Availability | Minimize service interruption and eliminate single points of failure. |
| Operational Efficiency | Simplify network administration through centralized management and automation. |
| Monitoring | Improve visibility into network health and accelerate incident response. |

## Current Business Challenges

| ID | Current Problem | Business Impact | Priority |
|----|-----------------|-----------------|----------|
| BC-001 | Flat Network Architecture | Excessive broadcast traffic | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| BC-002 | No VLAN Segmentation | Increased security risks | ![](https://img.shields.io/badge/High-red?style=flat-square) |
| BC-003 | No Redundant Gateway | Single Point of Failure | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| BC-004 | No Centralized Monitoring | Slow fault detection | ![](https://img.shields.io/badge/Medium-yellow?style=flat-square&logoColor=black) |
| BC-005 | No Configuration Backup | Long recovery time | ![](https://img.shields.io/badge/High-red?style=flat-square) |

## Project Objectives

| Business Objectives | Technical Objectives |
| :--- | :--- |
| • Improve business continuity. | • Implement VLAN segmentation. |
| • Reduce operational risks. | • Deploy OSPF dynamic routing. |
| • Enhance network security. | • Configure BGP edge connectivity. |
| • Support future expansion. | • Implement HSRP gateway redundancy. |
| • Increase infrastructure availability. | • Deploy centralized monitoring. |
| | • Automate configuration backups. |

## Project Scope

<table>
<tr>
<td width="55%" valign="top">

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

<td width="45%" valign="top">

### Out of Scope

- [ ] IPv6 Deployment
- [ ] MPLS
- [ ] SD-WAN
- [ ] Wireless Controller
- [ ] Cloud Infrastructure

</td>
</tr>
</table>

### Future Scope

| Feature | Status |
|---------|--------|
| IPv6 Deployment | 🔜 Planned |
| MPLS Integration | 🔜 Planned |
| SD-WAN | 🔜 Planned |
| AWS / Azure Connectivity | 🔜 Planned |
| Wireless Controller | 🔜 Planned |

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

## Functional Requirements

| ID | Requirement | Priority |
|:---:|:------------|:--------:|
| **FR-001** | Automatically assign IP addresses using DHCP. | ![](https://img.shields.io/badge/High-red?style=flat-square) |
| **FR-002** | Provide secure Internet connectivity for all departments. | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **FR-003** | Implement dynamic routing using OSPF. | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **FR-004** | Establish WAN connectivity through BGP. | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **FR-005** | Provide gateway redundancy using HSRP. | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **FR-006** | Control inter-VLAN traffic using Access Control Lists (ACLs). | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **FR-007** | Provide internal DNS name resolution. | ![](https://img.shields.io/badge/High-red?style=flat-square) |
| **FR-008** | Synchronize network devices using NTP. | ![](https://img.shields.io/badge/Medium-yellow?style=flat-square&logoColor=black) |
| **FR-009** | Collect logs through a centralized Syslog server. | ![](https://img.shields.io/badge/Medium-yellow?style=flat-square&logoColor=black) |
| **FR-010** | Monitor network devices using SNMP and LibreNMS. | ![](https://img.shields.io/badge/Medium-yellow?style=flat-square&logoColor=black) |
| **FR-011** | Enable secure remote management via SSH. | ![](https://img.shields.io/badge/High-red?style=flat-square) |
| **FR-012** | Automatically back up device configurations using Python automation. | ![](https://img.shields.io/badge/High-red?style=flat-square) |

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

## Security Baseline

Identify the minimum security controls necessary to protect enterprise network infrastructure through secure authentication, access control, device enhancement, management security, and centralized logging.

| ID | Category | Security Control | Priority |
|:---:|:---------|:-----------------|:--------:|
| **Authentication** | | | |
| **SEC-001** | Authentication | Disable Telnet and allow SSH only for remote administration. | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **SEC-002** | Authentication | Configure SSH Version 2 with encrypted management access. | ![](https://img.shields.io/badge/High-red?style=flat-square) |
| **Access Control** | | | |
| **SEC-003** | Access Control | Implement Access Control Lists (ACLs) to restrict unauthorized traffic. | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **SEC-004** | Access Control | Apply the Principle of Least Privilege to all user accounts. | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **Device Hardening** | | | |
| **SEC-005** | Device Hardening | Protect device passwords using strong encryption. | ![](https://img.shields.io/badge/High-red?style=flat-square) |
| **SEC-006** | Device Hardening | Secure routing protocols with authentication where applicable. | ![](https://img.shields.io/badge/High-red?style=flat-square) |
| **SEC-007** | Device Hardening | Disable unused switch ports and services. | ![](https://img.shields.io/badge/Medium-yellow?style=flat-square&logoColor=black) |
| **SEC-008** | Device Hardening | Configure Port Security on access switches. | ![](https://img.shields.io/badge/High-red?style=flat-square) |
| **Management Security** | | | |
| **SEC-009** | Management Security | Restrict management access to authorized administrator VLANs only. | ![](https://img.shields.io/badge/Critical-darkred?style=flat-square) |
| **Logging & Auditing** | | | |
| **SEC-010** | Logging & Auditing | Log security events to a centralized Syslog server for auditing. | ![](https://img.shields.io/badge/Medium-yellow?style=flat-square&logoColor=black) |

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

### Project Dependencies

- Cisco IOS images
- Windows Server 2022
- Ubuntu Server 24.04 LTS
- LibreNMS / Zabbix
- Python 3.x

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

### Project Acceptance Criteria

The project will be considered successfully completed when:

- [x] All functional requirements are implemented.
- [x] All verification test cases pass successfully.
- [x] No critical issues remain unresolved.
- [x] Network monitoring is fully operational.
- [x] Technical documentation is complete and up to date.

## Project Deliverables

| ID | Deliverable | Owner | Status |
|:---:|:------------|:----------------------|:------:|
| **DEL-001** | High-Level Design (HLD) Document | Solution Architect | ☐ |
| **DEL-002** | Low-Level Design (LLD) Document | Network Engineer | ☐ |
| **DEL-003** | Enterprise Network Diagram | Network Engineer | ☐ |
| **DEL-004** | IP Addressing Plan | Network Engineer | ☐ |
| **DEL-005** | VLAN Design Document | Network Engineer | ☐ |
| **DEL-006** | Device Configuration Files | Network Engineer | ☐ |
| **DEL-007** | Security Configuration Guide | Security Engineer | ☐ |
| **DEL-008** | Network Verification Report | Network Engineer | ☐ |
| **DEL-009** | Test Plan and Test Results | QA Engineer | ☐ |
| **DEL-010** | Troubleshooting Guide | Network Engineer | ☐ |
| **DEL-011** | Python Automation Scripts | Automation Engineer | ☐ |
| **DEL-012** | Project README Documentation | Technical Writer | ☐ |

## Glossary

| Acronym | Definition |
|----------|------------|
| ACL | Access Control List |
| BGP | Border Gateway Protocol |
| DHCP | Dynamic Host Configuration Protocol |
| DNS | Domain Name System |
| HSRP | Hot Standby Router Protocol |
| NAT | Network Address Translation |
| NTP | Network Time Protocol |
| OSPF | Open Shortest Path First |
| SNMP | Simple Network Management Protocol |
| SSH | Secure Shell |
| VLAN | Virtual Local Area Network |

## References

- Cisco Enterprise Campus Design Guide
- Cisco Validated Designs (CVD)
- RFC 2328 - OSPF Version 2
- RFC 4271 - Border Gateway Protocol 4 (BGP-4)
- NIST SP 800-41 - Guidelines on Firewalls and Firewall Policy
- Microsoft Windows Server Security Baseline
