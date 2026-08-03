# Network Diagrams

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#diagram-guidelines"><img src="https://img.shields.io/badge/GUIDELINES-27AE60?style=for-the-badge"></a>
<a href="#logical-network-topology"><img src="https://img.shields.io/badge/LOGICAL-8E44AD?style=for-the-badge"></a>
<a href="#physical-network-topology"><img src="https://img.shields.io/badge/PHYSICAL-16A085?style=for-the-badge"></a>
<a href="#ip-addressing-diagram"><img src="https://img.shields.io/badge/IP%20ADDRESSING-2980B9?style=for-the-badge"></a>
<a href="#vlan-topology"><img src="https://img.shields.io/badge/VLAN-3498DB?style=for-the-badge"></a>
<a href="#routing-topology"><img src="https://img.shields.io/badge/ROUTING-E67E22?style=for-the-badge"></a>
<a href="#security-architecture"><img src="https://img.shields.io/badge/SECURITY-D35400?style=for-the-badge"></a>
<a href="#management-network"><img src="https://img.shields.io/badge/MANAGEMENT-C0392B?style=for-the-badge"></a>
<a href="#monitoring-architecture"><img src="https://img.shields.io/badge/MONITORING-E74C3C?style=for-the-badge"></a>
<a href="#traffic-flow"><img src="https://img.shields.io/badge/TRAFFIC%20FLOW-1ABC9C?style=for-the-badge"></a>
<a href="#high-availability"><img src="https://img.shields.io/badge/HIGH%20AVAILABILITY-2ECC71?style=for-the-badge"></a>
<a href="#diagram-index"><img src="https://img.shields.io/badge/INDEX-34495E?style=for-the-badge"></a>
<a href="#summary"><img src="https://img.shields.io/badge/SUMMARY-2C3E50?style=for-the-badge"></a>

</p>

## Overview

This document consolidates all architectural diagrams developed for the Secure Enterprise Network Infrastructure project.

The diagrams provide visual representations of the enterprise network architecture, physical connectivity, logical topology, VLAN segmentation, routing design, security controls, management infrastructure, monitoring services, traffic flow, and high availability mechanisms.

These diagrams complement the High-Level Design (HLD), Low-Level Design (LLD), IP Addressing Plan, VLAN Design, Routing Design, Security Design, Monitoring Guide, Implementation Guide, and Operations Runbook.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Diagram Guidelines

The following standards are applied consistently throughout every network diagram included in this document.

| Standard | Description |
|:----------|:------------|
| **Cisco Icons** | Cisco enterprise icons are used where appropriate. |
| **Naming Convention** | Device names follow the approved enterprise naming standard. |
| **Layered Architecture** | Core, Distribution, Access, Server, and Management layers are clearly identified. |
| **Consistent Colors** | Standardized colors represent the same network functions across all diagrams. |
| **Traffic Direction** | Logical traffic flow is illustrated from top to bottom whenever practical. |
| **Labeling** | Interfaces, VLANs, IP subnets, and routing protocols are clearly labeled. |
| **Version Control** | Every diagram is updated whenever the production design changes. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Logical Network Topology

The logical topology illustrates the overall enterprise architecture, including Internet connectivity, routing hierarchy, VLAN segmentation, server infrastructure, and user access.

<p align="center">

<img width="1536" height="1024" alt="logical-topolofy" src="https://github.com/user-attachments/assets/3137f950-547d-4559-a1b3-590b31a1a6b2" />


</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Physical Network Topology

The physical topology illustrates the physical interconnection between routers, switches, servers, and enterprise access devices.

<p align="center">

<img width="1578" height="997" alt="physical-network-topology-adv" src="https://github.com/user-attachments/assets/a2fb0e68-ec9b-456f-8318-b349d2229a67" />


</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## IP Addressing Diagram

<p align="center">

<img width="1536" height="1024" alt="ip-addressing-diagram" src="https://github.com/user-attachments/assets/965639e7-70b1-49bd-b45c-79cd9d7b23a3" />


</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## VLAN Topology

The VLAN topology demonstrates logical segmentation between enterprise departments, management services, and server infrastructure.

<p align="center">

![VLAN Topology](images/network/vlan-topology.png)

</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Routing Topology

The routing topology illustrates OSPF, BGP, HSRP, default routing, and Internet connectivity throughout the enterprise.

<p align="center">

![Routing Topology](images/network/routing-topology.png)

</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Security Architecture

The security architecture demonstrates the layered defense model protecting enterprise resources through VLAN segmentation, ACLs, NAT, secure management, and perimeter controls.

<p align="center">

![Security Architecture](images/network/security-architecture.png)

</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Management Network

The management network diagram illustrates the isolated Management VLAN used for secure administrative access, monitoring, centralized logging, and infrastructure management.

<p align="center">

![Management Network](images/network/management-network.png)

</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Architecture

The monitoring architecture illustrates centralized infrastructure monitoring using SNMP, Syslog, and enterprise monitoring platforms.

<p align="center">

![Monitoring Architecture](images/network/monitoring-architecture.png)

</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Traffic Flow

This diagram illustrates the normal packet flow between enterprise users, internal services, and external Internet resources.

<p align="center">

![Traffic Flow](images/network/traffic-flow.png)

</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## High Availability

The high availability diagram demonstrates gateway redundancy, routing resilience, and failover mechanisms implemented throughout the enterprise infrastructure.

<p align="center">

![High Availability](images/network/high-availability.png)

</p>

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Diagram Index

| Diagram ID | Diagram | Related Document |
|:-----------|:--------|:-----------------|
| **ND-001** | Logical Network Topology | High-Level Design |
| **ND-002** | Physical Network Topology | Low-Level Design |
| **ND-003** | IP Addressing Diagram | IP Addressing Plan |
| **ND-004** | VLAN Topology | VLAN Design |
| **ND-005** | Routing Topology | Routing Design |
| **ND-006** | Security Architecture | Security Design |
| **ND-007** | Management Network | Management Guide |
| **ND-008** | Monitoring Architecture | Monitoring Guide |
| **ND-009** | Traffic Flow | High-Level Design |
| **ND-010** | High Availability | High-Level Design / Low-Level Design |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Summary

This document serves as the centralized repository for all network architecture diagrams developed for the Secure Enterprise Network Infrastructure project.

Each diagram provides a visual reference supporting enterprise planning, implementation, validation, troubleshooting, operations, monitoring, and future infrastructure expansion. Together, these diagrams ensure consistency across all technical documentation while supporting the approved High-Level Design (HLD) and Low-Level Design (LLD).

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
