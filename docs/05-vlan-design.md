# VLAN Design Document

## <a id="contents"></a>Contents
<p align="left">
  
<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#vlan-design-purposes"><img src="https://img.shields.io/badge/VLAN%20PURPOSES-27AE60?style=for-the-badge"></a>
<a href="#vlan-design-principles"><img src="https://img.shields.io/badge/VLAN%20PRINCIPLES-16A085?style=for-the-badge"></a>
<a href="#vlan-summary"><img src="https://img.shields.io/badge/VLAN%20SUMMARY-8E44AD?style=for-the-badge"></a>
<a href="#vlan-to-department-mapping"><img src="https://img.shields.io/badge/VLAN%20MAPPING-2980B9?style=for-the-badge"></a>
<a href="#vlan-security-policy"><img src="https://img.shields.io/badge/SECURITY%20POLICY-E67E22?style=for-the-badge"></a>
<a href="#trunk-design"><img src="https://img.shields.io/badge/TRUNK%20DESIGN-34495E?style=for-the-badge"></a>
<a href="#access-port-design"><img src="https://img.shields.io/badge/ACCESS%20PORTS-2C3E50?style=for-the-badge"></a>
<a href="#native-vlan-design"><img src="https://img.shields.io/badge/NATIVE%20VLAN-C0392B?style=for-the-badge"></a>
<a href="#vlan-implementation-notes"><img src="https://img.shields.io/badge/IMPLEMENTATION%20NOTES-7F8C8D?style=for-the-badge"></a>
<a href="#verification-requirements"><img src="https://img.shields.io/badge/VERIFICATION-1ABC9C?style=for-the-badge"></a>
<a href="#verification-commands"><img src="https://img.shields.io/badge/COMMANDS-95A5A6?style=for-the-badge"></a>

</p>

## Overview
VLAN architecture is designed to logically separate business departments, reduce broadcast traffic, improve security, simplify troubleshooting, and support future expansion for Verra Technology Inc.
## VLAN Design Purposes

| ID | Purposes |
|:---:|:----------|
| **VLAN-P-001** | Segment business departments into separate broadcast domains. |
| **VLAN-P-002** | Improve security by limiting lateral movement between departments. |
| **VLAN-P-003** | Support inter-VLAN routing through redundant core gateways. |
| **VLAN-P-004** | Isolate management traffic from user traffic. |
| **VLAN-P-005** | Reserve an unused native VLAN to reduce VLAN hopping risk. |
| **VLAN-P-006** | Provide a scalable VLAN structure for future growth. |

## VLAN Design Principles

| ID | Objective |
|:---:|:----------|
| **VLAN-OBJ-001** | Segment business departments into separate broadcast domains. |
| **VLAN-OBJ-002** | Improve security by limiting lateral movement between departments. |
| **VLAN-OBJ-003** | Support inter-VLAN routing through redundant core gateways. |
| **VLAN-OBJ-004** | Isolate management traffic from user traffic. |
| **VLAN-OBJ-005** | Reserve an unused native VLAN to reduce VLAN hopping risk. |
| **VLAN-OBJ-006** | Provide a scalable VLAN structure for future growth. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## VLAN Summary

| VLAN ID | VLAN Name | Subnet | Gateway | Description | Criticality |
|:-------:|:----------|:-------|:--------|:--------|:-----------:|
| **10** | HR | `10.10.10.0/24` | `10.10.10.1` | Human Resources users | ![](https://img.shields.io/badge/MEDIUM-F1C40F?style=flat-square) |
| **20** | IT | `10.10.20.0/24` | `10.10.20.1` | IT support and administration | ![](https://img.shields.io/badge/HIGH-E67E22?style=flat-square) |
| **30** | Finance | `10.10.30.0/24` | `10.10.30.1` | Accounting and payroll systems | ![](https://img.shields.io/badge/CRITICAL-E74C3C?style=flat-square) |
| **40** | Sales | `10.10.40.0/24` | `10.10.40.1` | Sales and business operations | ![](https://img.shields.io/badge/MEDIUM-F1C40F?style=flat-square) |
| **50** | Servers | `10.10.50.0/24` | `10.10.50.1` | Centralized infrastructure services | ![](https://img.shields.io/badge/CRITICAL-E74C3C?style=flat-square) |
| **99** | Management | `10.10.99.0/24` | `10.10.99.1` | Network device management | ![](https://img.shields.io/badge/CRITICAL-E74C3C?style=flat-square) |
| **999** | Native-BlackHole | `N/A` | `N/A` | Unused native VLAN | ![](https://img.shields.io/badge/HIGH-E67E22?style=flat-square) |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## VLAN to Department Mapping

| Department / Function | VLAN ID | VLAN Name | Typical Devices | Primary Users |
|:----------------------|:-------:|:----------|:----------------|:--------------|
| Human Resources | **10** | HR | Desktop PCs, Printers | HR staff |
| Information Technology | **20** | IT | Admin PCs, Laptops, Test Devices | IT administrators and support staff |
| Finance | **30** | Finance | Desktop PCs, Financial Workstations | Finance and payroll users |
| Sales | **40** | Sales | Desktop PCs, Laptops | Sales staff |
| Server Infrastructure | **50** | Servers | Windows Server, Linux Server, DHCP, DNS, Syslog | Enterprise services |
| Network Management | **99** | Management | Switches, Routers, Firewall, Management PCs | Network administrators |
| Native / Unused Traffic | **999** | Native-BlackHole | None | No user assignment |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## VLAN Security Policy

| VLAN | Security Requirement | Access Control Approach |
|:----:|----------------------|-------------------------|
| **10 HR** | Protect employee information | Deny direct access to Finance; allow required server services |
| **20 IT** | Support administration | Allow administrative access to infrastructure devices and internal VLANs |
| **30 Finance** | Protect payroll and financial data | Restrict access from non-authorized VLANs |
| **40 Sales** | Support business applications | Allow only approved access to server services |
| **50 Servers** | Protect centralized services | Allow access only from authorized VLANs and services |
| **99 Management** | Protect infrastructure management | Allow only IT and management workstations |
| **999 Native-BlackHole** | Prevent user traffic | No access ports assigned |

## Trunk Design

Trunk links are used between Core, Distribution, and Access switches to carry multiple VLANs across the enterprise network.

| Trunk Link | Native VLAN | Allowed VLANs | Purpose |
|:-----------|:-----------:|---------------|---------|
| CORE-R1 ↔ DIST-SW1 | 999 | 10,20,30,40,50,99 | Carries enterprise VLANs to core gateway |
| CORE-R2 ↔ DIST-SW1 | 999 | 10,20,30,40,50,99 | Carries enterprise VLANs to redundant core gateway |
| DIST-SW1 ↔ HR-SW01 | 999 | 10,99 | HR access and management |
| DIST-SW1 ↔ IT-SW01 | 999 | 20,99 | IT access and management |
| DIST-SW1 ↔ FIN-SW01 | 999 | 30,99 | Finance access and management |
| DIST-SW1 ↔ SALES-SW01 | 999 | 40,99 | Sales access and management |
| DIST-SW1 ↔ SRV-SW01 | 999 | 50,99 | Server and management VLANs |

### Trunk Security Standards

| Control | Design Decision |
|:--------|:----------------|
| DTP | Disabled |
| Native VLAN | VLAN 999 |
| Allowed VLANs | Explicitly configured |
| Unused VLANs | Not allowed on trunks |
| User Access | No end-user devices connected to trunk ports |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Access Port Design

Access ports are assigned to a single VLAN based on the department or function of the connected endpoint.

| Switch | Access Port Range | Assigned VLAN | Endpoint Type |
|:-------|-------------------|:-------------:|---------------|
| HR-SW01 | Fa0/1 - Fa0/20 | 10 | HR workstations |
| IT-SW01 | Fa0/1 - Fa0/20 | 20 | IT workstations |
| FIN-SW01 | Fa0/1 - Fa0/20 | 30 | Finance workstations |
| SALES-SW01 | Fa0/1 - Fa0/20 | 40 | Sales workstations |
| SRV-SW01 | Fa0/1 - Fa0/10 | 50 | Servers |
| All Access Switches | Fa0/21 - Fa0/24 | Shutdown | Reserved / unused |

### Access Port Security Standards

| Feature | Design Decision |
|:--------|:----------------|
| PortFast | Enabled on access ports |
| BPDU Guard | Enabled on access ports |
| Port Security | Enabled on user-facing ports |
| Maximum MAC Addresses | 2 |
| Violation Mode | Restrict |
| Unused Ports | Administratively shutdown |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Native VLAN Design

VLAN 999 is configured as an unused native VLAN on all trunk links.

| Item | Design |
|:-----|:-------|
| Native VLAN | 999 |
| VLAN Name | Native-BlackHole |
| User Ports | None |
| DHCP | Not configured |
| Gateway | Not configured |
| Purpose | Isolate untagged traffic and reduce VLAN hopping risk |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## VLAN Implementation Notes

| Item | Notes |
|:-----|:------|
| VLAN Creation | VLANs are created consistently on Core, Distribution, and Access switches where required. |
| VLAN Naming | VLAN names must match the documented naming convention. |
| Trunk Configuration | Only required VLANs are allowed on each trunk. |
| Access Port Assignment | End-user ports are assigned based on department. |
| Management Access | Switch management interfaces use VLAN 99. |
| Native VLAN | VLAN 999 must not be used for end-user traffic. |

## Verification Requirements

| ID | Verification Item | Expected Result |
|:---:|------------------|-----------------|
| **VLAN-VER-001** | Verify VLAN database | VLANs 10,20,30,40,50,99,999 exist |
| **VLAN-VER-002** | Verify trunk links | Correct allowed VLANs are present |
| **VLAN-VER-003** | Verify native VLAN | Native VLAN is set to 999 |
| **VLAN-VER-004** | Verify access ports | Ports are assigned to correct VLANs |
| **VLAN-VER-005** | Verify unused ports | Unused ports are administratively shutdown |
| **VLAN-VER-006** | Verify PortFast | PortFast enabled only on access ports |
| **VLAN-VER-007** | Verify BPDU Guard | BPDU Guard enabled on access ports |
| **VLAN-VER-008** | Verify management VLAN | Switch management reachable through VLAN 99 |

### Verification Commands

> - show vlan brief
> - show interfaces trunk
> - show spanning-tree vlan 10
> - show spanning-tree summary
> - show port-security interface
> - show running-config interface


<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
