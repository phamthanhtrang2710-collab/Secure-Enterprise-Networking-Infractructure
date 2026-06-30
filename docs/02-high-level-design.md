# High-Level Design Document

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#design-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-27AE60?style=for-the-badge"></a>
<a href="#enterprise-design-principles"><img src="https://img.shields.io/badge/PRINCIPLES-16A085?style=for-the-badge"></a>
<a href="#enterprise-network-architecture"><img src="https://img.shields.io/badge/ARCHITECTURE-8E44AD?style=for-the-badge"></a>
<a href="#site-design"><img src="https://img.shields.io/badge/SITE%20DESIGN-9B59B6?style=for-the-badge"></a>
<a href="#logical-topology"><img src="https://img.shields.io/badge/TOPOLOGY-2980B9?style=for-the-badge"></a>
<a href="#network-zones"><img src="https://img.shields.io/badge/NETWORK%20ZONES-3498DB?style=for-the-badge"></a>
<a href="#vlan-architecture"><img src="https://img.shields.io/badge/VLAN-2ECC71?style=for-the-badge"></a>
<a href="#routing-design"><img src="https://img.shields.io/badge/ROUTING-E67E22?style=for-the-badge"></a>

</p>

<p align="left">
<a href="#high-availability-design"><img src="https://img.shields.io/badge/HIGH%20AVAILABILITY-C0392B?style=for-the-badge"></a>
<a href="#security-design"><img src="https://img.shields.io/badge/SECURITY-D35400?style=for-the-badge"></a>
<a href="#inter-vlan-access-policy"><img src="https://img.shields.io/badge/ACL%20POLICY-E74C3C?style=for-the-badge"></a>
<a href="#server--infrastructure-services-design"><img src="https://img.shields.io/badge/SERVICES-1ABC9C?style=for-the-badge"></a>
<a href="#monitoring--management-design"><img src="https://img.shields.io/badge/MONITORING-34495E?style=for-the-badge"></a>
<a href="#automation-design"><img src="https://img.shields.io/badge/AUTOMATION-7F8C8D?style=for-the-badge"></a>
<a href="#design-assumptions"><img src="https://img.shields.io/badge/ASSUMPTIONS-95A5A6?style=for-the-badge"></a>
<a href="#design-constraints"><img src="https://img.shields.io/badge/CONSTRAINTS-8E44AD?style=for-the-badge"></a>
  
</p>

<p align="left">

<a href="#summary"><img src="https://img.shields.io/badge/SUMMARY-2C3E50?style=for-the-badge"></a>

</p>

## Overview

This High-Level Design (HLD) document defines the overall enterprise network architecture for Verra Technology Inc. The purpose of this document is to describe the proposed network design at an architectural level, including site layout, routing strategy, security zones, high availability, centralized services, monitoring, and automation.

This document does not include device-level commands or detailed interface configuration. Those details will be provided in the Low-Level Design (LLD) and implementation documents.

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Design Objectives

| ID | Design Objective | Priority |
|:---|:-----------------|:--------:|
| **HLD-OBJ-001** | Design a scalable enterprise network architecture for a growing organization. | High |
| **HLD-OBJ-002** | Provide logical separation between departments using VLANs. | High |
| **HLD-OBJ-003** | Support dynamic routing between internal network segments. | High |
| **HLD-OBJ-004** | Provide redundant default gateway availability using HSRP. | High |
| **HLD-OBJ-005** | Secure traffic between departments using access control policies (ACLs). | High |
| **HLD-OBJ-006** | Provide centralized network services such as DHCP, DNS, Syslog, NTP, and SNMP. | Medium |
| **HLD-OBJ-007** | Enable centralized network monitoring and operational visibility. | Medium |
| **HLD-OBJ-008** | Support configuration backup and basic network automation using Python. | Medium |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Enterprise Design Principles

The network architecture is designed according to the following engineering principles.

| Principle | Description |
|-----------|-------------|
| **Scalability** | Supports future business growth with minimal architectural changes. |
| **High Availability** | Redundant core infrastructure minimizes service interruption. |
| **Security** | Implements network segmentation, ACLs, firewall protection, and secure management access. |
| **Performance** | Optimizes traffic flow using hierarchical routing and switching. |
| **Manageability** | Centralized services simplify monitoring, troubleshooting, and administration. |
| **Modularity** | Individual network layers can be expanded independently without affecting the overall design. |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Enterprise Network Architecture

The proposed network adopts a hierarchical enterprise campus architecture that separates network functions into dedicated layers. This approach improves scalability, simplifies management, enhances security, and provides high availability for business-critical services.

| ID | Network Layer | Primary Role | Key Technologies | Design Considerations |
|:--:|---------------|--------------|------------------|-----------------------|
| **LYR-001** | Internet Edge | Provides enterprise connectivity to the Internet and external WAN services. | BGP | Supports scalable external routing and ISP connectivity. |
| **LYR-002** | Security Edge | Protects the enterprise perimeter by enforcing security policies and filtering traffic. | Firewall, NAT, ACL | All inbound and outbound traffic is inspected before entering the internal network. |
| **LYR-003** | Core Layer | Serves as the Layer 3 backbone for inter-VLAN routing, gateway redundancy, and internal route exchange. | OSPF, HSRP, Layer 3 Routing | Designed for maximum availability with redundant core routers. |
| **LYR-004** | Distribution Layer | Aggregates access switches and provides VLAN trunk aggregation between the Core and Access layers. | VLAN Trunking, STP, Port Security | Centralizes Layer 2 aggregation while Layer 3 routing and gateway redundancy are performed at the Core Layer. |
| **LYR-005** | Access Layer | Connects end-user devices including PCs, printers, IP phones, and wireless access points. | VLAN, STP, Port Security | Provides secure endpoint connectivity while limiting broadcast domains. |
| **LYR-006** | Server Farm | Hosts centralized enterprise services and internal business applications. | DHCP, DNS, Syslog, NTP, SNMP | Centralizes critical infrastructure services for easier administration. |
| **LYR-007** | Management Network | Provides secure administrative access for monitoring and managing infrastructure devices. | SSH, SNMP, Syslog | Isolated management network prevents unauthorized administrative access. |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Site Design

Verra Technology Inc. operates across multiple simulated locations connected through an enterprise WAN architecture. Each site has a clearly defined operational role to support business continuity and future expansion.

| Site | Role | Primary Functions | Connectivity | Design Considerations |
|:-----|------|-------------------|--------------|-----------------------|
| **Head Office** | Corporate Headquarters | Hosts the enterprise core network, centralized servers, IT operations, and the majority of business users. | Internal LAN + ISP | Acts as the primary data processing and management center. |
| **Branch Office** | Regional Office | Supports branch employees and local business operations while maintaining secure connectivity to headquarters. | WAN / ISP | Enables remote business operations with centralized resource access. |
| **ISP Network** | External Service Provider | Simulates Internet access and WAN connectivity between enterprise locations. | BGP | Provides external routing and Internet connectivity for the enterprise. |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Logical Topology

<img width="1536" height="1024" alt="logical-topology" src="https://github.com/user-attachments/assets/5c012bff-5abd-42ef-8c0d-532854caa126" />

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Network Zones

The enterprise network is segmented into multiple logical security zones to improve access control, reduce attack surfaces, and simplify network management.

| Zone ID | Network Zone | Purpose | Typical Components | Security Controls |
|:-------:|--------------|---------|--------------------|-------------------|
| **ZONE-001** | User Zone | Provides network access for corporate users across all business departments. | PCs, Laptops, IP Phones, Printers | VLAN Segmentation, ACL, Port Security |
| **ZONE-002** | Server Zone | Hosts centralized enterprise infrastructure and internal business services. | DHCP, DNS, Syslog, NTP, SNMP, File Servers | Server VLAN, ACL, Restricted Access |
| **ZONE-003** | Management Zone | Dedicated network used by administrators to manage infrastructure devices securely. | Network Management Workstations, SSH, Monitoring Server | SSH, SNMPv3, AAA, Management ACL |
| **ZONE-004** | Internet Edge Zone | Provides controlled connectivity between the enterprise network and external Internet services. | Edge Router, Firewall, NAT | Firewall Policies, NAT, Ingress/Egress Filtering |
| **ZONE-005** | Branch Zone | Represents the remote office connected securely to the Head Office. | Branch Router, Access Switches, End Devices | WAN Routing, ACL, VPN (Future Expansion) |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## VLAN Architecture

| VLAN | Name | Function | Subnet | HSRP Virtual Gateway | Criticality | Access Control |
|:----:|------|----------|---------|:---------------:|:-----------:|----------------|
| **10** | HR | Human Resources | `10.10.10.0/24` | `10.10.10.1` | Medium | ACL Protected |
| **20** | IT | Infrastructure Administration | `10.10.20.0/24` | `10.10.20.1` | High | Full Administrative Access |
| **30** | Finance | Financial Systems | `10.10.30.0/24` | `10.10.30.1` | Critical | Strict ACL |
| **40** | Sales | Business Operations | `10.10.40.0/24` | `10.10.40.1` | Medium | Limited Access |
| **50** | Server | Enterprise Services | `10.10.50.0/24` | `10.10.50.1` | Critical | Authorized VLANs Only |
| **99** | Management | Device Administration | `10.10.99.0/24` | `10.10.99.1` | Critical | IT Administrators Only |
| **999** | Native / Black Hole | Security VLAN | N/A | N/A | High | No User Traffic |


> **Design Notes**
>
> - Each department is assigned a dedicated VLAN to reduce broadcast traffic.
> - Inter-VLAN routing and default gateway redundancy are performed at the Core Layer using CORE-R1 and CORE-R2 with HSRP.
> - Access between VLANs is controlled through Access Control Lists (ACLs).
> - HSRP provides redundant default gateways for user VLANs.
> - VLAN 99 is reserved for infrastructure management.
> - VLAN 999 is configured as the unused native VLAN to improve switch security.

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Routing Design

The enterprise routing architecture combines dynamic and static routing protocols to provide scalable internal communication, resilient default gateway operation, and secure Internet connectivity.

| Routing Protocol | Scope | Configuration | Primary Purpose | Design Considerations |
|------------------|-------|---------------|-----------------|-----------------------|
| **OSPF** | Internal (IGP) | Area 0 | Provides dynamic routing between enterprise VLANs and Layer 3 devices. | Enables fast convergence, scalability, and simplified route management. |
| **BGP** | External (EGP) | Enterprise AS ↔ ISP AS | Simulates WAN and Internet edge connectivity with the ISP. | Separates enterprise routing from external Internet routing policies. |
| **Static Default Route** | Edge Router | `0.0.0.0/0` | Directs unknown traffic toward the ISP gateway. | Reduces routing table complexity and provides deterministic Internet access. |
| **HSRP** | Gateway Redundancy | Virtual Gateway | Provides highly available default gateways for all VLANs. | Ensures uninterrupted gateway availability during router failure. |

#### Routing Protocol Overview

| Protocol | Type | Administrative Distance | Purpose |
|:---------|:----:|:-----------------------:|---------|
| **OSPF** | IGP | 110 | Internal dynamic routing |
| **BGP** | EGP | 20 (eBGP) | ISP / WAN routing |
| **Static Route** | Static | 1 | Default Internet path |
| **HSRP** | FHRP | N/A | Gateway redundancy |

### OSPF Design

OSPF (Open Shortest Path First) is selected as the enterprise Interior Gateway Protocol (IGP) to provide dynamic, scalable, and resilient routing across the internal network infrastructure.

| Design Element | Configuration | Design Rationale |
|----------------|---------------|------------------|
| **Protocol** | OSPFv2 | Industry-standard link-state routing protocol for IPv4 enterprise networks. |
| **OSPF Area** | Area 0 (Backbone) | Simplifies routing design and provides a scalable backbone architecture. |
| **Scope** | Core Routers, Edge Router, Internal Layer 3 Links | Advertises all internal enterprise networks dynamically. |
| **Route Advertisement** | All VLAN Subnets | Eliminates the need for manually configured static routes. |
| **Default Route** | Redistributed from the Edge Router | Allows internal devices to reach external networks through a single exit point. |
| **Convergence** | Fast | Automatically recalculates the optimal path after topology changes. |
| **Scalability** | High | Supports future network expansion with minimal configuration changes. |
| **Redundancy Support** | Integrated with HSRP | Maintains uninterrupted Layer 3 connectivity during gateway failover. |

> **OSPF Design Principles**
>
> - A single backbone area (Area 0) is used to simplify enterprise routing.
> - Dynamic route advertisement reduces administrative overhead.
> - Equal-cost paths can be utilized automatically when available.
> - OSPF enables rapid convergence after link or device failures.
> - The protocol supports future enterprise growth without redesigning the routing architecture.

#### OSPF Configuration Summary

| Parameter | Value |
|-----------|-------|
| Version | OSPFv2 |
| Area | Area 0 |
| Network Type | Broadcast Ethernet |
| Hello Timer | Default |
| Dead Timer | Default |
| Authentication | Planned (Future Enhancement) |
| Route Summarization | Not Required |
| ECMP | Enabled |

### BGP Design

Border Gateway Protocol (BGP) is deployed at the Internet Edge to simulate enterprise connectivity with an Internet Service Provider (ISP). It enables external route exchange while maintaining a clear separation between internal and external routing domains.

| Design Element | Configuration | Design Rationale |
|----------------|---------------|------------------|
| **Protocol** | eBGP | Industry-standard Exterior Gateway Protocol (EGP) for inter-domain routing. |
| **Enterprise AS** | AS 65001 | Represents Verra Technology Inc.'s autonomous system. |
| **ISP AS** | AS 65000 | Simulates the upstream Internet Service Provider. |
| **Peer Relationship** | Edge Router ↔ ISP Router | Establishes external route exchange between the enterprise and ISP. |
| **Route Advertisement** | Enterprise Networks | Advertises internal enterprise prefixes to the ISP. |
| **Default Route** | Received from ISP | Provides Internet connectivity through the upstream provider. |
| **Scope** | Internet Edge Only | Limits BGP deployment to the enterprise perimeter. |
| **Scalability** | High | Supports future WAN expansion and multi-ISP connectivity. |

> **BGP Design Principles**
>
> - eBGP is deployed exclusively at the Internet Edge.
> - Internal routing remains isolated from external routing policies.
> - Enterprise routes are advertised to the ISP through BGP.
> - Internet-bound traffic follows the default route received from the ISP.
> - The design supports future dual-ISP redundancy if required.

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## High Availability Design

| Design Element | Technology | Purpose | Business Benefit |
|----------------|------------|---------|------------------|
| **Default Gateway Redundancy** | HSRP | Provides a virtual default gateway for all user VLANs. | Minimizes user disruption during router failures. |
| **Core Redundancy** | Dual Core Routers | Eliminates single points of failure within the campus backbone. | Improves network uptime and resilience. |
| **Dynamic Routing** | OSPF | Automatically recalculates routes after topology changes. | Fast convergence and reduced manual intervention. |
| **Internet Connectivity** | BGP | Maintains controlled communication with the ISP. | Supports scalable WAN and Internet connectivity. |
| **Configuration Backup** | Python Automation | Automatically backs up device configurations. | Accelerates recovery from hardware failure or misconfiguration. |
| **Centralized Monitoring** | Syslog & SNMP | Continuously monitors infrastructure health and network events. | Enables proactive fault detection and troubleshooting. |

### HSRP Design

Hot Standby Router Protocol (HSRP) is implemented on the core routers to provide highly available default gateway services for all enterprise VLANs. End devices use a virtual IP address as their default gateway, ensuring uninterrupted connectivity during router failures.

| Design Element | Configuration | Design Rationale |
|----------------|---------------|------------------|
| **Protocol** | HSRP Version 2 | Provides first-hop redundancy for enterprise IPv4 networks. |
| **Deployment** | Core Router 1 & Core Router 2 | Eliminates a single point of failure at the default gateway. |
| **Active Router** | Core Router 1 | Handles all gateway traffic during normal operation. |
| **Standby Router** | Core Router 2 | Automatically assumes the gateway role if the active router becomes unavailable. |
| **Virtual Gateway** | One Virtual IP per VLAN | End devices always use the virtual IP as their default gateway. |
| **Preemption** | Enabled | Restores the preferred active router after recovery. |
| **Interface Tracking** | WAN / Uplink Interface | Initiates failover if the primary uplink becomes unavailable. |
| **Availability Goal** | Continuous Gateway Service | Minimizes downtime and ensures uninterrupted network access. |

> **HSRP Design Principles**
>
> - A virtual IP address is configured as the default gateway for every user VLAN.
> - Core Router 1 operates as the preferred active gateway under normal conditions.
> - Core Router 2 remains in standby mode and automatically takes over upon failure.
> - Preemption ensures the preferred router resumes the active role after recovery.
> - Interface tracking enables intelligent failover based on uplink status.
> - HSRP operates together with OSPF to maintain resilient Layer 3 connectivity.

#### HSRP Configuration Summary

| Parameter | Value |
|-----------|-------|
| Protocol | HSRP Version 2 |
| Active Device | Core Router 1 |
| Standby Device | Core Router 2 |
| Virtual Gateway | `10.10.x.1` |
| Preemption | Enabled |
| Interface Tracking | Enabled |
| Authentication | Not Configured (Lab Environment) |
| Load Sharing | Not Implemented |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Security Design

The enterprise security architecture follows a **defense-in-depth** strategy and the **Principle of Least Privilege (PoLP)**. Security controls are implemented at multiple layers to protect network infrastructure, enterprise services, and business data.

| Security Control | Technology | Purpose | Design Rationale |
|------------------|------------|---------|------------------|
| **Network Segmentation** | VLANs | Separates departments into isolated broadcast domains. | Reduces broadcast traffic and limits lateral movement. |
| **Inter-VLAN Filtering** | ACLs | Controls traffic between VLANs. | Allows only authorized communication based on business requirements. |
| **Perimeter Protection** | Firewall & NAT | Protects the enterprise network from external threats. | Filters inbound/outbound traffic and hides internal IP addressing. |
| **Secure Management** | SSH | Provides encrypted remote administration. | Prevents credential exposure during device management. |
| **Legacy Protocol Control** | Telnet Disabled | Eliminates insecure remote access. | Ensures all administrative access uses encrypted protocols. |
| **Access Layer Security** | Port Security | Restricts unauthorized devices on switch ports. | Protects against rogue devices and MAC spoofing. |
| **Infrastructure Monitoring** | Syslog & SNMP | Centralizes logging and device monitoring. | Improves operational visibility and incident response. |
| **Management Network** | Management VLAN | Isolates administrative traffic from user networks. | Prevents unauthorized access to network devices. |

> **Security Design Principles**
>
> - Apply the Principle of Least Privilege (PoLP).
> - Isolate departments using dedicated VLANs.
> - Control inter-VLAN communication through ACLs.
> - Encrypt all management traffic using SSH.
> - Disable insecure legacy protocols such as Telnet.
> - Centralize logging and monitoring for auditing and troubleshooting.

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Inter-VLAN Access Policy

The following table summarizes the high-level access control policy between enterprise VLANs. Detailed ACL configurations are provided in the Low-Level Design (LLD) document.

| Source VLAN | Destination | Access Policy | Business Justification |
|--------------|-------------|---------------|------------------------|
| **IT VLAN** | All Enterprise VLANs | Allow | Required for infrastructure administration and technical support. |
| **HR VLAN** | Finance VLAN | Deny | Protects confidential payroll and financial information. |
| **Finance VLAN** | Server VLAN | Allow | Required for financial applications and database services. |
| **Sales VLAN** | Server VLAN | Allow (Business Services Only) | Permits access only to approved enterprise applications. |
| **Management VLAN** | Network Devices | Allow (SSH/SNMP) | Supports secure device administration and monitoring. |
| **Guest / Untrusted Network** | Internal VLANs | Deny | Prevents unauthorized access to enterprise resources. |
| **All User VLANs** | Internet | Allow | Internet access controlled through the enterprise firewall. |

> **ACL Policy Principles**
>
> - Deny-by-default unless business requirements explicitly allow access.
> - IT administrators receive elevated access based on operational responsibilities.
> - Finance resources are accessible only by authorized systems and users.
> - Management traffic is isolated within the dedicated Management VLAN.
> - Guest networks are completely isolated from internal enterprise resources.

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Server & Infrastructure Services Design

The enterprise infrastructure provides centralized network services that support core business operations, network management, security, and service availability.

| Service | Platform | Function | Design Rationale |
|----------|----------|----------|------------------|
| **DHCP** | Windows Server / Router | Automatically assigns IP addresses to client devices. | Centralized IP management reduces administrative effort and configuration errors. |
| **DNS** | Windows Server | Resolves hostnames to IP addresses for internal resources. | Simplifies resource access and supports enterprise applications. |
| **NTP** | Windows Server / Network Devices | Synchronizes time across all infrastructure devices. | Ensures accurate timestamps for authentication, logging, and troubleshooting. |
| **Syslog** | Linux Server | Collects and stores centralized device logs. | Improves auditing, troubleshooting, and incident investigation. |
| **SNMP** | Network Devices | Monitors device status and performance metrics. | Enables proactive monitoring and network health visibility. |
| **Web Services** | Linux Server | Hosts internal web applications and administrative portals. | Provides centralized access to enterprise services. |
| **Network Monitoring** | LibreNMS / Zabbix | Monitors infrastructure availability, performance, and alerts. | Supports proactive fault detection and operational visibility. |

### Service Availability Requirements

| Service | Criticality | Availability Requirement |
|----------|:-----------:|--------------------------|
| DHCP | High | Required for automatic client addressing |
| DNS | Critical | Required for enterprise name resolution |
| NTP | Medium | Required for log consistency and authentication |
| Syslog | Medium | Required for centralized event logging |
| SNMP | Medium | Required for infrastructure monitoring |
| Web Services | Medium | Required for internal applications |
| Network Monitoring | High | Required for proactive fault detection |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Monitoring & Management Design

The enterprise network incorporates centralized monitoring and management capabilities to provide operational visibility, proactive fault detection, and efficient infrastructure administration.

| Monitoring Component | Technology | Monitoring Scope | Business Benefit |
|----------------------|------------|------------------|------------------|
| **Routers** | SNMP, ICMP | Device availability, routing status, interface health | Ensures reliable WAN and internal routing operations. |
| **Switches** | SNMP | Port utilization, VLAN status, device uptime | Detects switching issues and improves network availability. |
| **Servers** | SNMP, Agent-Based Monitoring | CPU, memory, storage, and service availability | Maintains infrastructure health and business service continuity. |
| **Network Interfaces** | SNMP | Bandwidth utilization, errors, packet loss, link status | Identifies congestion and potential network bottlenecks. |
| **Syslog** | Syslog Server | Security events, configuration changes, system logs | Provides centralized auditing and troubleshooting capabilities. |
| **Network Monitoring Platform** | LibreNMS / Zabbix | Performance monitoring, alerting, dashboards, reporting | Enables proactive monitoring and faster incident response. |

### Monitoring Metrics

| Metric | Threshold | Action |
|---------|:---------:|--------|
| Device Availability | 100% | Alert if unreachable |
| CPU Utilization | > 80% | Generate warning |
| Memory Utilization | > 85% | Generate warning |
| Interface Utilization | > 80% | Monitor for congestion |
| Packet Loss | > 1% | Investigate network path |
| Syslog Events | Critical | Immediate administrator notification |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Automation Design

Network automation is introduced to reduce manual administrative effort, improve operational consistency, and support repeatable infrastructure management tasks.

| Automation Function | Technology | Purpose | Business Benefit |
|---------------------|------------|---------|------------------|
| **Configuration Backup** | Python, Netmiko | Automatically backs up router and switch configurations. | Accelerates disaster recovery and configuration rollback. |
| **Device Inventory** | Python | Collects and maintains infrastructure inventory information. | Improves asset visibility and documentation accuracy. |
| **Health Check** | Python | Verifies device reachability and operational status. | Enables proactive fault detection. |
| **Configuration Validation** | Python | Verifies device configurations against baseline standards. | Reduces configuration drift and human error. |
| **Operational Reporting** | Python | Generates periodic infrastructure reports. | Improves operational visibility and management reporting. |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Design Assumptions

| ID | Assumption | Design Impact |
|:---|------------|---------------|
| **HLD-ASM-001** | The environment is implemented for educational and portfolio purposes. | Enterprise best practices are demonstrated without production constraints. |
| **HLD-ASM-002** | Cisco IOS devices support VLANs, OSPF, BGP, HSRP, ACLs, SSH, and SNMP. | Required enterprise features are available throughout the design. |
| **HLD-ASM-003** | Windows Server and Linux platforms are available for infrastructure services. | Centralized enterprise services can be deployed. |
| **HLD-ASM-004** | Network monitoring and automation are introduced after core connectivity is completed. | Deployment follows a phased implementation approach. |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Design Constraints

| ID | Constraint | Design Impact |
|:---|------------|---------------|
| **HLD-CON-001** | Simulated Lab Environment | Some production-grade features are simplified. |
| **HLD-CON-002** | Limited Hardware Resources | High-end redundancy features may not be fully implemented. |
| **HLD-CON-003** | Project Timeline | Deployment is completed within the planned implementation schedule. |
| **HLD-CON-004** | Software Availability | Final implementation depends on Packet Tracer, GNS3, or EVE-NG capabilities. |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Summary

This High-Level Design (HLD) defines the architectural foundation of this **Secure Enterprise Network Infrastructure** project.

The proposed solution adopts a hierarchical enterprise campus architecture incorporating network segmentation, dynamic routing, gateway redundancy, centralized infrastructure services, layered security controls, monitoring, and network automation. The design emphasizes scalability, high availability, security, and operational simplicity while following widely accepted enterprise networking best practices.

The subsequent **Low-Level Design (LLD)** document will translate this architecture into implementation-ready technical specifications, including IP addressing, VLAN assignments, interface mapping, routing configurations, security policies, device configurations, and validation procedures.

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## Glossary

| Acronym | Definition |
|:--------:|------------|
| **AAA** | Authentication, Authorization, and Accounting |
| **ACL** | Access Control List |
| **AS** | Autonomous System |
| **BGP** | Border Gateway Protocol |
| **DHCP** | Dynamic Host Configuration Protocol |
| **DNS** | Domain Name System |
| **EGP** | Exterior Gateway Protocol |
| **FHRP** | First Hop Redundancy Protocol |
| **HLD** | High-Level Design |
| **HSRP** | Hot Standby Router Protocol |
| **ICMP** | Internet Control Message Protocol |
| **IGP** | Interior Gateway Protocol |
| **LLD** | Low-Level Design |
| **NAT** | Network Address Translation |
| **NTP** | Network Time Protocol |
| **OSPF** | Open Shortest Path First |
| **PoLP** | Principle of Least Privilege |
| **SNMP** | Simple Network Management Protocol |
| **SSH** | Secure Shell |
| **Syslog** | System Logging Protocol |
| **VLAN** | Virtual Local Area Network |
| **WAN** | Wide Area Network |

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>

## References

- Cisco Enterprise Campus Design Guide
- Cisco Validated Designs
- RFC 2328 - OSPF Version 2
- RFC 4271 - Border Gateway Protocol 4
- RFC 2281 – Hot Standby Router Protocol (HSRP)
- RFC 1918 – Address Allocation for Private Internets
- Microsoft Windows Server Security Baseline
- NIST SP 800-41 - Guidelines on Firewalls and Firewall Policy

<p align="right">
<a href="#contents">⬆ Back to Contents</a>
</p>
