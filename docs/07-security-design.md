# Security Design Document

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#security-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-27AE60?style=for-the-badge"></a>
<a href="#security-architecture"><img src="https://img.shields.io/badge/ARCHITECTURE-8E44AD?style=for-the-badge"></a>
<a href="#security-principles"><img src="https://img.shields.io/badge/PRINCIPLES-16A085?style=for-the-badge"></a>
<a href="#security-zones"><img src="https://img.shields.io/badge/SECURITY%20ZONES-2980B9?style=for-the-badge"></a>
<a href="#network-segmentation"><img src="https://img.shields.io/badge/SEGMENTATION-3498DB?style=for-the-badge"></a>
<a href="#management-plane-security"><img src="https://img.shields.io/badge/MANAGEMENT-E67E22?style=for-the-badge"></a>
<a href="#layer-2-security"><img src="https://img.shields.io/badge/LAYER%202-D35400?style=for-the-badge"></a>
<a href="#layer-3-security"><img src="https://img.shields.io/badge/LAYER%203-C0392B?style=for-the-badge"></a>
<a href="#access-control-strategy"><img src="https://img.shields.io/badge/ACL-9B59B6?style=for-the-badge"></a>
<a href="#firewall-design"><img src="https://img.shields.io/badge/FIREWALL-E74C3C?style=for-the-badge"></a>
<a href="#nat-security"><img src="https://img.shields.io/badge/NAT-1ABC9C?style=for-the-badge"></a>
<a href="#device-hardening"><img src="https://img.shields.io/badge/HARDENING-2ECC71?style=for-the-badge"></a>
<a href="#aaa-strategy"><img src="https://img.shields.io/badge/AAA-34495E?style=for-the-badge"></a>
<a href="#management-access"><img src="https://img.shields.io/badge/SSH-7F8C8D?style=for-the-badge"></a>
<a href="#infrastructure-services-security"><img src="https://img.shields.io/badge/SERVICES-2C3E50?style=for-the-badge"></a>
<a href="#logging-and-auditing"><img src="https://img.shields.io/badge/LOGGING-95A5A6?style=for-the-badge"></a>
<a href="#monitoring-integration"><img src="https://img.shields.io/badge/MONITORING-8E44AD?style=for-the-badge"></a>
<a href="#security-verification"><img src="https://img.shields.io/badge/VERIFICATION-27AE60?style=for-the-badge"></a>
<a href="#security-best-practices"><img src="https://img.shields.io/badge/BEST%20PRACTICES-0B8FD3?style=for-the-badge"></a>
<a href="#summary"><img src="https://img.shields.io/badge/SUMMARY-2C3E50?style=for-the-badge"></a>

</p>

## Overview

This document defines the enterprise security architecture for the Secure Enterprise Network Infrastructure project.

The security design applies a layered defense strategy that protects enterprise assets through network segmentation, secure routing, access control, perimeter protection, device hardening, centralized management, monitoring, and auditing.

Rather than relying on a single security mechanism, multiple complementary controls are implemented across the network infrastructure to reduce attack surfaces, limit lateral movement, and improve operational resilience.

This document focuses on security architecture and design principles. Detailed implementation commands and device-specific configurations are documented separately in the Low-Level Design (LLD) and implementation guides.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


# Security Objectives

| ID | Objective |
|:---:|:----------|
| **SEC-OBJ-001** | Protect enterprise infrastructure using a defense-in-depth security architecture. |
| **SEC-OBJ-002** | Minimize lateral movement through network segmentation and VLAN isolation. |
| **SEC-OBJ-003** | Enforce the Principle of Least Privilege (PoLP) for all administrative and user access. |
| **SEC-OBJ-004** | Secure management traffic using encrypted administrative protocols. |
| **SEC-OBJ-005** | Protect the enterprise perimeter through firewall policies and Network Address Translation (NAT). |
| **SEC-OBJ-006** | Centralize logging, monitoring, and auditing to improve operational visibility. |
| **SEC-OBJ-007** | Reduce common Layer 2 and Layer 3 attack vectors using enterprise security best practices. |
| **SEC-OBJ-008** | Support future integration of enterprise authentication and advanced security services without redesigning the security architecture. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Security Architecture

| Security Layer | Primary Controls | Purpose |
|:---------------|:-----------------|:--------|
| **Perimeter Security** | Firewall, NAT, Edge ACLs | Protects the enterprise from external networks. |
| **Network Segmentation** | VLANs, Inter-VLAN ACLs | Isolates business departments and sensitive resources. |
| **Routing Security** | OSPF, BGP, HSRP | Protects routing infrastructure and ensures resilient connectivity. |
| **Management Security** | SSH, Management VLAN, AAA | Secures administrative access to infrastructure devices. |
| **Infrastructure Services** | DNS, DHCP, NTP, Syslog, SNMP | Provides secure centralized enterprise services. |
| **Monitoring & Auditing** | Syslog, SNMP, Monitoring Platform | Detects abnormal events and supports incident response. |

### Enterprise Security Model

| Layer | Protection Focus |
|:------|:-----------------|
| Users | Identity and authorized access |
| Access Layer | Endpoint security and Layer 2 protection |
| Distribution Layer | VLAN aggregation and traffic isolation |
| Core Layer | Secure Layer 3 routing and gateway redundancy |
| Edge Layer | Internet boundary protection |
| Management Layer | Secure administration and monitoring |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Security Principles

| Principle | Implementation |
|:----------|:---------------|
| **Defense in Depth** | Multiple independent security layers protect enterprise assets. |
| **Least Privilege (PoLP)** | Users and devices receive only the minimum required access. |
| **Network Segmentation** | Departments are isolated using dedicated VLANs. |
| **Default Deny** | Traffic is denied unless explicitly authorized. |
| **Secure Management** | Administrative access uses encrypted management protocols. |
| **Centralized Monitoring** | Security events are collected through centralized logging and monitoring platforms. |
| **High Availability** | Security controls operate together with redundant network infrastructure. |
| **Scalability** | The architecture supports future security enhancements without major redesign. |

### Security Design Philosophy

| Category | Design Decision |
|:---------|:----------------|
| Security Strategy | Defense in Depth |
| Access Model | Principle of Least Privilege |
| Network Isolation | Department-based VLAN Segmentation |
| Administrative Access | Secure Management Only |
| Monitoring | Centralized Logging and Monitoring |
| Future Expansion | Enterprise Security Ready |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Security Zones

The enterprise network is divided into multiple security zones based on business function, trust level, and access requirements. This zoning strategy limits lateral movement, simplifies policy enforcement, and improves overall security.

Traffic between zones is explicitly controlled through Access Control Lists (ACLs), firewall policies, and routing policies according to the Principle of Least Privilege (PoLP).

### Security Zone Matrix

| Zone ID | Security Zone | Trust Level | Typical Assets | Security Controls |
|:--------:|:--------------|:-----------:|:---------------|:------------------|
| **ZONE-001** | User Zone | Medium | HR, IT, Finance, Sales user devices | VLAN Segmentation, ACLs, Port Security |
| **ZONE-002** | Server Zone | High | Windows Server, Linux Server | ACLs, Restricted Access, Monitoring |
| **ZONE-003** | Management Zone | Critical | Routers, Switches, Management Workstations | SSH, AAA, SNMPv3, Management ACL |
| **ZONE-004** | Internet Edge Zone | Low Trust | EDGE-R1, ISP Connectivity | Firewall Policies, NAT, Edge ACLs |
| **ZONE-005** | Infrastructure Services Zone | High | DNS, DHCP, NTP, Syslog, SNMP | Service-specific ACLs, Centralized Monitoring |

### Trust Model

| Zone | Allowed Communication |
|:-----|:----------------------|
| User Zone | Business applications and approved infrastructure services |
| Server Zone | Authorized enterprise services only |
| Management Zone | Administrative traffic only |
| Internet Edge | Internet-bound traffic and established return sessions |
| Infrastructure Services | Enterprise infrastructure protocols only |

### Zone Communication Principles

| Principle | Implementation |
|:----------|:---------------|
| Default Trust | No implicit trust between zones |
| East-West Traffic | Controlled by Inter-VLAN ACLs |
| North-South Traffic | Protected by Firewall and NAT |
| Administrative Access | Restricted to the Management Zone |
| Sensitive Resources | Accessible only by authorized VLANs |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Network Segmentation

Network segmentation provides logical isolation between departments and infrastructure services by assigning each business function to a dedicated VLAN.

Segmentation reduces broadcast domains, improves traffic management, limits attack propagation, and simplifies security policy enforcement.

Inter-VLAN communication is routed by the Core Layer and controlled through Access Control Lists based on business requirements.

### Enterprise VLAN Segmentation

| VLAN | Department | Security Level | Primary Purpose |
|:----:|:-----------|:--------------:|:----------------|
| **10** | HR | Medium | Human Resources |
| **20** | IT | High | Infrastructure Administration |
| **30** | Finance | Critical | Financial Systems |
| **40** | Sales | Medium | Business Operations |
| **50** | Server | Critical | Enterprise Services |
| **99** | Management | Critical | Infrastructure Management |
| **999** | Native / Black Hole | High | Security Isolation |

### Segmentation Objectives

| Objective | Implementation |
|:----------|:---------------|
| Broadcast Isolation | Dedicated VLAN per department |
| Lateral Movement Reduction | ACL-controlled Inter-VLAN communication |
| Administrative Isolation | Dedicated Management VLAN |
| Server Protection | Separate Server VLAN |
| Native VLAN Security | Unused VLAN 999 configured as Native VLAN |

### Inter-VLAN Security Model

| Source | Destination | Default Policy |
|:-------|:------------|:---------------|
| User VLAN | User VLAN | Restricted |
| User VLAN | Server VLAN | Business Services Only |
| User VLAN | Management VLAN | Denied |
| IT VLAN | Enterprise VLANs | Authorized Administrative Access |
| Management VLAN | Infrastructure Devices | Full Administrative Access |
| Internet | Internal VLANs | Denied unless explicitly permitted |

### Segmentation Benefits

| Benefit | Description |
|:--------|:------------|
| Improved Security | Restricts unauthorized communication between departments |
| Better Performance | Reduces unnecessary broadcast traffic |
| Simplified Management | Clear logical separation of business functions |
| Regulatory Alignment | Supports protection of sensitive business data |
| Scalability | Additional VLANs can be introduced without redesigning the architecture |

### Segmentation Summary

| Category | Design Decision |
|:---------|:----------------|
| Segmentation Method | Department-based VLANs |
| Routing Location | Core Layer |
| Policy Enforcement | Inter-VLAN ACLs |
| Management Isolation | Dedicated VLAN 99 |
| Server Isolation | Dedicated VLAN 50 |
| Native VLAN | VLAN 999 |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Management Plane Security

The management plane protects administrative access to network infrastructure devices. Only authorized administrators are permitted to manage routers, switches, and servers through encrypted management protocols.

A dedicated Management VLAN isolates administrative traffic from user and production networks, significantly reducing the attack surface.

### Management Plane Controls

| Control | Implementation | Purpose |
|:--------|:---------------|:--------|
| Dedicated Management Network | VLAN 99 | Isolates administrative traffic |
| Secure Remote Access | SSH Version 2 | Encrypted device management |
| Legacy Protocols | Telnet Disabled | Eliminates insecure management access |
| Authentication | Local AAA | Authenticates administrative users |
| Monitoring | SNMPv3 | Secure infrastructure monitoring |
| Logging | Syslog | Records management activities |

### Administrative Access Policy

| Source | Destination | Access |
|:-------|:------------|:------:|
| Management VLAN | Infrastructure Devices | Allow |
| IT VLAN | Infrastructure Devices | Allow |
| User VLANs | Infrastructure Devices | Deny |
| Internet | Infrastructure Devices | Deny |

### Management Security Summary

| Category | Design Decision |
|:---------|:----------------|
| Management Network | VLAN 99 |
| Remote Access | SSH Version 2 |
| Authentication | AAA |
| Monitoring | SNMPv3 |
| Logging | Syslog |
| Internet Management | Disabled |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Layer 2 Security

Layer 2 security protects the switching infrastructure against unauthorized access, switching loops, MAC flooding, VLAN hopping, and rogue devices.

Security controls are applied primarily at the Access Layer while maintaining operational simplicity.

### Layer 2 Security Controls

| Feature | Status | Purpose |
|:--------|:------:|:--------|
| Port Security | Enabled | Restricts unauthorized devices |
| BPDU Guard | Enabled | Prevents rogue switches |
| PortFast | Enabled | Accelerates endpoint initialization |
| Rapid PVST+ | Enabled | Fast Layer 2 convergence |
| Native VLAN 999 | Enabled | Prevents VLAN hopping |
| DTP | Disabled | Prevents unauthorized trunk negotiation |
| Unused Ports | Shutdown | Reduces attack surface |
| Storm Control | Enabled | Limits broadcast and multicast storms |

### Protected Threats

| Threat | Protection |
|:-------|:-----------|
| Rogue Switch | BPDU Guard |
| MAC Flooding | Port Security |
| VLAN Hopping | Native VLAN 999 + Disabled DTP |
| Broadcast Storm | Storm Control |
| Unauthorized Endpoints | Port Security |

### Layer 2 Security Summary

| Category | Design Decision |
|:---------|:----------------|
| Switching Security | Hardened |
| Port Security | Enabled |
| STP Protection | BPDU Guard |
| Native VLAN | VLAN 999 |
| Dynamic Trunking | Disabled |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Layer 3 Security

Layer 3 security protects routed communications between enterprise VLANs and external networks.

Security controls focus on route integrity, controlled traffic forwarding, and resilient gateway operation.

### Layer 3 Security Controls

| Control | Purpose |
|:--------|:--------|
| OSPF | Dynamic routing with controlled advertisements |
| HSRP | Gateway redundancy |
| ACLs | Inter-VLAN traffic filtering |
| Static Default Route | Controlled Internet forwarding |
| NAT | Internal address protection |
| BGP | Secure Internet edge routing |

### Future Security Enhancements

| Technology | Status |
|:-----------|:------|
| OSPF Authentication | Future Enhancement |
| BGP MD5 Authentication | Future Enhancement |
| TTL Security | Future Enhancement |
| Route Filtering | Future Enhancement |
| Prefix Lists | Future Enhancement |

### Layer 3 Security Summary

| Category | Design Decision |
|:---------|:----------------|
| Internal Routing | OSPF |
| Gateway Protection | HSRP |
| Route Filtering | ACLs |
| Internet Routing | BGP |
| Address Protection | NAT |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Access Control Strategy

Access Control Lists (ACLs) enforce business-driven communication policies between enterprise VLANs while preventing unauthorized lateral movement.

The design follows a default-deny security model, permitting only explicitly authorized traffic.

### Access Control Strategy

| Policy | Implementation |
|:-------|:---------------|
| Default Policy | Deny |
| Business Services | Explicit Allow |
| Management Access | IT & Management VLAN Only |
| Finance Protection | Restricted |
| Internet Access | Controlled via Edge |

### ACL Design Principles

| Principle | Description |
|:----------|:------------|
| Least Privilege | Minimum required permissions |
| Explicit Permit | Required business communication only |
| Implicit Deny | All other traffic denied |
| Management Isolation | Administrative traffic separated |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Firewall Design

The enterprise perimeter is protected through a firewall policy that inspects and controls traffic entering and leaving the enterprise network.

In this lab environment, firewall functionality is simulated using perimeter ACLs and NAT policies on EDGE-R1.

### Firewall Zones

| Zone | Trust |
|:-----|:-----:|
| Inside | High |
| Management | High |
| Outside | Low |

### Firewall Policy

| Source | Destination | Action |
|:-------|:------------|:------:|
| Inside | Outside | Allow |
| Outside | Inside | Deny |
| Established Sessions | Inside | Allow |
| Internet | Management | Deny |

### Firewall Summary

| Category | Design Decision |
|:---------|:----------------|
| Security Model | Default Deny |
| Inbound Traffic | Restricted |
| Outbound Traffic | Controlled |
| Logging | Enabled |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## NAT Security

Network Address Translation (PAT) protects internal addressing by translating private enterprise addresses into a single public address before Internet access.

### NAT Security Benefits

| Benefit | Description |
|:--------|:------------|
| Address Hiding | Internal IP addresses remain private |
| Internet Connectivity | Shared public address |
| Reduced Attack Surface | Internal hosts not directly reachable |
| Simple Administration | Centralized translation |

### NAT Summary

| Category | Design Decision |
|:---------|:----------------|
| NAT Type | PAT |
| Translation Device | EDGE-R1 |
| Public Address | Internet Interface |
| Static NAT | Not Implemented |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Device Hardening

Device hardening reduces unnecessary services and minimizes opportunities for unauthorized access.

### Hardening Standards

| Item | Status |
|:-----|:------:|
| SSH Version 2 | Enabled |
| Telnet | Disabled |
| HTTP Server | Disabled |
| HTTPS Server | Disabled (Lab) |
| Unused Interfaces | Shutdown |
| Secure Passwords | Enabled |
| Login Banner | Configured |
| Service Password Encryption | Enabled |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## AAA Strategy

Authentication, Authorization, and Accounting (AAA) controls administrative access to enterprise infrastructure.

### AAA Design

| Function | Implementation |
|:---------|:---------------|
| Authentication | Local AAA |
| Authorization | Privilege Levels |
| Accounting | Future Enhancement |
| TACACS+ | Future Enhancement |
| RADIUS | Future Enhancement |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Management Access

Administrative access is restricted to trusted administrators through secure encrypted protocols.

### Access Matrix

| Source | Destination | Protocol |
|:-------|:------------|:---------|
| IT VLAN | Network Devices | SSH |
| Management VLAN | Network Devices | SSH, SNMP |
| Internet | Infrastructure | Denied |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Infrastructure Services Security

### Protected Services

| Service | Security Control |
|:--------|:-----------------|
| DHCP | Server VLAN |
| DNS | ACL Protected |
| Syslog | Centralized Logging |
| SNMP | SNMPv3 |
| NTP | Internal Synchronization |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Logging and Auditing

### Logging Sources

| Source | Destination |
|:-------|:------------|
| Routers | Syslog Server |
| Switches | Syslog Server |
| Servers | Syslog Server |

### Audit Events

- Configuration changes
- Administrative logins
- Interface status changes
- Routing protocol events
- Security violations

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Monitoring Integration

### Monitoring Components

| Component | Technology |
|:----------|:-----------|
| Device Health | SNMP |
| Performance | LibreNMS / Zabbix |
| Availability | ICMP |
| Event Logging | Syslog |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Security Verification

### Verification Checklist

| ID | Verification Item | Expected Result |
|:--:|:------------------|:----------------|
| SEC-VER-001 | SSH Access | Successful |
| SEC-VER-002 | Telnet | Disabled |
| SEC-VER-003 | Port Security | Operational |
| SEC-VER-004 | BPDU Guard | Enabled |
| SEC-VER-005 | ACL Enforcement | Successful |
| SEC-VER-006 | NAT Translation | Operational |
| SEC-VER-007 | Syslog | Logs Received |
| SEC-VER-008 | SNMP | Monitoring Operational |
| SEC-VER-009 | Management VLAN | Isolated |
| SEC-VER-010 | Internet Access | Successful |

> Verification Commands
> - show access-lists
> - show port-security
> - show spanning-tree summary
> - show interfaces trunk
> - show ip ssh
> - show users
> - show login
> - show ip nat translations
> - show ip nat statistics
> - show logging
> - show snmp
> - ping
> - traceroute

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Security Best Practices

| Best Practice | Implementation |
|:--------------|:---------------|
| Defense in Depth | Multiple security layers |
| Least Privilege | ACL-based access |
| Secure Management | SSH only |
| Segmentation | VLAN isolation |
| Continuous Monitoring | Syslog & SNMP |
| Configuration Backup | Python Automation |
| Periodic Auditing | Verification Procedures |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Summary

This security architecture implements a layered defense strategy by combining network segmentation, secure routing, access control, device hardening, perimeter protection, centralized management, and continuous monitoring.

The design follows Cisco enterprise campus security best practices by separating user, server, management, and Internet edge functions into dedicated security domains. Together with VLAN segmentation, ACL enforcement, HSRP, OSPF, NAT, and centralized monitoring, the architecture provides a scalable and resilient security foundation suitable for enterprise environments while supporting future enhancements such as TACACS+, RADIUS, OSPF authentication, and advanced firewall services.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Glossary

| Acronym | Definition |
|:--------:|:-----------|
| AAA | Authentication, Authorization and Accounting |
| ACL | Access Control List |
| BPDU | Bridge Protocol Data Unit |
| DTP | Dynamic Trunking Protocol |
| NAT | Network Address Translation |
| PAT | Port Address Translation |
| PoLP | Principle of Least Privilege |
| SNMP | Simple Network Management Protocol |
| SSH | Secure Shell |
| STP | Spanning Tree Protocol |
| Syslog | System Logging Protocol |
| TACACS+ | Terminal Access Controller Access-Control System Plus |
| VLAN | Virtual LAN |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
