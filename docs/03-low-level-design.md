# Low-Level Design Document

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#design-scope"><img src="https://img.shields.io/badge/DESIGN%20SCOPE-27AE60?style=for-the-badge"></a>
<a href="#device-inventory"><img src="https://img.shields.io/badge/DEVICE%20INVENTORY-8E44AD?style=for-the-badge"></a>
<a href="#device-naming-convention"><img src="https://img.shields.io/badge/NAMING%20CONVENTION-16A085?style=for-the-badge"></a>
<a href="#physical-connectivity"><img src="https://img.shields.io/badge/PHYSICAL%20CONNECTIVITY-2980B9?style=for-the-badge"></a>
<a href="#interface-assignment"><img src="https://img.shields.io/badge/INTERFACE%20ASSIGNMENT-3498DB?style=for-the-badge"></a>

</p>

<p align="left">
  
<a href="#layer-2-design"><img src="https://img.shields.io/badge/LAYER%202-E67E22?style=for-the-badge"></a>
<a href="#layer-3-design"><img src="https://img.shields.io/badge/LAYER%203-D35400?style=for-the-badge"></a>
<a href="#routing-design"><img src="https://img.shields.io/badge/ROUTING-C0392B?style=for-the-badge"></a>
<a href="#hsrp-design"><img src="https://img.shields.io/badge/HSRP-9B59B6?style=for-the-badge"></a>
<a href="#acl-design"><img src="https://img.shields.io/badge/ACL%20DESIGN-E74C3C?style=for-the-badge"></a>
<a href="#nat-design"><img src="https://img.shields.io/badge/NAT%20DESIGN-1ABC9C?style=for-the-badge"></a>
<a href="#firewall-policy"><img src="https://img.shields.io/badge/FIREWALL%20POLICY-34495E?style=for-the-badge"></a>
<a href="#dns-design"><img src="https://img.shields.io/badge/DNS%20DESIGN-2ECC71?style=for-the-badge"></a>
<a href="#management-design"><img src="https://img.shields.io/badge/MANAGEMENT-7F8C8D?style=for-the-badge"></a>

</p>

<p align="left">
  
<a href="#monitoring-design"><img src="https://img.shields.io/badge/MONITORING-2C3E50?style=for-the-badge"></a>
<a href="#implementation-sequence"><img src="https://img.shields.io/badge/IMPLEMENTATION-95A5A6?style=for-the-badge"></a>
<a href="#rollback-strategy"><img src="https://img.shields.io/badge/ROLLBACK-8E44AD?style=for-the-badge"></a>
<a href="#verification-plan"><img src="https://img.shields.io/badge/VERIFICATION-27AE60?style=for-the-badge"></a>
<a href="#appendix"><img src="https://img.shields.io/badge/APPENDIX-0B8FD3?style=for-the-badge"></a>

</p>

## Overview

This Low-Level Design (LLD) document translates the approved High-Level Design into detailed implementation specifications.

The document defines device naming conventions, physical connectivity, interface assignments, Layer 2 and Layer 3 configurations, routing design, gateway redundancy, security policies, management architecture, implementation sequence, and verification requirements.

This document serves as the primary technical reference during implementation.

## Design Scope

| Scope Area | Description |
|:---|:---|
| **Physical Interface Assignments** | Defines device interface usage, uplink connections, access ports, and inter-device links. |
| **Layer 2 Switching Design** | Covers switchport configuration, trunk links, access ports, and VLAN forwarding behavior. |
| **Layer 3 Routing Design** | Defines inter-VLAN routing, routed links, gateway placement, and routing responsibilities. |
| **VLAN Implementation** | Documents VLAN IDs, VLAN names, subnet mapping, and department-based segmentation. |
| **HSRP Configuration** | Provides redundant default gateway design for critical VLANs. |
| **OSPF Configuration** | Defines internal dynamic routing between enterprise network devices. |
| **BGP Configuration** | Defines simulated WAN/ISP edge routing behavior. |
| **NAT Implementation** | Covers private-to-public address translation for internet access. |
| **ACL Implementation** | Defines traffic filtering rules between VLANs and security zones. |
| **Server Connectivity** | Documents server VLAN access, gateway configuration, and service reachability. |
| **Monitoring Integration** | Covers Syslog, SNMP, and network visibility requirements. |
| **Device Management** | Defines management VLAN access, SSH access, and administrative connectivity. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Device Inventory

The following table lists all network and server devices included in the enterprise infrastructure.

| Hostname | Device Model | Layer | Primary Role | Management IP | Location |
|:---------|:-------------|:------|:-------------|:-------------|:---------|
| **EDGE-R1** | Cisco ISR 4331 | Edge | Internet Edge Router (BGP, NAT) | `10.10.99.4` | Data Center |
| **CORE-R1** | Cisco ISR 4331 | Core | Core Router (HSRP Active, OSPF) | `10.10.99.2` | Data Center |
| **CORE-R2** | Cisco ISR 4331 | Core | Core Router (HSRP Standby, OSPF) | `10.10.99.3` | Data Center |
| **DIST-SW1** | Cisco Catalyst 3560 | Distribution | Distribution Switch for VLAN Trunking and Access Switch Aggregation | `10.10.99.10` | Data Center |
| **HR-SW01** | Cisco Catalyst 2960 | Access | HR Department Access Switch | `10.10.99.21` | HR Office |
| **IT-SW01** | Cisco Catalyst 2960 | Access | IT Department Access Switch | `10.10.99.22` | IT Office |
| **FIN-SW01** | Cisco Catalyst 2960 | Access | Finance Department Access Switch | `10.10.99.23` | Finance Office |
| **SALES-SW01** | Cisco Catalyst 2960 | Access | Sales Department Access Switch | `10.10.99.24` | Sales Office |
| **SRV-SW01** | Cisco Catalyst 2960 | Access | Server Access Switch | `10.10.99.25` | Server Room |
| **WIN-SRV01** | Windows Server 2022 | Server | Active Directory, DHCP, DNS | `10.10.50.10` | Server Room |
| **LNX-SRV01** | Ubuntu Server 24.04 LTS | Server | Syslog, SNMP, Web Services | `10.10.50.20` | Server Room |

### Design Assumptions

| Item | Description |
|:-----|:------------|
| Device Models | Cisco ISR 4331 and Catalyst switches are used to represent enterprise routing and switching platforms. |
| Server Platform | Windows Server 2022 and Ubuntu Server 24.04 LTS provide centralized network services. |
| Management Network | All infrastructure devices are managed through a dedicated Management VLAN. |
| IP Addressing | Management IP addresses follow the Enterprise IP Addressing Plan. |

### Design Notes

| Item | Description |
|:-----|:------------|
| **Naming Convention** | Device hostnames follow the format **Function-Type-Number** (e.g., `CORE-R1`, `HR-SW01`). |
| **Management Network** | All infrastructure devices are managed through the dedicated **Management VLAN (VLAN 99)**. |
| **Management Access** | SSH is used for secure remote administration. Telnet is disabled. |
| **Gateway Redundancy** | CORE-R1 and CORE-R2 provide high availability using HSRP. |
| **Routing Protocols** | OSPF is used for internal routing, while BGP connects the enterprise edge to the simulated ISP. |
| **IP Addressing** | Management IP addresses follow the enterprise IP addressing plan defined in the IP Addressing document. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Device Naming Convention

All infrastructure devices follow a standardized naming convention to simplify identification, administration, troubleshooting, and future expansion.

| Prefix | Device Type | Naming Pattern | Example | Description |
|:-------:|:------------|:---------------|:--------|:------------|
| **EDGE** | Edge Router | `EDGE-R#` | `EDGE-R1` | Internet edge router providing BGP and NAT services. |
| **CORE** | Core Router | `CORE-R#` | `CORE-R1` | Core routing device responsible for OSPF and HSRP. |
| **DIST** | Distribution Switch | `DIST-SW#` | `DIST-SW1` | Distribution switch providing VLAN trunk aggregation and access-layer connectivity. |
| **HR** | Access Switch | `HR-SW##` | `HR-SW01` | Access switch serving the Human Resources department. |
| **IT** | Access Switch | `IT-SW##` | `IT-SW01` | Access switch serving the IT department. |
| **FIN** | Access Switch | `FIN-SW##` | `FIN-SW01` | Access switch serving the Finance department. |
| **SALES** | Access Switch | `SALES-SW##` | `SALES-SW01` | Access switch serving the Sales department. |
| **SRV** | Server Access Switch | `SRV-SW##` | `SRV-SW01` | Access switch connecting enterprise servers. |
| **WIN** | Windows Server | `WIN-SRV##` | `WIN-SRV01` | Windows Server providing AD DS, DHCP, and DNS services. |
| **LNX** | Linux Server | `LNX-SRV##` | `LNX-SRV01` | Ubuntu Server providing Syslog, SNMP, and web services. |

### Naming Standards

| Rule | Convention |
|:-----|:-----------|
| **Uppercase** | All hostnames use uppercase letters. |
| **Hyphen Separator** | Device function and identifier are separated using hyphens (`-`). |
| **Numeric Suffix** | Sequential numbers (`01`, `02`, etc.) uniquely identify devices of the same type. |
| **Consistent Prefixes** | Prefixes represent either the device role or the department served. |
| **Enterprise Standard** | All network devices follow a consistent enterprise-wide naming convention to improve operational efficiency and simplify troubleshooting. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Physical Connectivity

The following table defines the physical interconnections between all network devices in the enterprise infrastructure.

| Device A | Interface | Device B | Interface | Link Type | Purpose |
|:---------|:---------:|:---------|:---------:|:----------|:--------|
| **EDGE-R1** | G0/1 | **CORE-R1** | G0/1 | Routed Link | Primary WAN connection |
| **EDGE-R1** | G0/2 | **CORE-R2** | G0/1 | Routed Link | Secondary WAN connection |
| **CORE-R1** | G0/0 | **DIST-SW1** | G1/0/1 | 802.1Q Trunk | Core-to-Distribution VLAN trunk |
| **CORE-R2** | G0/0 | **DIST-SW1** | G1/0/2 | 802.1Q Trunk | Core-to-Distribution VLAN trunk |
| **DIST-SW1** | G1/0/3 | **HR-SW01** | G0/1 | 802.1Q Trunk | HR VLANs |
| **DIST-SW1** | G1/0/4 | **IT-SW01** | G0/1 | 802.1Q Trunk | IT VLANs |
| **DIST-SW1** | G1/0/5 | **FIN-SW01** | G0/1 | 802.1Q Trunk | Finance VLANs |
| **DIST-SW1** | G1/0/6 | **SALES-SW01** | G0/1 | 802.1Q Trunk | Sales VLANs |
| **DIST-SW1** | G1/0/7 | **SRV-SW01** | G0/1 | 802.1Q Trunk | Server VLAN |
| **SRV-SW01** | G0/2 | **WIN-SRV01** | NIC1 | Access Port | Windows Server |
| **SRV-SW01** | G0/3 | **LNX-SRV01** | NIC1 | Access Port | Ubuntu Server |

### Connectivity Standards

| Item | Standard |
|:-----|:---------|
| **Core-to-Distribution Links** | IEEE 802.1Q trunk links carrying enterprise VLANs between the Core and Distribution layers. |
| **Distribution Links** | IEEE 802.1Q trunk links carrying multiple VLANs. |
| **Server Connections** | Access ports assigned to the Server VLAN. |
| **WAN Links** | Routed interfaces connecting the enterprise edge to the ISP simulation. |
| **Interface Naming** | Cisco default interface naming convention (GigabitEthernet). |
| **Documentation** | Every physical connection is documented to simplify deployment, troubleshooting, and future expansion. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Interface Assignment

This section defines the interface allocation for all network devices, including interface purpose, addressing, and operational characteristics.

| Device | Interface | Connected To | IP Address / Subnet | VLAN | Description |
|:-------|:---------:|:-------------|:-------------------|:----:|:------------|
| **EDGE-R1** | G0/0 | ISP Router | `203.0.113.1/30` | N/A | Internet uplink |
| **EDGE-R1** | G0/1 | CORE-R1 | `10.10.250.1/30` | N/A | Primary WAN link |
| **EDGE-R1** | G0/2 | CORE-R2 | `10.10.250.5/30` | N/A | Secondary WAN link |
| **CORE-R1** | G0/0 | DIST-SW1 | N/A | Trunk | Core-to-Distribution VLAN trunk |
| **CORE-R1** | G0/1 | EDGE-R1 | `10.10.250.2/30` | N/A | WAN connection |
| **CORE-R2** | G0/0 | DIST-SW1 | N/A | Trunk | Core-to-Distribution VLAN trunk |
| **CORE-R2** | G0/1 | EDGE-R1 | `10.10.250.6/30` | N/A | WAN connection |
| **DIST-SW1** | G1/0/1 | CORE-R1 | N/A | Trunk | Trunk uplink to CORE-R1 |
| **DIST-SW1** | G1/0/2 | CORE-R2 | N/A | Trunk | Trunk uplink to CORE-R2 |
| **DIST-SW1** | G1/0/3 | HR-SW01 | N/A | Trunk | HR VLANs |
| **DIST-SW1** | G1/0/4 | IT-SW01 | N/A | Trunk | IT VLANs |
| **DIST-SW1** | G1/0/5 | FIN-SW01 | N/A | Trunk | Finance VLANs |
| **DIST-SW1** | G1/0/6 | SALES-SW01 | N/A | Trunk | Sales VLANs |
| **DIST-SW1** | G1/0/7 | SRV-SW01 | N/A | Trunk | Server VLAN |
| **HR-SW01** | G0/1 | DIST-SW1 | N/A | Trunk | Distribution uplink |
| **IT-SW01** | G0/1 | DIST-SW1 | N/A | Trunk | Distribution uplink |
| **FIN-SW01** | G0/1 | DIST-SW1 | N/A | Trunk | Distribution uplink |
| **SALES-SW01** | G0/1 | DIST-SW1 | N/A | Trunk | Distribution uplink |
| **SRV-SW01** | G0/1 | DIST-SW1 | N/A | Trunk | Distribution uplink |
| **SRV-SW01** | G0/2 | WIN-SRV01 | N/A | VLAN 50 | Windows Server |
| **SRV-SW01** | G0/3 | LNX-SRV01 | N/A | VLAN 50 | Ubuntu Server |

### Interface Design Standards

| Item | Standard |
|:-----|:---------|
| **WAN Interfaces** | Configured as Layer 3 routed interfaces using `/30` point-to-point subnets. |
| **Core-to-Distribution Links** | Configured as IEEE 802.1Q trunk interfaces to carry VLAN traffic between CORE-R1/CORE-R2 and DIST-SW1. |
| **Switch Uplinks** | Distribution-to-Access uplinks are configured as IEEE 802.1Q trunk interfaces carrying assigned VLANs. |
| **Server Ports** | Configured as access ports assigned to the Server VLAN. |
| **Interface Naming** | Cisco GigabitEthernet naming convention is used throughout the infrastructure. |
| **Addressing** | All interface IP addresses follow the Enterprise IP Addressing Plan. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Layer 2 Design

### Overview

The Layer 2 network is designed to provide secure, scalable, and resilient connectivity between the Distribution and Access layers. VLAN segmentation, IEEE 802.1Q trunking, Rapid PVST+, and Layer 2 security features are implemented to improve network availability while preventing unauthorized access and switching loops.

### Layer 2 Design Standards

| Feature | Design Standard | Purpose |
|:--------|:----------------|:--------|
| **Switching Model** | Hierarchical Layer 2 Access Design | Simplifies scalability and network management |
| **VLAN Segmentation** | Department-based VLANs | Logical isolation between business units |
| **Trunk Protocol** | IEEE 802.1Q | VLAN tagging between switches |
| **Dynamic Trunking Protocol (DTP)** | Disabled | Prevents unauthorized trunk negotiation |
| **Native VLAN** | VLAN 999 | Isolates untagged traffic for security |
| **Allowed VLANs** | Explicitly configured | Prevents unnecessary VLAN propagation |
| **Spanning Tree Mode** | Rapid PVST+ | Fast Layer 2 convergence |
| **Root Bridge Placement** | DIST-SW1 | Centralized spanning-tree control |
| **PortFast** | Enabled on all access ports | Reduces endpoint startup delay |
| **BPDU Guard** | Enabled on all access ports | Protects against rogue switches |
| **Port Security** | Enabled on user-facing ports | Restricts unauthorized devices |
| **Storm Control** | Enabled | Mitigates broadcast and multicast storms |

### VLAN Trunk Design

| Parameter | Configuration |
|:----------|:--------------|
| **Encapsulation** | IEEE 802.1Q |
| **Native VLAN** | VLAN 999 |
| **Allowed VLANs** | 10,20,30,40,50,99 |
| **DTP Mode** | Disabled (`switchport nonegotiate`) |
| **Trunk Links** | Core ↔ Distribution, Distribution ↔ Access |

### Spanning Tree Design

| Item | Configuration |
|:-----|:--------------|
| **STP Mode** | Rapid PVST+ |
| **Root Bridge** | DIST-SW1 |
| **Secondary Root** | Not Applicable |
| **PortFast** | Enabled on all access interfaces |
| **BPDU Guard** | Enabled on all access interfaces |
| **Loop Guard** | Not Implemented |
| **Root Guard** | Not Implemented |


### Layer 2 Security Controls

| Security Feature | Design Decision | Purpose |
|:-----------------|:----------------|:--------|
| **Port Security** | Enabled | Restricts unauthorized MAC addresses |
| **BPDU Guard** | Enabled | Prevents rogue switch connections |
| **Unused Ports** | Administratively shutdown | Reduces attack surface |
| **Native VLAN** | Dedicated VLAN 999 | Prevents VLAN hopping attacks |
| **DTP** | Disabled | Eliminates dynamic trunk negotiation |


### Design Summary

| Category | Design Decision |
|:---------|:----------------|
| VLAN Segmentation | Department-based VLANs |
| Trunk Standard | IEEE 802.1Q |
| STP | Rapid PVST+ |
| Root Bridge | DIST-SW1 |
| Layer 2 Security | Port Security, BPDU Guard, Native VLAN, Disabled DTP |
| Access Port Optimization | PortFast enabled |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Layer 3 Design

### Overview

The Layer 3 design defines how traffic is routed between VLANs, routed links, internal networks, and the enterprise edge. In this design, inter-VLAN routing and default gateway redundancy are performed at the Core Layer using **CORE-R1** and **CORE-R2** with HSRP.

DIST-SW1 operates as a distribution switch for VLAN trunk aggregation and access-layer connectivity.

### Layer 3 Design Standards

| Design Element | Implementation | Purpose |
|:---------------|:---------------|:--------|
| **Inter-VLAN Routing** | Core Layer | Routes traffic between enterprise VLANs |
| **Default Gateway** | HSRP Virtual IP | Provides redundant default gateways for VLANs |
| **Gateway Devices** | CORE-R1, CORE-R2 | Provides Layer 3 gateway redundancy |
| **Distribution Role** | VLAN Trunk Aggregation | Aggregates Access Layer VLAN trunks |
| **Routed Links** | `/30` Point-to-Point | Used for Edge-to-Core Layer 3 connectivity |
| **Internal Routing** | OSPF Area 0 | Provides dynamic internal route exchange |
| **External Routing** | BGP | Simulates ISP/WAN connectivity |
| **Default Route** | Edge Router toward ISP | Provides Internet-bound traffic forwarding |

### Layer 3 Responsibility Matrix

| Function | Responsible Device | Design Notes |
|:---------|:-------------------|:-------------|
| **Inter-VLAN Routing** | CORE-R1 / CORE-R2 | Core routers route traffic between VLANs. |
| **Default Gateway Redundancy** | CORE-R1 / CORE-R2 | HSRP provides one virtual gateway per VLAN. |
| **Internal Route Exchange** | CORE-R1 / CORE-R2 / EDGE-R1 | OSPF Area 0 advertises internal networks. |
| **Internet Edge Routing** | EDGE-R1 | BGP and default routing provide ISP connectivity. |
| **VLAN Aggregation** | DIST-SW1 | Distribution switch carries VLAN trunks only. |
| **Access Connectivity** | Access Switches | Access switches connect end-user and server devices. |

### Routed Link Design

| Link | Subnet | Purpose |
|:-----|:-------|:--------|
| **EDGE-R1 ↔ CORE-R1** | `10.10.250.0/30` | Primary routed WAN/Core link |
| **EDGE-R1 ↔ CORE-R2** | `10.10.250.4/30` | Secondary routed WAN/Core link |

### Default Gateway Design

| VLAN | Network | Default Gateway Type | Virtual Gateway |
|:----:|:--------|:---------------------|:----------------|
| **10** | `10.10.10.0/24` | HSRP | `10.10.10.1` |
| **20** | `10.10.20.0/24` | HSRP | `10.10.20.1` |
| **30** | `10.10.30.0/24` | HSRP | `10.10.30.1` |
| **40** | `10.10.40.0/24` | HSRP | `10.10.40.1` |
| **50** | `10.10.50.0/24` | HSRP | `10.10.50.1` |
| **99** | `10.10.99.0/24` | HSRP | `10.10.99.1` |

### Layer 3 Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Inter-VLAN Routing | Performed at the Core Layer |
| Default Gateway | HSRP virtual IP per VLAN |
| Core Routers | CORE-R1 and CORE-R2 |
| Distribution Switch | VLAN trunk aggregation only |
| Internal Routing | OSPF Area 0 |
| Edge Routing | BGP and static default route |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Routing Design

### Overview

The routing design defines how internal VLAN networks, core routers, and the Internet edge exchange routing information. OSPF is used as the internal routing protocol, while BGP is used at the Internet Edge to simulate ISP connectivity.

### Routing Design Summary

| Routing Component | Design Decision | Purpose |
|:------------------|:----------------|:--------|
| **Internal Routing** | OSPFv2 | Provides dynamic routing between internal Layer 3 devices |
| **OSPF Area** | Area 0 | Uses a single backbone area for simplified routing |
| **Edge Routing** | eBGP | Provides simulated ISP connectivity |
| **Default Route** | Injected by EDGE-R1 | Provides Internet-bound traffic forwarding |
| **Gateway Redundancy** | HSRP | Provides redundant default gateways for VLANs |
| **Inter-VLAN Routing** | CORE-R1 / CORE-R2 | Routes traffic between enterprise VLANs |

### OSPF Design

| Item | Value |
|:-----|:------|
| **Protocol** | OSPFv2 |
| **Process ID** | 1 |
| **Area** | 0 |
| **Router IDs** | Defined per router |
| **Participating Devices** | CORE-R1, CORE-R2, EDGE-R1 |
| **Route Advertisement** | Enterprise VLANs and routed links |
| **Default Route** | Injected by EDGE-R1 |
| **Authentication** | Not implemented in lab environment |

### OSPF Routing Interface Table

| Router | Interface | Network | OSPF Area | Purpose |
|:-------|:---------:|:--------|:---------:|:--------|
| **EDGE-R1** | G0/1 | `10.10.250.0/30` | 0 | Link to CORE-R1 |
| **EDGE-R1** | G0/2 | `10.10.250.4/30` | 0 | Link to CORE-R2 |
| **CORE-R1** | G0/1 | `10.10.250.0/30` | 0 | Link to EDGE-R1 |
| **CORE-R2** | G0/1 | `10.10.250.4/30` | 0 | Link to EDGE-R1 |
| **CORE-R1** | VLAN Interfaces | `10.10.10.0/24 - 10.10.99.0/24` | 0 | VLAN gateway networks |
| **CORE-R2** | VLAN Interfaces | `10.10.10.0/24 - 10.10.99.0/24` | 0 | VLAN gateway networks |

### BGP Design

| Item | Value |
|:-----|:------|
| **Protocol** | eBGP |
| **Enterprise AS** | 65001 |
| **ISP AS** | 65000 |
| **BGP Peer** | ISP Router |
| **BGP Location** | EDGE-R1 |
| **Purpose** | Simulated ISP / Internet connectivity |
| **Default Route** | Learned from ISP or statically configured toward ISP |

### BGP Peering Table

| Local Device | Local AS | Peer Device | Peer AS | Purpose |
|:-------------|:--------:|:------------|:-------:|:--------|
| **EDGE-R1** | 65001 | **ISP Router** | 65000 | Internet edge routing |

### Static Route Design

| Device | Route | Next Hop | Purpose |
|:-------|:------|:---------|:--------|
| **EDGE-R1** | `0.0.0.0/0` | ISP Gateway | Default route toward the ISP |
| **ISP Router** | Enterprise Prefixes | EDGE-R1 | Return path to enterprise networks |

### Routing Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Internal Routing Protocol | OSPFv2 |
| OSPF Area | Area 0 |
| External Routing Protocol | eBGP |
| Enterprise AS | 65001 |
| ISP AS | 65000 |
| Default Route Source | EDGE-R1 / ISP |
| Inter-VLAN Routing Location | Core Layer |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## HSRP Design

### Overview

Hot Standby Router Protocol (HSRP) is implemented on **CORE-R1** and **CORE-R2** to provide highly available default gateway services for all enterprise VLANs. End devices use a virtual IP address as their default gateway, allowing seamless failover if the active router becomes unavailable.

### HSRP Design Parameters

| Item | Value |
|:-----|:------|
| **Protocol Version** | HSRPv2 |
| **Active Router** | CORE-R1 |
| **Standby Router** | CORE-R2 |
| **Preemption** | Enabled |
| **Interface Tracking** | Enabled |
| **Authentication** | Not Configured (Lab Environment) |
| **Load Sharing** | Not Implemented |
| **Failover Method** | Automatic |

### HSRP VLAN Mapping

| VLAN | HSRP Group | Virtual IP | Active Router | Standby Router | CORE-R1 Priority | CORE-R2 Priority |
|:----:|:----------:|:-----------|:--------------|:---------------|:----------------:|:----------------:|
| **10** | 10 | `10.10.10.1` | CORE-R1 | CORE-R2 | 110 | 100 |
| **20** | 20 | `10.10.20.1` | CORE-R1 | CORE-R2 | 110 | 100 |
| **30** | 30 | `10.10.30.1` | CORE-R1 | CORE-R2 | 110 | 100 |
| **40** | 40 | `10.10.40.1` | CORE-R1 | CORE-R2 | 110 | 100 |
| **50** | 50 | `10.10.50.1` | CORE-R1 | CORE-R2 | 110 | 100 |
| **99** | 99 | `10.10.99.1` | CORE-R1 | CORE-R2 | 110 | 100 |

### HSRP Failover Behavior

| Event | Expected Behavior |
|:------|:------------------|
| **CORE-R1 Failure** | CORE-R2 automatically becomes the active gateway. |
| **CORE-R1 Recovery** | CORE-R1 resumes the active role through preemption. |
| **WAN Interface Failure** | Interface tracking reduces HSRP priority and initiates failover. |
| **Normal Operation** | CORE-R1 forwards gateway traffic while CORE-R2 remains in standby mode. |

### HSRP Design Summary

| Category | Design Decision |
|:---------|:----------------|
| HSRP Version | HSRPv2 |
| Gateway Redundancy | CORE-R1 / CORE-R2 |
| Virtual Gateway | One VIP per VLAN |
| Preferred Active Device | CORE-R1 |
| Standby Device | CORE-R2 |
| Preemption | Enabled |
| Interface Tracking | Enabled |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## ACL Design

### Overview

ACL design defines the traffic control policy between enterprise VLANs and security zones. Access Control Lists are used to enforce least-privilege communication, restrict unauthorized inter-VLAN traffic, and protect critical business services.

ACL commands are not included in this section. This section documents the intended access policy only.

### ACL Policy Matrix

| Source | Destination | Service | Action | Reason |
|:-------|:------------|:--------|:------:|:-------|
| **IT VLAN** | All Enterprise VLANs | Any | Allow | Infrastructure administration and technical support |
| **Management VLAN** | Network Devices | SSH, SNMP | Allow | Secure device management and monitoring |
| **HR VLAN** | Finance VLAN | Any | Deny | Protects payroll and financial data |
| **Finance VLAN** | Server VLAN | DNS, DHCP, HTTPS, Database Services | Allow | Required for finance applications |
| **Sales VLAN** | Server VLAN | HTTPS, DNS | Allow | Access to approved business applications |
| **HR VLAN** | Server VLAN | DNS, DHCP, HTTPS | Allow | Access to centralized services |
| **User VLANs** | Management VLAN | Any | Deny | Prevents unauthorized administrative access |
| **User VLANs** | Internet | HTTP, HTTPS, DNS | Allow | Standard business Internet access |
| **Guest / Untrusted VLAN** | Internal VLANs | Any | Deny | Prevents access to enterprise resources |
| **Any** | Any | Any | Deny | Default deny policy |

### ACL Design Standards

| Item | Design Standard |
|:-----|:----------------|
| **Policy Model** | Least privilege |
| **Default Policy** | Deny by default |
| **Administrative Access** | Allowed only from the Management VLAN and IT VLAN |
| **Server Access** | Restricted to required business services |
| **Finance Protection** | Finance VLAN is isolated from non-authorized user VLANs |
| **Management Protection** | User VLANs cannot access infrastructure management interfaces |
| **Internet Access** | User VLANs are allowed outbound access through the firewall/NAT boundary |

### ACL Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Inter-VLAN Filtering | Enforced using ACLs |
| Access Model | Business-justified access only |
| Management Access | Restricted to authorized VLANs |
| Sensitive VLANs | Finance and Management VLANs receive stricter controls |
| Default Rule | Explicit deny |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## NAT Design

Network Address Translation (NAT) is implemented on **EDGE-R1** to provide Internet connectivity for internal enterprise networks. Port Address Translation (PAT) is used so that multiple private IP addresses can share a single public IP address assigned to the Internet-facing interface.

### NAT Design Parameters

| Feature | Implementation |
|:--------|:---------------|
| **NAT Type** | Port Address Translation (PAT) |
| **Translation Device** | EDGE-R1 |
| **Inside Interface** | Enterprise-facing interfaces |
| **Outside Interface** | ISP-facing interface (G0/0) |
| **Public IP** | EDGE-R1 Internet interface (`203.0.113.1`) |
| **Translated Networks** | Enterprise VLANs (`10.10.0.0/16`) |
| **Translation Direction** | Inside Local → Inside Global |

### NAT Traffic Flow

| Source | Destination | NAT Action | Result |
|:-------|:------------|:----------|:-------|
| Enterprise VLANs | Internet | PAT Translation | Private addresses translated to the EDGE-R1 public IP |
| Internet | Enterprise | Return Translation | Existing NAT sessions are translated back to the originating internal hosts |
| Internet | Internal Devices | Direct Access | Not permitted unless explicitly configured |

### NAT Design Summary

| Category | Design Decision |
|:---------|:----------------|
| NAT Type | PAT (Overload) |
| NAT Device | EDGE-R1 |
| Public Address | Internet-facing interface |
| Inside Network | Enterprise VLANs |
| Outside Network | ISP |
| Static NAT | Not Implemented |
| Dynamic NAT Pool | Not Implemented |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Firewall Policy

### Overview

The firewall policy defines how traffic is controlled between the enterprise internal network and the external ISP/Internet zone. In this lab environment, perimeter filtering is simulated using firewall rules or edge ACLs on **EDGE-R1**.

### Firewall Zone Design

| Zone | Description | Typical Networks |
|:-----|:------------|:-----------------|
| **Inside Zone** | Trusted enterprise network | User VLANs, Server VLAN, Management VLAN |
| **Outside Zone** | Untrusted external network | ISP / Internet |
| **Management Zone** | Administrative network | VLAN 99 |

### Firewall Policy Matrix

| Source Zone | Destination Zone | Service | Action | Reason |
|:------------|:-----------------|:--------|:------:|:-------|
| **Inside Zone** | Outside Zone | HTTP, HTTPS, DNS, ICMP | Allow | Standard outbound Internet access |
| **Inside Zone** | Outside Zone | Any | Deny | Blocks unauthorized outbound traffic |
| **Outside Zone** | Inside Zone | Established Sessions | Allow | Allows return traffic for internal-initiated sessions |
| **Outside Zone** | Inside Zone | Any | Deny | Blocks unsolicited inbound traffic |
| **Management Zone** | EDGE-R1 | SSH, SNMP | Allow | Secure administrative access |
| **Outside Zone** | Management Zone | Any | Deny | Prevents external access to management network |
| **Any** | Any | Any | Deny | Default deny policy |

### Firewall Design Standards

| Item | Design Standard |
|:-----|:----------------|
| **Security Model** | Default deny |
| **Inbound Access** | Denied unless explicitly permitted |
| **Outbound Access** | Limited to required business services |
| **Management Access** | Allowed only from the Management VLAN |
| **NAT Integration** | Inside traffic is translated using PAT on EDGE-R1 |
| **Logging** | Security events should be forwarded to the Syslog server |

### Firewall Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Perimeter Control | Simulated on EDGE-R1 |
| Trusted Zone | Inside enterprise VLANs |
| Untrusted Zone | ISP / Internet |
| Default Policy | Deny |
| Internet Access | Internal-initiated traffic only |
| Management Access | Restricted to VLAN 99 |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## DNS Design

### Overview

The enterprise DNS infrastructure provides centralized name resolution for internal resources while forwarding external queries to public DNS servers.

### DNS Configuration

| Item | Configuration |
|:-----|:--------------|
| **DNS Platform** | Windows Server 2022 |
| **DNS Type** | AD-Integrated DNS |
| **Primary Zone** | verra.local |
| **Zone Type** | Forward Lookup Zone |
| **Dynamic Updates** | Secure Only |
| **DNS Forwarders** | 8.8.8.8, 1.1.1.1 |
| **Primary DNS Server** | WIN-SRV01 |

### Internal DNS Records

| Hostname | Record Type | IP Address | Purpose |
|:---------|:-----------:|:-----------|:--------|
| **win-srv01.verra.local** | A | 10.10.50.10 | Windows Server |
| **lnx-srv01.verra.local** | A | 10.10.50.20 | Linux Server |
| **edge-r1.verra.local** | A | 10.10.99.4 | Edge Router |
| **core-r1.verra.local** | A | 10.10.99.2 | Core Router |
| **core-r2.verra.local** | A | 10.10.99.3 | Core Router |
| **dist-sw1.verra.local** | A | 10.10.99.10 | Distribution Switch |

### DNS Design Summary

| Category | Design Decision |
|:---------|:----------------|
| DNS Platform | Windows Server 2022 |
| DNS Integration | Active Directory Integrated |
| Internal Zone | verra.local |
| External Resolution | DNS Forwarders |
| Dynamic Updates | Secure Only |
| High Availability | Future Enhancement |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Management Design

### Overview

The management network provides secure administrative access to all enterprise infrastructure devices. Centralized management services improve operational efficiency, enhance security, and simplify troubleshooting.

### Management Services

| Service | Configuration | Purpose |
|:--------|:--------------|:--------|
| **Management VLAN** | VLAN 99 (`10.10.99.0/24`) | Dedicated management network for infrastructure devices |
| **SSH** | Version 2 | Secure remote device administration |
| **Telnet** | Disabled | Eliminates insecure remote management |
| **AAA** | Local Authentication | Administrative login authentication |
| **SNMP** | SNMPv3 (SNMPv2c for Lab if required) | Infrastructure monitoring |
| **Syslog** | Ubuntu Server (`LNX-SRV01`) | Centralized event logging |
| **NTP** | Windows Server (`WIN-SRV01`) | Time synchronization |

### Management Access Policy

| Source | Destination | Access | Protocol |
|:-------|:------------|:------:|:---------|
| **IT VLAN** | Infrastructure Devices | Allow | SSH |
| **Management VLAN** | Infrastructure Devices | Allow | SSH, SNMP |
| **User VLANs** | Infrastructure Devices | Deny | All |
| **Internet** | Infrastructure Devices | Deny | All |

### Device Management Summary

| Device Category | Management IP Range | Access Method |
|:----------------|:-------------------|:--------------|
| Edge Router | 10.10.99.4 | SSH |
| Core Routers | 10.10.99.2–3 | SSH |
| Distribution Switch | 10.10.99.10 | SSH |
| Access Switches | 10.10.99.21–25 | SSH |
| Windows Server | 10.10.50.10 | RDP / PowerShell |
| Ubuntu Server | 10.10.50.20 | SSH |

### Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Management Network | Dedicated VLAN 99 |
| Remote Access | SSH Version 2 |
| Authentication | Local AAA |
| Monitoring | SNMPv3 |
| Logging | Centralized Syslog |
| Time Synchronization | Centralized NTP |
| Legacy Protocols | Telnet Disabled |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Design

### Overview

The enterprise monitoring solution provides centralized visibility into network devices, servers, and infrastructure services. Monitoring enables proactive fault detection, performance analysis, and operational reporting.

### Monitoring Components

| Component | Monitoring Items | Technology |
|:----------|:-----------------|:-----------|
| **Routers** | CPU, Memory, Interface Status, Routing | SNMP |
| **Switches** | Port Status, VLANs, Interface Utilization | SNMP |
| **Servers** | CPU, Memory, Disk, Service Availability | SNMP / Agent |
| **Syslog** | Security Events, Configuration Changes | Syslog |
| **Network Services** | DHCP, DNS, NTP Availability | ICMP / Service Checks |

### Monitoring Platform

| Item | Configuration |
|:-----|:--------------|
| **Monitoring Platform** | LibreNMS / Zabbix |
| **Monitoring Protocol** | SNMPv3 (SNMPv2c for Lab if required) |
| **Log Collection** | Syslog Server (LNX-SRV01) |
| **Alert Method** | Dashboard & Email (Future Enhancement) |
| **Polling Interval** | Default |

### Monitoring Workflow

<img width="1695" height="928" alt="monitoring-workflow" src="https://github.com/user-attachments/assets/52fc8303-ea04-40d8-9957-75ae394196d4" />


### Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Monitoring Platform | LibreNMS / Zabbix |
| Device Monitoring | SNMP |
| Log Collection | Syslog |
| Service Monitoring | ICMP / Service Checks |
| Alerting | Dashboard |
| Reporting | Historical Performance |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Implementation Sequence

### Overview

The implementation sequence defines the recommended deployment order to ensure that network dependencies are satisfied and to minimize service disruption during implementation.

| Step | Phase | Activity | Expected Outcome |
|:---:|:------|:---------|:-----------------|
| **1** | Device Preparation | Configure hostnames, passwords, and management interfaces | Devices are ready for deployment |
| **2** | Layer 2 | Create VLANs and configure access ports | Department segmentation established |
| **3** | Layer 2 | Configure trunk links and native VLAN | VLAN traffic can traverse the network |
| **4** | Layer 2 | Configure STP, PortFast, BPDU Guard, and Port Security | Layer 2 security enabled |
| **5** | Layer 3 | Configure SVIs, routed interfaces, and default gateways | Layer 3 connectivity established |
| **6** | High Availability | Configure HSRP | Redundant default gateways available |
| **7** | Routing | Configure OSPF | Internal dynamic routing operational |
| **8** | Routing | Configure BGP | Internet edge routing established |
| **9** | Security | Configure NAT and ACLs | Secure Internet access and traffic filtering |
| **10** | Infrastructure Services | Deploy DHCP, DNS, NTP, Syslog, and SNMP | Centralized services available |
| **11** | Monitoring | Configure LibreNMS / Zabbix monitoring | Infrastructure monitoring enabled |
| **12** | Automation | Configure Python backup and validation scripts | Automated operations available |
| **13** | Validation | Perform connectivity and service verification | Implementation successfully validated |

### Deployment Dependencies

| Component | Depends On |
|:----------|:-----------|
| VLANs | Switch configuration |
| Trunk Links | VLAN creation |
| Layer 3 Interfaces | VLAN implementation |
| HSRP | Layer 3 interfaces |
| OSPF | Routed interfaces |
| BGP | OSPF |
| NAT | BGP |
| ACLs | Routing |
| Infrastructure Services | Layer 3 connectivity |
| Monitoring | Infrastructure Services |
| Validation | All previous phases |

### Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Deployment Method | Phased Implementation |
| Layer 2 Before Layer 3 | Yes |
| Routing Before Services | Yes |
| Services Before Monitoring | Yes |
| Validation | Final Implementation Phase |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Rollback Strategy

### Overview

If an implementation step causes service disruption or fails validation, the affected configuration should be reverted to the last known stable state before proceeding.

### Rollback Plan

| Failure Scenario | Possible Impact | Rollback Action |
|:-----------------|:----------------|:----------------|
| **VLAN Configuration Failure** | End devices lose Layer 2 connectivity | Restore the previous VLAN database and trunk configuration |
| **Trunk Configuration Failure** | VLAN traffic cannot traverse between switches | Restore the previous trunk configuration |
| **OSPF Failure** | Internal routing becomes unavailable | Restore the previous OSPF configuration from backup |
| **HSRP Failure** | Default gateway redundancy is lost | Restore the previous HSRP configuration or force traffic to the standby router |
| **BGP Failure** | Internet connectivity is unavailable | Restore the previous BGP neighbor configuration |
| **ACL Misconfiguration** | Legitimate traffic is blocked | Remove the new ACL and restore the previous access policy |
| **NAT Failure** | Internal users cannot access the Internet | Restore the previous NAT configuration |
| **Management Service Failure** | Devices become inaccessible via SSH/SNMP | Restore the previous management configuration |
| **Infrastructure Service Failure** | DHCP, DNS, Syslog, or NTP services become unavailable | Restore the previous server configuration or service backup |

### Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Configuration Backup | Required before every major change |
| Rollback Trigger | Service disruption or failed validation |
| Recovery Method | Restore last known good configuration |
| Validation | Re-run verification after rollback |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Verification Plan

### Verification Checklist

| ID | Verification Item | Expected Result | Status |
|:--:|:------------------|:----------------|:------:|
| **VER-001** | Device Reachability | All infrastructure devices respond to ICMP | ☐ |
| **VER-002** | Management Access | SSH access successful from Management VLAN | ☐ |
| **VER-003** | VLAN Segmentation | End devices receive correct VLAN assignment | ☐ |
| **VER-004** | Trunk Links | All required VLANs traverse trunk interfaces | ☐ |
| **VER-005** | Inter-VLAN Routing | Communication between authorized VLANs succeeds | ☐ |
| **VER-006** | HSRP Failover | Standby router becomes active after failure | ☐ |
| **VER-007** | OSPF Routing | All internal routes are learned dynamically | ☐ |
| **VER-008** | BGP Connectivity | Edge router establishes BGP neighbor relationship | ☐ |
| **VER-009** | Internet Access | Internal users successfully access external networks | ☐ |
| **VER-010** | NAT Operation | Private addresses translate successfully | ☐ |
| **VER-011** | ACL Enforcement | Unauthorized traffic is blocked | ☐ |
| **VER-012** | DHCP Service | Clients receive valid IP configuration | ☐ |
| **VER-013** | DNS Resolution | Internal and external names resolve successfully | ☐ |
| **VER-014** | Syslog | Device logs appear on Syslog Server | ☐ |
| **VER-015** | SNMP Monitoring | Devices report status to monitoring platform | ☐ |
| **VER-016** | NTP Synchronization | Infrastructure devices share consistent time | ☐ |
| **VER-017** | Monitoring Dashboard | Devices display healthy operational status | ☐ |
| **VER-018** | Configuration Backup | Python automation successfully backs up configurations | ☐ |

### Acceptance Criteria

| Category | Success Criteria |
|:---------|:-----------------|
| Connectivity | All devices reachable |
| Routing | OSPF & BGP operational |
| High Availability | HSRP failover successful |
| Security | ACL & NAT function correctly |
| Infrastructure Services | DHCP, DNS, Syslog, SNMP, NTP operational |
| Monitoring | All devices visible on monitoring platform |
| Automation | Backup scripts complete successfully |

### Design Summary

| Category | Design Decision |
|:---------|:----------------|
| Verification Method | Functional Testing |
| Validation Type | End-to-End Verification |
| Acceptance Requirement | All verification items must pass |

## Appendix

### VLAN Matrix

| VLAN | Name | Subnet | Gateway |
|:----:|------|---------|---------|
| 10 | HR | 10.10.10.0/24 | 10.10.10.1 |
| 20 | IT | 10.10.20.0/24 | 10.10.20.1 |
| 30 | Finance | 10.10.30.0/24 | 10.10.30.1 |
| 40 | Sales | 10.10.40.0/24 | 10.10.40.1 |
| 50 | Server | 10.10.50.0/24 | 10.10.50.1 |
| 99 | Management | 10.10.99.0/24 | 10.10.99.1 |
| 999 | Native | N/A | N/A |


### Device Summary

| Device | Role | Management IP |
|:-------|:-----|:--------------|
| EDGE-R1 | Internet Edge Router | 10.10.99.4 |
| CORE-R1 | Core Router | 10.10.99.2 |
| CORE-R2 | Core Router | 10.10.99.3 |
| DIST-SW1 | Distribution Switch | 10.10.99.10 |
| HR-SW01 | Access Switch | 10.10.99.21 |
| IT-SW01 | Access Switch | 10.10.99.22 |
| FIN-SW01 | Access Switch | 10.10.99.23 |
| SALES-SW01 | Access Switch | 10.10.99.24 |
| SRV-SW01 | Server Switch | 10.10.99.25 |


### Interface Summary

| Link | Interface |
|:-----|:----------|
| EDGE-R1 ↔ CORE-R1 | G0/1 |
| EDGE-R1 ↔ CORE-R2 | G0/2 |
| CORE-R1 ↔ DIST-SW1 | G0/0 ↔ G1/0/1 |
| CORE-R2 ↔ DIST-SW1 | G0/0 ↔ G1/0/2 |
| DIST-SW1 ↔ Access Switches | IEEE 802.1Q Trunks |


### IP Allocation Summary

| Network | Purpose |
|:---------|:--------|
| 10.10.10.0/24 | HR |
| 10.10.20.0/24 | IT |
| 10.10.30.0/24 | Finance |
| 10.10.40.0/24 | Sales |
| 10.10.50.0/24 | Servers |
| 10.10.99.0/24 | Management |
| 10.10.250.0/30 | EDGE-R1 ↔ CORE-R1 |
| 10.10.250.4/30 | EDGE-R1 ↔ CORE-R2 |
| 203.0.113.0/30 | EDGE-R1 ↔ ISP |


### Related Documents

| Document | Purpose |
|:---------|:--------|
| Business Requirements | Business objectives |
| High-Level Design | Enterprise architecture |
| Low-Level Design | Implementation details |
| IP Addressing Plan | IP allocation |
| VLAN Design | Layer 2 segmentation |
| Device Configuration | Cisco IOS configurations |
| Test Plan | Validation procedures |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## References

- Cisco Enterprise Campus Design Guide
- Cisco Validated Design (CVD)
- RFC 1918 - Address Allocation for Private Internets
- RFC 2328 - OSPF Version 2
- RFC 4271 - Border Gateway Protocol 4

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
