# Routing Design Document

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#routing-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-27AE60?style=for-the-badge"></a>
<a href="#routing-architecture"><img src="https://img.shields.io/badge/ARCHITECTURE-8E44AD?style=for-the-badge"></a>
<a href="#routing-responsibilities"><img src="https://img.shields.io/badge/RESPONSIBILITIES-16A085?style=for-the-badge"></a>
<a href="#routed-link-design"><img src="https://img.shields.io/badge/ROUTED%20LINKS-2980B9?style=for-the-badge"></a>
<a href="#ospf-design"><img src="https://img.shields.io/badge/OSPF-E67E22?style=for-the-badge"></a>
<a href="#ospf-interface-plan"><img src="https://img.shields.io/badge/OSPF%20INTERFACES-D35400?style=for-the-badge"></a>
<a href="#ospf-route-advertisement"><img src="https://img.shields.io/badge/OSPF%20ROUTES-C0392B?style=for-the-badge"></a>
<a href="#bgp-design"><img src="https://img.shields.io/badge/BGP-9B59B6?style=for-the-badge"></a>
<a href="#static-routing"><img src="https://img.shields.io/badge/STATIC%20ROUTES-3498DB?style=for-the-badge"></a>
<a href="#default-route-design"><img src="https://img.shields.io/badge/DEFAULT%20ROUTE-2ECC71?style=for-the-badge"></a>
<a href="#routing-redundancy"><img src="https://img.shields.io/badge/REDUNDANCY-E74C3C?style=for-the-badge"></a>
<a href="#route-failover"><img src="https://img.shields.io/badge/FAILOVER-1ABC9C?style=for-the-badge"></a>
<a href="#routing-security"><img src="https://img.shields.io/badge/SECURITY-34495E?style=for-the-badge"></a>
<a href="#routing-verification"><img src="https://img.shields.io/badge/VERIFICATION-7F8C8D?style=for-the-badge"></a>
</p>

## Overview

This document describes the enterprise routing topology, dynamic and static routing mechanisms, routed links, gateway redundancy, route advertisement, routing security, and verification procedures required to support reliable internal and external network connectivity.

Also, this complements the High-Level Design (HLD) and Low-Level Design (LLD) by focusing exclusively on Layer 3 routing behavior and routing protocol implementation.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Routing Objectives

| ID | Objective |
|:---:|:----------|
| **RT-OBJ-001** | Provide scalable dynamic routing throughout the enterprise network. |
| **RT-OBJ-002** | Support resilient gateway redundancy using HSRP. |
| **RT-OBJ-003** | Enable fast route convergence after network failures. |
| **RT-OBJ-004** | Provide controlled Internet connectivity through the Edge Router. |
| **RT-OBJ-005** | Support future enterprise expansion without redesigning the routing architecture. |
| **RT-OBJ-006** | Maintain clear separation between internal and external routing domains. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Routing Architecture

The enterprise routing architecture follows a hierarchical design that separates internal routing from Internet edge routing.

| Layer | Routing Function | Protocol |
|:------|:-----------------|:---------|
| **Access Layer** | End-user VLAN connectivity | Layer 2 Switching |
| **Distribution Layer** | VLAN trunk aggregation only | Layer 2 Trunking |
| **Core Layer** | Inter-VLAN routing and gateway redundancy | OSPF + HSRP |
| **Edge Layer** | Internet connectivity, NAT, and ISP routing | BGP + Static Default Route |
| **ISP** | External routing | eBGP |

### Routing Principles

| Principle | Description |
|:----------|:------------|
| **Single IGP** | OSPF is the only Interior Gateway Protocol. |
| **Single Backbone Area** | All routers participate in Area 0. |
| **Edge Isolation** | BGP is deployed only at the Internet Edge. |
| **Gateway Redundancy** | HSRP provides resilient default gateways. |
| **Fast Convergence** | Dynamic routing minimizes downtime after failures. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Routing Responsibilities

| Device | Primary Responsibility |
|:-------|:-----------------------|
| **EDGE-R1** | Internet edge routing, BGP peering, NAT, default route injection |
| **CORE-R1** | Inter-VLAN routing, HSRP Active, OSPF |
| **CORE-R2** | Inter-VLAN routing, HSRP Standby, OSPF |
| **DIST-SW1** | VLAN aggregation and Layer 2 forwarding |
| **Access Switches** | User access and VLAN connectivity |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Routed Link Design

| Link | Network | Prefix | Purpose |
|:-----|:--------|:------:|:--------|
| **EDGE-R1 ↔ ISP** | `203.0.113.0` | /30 | Internet connectivity |
| **EDGE-R1 ↔ CORE-R1** | `10.10.250.0` | /30 | Primary routed link |
| **EDGE-R1 ↔ CORE-R2** | `10.10.250.4` | /30 | Secondary routed link |

### Routed Link Standards

| Item | Standard |
|:-----|:---------|
| Addressing | /30 Point-to-Point |
| Duplex | Full |
| Speed | Auto Negotiation |
| Routing | OSPF Enabled |
| STP | Not Applicable |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## OSPF Design

Open Shortest Path First (OSPF) is implemented as the enterprise Interior Gateway Protocol (IGP) to provide dynamic routing between internal Layer 3 devices. A single backbone area (Area 0) is used throughout the network to simplify routing, accelerate convergence, and support future expansion without redesigning the routing architecture.


### OSPF Design Parameters

| Parameter | Value |
|:----------|:------|
| **Routing Protocol** | OSPFv2 |
| **Process ID** | 1 |
| **Backbone Area** | Area 0 |
| **Network Type** | Broadcast Ethernet |
| **Hello Timer** | Default |
| **Dead Timer** | Default |
| **Authentication** | Not Implemented (Lab Environment) |
| **ECMP** | Enabled |
| **Route Summarization** | Not Required |


### OSPF Router Identification

| Device | Router ID |
|:-------|:----------|
| **EDGE-R1** | 1.1.1.1 |
| **CORE-R1** | 2.2.2.2 |
| **CORE-R2** | 3.3.3.3 |


### OSPF Interface Plan

### OSPF Interface Plan

| Device | Interface | Network | Area | Passive |
|:-------|:----------|:--------|:----:|:-------:|
| EDGE-R1 | G0/1 | 10.10.250.0/30 | 0 | No |
| EDGE-R1 | G0/2 | 10.10.250.4/30 | 0 | No |
| CORE-R1 | G0/1 | 10.10.250.0/30 | 0 | No |
| CORE-R2 | G0/1 | 10.10.250.4/30 | 0 | No |
| CORE-R1 | Vlan10 | 10.10.10.0/24 | 0 | Yes |
| CORE-R1 | Vlan20 | 10.10.20.0/24 | 0 | Yes |
| CORE-R1 | Vlan30 | 10.10.30.0/24 | 0 | Yes |
| CORE-R1 | Vlan40 | 10.10.40.0/24 | 0 | Yes |
| CORE-R1 | Vlan50 | 10.10.50.0/24 | 0 | Yes |
| CORE-R1 | Vlan99 | 10.10.99.0/24 | 0 | Yes |
| CORE-R2 | Vlan10 | 10.10.10.0/24 | 0 | Yes |
| CORE-R2 | Vlan20 | 10.10.20.0/24 | 0 | Yes |
| CORE-R2 | Vlan30 | 10.10.30.0/24 | 0 | Yes |
| CORE-R2 | Vlan40 | 10.10.40.0/24 | 0 | Yes |
| CORE-R2 | Vlan50 | 10.10.50.0/24 | 0 | Yes |
| CORE-R2 | Vlan99 | 10.10.99.0/24 | 0 | Yes |


### OSPF Route Advertisement

| Advertised Network | Advertising Device |
|:-------------------|:-------------------|
| 10.10.10.0/24 | CORE-R1 / CORE-R2 |
| 10.10.20.0/24 | CORE-R1 / CORE-R2 |
| 10.10.30.0/24 | CORE-R1 / CORE-R2 |
| 10.10.40.0/24 | CORE-R1 / CORE-R2 |
| 10.10.50.0/24 | CORE-R1 / CORE-R2 |
| 10.10.99.0/24 | CORE-R1 / CORE-R2 |
| 10.10.250.0/30 | EDGE-R1 / CORE-R1 |
| 10.10.250.4/30 | EDGE-R1 / CORE-R2 |



### OSPF Design Principles

| Principle | Implementation |
|:----------|:---------------|
| Single Area | Area 0 only |
| Dynamic Routing | Enabled |
| Passive Interfaces | Enabled on VLAN interfaces |
| Fast Convergence | Native OSPF convergence |
| Future Expansion | Additional routers may join Area 0 |


### OSPF Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Version | OSPFv2 |
| Area | Area 0 |
| Router IDs | Manually Assigned |
| Dynamic Routing | Enabled |
| Authentication | Future Enhancement |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## BGP Design

Border Gateway Protocol (BGP) is implemented at the Internet Edge to simulate communication between the enterprise network and an upstream Internet Service Provider (ISP).

Only the Edge Router participates in BGP. Internal routers remain isolated from external routing policies through OSPF.

### BGP Design Parameters

| Parameter | Value |
|:----------|:------|
| **Protocol** | eBGP |
| **Enterprise AS** | 65001 |
| **ISP AS** | 65000 |
| **Peering Device** | ISP Router |
| **Peering Interface** | G0/0 |
| **Default Route** | Statically configured toward ISP |

### BGP Neighbor Table

| Local Device | Local AS | Peer Device | Peer AS |
|:-------------|:--------:|:-----------|:-------:|
| EDGE-R1 | 65001 | ISP Router | 65000 |


### Advertised Prefixes

| Network | Advertisement |
|:---------|:--------------|
| 10.10.0.0/16 | Advertised to ISP |
| Default Route | Received from ISP |

### BGP Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Protocol | eBGP |
| AS Number | 65001 |
| Edge Device | EDGE-R1 |
| Internet Routing | BGP |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Static Routing

### Static Route Table

| Device | Destination | Next Hop | Administrative Distance | Purpose |
|:-------|:------------|:---------|:-----------------------:|:--------|
| EDGE-R1 | 0.0.0.0/0 | ISP Gateway | 1 | Internet access |
| ISP Router | 10.10.0.0/16 | EDGE-R1 | 1 | Return traffic to enterprise networks |


### Static Routing Principles

| Principle | Implementation |
|:----------|:---------------|
| Static routes kept minimal | Yes |
| Internal routing | OSPF |
| Internet routing | Static Default Route |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Default Route Design

### Traffic Flow

<img width="1717" height="916" alt="traffic flow" src="https://github.com/user-attachments/assets/431c889c-afe4-4248-a62f-4cc30046a142" />


### Default Route Operation

| Step | Description |
|:-----|:------------|
| User sends Internet traffic | Gateway forwards packet |
| Core Router checks routing table | Unknown destination forwarded to EDGE-R1 |
| EDGE-R1 performs NAT | Source translated |
| Packet forwarded to ISP | Internet access established |


### Design Summary

| Category | Decision |
|:---------|:---------|
| Default Route | EDGE-R1 |
| Internet Gateway | ISP |
| NAT | Before Internet forwarding |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Routing Redundancy

### Redundancy Components

| Component | Technology | Purpose |
|:----------|:-----------|:--------|
| Gateway | HSRP | Gateway redundancy |
| Dynamic Routing | OSPF | Route convergence |
| Core Layer | Dual Routers | Hardware redundancy |
| Routed Links | Dual Links | WAN resiliency |


### Design Summary

| Category | Decision |
|:---------|:---------|
| Gateway | HSRP |
| Dynamic Routing | OSPF |
| Core Redundancy | Dual Core Routers |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Route Failover

### Expected Failover Behavior

| Failure Scenario | Expected Result |
|:-----------------|:----------------|
| CORE-R1 Failure | CORE-R2 becomes the active default gateway through HSRP. |
| EDGE-R1 Link Failure | OSPF reconverges automatically and forwards traffic through the remaining available path. |
| HSRP Active Failure | The standby router assumes the active gateway role. |
| ISP Failure | Internet connectivity becomes unavailable because the initial design uses a single ISP. |


### Recovery Principles

| Event | Action |
|:------|:-------|
| Device restored | OSPF neighbors reform |
| HSRP recovery | Preemption returns gateway |
| Link restored | Routes automatically updated |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Routing Security

### Security Controls

| Control | Status | Purpose |
|:--------|:-------|:--------|
| Private IPv4 Addressing | Implemented | Prevents internal addressing from being publicly routable. |
| Passive Interfaces | Implemented | Reduces unnecessary OSPF advertisements on user-facing VLAN interfaces. |
| SSH Management | Implemented | Provides secure administrative access. |
| Management VLAN | Implemented | Separates routing management traffic from user traffic. |
| Route Filtering | Future Enhancement | Controls which routes are advertised or accepted. |
| OSPF Authentication | Future Enhancement | Protects OSPF neighbor relationships. |
| BGP MD5 Authentication | Future Enhancement | Protects BGP peering sessions. |
| TTL Security | Not Implemented | Can help protect BGP sessions in advanced designs. |

### Security Summary

| Category | Design Decision |
|:---------|:----------------|
| Internal Routing | OSPF with passive interfaces |
| Edge Routing | Isolated to EDGE-R1 |
| Management | VLAN 99 |
| OSPF Authentication | Future Enhancement |
| BGP Authentication | Future Enhancement |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>


## Routing Verification

### Verification Checklist

| ID | Verification Item | Expected Result |
|:--:|:------------------|:----------------|
| RT-VER-001 | OSPF Neighbor | FULL state |
| RT-VER-002 | Routing Table | Enterprise routes present |
| RT-VER-003 | BGP Session | Established |
| RT-VER-004 | Internet Reachability | Successful |
| RT-VER-005 | Default Route | Installed |
| RT-VER-006 | HSRP Gateway | Active/Standby operational |
| RT-VER-007 | Failover Test | Successful |
| RT-VER-008 | End-to-End Connectivity | Successful |



> Verification Commands
> - show ip interface brief
> - show ip route
> - show ip route ospf
> - show ip route bgp
> - show ip protocols
> - show ip ospf neighbor
> - show ip ospf interface
> - show ip ospf interface brief
> - show ip ospf database
> - show ip bgp summary
> - show ip bgp
> - show standby
> - show standby brief
> - show standby vlan
> - show ip cef
> - show ip nat translations
> - ping
> - traceroute

### Acceptance Criteria

| Category | Requirement |
|:---------|:------------|
| OSPF | All neighbors FULL |
| BGP | Established |
| Default Route | Installed |
| Failover | Successful |
| Internet Access | Operational |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Summary

This routing architecture provides a scalable and resilient Layer 3 infrastructure by combining OSPF for internal dynamic routing, HSRP for default gateway redundancy, and BGP for Internet edge connectivity.

The design follows Cisco enterprise campus best practices by separating Layer 2 switching, Layer 3 routing, Internet edge services, and gateway redundancy into dedicated functional roles.

The design separates internal and external routing domains, supports fast convergence after failures, simplifies route management, and provides a foundation for future enterprise expansion.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Glossary

| Acronym | Definition |
|:--------|:-----------|
| AS | Autonomous System |
| BGP | Border Gateway Protocol |
| ECMP | Equal-Cost Multi-Path |
| FIB | Forwarding Information Base |
| HSRP | Hot Standby Router Protocol |
| IGP | Interior Gateway Protocol |
| OSPF | Open Shortest Path First |
| PAT | Port Address Translation |
| RIB | Routing Information Base |
| VIP | Virtual IP |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
