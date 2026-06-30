# Low-Level Design Document

## Contents

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

## Device Inventory

The following table lists all network and server devices included in the enterprise infrastructure.

| Hostname | Device Model | Layer | Primary Role | Management IP | Location |
|:---------|:-------------|:------|:-------------|:-------------|:---------|
| **EDGE-R1** | Cisco ISR 4331 | Edge | Internet Edge Router (BGP, NAT) | `10.10.99.1` | Data Center |
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
| **DIST** | Distribution Switch | `DIST-SW#` | `DIST-SW1` | Layer 3 distribution switch for inter-VLAN routing. |
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
| **CORE-R1** | G0/0 | **DIST-SW1** | G1/0/1 | Layer 3 Link | Core to Distribution |
| **CORE-R2** | G0/0 | **DIST-SW1** | G1/0/2 | Layer 3 Link | Core to Distribution |
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
| **Core Links** | Routed Layer 3 point-to-point connections. |
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
| **EDGE-R1** | G0/0 | ISP Router | `203.0.113.2/30` | N/A | Internet uplink |
| **EDGE-R1** | G0/1 | CORE-R1 | `10.10.250.1/30` | N/A | Primary WAN link |
| **EDGE-R1** | G0/2 | CORE-R2 | `10.10.250.5/30` | N/A | Secondary WAN link |
| **CORE-R1** | G0/0 | DIST-SW1 | `10.10.251.1/30` | N/A | Layer 3 uplink |
| **CORE-R1** | G0/1 | EDGE-R1 | `10.10.250.2/30` | N/A | WAN connection |
| **CORE-R2** | G0/0 | DIST-SW1 | `10.10.251.5/30` | N/A | Layer 3 uplink |
| **CORE-R2** | G0/1 | EDGE-R1 | `10.10.250.6/30` | N/A | WAN connection |
| **DIST-SW1** | G1/0/1 | CORE-R1 | `10.10.251.2/30` | N/A | Routed uplink |
| **DIST-SW1** | G1/0/2 | CORE-R2 | `10.10.251.6/30` | N/A | Routed uplink |
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
| **Core Links** | Routed Layer 3 connections between the Core and Distribution layers. |
| **Switch Uplinks** | IEEE 802.1Q trunk interfaces carrying multiple VLANs. |
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
| **Trunk Links** | Distribution ↔ Access |

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


