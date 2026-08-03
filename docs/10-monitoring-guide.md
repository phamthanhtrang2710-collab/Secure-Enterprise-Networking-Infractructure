# Network Monitoring Guide

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#monitoring-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-27AE60?style=for-the-badge"></a>
<a href="#monitoring-architecture"><img src="https://img.shields.io/badge/ARCHITECTURE-8E44AD?style=for-the-badge"></a>
<a href="#monitoring-scope"><img src="https://img.shields.io/badge/SCOPE-16A085?style=for-the-badge"></a>
<a href="#monitoring-platform"><img src="https://img.shields.io/badge/PLATFORM-2980B9?style=for-the-badge"></a>
<a href="#monitored-devices"><img src="https://img.shields.io/badge/DEVICES-3498DB?style=for-the-badge"></a>
<a href="#monitoring-protocols"><img src="https://img.shields.io/badge/PROTOCOLS-E67E22?style=for-the-badge"></a>
<a href="#monitoring-metrics"><img src="https://img.shields.io/badge/METRICS-D35400?style=for-the-badge"></a>
<a href="#alert-severity-model"><img src="https://img.shields.io/badge/SEVERITY-C0392B?style=for-the-badge"></a>
<a href="#alert-thresholds"><img src="https://img.shields.io/badge/THRESHOLDS-9B59B6?style=for-the-badge"></a>
<a href="#dashboard-design"><img src="https://img.shields.io/badge/DASHBOARDS-E74C3C?style=for-the-badge"></a>
<a href="#syslog-integration"><img src="https://img.shields.io/badge/SYSLOG-1ABC9C?style=for-the-badge"></a>
<a href="#snmp-configuration-standard"><img src="https://img.shields.io/badge/SNMP-2ECC71?style=for-the-badge"></a>
<a href="#device-onboarding"><img src="https://img.shields.io/badge/ONBOARDING-34495E?style=for-the-badge"></a>
<a href="#monitoring-workflow"><img src="https://img.shields.io/badge/WORKFLOW-7F8C8D?style=for-the-badge"></a>
<a href="#alert-response-procedure"><img src="https://img.shields.io/badge/RESPONSE-2C3E50?style=for-the-badge"></a>
<a href="#monitoring-verification"><img src="https://img.shields.io/badge/VERIFICATION-95A5A6?style=for-the-badge"></a>
<a href="#monitoring-maintenance"><img src="https://img.shields.io/badge/MAINTENANCE-8E44AD?style=for-the-badge"></a>
<a href="#monitoring-security"><img src="https://img.shields.io/badge/SECURITY-27AE60?style=for-the-badge"></a>
<a href="#limitations-and-future-enhancements"><img src="https://img.shields.io/badge/FUTURE-0B8FD3?style=for-the-badge"></a>
<a href="#summary"><img src="https://img.shields.io/badge/SUMMARY-2C3E50?style=for-the-badge"></a>
<a href="#glossary"><img src="https://img.shields.io/badge/GLOSSARY-16A085?style=for-the-badge"></a>
<a href="#references"><img src="https://img.shields.io/badge/REFERENCES-27AE60?style=for-the-badge"></a>

</p>

## Overview

This document defines the monitoring implementation and operational procedures for the Secure Enterprise Network Infrastructure project.

The monitoring solution provides centralized visibility into routers, switches, servers, network interfaces, routing protocols, infrastructure services, and security events.

LibreNMS is selected as the primary monitoring platform because it provides automated network discovery, SNMP-based performance monitoring, interface graphs, device health visibility, alerting, and historical reporting.

Centralized Syslog is implemented on **LNX-SRV01** to collect operational and security events from network infrastructure devices.

This document complements the High-Level Design, Low-Level Design, Security Design, Test Plan, and Troubleshooting Guide by describing how monitoring services are deployed, verified, maintained, and used during incident response.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Objectives

| ID | Monitoring Objective |
|:---:|:---------------------|
| **MON-OBJ-001** | Provide centralized visibility into all enterprise network devices. |
| **MON-OBJ-002** | Detect device, interface, routing, and infrastructure service failures. |
| **MON-OBJ-003** | Collect historical performance data for capacity planning and troubleshooting. |
| **MON-OBJ-004** | Generate alerts when predefined operational thresholds are exceeded. |
| **MON-OBJ-005** | Centralize network and security event logs for auditing and incident analysis. |
| **MON-OBJ-006** | Monitor OSPF, BGP, HSRP, VLAN, and interface operational status. |
| **MON-OBJ-007** | Reduce fault detection time and support faster incident response. |
| **MON-OBJ-008** | Protect monitoring access through secure management protocols and access controls. |
| **MON-OBJ-009** | Maintain accurate monitoring records for all infrastructure devices. |
| **MON-OBJ-010** | Support future integration with email, ticketing, and automated remediation systems. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Architecture

The monitoring architecture uses a centralized monitoring model. Network devices expose operational metrics through SNMP and forward event messages to the centralized Syslog service.

The monitoring server polls infrastructure devices through their Management VLAN addresses. ICMP is used for availability testing, while SNMP provides performance and device health information.

### Monitoring Architecture Components

| Component | Host / Platform | Function |
|:----------|:----------------|:---------|
| **Monitoring Platform** | LibreNMS on LNX-SRV01 | Device polling, dashboards, alerting, and historical reporting |
| **Syslog Server** | LNX-SRV01 | Centralized collection of operational and security logs |
| **Managed Network** | VLAN 99 | Secure path to infrastructure management interfaces |
| **Time Source** | WIN-SRV01 | NTP synchronization for devices and servers |
| **Network Devices** | Cisco Routers and Switches | Provide SNMP metrics and Syslog events |
| **Windows Server** | WIN-SRV01 | DHCP, DNS, NTP, and server health monitoring |
| **Linux Server** | LNX-SRV01 | Monitoring platform, Syslog, and web service monitoring |

### Monitoring Traffic Flow

```text
Routers / Switches
        │
        ├──────── SNMP Polling ────────┐
        │                              │
        └──────── Syslog Events ───────┤
                                       ▼
                                  LNX-SRV01
                               LibreNMS + Syslog
                                       │
                                       ▼
                            Dashboards, Graphs & Alerts
```

### Architectural Principles

| Principle | Implementation |
|:----------|:---------------|
| **Centralized Monitoring** | Infrastructure visibility is consolidated on LNX-SRV01. |
| **Management Isolation** | Devices are monitored through Management VLAN 99. |
| **Secure Polling** | SNMPv3 is preferred for monitoring communication. |
| **Consistent Time** | NTP provides accurate timestamps across monitored devices. |
| **Event Correlation** | SNMP alerts and Syslog messages are reviewed together. |
| **Historical Visibility** | Performance metrics are retained for trend analysis. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Scope

The monitoring solution covers infrastructure components that directly affect enterprise connectivity, security, availability, and centralized services.

### In-Scope Monitoring

| Scope Area | Monitoring Coverage |
|:-----------|:--------------------|
| **Device Availability** | Router, switch, and server reachability |
| **Physical Interfaces** | Link status, utilization, errors, discards, and packet rates |
| **Device Resources** | CPU, memory, uptime, and temperature where supported |
| **Layer 2 Services** | VLAN state, trunk status, STP state, and port security events |
| **Layer 3 Services** | Routing table health, OSPF neighbors, BGP peering, and HSRP state |
| **Infrastructure Services** | DHCP, DNS, NTP, Syslog, and SNMP availability |
| **Security Events** | Login failures, ACL events, port security violations, and configuration changes |
| **Server Health** | CPU, memory, disk utilization, and service status |
| **Internet Edge** | EDGE-R1 availability, ISP interface status, and external reachability |
| **Configuration Events** | Device reloads, interface changes, and administrative configuration activity |

### Out-of-Scope Monitoring

| Item | Reason |
|:-----|:-------|
| Application Performance Monitoring | Not required for the initial networking portfolio scope |
| Cloud Infrastructure Monitoring | Cloud services are outside the current project scope |
| Wireless Controller Monitoring | Wireless controller deployment is not included |
| Endpoint Detection and Response | Requires a dedicated endpoint security platform |
| Advanced Flow Analytics | NetFlow or similar technologies are reserved for future enhancement |
| SIEM Integration | Enterprise SIEM deployment is outside the initial lab scope |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Platform

LibreNMS is selected as the primary network monitoring platform for the initial implementation.

### Platform Selection

| Item | Design Decision |
|:-----|:----------------|
| **Primary Platform** | LibreNMS |
| **Deployment Host** | LNX-SRV01 |
| **Operating System** | Ubuntu Server 24.04 LTS |
| **Device Polling** | SNMP |
| **Availability Testing** | ICMP |
| **Log Collection** | Syslog |
| **Dashboard Access** | HTTPS Web Interface |
| **Alternative Platform** | Zabbix |
| **Alternative Status** | Optional / Future Enhancement |

### LibreNMS Functions

| Function | Purpose |
|:---------|:--------|
| Automated Discovery | Identifies and adds supported infrastructure devices |
| Device Health | Displays availability, uptime, CPU, and memory |
| Interface Monitoring | Tracks utilization, errors, and operational status |
| Historical Graphing | Provides performance trends over time |
| Alerting | Generates alerts when thresholds or state conditions are triggered |
| Inventory | Records hardware, software, interface, and device information |
| Event Logs | Displays device state changes and monitoring events |
| Service Monitoring | Validates infrastructure service availability |

### Platform Design Notes

- LibreNMS is the authoritative monitoring platform for this project.
- Zabbix should not be deployed simultaneously unless a specific comparative lab is created.
- Monitoring data is collected from Management VLAN 99 whenever the device supports a dedicated management address.
- Server monitoring may use SNMP, LibreNMS agents, or service checks depending on platform support.
- Monitoring access should be restricted to authorized IT and Management VLAN users.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitored Devices

### Network Device Inventory

| Hostname | Device Role | Management IP | Monitoring Method |
|:---------|:------------|:--------------|:------------------|
| **EDGE-R1** | Internet Edge Router | `10.10.99.4` | ICMP, SNMP, Syslog |
| **CORE-R1** | Primary Core Router | `10.10.99.2` | ICMP, SNMP, Syslog |
| **CORE-R2** | Secondary Core Router | `10.10.99.3` | ICMP, SNMP, Syslog |
| **DIST-SW1** | Distribution Switch | `10.10.99.10` | ICMP, SNMP, Syslog |
| **HR-SW01** | HR Access Switch | `10.10.99.21` | ICMP, SNMP, Syslog |
| **IT-SW01** | IT Access Switch | `10.10.99.22` | ICMP, SNMP, Syslog |
| **FIN-SW01** | Finance Access Switch | `10.10.99.23` | ICMP, SNMP, Syslog |
| **SALES-SW01** | Sales Access Switch | `10.10.99.24` | ICMP, SNMP, Syslog |
| **SRV-SW01** | Server Access Switch | `10.10.99.25` | ICMP, SNMP, Syslog |

### Server Monitoring Inventory

| Hostname | Platform | IP Address | Monitoring Method |
|:---------|:---------|:-----------|:------------------|
| **WIN-SRV01** | Windows Server 2022 | `10.10.50.10` | ICMP, SNMP / Agent, Service Checks |
| **LNX-SRV01** | Ubuntu Server 24.04 LTS | `10.10.50.20` | ICMP, SNMP / Agent, Local Service Monitoring |

### Monitoring Priority

| Device Category | Priority | Reason |
|:----------------|:--------:|:-------|
| Edge Router | Critical | Required for Internet access |
| Core Routers | Critical | Required for routing and default gateway availability |
| Distribution Switch | Critical | Aggregates all access switches |
| Server Access Switch | Critical | Provides connectivity to infrastructure servers |
| Access Switches | High | Support departmental user connectivity |
| Windows Server | Critical | Provides DHCP, DNS, and NTP |
| Linux Server | Critical | Hosts monitoring and centralized logging |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Protocols

| Protocol | Port / Transport | Function | Security Requirement |
|:---------|:-----------------|:---------|:---------------------|
| **ICMP** | IP Protocol | Device reachability and latency testing | Restricted to authorized monitoring systems |
| **SNMPv3** | UDP 161 | Secure device polling and performance collection | Authentication and privacy enabled |
| **SNMP Traps** | UDP 162 | Unsolicited device notifications | Sent only to the monitoring server |
| **Syslog** | UDP 514 | Centralized event collection | Restricted to LNX-SRV01 |
| **SSH** | TCP 22 | Secure administration and troubleshooting | Management VLAN only |
| **HTTPS** | TCP 443 | Secure LibreNMS dashboard access | Authorized administrators only |
| **NTP** | UDP 123 | Time synchronization | Internal trusted time source |
| **DNS** | UDP/TCP 53 | Monitoring hostname resolution | Internal DNS service |

### Protocol Preference

| Function | Preferred Protocol | Lab Fallback |
|:---------|:-------------------|:-------------|
| Device Monitoring | SNMPv3 | SNMPv2c |
| Dashboard Access | HTTPS | HTTP only during initial installation |
| Device Management | SSH Version 2 | No Telnet fallback |
| Log Collection | Syslog | Local device logs |
| Time Synchronization | NTP | Manual verification only |

> **Security Note**
>
> SNMPv2c may be used only when the lab platform does not support SNMPv3. Community strings must not be published in screenshots, configuration examples, or the public GitHub repository.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Metrics

### Router Metrics

| Metric | Purpose |
|:-------|:--------|
| Device Availability | Detects router outages |
| CPU Utilization | Identifies processing overload |
| Memory Utilization | Detects resource exhaustion |
| Interface Status | Detects link failures |
| Interface Utilization | Identifies congestion |
| Interface Errors | Detects physical or data-link problems |
| OSPF Neighbor Status | Detects internal routing failures |
| BGP Peer Status | Detects ISP routing failure |
| HSRP State | Detects gateway role changes |
| Uptime | Identifies reloads and unexpected restarts |

### Switch Metrics

| Metric | Purpose |
|:-------|:--------|
| Device Availability | Detects switch outages |
| Interface Status | Detects endpoint or uplink failures |
| Interface Utilization | Identifies congested ports |
| CRC and Input Errors | Identifies cabling or duplex problems |
| VLAN Status | Detects VLAN availability problems |
| Trunk Status | Detects uplink and VLAN propagation issues |
| STP Events | Identifies topology changes |
| Port Security Events | Detects unauthorized endpoint activity |
| CPU and Memory | Detects resource pressure |

### Server Metrics

| Metric | Purpose |
|:-------|:--------|
| Server Availability | Detects server outages |
| CPU Utilization | Identifies processing pressure |
| Memory Utilization | Detects memory exhaustion |
| Disk Utilization | Prevents service failure caused by full storage |
| Service Status | Validates DHCP, DNS, NTP, Syslog, and monitoring services |
| Network Interface Status | Detects connectivity problems |
| System Uptime | Identifies unexpected restart events |

### Infrastructure Service Metrics

| Service | Monitoring Method | Expected State |
|:--------|:------------------|:---------------|
| DHCP | Service Check | Running |
| DNS | Query Test | Internal and external resolution successful |
| NTP | Synchronization Check | Devices synchronized |
| Syslog | Log Reception Test | Messages received |
| SNMP | Polling Test | Device responds |
| LibreNMS Web Interface | HTTPS Check | Accessible |
| Database Service | Local Service Check | Running |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Alert Severity Model

Alerts are categorized according to their operational impact and required response time.

| Severity | Description | Example | Response Priority |
|:--------:|:------------|:--------|:-----------------:|
| **Critical** | Major service outage or loss of a business-critical device | Core router, edge router, or monitoring server unavailable | Immediate |
| **High** | Significant degradation affecting multiple users or an important service | Distribution switch down or OSPF adjacency lost | Urgent |
| **Medium** | Partial degradation or threshold warning | High CPU, interface utilization, or access switch failure | Scheduled investigation |
| **Low** | Informational event with limited operational impact | Interface flap on an unused port | Review |
| **Informational** | Normal operational or administrative event | Configuration saved or device rediscovered | No immediate action |

### Severity Assignment

| Event | Severity |
|:------|:--------:|
| EDGE-R1 Unreachable | Critical |
| CORE-R1 and CORE-R2 Unreachable | Critical |
| One Core Router Unreachable | High |
| DIST-SW1 Unreachable | Critical |
| Access Switch Unreachable | High |
| OSPF Neighbor Down | High |
| BGP Session Down | Critical |
| HSRP State Change | High |
| DHCP or DNS Service Down | Critical |
| Syslog Service Down | High |
| SNMP Polling Failure | Medium |
| CPU Above Threshold | Medium |
| Interface Utilization Above Threshold | Medium |
| Port Security Violation | High |
| Configuration Change | Informational |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Alert Thresholds

| Metric | Warning Threshold | Critical Threshold | Recommended Action |
|:-------|:-----------------:|:------------------:|:-------------------|
| Device Availability | N/A | Unreachable | Validate device and network path |
| CPU Utilization | `> 80%` for 5 minutes | `> 90%` for 5 minutes | Identify high CPU process |
| Memory Utilization | `> 85%` | `> 95%` | Review memory consumption |
| Interface Utilization | `> 80%` for 10 minutes | `> 95%` for 5 minutes | Investigate congestion |
| Packet Loss | `> 1%` | `> 5%` | Investigate path and interface health |
| Interface Errors | Increasing errors | Sustained rapid increase | Check cabling, speed, and duplex |
| Disk Utilization | `> 80%` | `> 90%` | Free storage or expand disk |
| OSPF Neighbor | State change | Neighbor down | Review OSPF and link status |
| BGP Neighbor | State change | Session not Established | Verify ISP path and BGP configuration |
| HSRP | Role change | No active gateway | Verify core router and tracked interfaces |
| DNS Query | Slow or intermittent | Query failure | Check DNS service and forwarding |
| DHCP Service | Reduced capacity | Service unavailable | Verify scope and Windows DHCP service |
| Syslog Reception | No recent events | Service unavailable | Verify rsyslog and network access |

> **Threshold Note**
>
> Thresholds are initial operational baselines for the lab environment. They should be reviewed after sufficient historical monitoring data has been collected.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Dashboard Design

LibreNMS dashboards should present the most important operational information without requiring administrators to navigate through multiple pages.

### Primary Dashboard

| Dashboard Element | Purpose |
|:------------------|:--------|
| Device Availability Summary | Displays up, down, and unreachable devices |
| Critical Alerts | Highlights active high-impact incidents |
| Core and Edge Status | Displays EDGE-R1, CORE-R1, and CORE-R2 health |
| Interface Utilization | Identifies high-traffic interfaces |
| Recent Syslog Events | Displays current operational and security events |
| CPU and Memory Summary | Identifies infrastructure resource pressure |
| Service Availability | Displays DHCP, DNS, NTP, Syslog, and monitoring service status |

### Routing Dashboard

| Widget | Purpose |
|:-------|:--------|
| OSPF Neighbor Status | Identifies internal routing adjacency failures |
| BGP Session Status | Displays ISP peering state |
| HSRP State | Displays active and standby gateway roles |
| Routed Link Utilization | Monitors EDGE-R1 to CORE-R1 and CORE-R2 links |
| Internet Uplink Status | Displays ISP interface availability |

### Switching Dashboard

| Widget | Purpose |
|:-------|:--------|
| Access Switch Availability | Displays departmental switch status |
| Trunk Interface Status | Identifies failed uplinks |
| Interface Errors | Highlights CRC, input, and output errors |
| STP Events | Displays topology changes |
| Port Security Events | Highlights unauthorized endpoint activity |

### Server and Services Dashboard

| Widget | Purpose |
|:-------|:--------|
| WIN-SRV01 Availability | Monitors DHCP, DNS, and NTP host |
| LNX-SRV01 Availability | Monitors LibreNMS and Syslog host |
| Disk Utilization | Prevents storage exhaustion |
| Service Status | Displays critical infrastructure services |
| DNS and DHCP Health | Validates addressing and name-resolution services |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Syslog Integration

Network devices forward operational and security events to **LNX-SRV01 (`10.10.50.20`)**.

### Syslog Sources

| Source | Events Collected |
|:-------|:-----------------|
| EDGE-R1 | BGP, NAT, interface, login, and perimeter events |
| CORE-R1 / CORE-R2 | OSPF, HSRP, ACL, interface, and configuration events |
| DIST-SW1 | Trunk, STP, VLAN, and interface events |
| Access Switches | Port Security, BPDU Guard, interface, and login events |
| WIN-SRV01 | DHCP, DNS, NTP, and Windows system events |
| LNX-SRV01 | Monitoring, Syslog, web, and Linux system events |

### Recommended Syslog Events

| Event Category | Examples |
|:---------------|:---------|
| Interface Events | Link up, link down, interface flap |
| Routing Events | OSPF adjacency change, BGP session change |
| Redundancy Events | HSRP active or standby transition |
| Security Events | Login failure, ACL deny, Port Security violation |
| Configuration Events | Configuration entered, saved, or changed |
| System Events | Reload, process failure, memory warning |
| Infrastructure Services | DHCP, DNS, NTP, SNMP, and Syslog service events |

### Logging Severity

| Severity Level | Name | Collection Policy |
|:--------------:|:-----|:------------------|
| 0 | Emergency | Collect |
| 1 | Alert | Collect |
| 2 | Critical | Collect |
| 3 | Error | Collect |
| 4 | Warning | Collect |
| 5 | Notification | Collect |
| 6 | Informational | Collect where appropriate |
| 7 | Debugging | Enable temporarily during troubleshooting only |

### Syslog Design Standards

- All network devices must use LNX-SRV01 as the centralized Syslog destination.
- Device clocks must be synchronized through NTP.
- Debug-level logging must not remain enabled during normal operations.
- Log access must be restricted to authorized administrators.
- Log files should be rotated to prevent disk exhaustion.
- Critical logs should be retained as evidence for troubleshooting and auditing.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## SNMP Configuration Standard

SNMP provides operational and performance data to LibreNMS.

### SNMP Design

| Item | Standard |
|:-----|:---------|
| Preferred Version | SNMPv3 |
| Authentication | SHA or supported secure authentication |
| Privacy | AES or supported encryption |
| Monitoring Source | LNX-SRV01 |
| Device Access | Restricted through Management ACL |
| Read Access | Read-only |
| Write Access | Not permitted |
| Trap Destination | LNX-SRV01 |
| Fallback | SNMPv2c for unsupported lab devices only |

### SNMP Security Requirements

| Requirement | Implementation |
|:------------|:---------------|
| Read-Only Monitoring | Monitoring users cannot modify device configuration |
| Source Restriction | SNMP accepted only from the monitoring server |
| Credential Protection | Credentials excluded from public documentation |
| Version Control | SNMPv1 prohibited |
| Management Isolation | Monitoring traffic uses trusted internal paths |
| Auditability | Authentication failures recorded in Syslog |

### SNMPv2c Lab Fallback

When SNMPv3 is unavailable:

- Use a unique read-only community string.
- Restrict the community with an ACL.
- Do not use common values such as `public` or `private`.
- Do not publish the actual community string in GitHub.
- Replace SNMPv2c with SNMPv3 when platform support becomes available.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Device Onboarding

Every infrastructure device must follow a standardized onboarding process before it is considered fully monitored.

### Onboarding Procedure

| Step | Activity | Expected Result |
|:----:|:---------|:----------------|
| **1** | Assign and verify the management IP address | Device reachable through VLAN 99 |
| **2** | Configure hostname and DNS record | Device resolvable by hostname |
| **3** | Configure NTP | Device time synchronized |
| **4** | Configure SNMP | Monitoring server can poll the device |
| **5** | Configure Syslog | Device events reach LNX-SRV01 |
| **6** | Add the device to LibreNMS | Device appears in the platform |
| **7** | Verify interfaces and hardware inventory | Device data is complete |
| **8** | Assign device group and location | Device classified correctly |
| **9** | Apply alert rules | Operational alerts enabled |
| **10** | Perform a controlled failure test | Alert and recovery events recorded |
| **11** | Capture verification evidence | Screenshots and outputs saved |
| **12** | Update the monitoring inventory | Documentation completed |

### Device Classification

| Device Group | Members |
|:-------------|:--------|
| Internet Edge | EDGE-R1 |
| Core Network | CORE-R1, CORE-R2 |
| Distribution | DIST-SW1 |
| Access Layer | HR-SW01, IT-SW01, FIN-SW01, SALES-SW01, SRV-SW01 |
| Windows Servers | WIN-SRV01 |
| Linux Servers | LNX-SRV01 |

### Onboarding Acceptance Criteria

- Device responds to ICMP.
- SNMP polling succeeds.
- Syslog messages are received.
- Device time is synchronized.
- Interfaces appear correctly.
- Alert rules are assigned.
- Device metadata is documented.
- Monitoring verification tests pass.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Workflow

Monitoring follows a continuous operational cycle.

| Phase | Activity |
|:------|:---------|
| **Collect** | LibreNMS polls devices and receives events. |
| **Analyze** | Metrics are compared against thresholds and expected states. |
| **Alert** | Operationally significant conditions generate alerts. |
| **Investigate** | Administrators review dashboards, logs, and packet paths. |
| **Resolve** | Corrective action restores normal service. |
| **Validate** | Monitoring confirms that the alert condition has cleared. |
| **Document** | Incident details and preventive actions are recorded. |

### Event Correlation Workflow

```text
Monitoring Alert
       │
       ▼
Verify Device Reachability
       │
       ▼
Review SNMP Metrics
       │
       ▼
Review Syslog Events
       │
       ▼
Check Recent Changes
       │
       ▼
Follow Troubleshooting Guide
       │
       ▼
Restore and Validate Service
```

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Alert Response Procedure

### Alert Handling Process

| Step | Action |
|:----:|:-------|
| **1** | Confirm that the alert is active and not stale. |
| **2** | Verify reachability from the monitoring server. |
| **3** | Identify affected users, devices, and services. |
| **4** | Review related SNMP metrics and Syslog events. |
| **5** | Check for recent configuration or maintenance activity. |
| **6** | Assign an operational severity. |
| **7** | Follow the relevant Troubleshooting Guide procedure. |
| **8** | Apply corrective action or escalate the incident. |
| **9** | Verify recovery through monitoring and functional testing. |
| **10** | Document the incident and preventive recommendations. |

### Escalation Matrix

| Severity | Initial Owner | Escalation |
|:--------:|:--------------|:-----------|
| Critical | Network Administrator | Senior Network Engineer / ISP / Vendor |
| High | Network Administrator | Senior Network Engineer |
| Medium | NOC / Network Administrator | Escalate if unresolved |
| Low | NOC / Help Desk | Network Administrator if recurring |
| Informational | Operations Team | No escalation required |

### Alert Closure Requirements

An alert may be closed only when:

- The affected device or service has returned to normal operation.
- The monitoring platform confirms recovery.
- Related test cases have passed.
- The root cause has been identified or documented as unknown.
- Corrective action has been recorded.
- Required documentation has been updated.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Verification

### Verification Checklist

| ID | Verification Item | Expected Result | Status |
|:--:|:------------------|:----------------|:------:|
| **MON-VER-001** | Monitoring server reachability | LNX-SRV01 responds successfully | ☐ |
| **MON-VER-002** | LibreNMS dashboard | Dashboard accessible through web interface | ☐ |
| **MON-VER-003** | Network device discovery | All routers and switches visible | ☐ |
| **MON-VER-004** | Server discovery | WIN-SRV01 and LNX-SRV01 visible | ☐ |
| **MON-VER-005** | SNMP polling | Device metrics collected successfully | ☐ |
| **MON-VER-006** | Interface monitoring | Interface graphs and status available | ☐ |
| **MON-VER-007** | Syslog reception | Device logs received on LNX-SRV01 | ☐ |
| **MON-VER-008** | NTP synchronization | Device timestamps are consistent | ☐ |
| **MON-VER-009** | Device-down alert | Controlled shutdown generates an alert | ☐ |
| **MON-VER-010** | Recovery notification | Device recovery clears the alert | ☐ |
| **MON-VER-011** | CPU threshold | Controlled threshold condition generates warning | ☐ |
| **MON-VER-012** | Interface-down alert | Link failure is detected | ☐ |
| **MON-VER-013** | OSPF event monitoring | Neighbor change recorded | ☐ |
| **MON-VER-014** | BGP event monitoring | Peer state change recorded | ☐ |
| **MON-VER-015** | HSRP event monitoring | Active/Standby transition recorded | ☐ |
| **MON-VER-016** | Disk utilization | Server disk usage visible | ☐ |
| **MON-VER-017** | Service monitoring | DHCP, DNS, Syslog, and monitoring services visible | ☐ |
| **MON-VER-018** | Management access control | Unauthorized users cannot access dashboard | ☐ |

### Recommended Verification Commands

On Cisco devices:

```text
show snmp
show logging
show clock
show ntp status
show ntp associations
show ip interface brief
show interfaces
show ip ospf neighbor
show ip bgp summary
show standby brief
```

On LNX-SRV01:

```text
sudo systemctl status rsyslog
sudo systemctl status mariadb
sudo systemctl status apache2
sudo ss -lunp | grep 514
df -h
free -m
top
```

Optional SNMP validation:

```text
snmpwalk -v3 -l authPriv -u <username> <device-ip>
```

### Acceptance Criteria

| Category | Requirement |
|:---------|:------------|
| Device Visibility | All in-scope devices appear in LibreNMS |
| Availability Monitoring | Device state changes are detected |
| Performance Monitoring | CPU, memory, and interface metrics are collected |
| Logging | Syslog messages are centrally received |
| Alerting | Critical events generate alerts |
| Recovery | Cleared conditions update automatically |
| Security | Monitoring access is restricted |
| Documentation | Inventory and verification evidence are complete |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Maintenance

### Daily Tasks

| Task | Purpose |
|:-----|:--------|
| Review Critical Alerts | Identify active service-impacting issues |
| Check Device Availability | Confirm all infrastructure devices are reachable |
| Review Syslog Events | Identify failures and security events |
| Verify Monitoring Server Health | Confirm LibreNMS and database services are operational |

### Weekly Tasks

| Task | Purpose |
|:-----|:--------|
| Review Interface Utilization | Identify congestion trends |
| Review Interface Errors | Detect cabling or hardware problems |
| Review CPU and Memory Trends | Identify resource pressure |
| Verify Device Inventory | Detect unmanaged or removed devices |
| Test a Sample Alert | Validate alerting functionality |

### Monthly Tasks

| Task | Purpose |
|:-----|:--------|
| Review Alert Thresholds | Adjust thresholds using historical data |
| Review Storage Utilization | Prevent database or log disk exhaustion |
| Audit SNMP Access | Confirm only authorized sources can poll devices |
| Review Stale Devices | Remove decommissioned infrastructure |
| Generate Monitoring Report | Document availability and recurring issues |
| Back Up Monitoring Configuration | Support recovery after server failure |

### Maintenance Records

Monitoring maintenance records should include:

- Date and time
- Administrator
- Activity performed
- Devices affected
- Findings
- Corrective actions
- Configuration changes
- Verification results

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Security

The monitoring platform contains sensitive operational information and must be protected as a critical infrastructure service.

### Security Controls

| Control | Implementation |
|:--------|:---------------|
| Dashboard Authentication | Required |
| Dashboard Encryption | HTTPS |
| Administrative Access | Restricted to IT and Management users |
| Device Polling | SNMPv3 preferred |
| SNMP Permissions | Read-only |
| Management ACL | Allows monitoring traffic only from LNX-SRV01 |
| Server Administration | SSH restricted to authorized administrators |
| Telnet | Disabled |
| Log Access | Restricted |
| Configuration Backup | Required |
| Credential Publication | Prohibited |

### Monitoring Data Classification

| Data Type | Classification |
|:----------|:---------------|
| Device Inventory | Internal |
| IP Addresses | Internal |
| Interface Utilization | Internal |
| Routing Status | Sensitive Internal |
| Security Events | Sensitive Internal |
| SNMP Credentials | Confidential |
| Administrator Credentials | Confidential |
| Syslog Records | Sensitive Internal |

### GitHub Publication Rules

The following information must not be committed to the public repository:

- SNMP authentication passwords
- SNMP privacy passwords
- Community strings
- Server account passwords
- Database credentials
- SSH private keys
- LibreNMS administrator credentials
- Publicly usable API tokens
- Unredacted configuration files containing secrets

Screenshots must be reviewed and redacted before publication.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Limitations and Future Enhancements

### Current Limitations

| Limitation | Impact |
|:-----------|:-------|
| Simulated Lab Environment | Some metrics may not reflect physical hardware behavior |
| Single Monitoring Server | Monitoring becomes unavailable if LNX-SRV01 fails |
| Limited Notification Integration | Email and ticketing may not be implemented initially |
| SNMPv2c Fallback | Some lab devices may not support SNMPv3 |
| No NetFlow | Detailed traffic-flow analytics are unavailable |
| No SIEM | Security event correlation remains limited |
| Limited Application Monitoring | Focus remains on infrastructure services |

### Future Enhancements

| Enhancement | Benefit |
|:------------|:--------|
| Secondary Monitoring Server | Improves monitoring availability |
| Zabbix Comparative Deployment | Demonstrates alternative monitoring architecture |
| Email Notifications | Provides proactive administrator alerts |
| Ticketing Integration | Automatically creates operational incidents |
| NetFlow / IPFIX | Provides detailed traffic-flow analysis |
| SIEM Integration | Improves security event correlation |
| Automated Remediation | Executes controlled responses to known incidents |
| API-Based Reporting | Automates health and availability reports |
| External Status Checks | Validates services from outside the enterprise network |
| Backup Monitoring | Protects LibreNMS configuration and database |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Summary

This Network Monitoring Guide defines the implementation and operational framework used to monitor the Secure Enterprise Network Infrastructure.

LibreNMS provides centralized device discovery, availability monitoring, performance graphing, inventory management, and alerting. LNX-SRV01 also provides centralized Syslog collection for operational and security events.

The design combines ICMP, SNMP, Syslog, NTP, and service checks to provide visibility across the Internet Edge, Core, Distribution, Access, Server, and Management layers.

Standardized onboarding, alert handling, verification, maintenance, and security procedures ensure that monitoring remains accurate, secure, and operationally useful throughout the infrastructure lifecycle.

The monitoring system supports proactive fault detection, faster incident response, historical analysis, capacity planning, and evidence-based troubleshooting while providing a foundation for future enhancements such as email alerting, ticketing integration, NetFlow, SIEM, and automated remediation.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Glossary

| Acronym | Definition |
|:--------|:-----------|
| **ACL** | Access Control List |
| **API** | Application Programming Interface |
| **BGP** | Border Gateway Protocol |
| **CPU** | Central Processing Unit |
| **HSRP** | Hot Standby Router Protocol |
| **HTTPS** | Hypertext Transfer Protocol Secure |
| **ICMP** | Internet Control Message Protocol |
| **IPFIX** | IP Flow Information Export |
| **NMS** | Network Management System |
| **NTP** | Network Time Protocol |
| **OSPF** | Open Shortest Path First |
| **SIEM** | Security Information and Event Management |
| **SNMP** | Simple Network Management Protocol |
| **SSH** | Secure Shell |
| **Syslog** | System Logging Protocol |
| **VLAN** | Virtual Local Area Network |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
