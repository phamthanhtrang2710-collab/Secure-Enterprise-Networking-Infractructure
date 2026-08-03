# Backup and Recovery

## <a id="contents"></a>Contents

<p align="left">

<a href="#overview"><img src="https://img.shields.io/badge/OVERVIEW-0B8FD3?style=for-the-badge"></a>
<a href="#backup-objectives"><img src="https://img.shields.io/badge/OBJECTIVES-27AE60?style=for-the-badge"></a>
<a href="#backup-scope"><img src="https://img.shields.io/badge/SCOPE-8E44AD?style=for-the-badge"></a>
<a href="#backup-architecture"><img src="https://img.shields.io/badge/ARCHITECTURE-16A085?style=for-the-badge"></a>
<a href="#backup-inventory"><img src="https://img.shields.io/badge/INVENTORY-2980B9?style=for-the-badge"></a>
<a href="#backup-policy"><img src="https://img.shields.io/badge/POLICY-3498DB?style=for-the-badge"></a>
<a href="#backup-types"><img src="https://img.shields.io/badge/BACKUP%20TYPES-E67E22?style=for-the-badge"></a>
<a href="#backup-schedule"><img src="https://img.shields.io/badge/SCHEDULE-D35400?style=for-the-badge"></a>
<a href="#backup-storage-design"><img src="https://img.shields.io/badge/STORAGE-C0392B?style=for-the-badge"></a>
<a href="#file-naming-convention"><img src="https://img.shields.io/badge/NAMING-9B59B6?style=for-the-badge"></a>
<a href="#network-device-backup"><img src="https://img.shields.io/badge/NETWORK%20DEVICES-E74C3C?style=for-the-badge"></a>
<a href="#python-automation-design"><img src="https://img.shields.io/badge/PYTHON-1ABC9C?style=for-the-badge"></a>
<a href="#windows-server-backup"><img src="https://img.shields.io/badge/WINDOWS-2ECC71?style=for-the-badge"></a>
<a href="#linux-server-backup"><img src="https://img.shields.io/badge/LINUX-34495E?style=for-the-badge"></a>
<a href="#monitoring-platform-backup"><img src="https://img.shields.io/badge/LIBRENMS-7F8C8D?style=for-the-badge"></a>
<a href="#backup-security"><img src="https://img.shields.io/badge/SECURITY-2C3E50?style=for-the-badge"></a>
<a href="#backup-verification"><img src="https://img.shields.io/badge/VERIFICATION-95A5A6?style=for-the-badge"></a>
<a href="#recovery-strategy"><img src="https://img.shields.io/badge/RECOVERY-8E44AD?style=for-the-badge"></a>
<a href="#network-device-recovery"><img src="https://img.shields.io/badge/DEVICE%20RECOVERY-27AE60?style=for-the-badge"></a>
<a href="#server-recovery"><img src="https://img.shields.io/badge/SERVER%20RECOVERY-0B8FD3?style=for-the-badge"></a>
<a href="#disaster-recovery-scenarios"><img src="https://img.shields.io/badge/DR%20SCENARIOS-C0392B?style=for-the-badge"></a>
<a href="#recovery-verification"><img src="https://img.shields.io/badge/VALIDATION-16A085?style=for-the-badge"></a>
<a href="#backup-maintenance"><img src="https://img.shields.io/badge/MAINTENANCE-2980B9?style=for-the-badge"></a>
<a href="#limitations-and-future-enhancements"><img src="https://img.shields.io/badge/FUTURE-E67E22?style=for-the-badge"></a>
<a href="#summary"><img src="https://img.shields.io/badge/SUMMARY-2C3E50?style=for-the-badge"></a>

</p>

## Overview

This document defines the backup and recovery framework for the Secure Enterprise Network Infrastructure project.

The backup strategy protects network device configurations, Windows and Linux infrastructure services, monitoring data, operational scripts, and project documentation against accidental changes, configuration corruption, hardware failure, software failure, and administrative error.

Automated configuration backups are collected from Cisco routers and switches using Python and Netmiko. Server configuration and application data are protected using platform-appropriate backup methods.

The recovery procedures provide standardized steps for restoring network connectivity and infrastructure services to the last known stable state.

This document complements the Low-Level Design, Test Plan, Troubleshooting Guide, Monitoring Guide, and device implementation documentation.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Objectives

| ID | Backup Objective |
|:---:|:-----------------|
| **BKP-OBJ-001** | Protect network device configurations against loss or corruption. |
| **BKP-OBJ-002** | Automate recurring configuration backups using Python and Netmiko. |
| **BKP-OBJ-003** | Maintain recoverable copies of critical Windows and Linux service configurations. |
| **BKP-OBJ-004** | Support rapid rollback after failed implementation or security changes. |
| **BKP-OBJ-005** | Preserve historical configuration versions for auditing and troubleshooting. |
| **BKP-OBJ-006** | Verify that backup files are complete, readable, and usable. |
| **BKP-OBJ-007** | Protect backup data from unauthorized access and accidental publication. |
| **BKP-OBJ-008** | Provide repeatable recovery procedures for common failure scenarios. |
| **BKP-OBJ-009** | Minimize service restoration time following infrastructure failure. |
| **BKP-OBJ-010** | Support future off-site storage and disaster recovery improvements. |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Scope

### In-Scope Components

| Component | Backup Coverage |
|:----------|:----------------|
| Cisco Routers | Running configuration, startup configuration, and device information |
| Cisco Switches | Running configuration, startup configuration, VLAN information, and device information |
| Windows Server | DHCP, DNS, NTP-related settings, system state where supported, and selected service configuration |
| Linux Server | Syslog, LibreNMS, web server, database, scheduled tasks, and system configuration |
| Monitoring Platform | LibreNMS configuration, database, device records, and alert rules |
| Python Automation | Backup scripts, inventory files, templates, and documentation |
| Project Documentation | HLD, LLD, IP Plan, VLAN Design, Routing Design, Security Design, and operational guides |
| Evidence | Test outputs, validation reports, and approved screenshots |

### Out-of-Scope Components

| Component | Reason |
|:----------|:-------|
| Full End-User Device Backup | Outside the networking infrastructure scope |
| Enterprise Cloud Backup | Cloud services are not deployed in the initial lab |
| Geographic Disaster Recovery Site | Not available in the current simulated environment |
| Application Database Recovery | Business applications are outside the initial project scope |
| Tape Backup Infrastructure | Not required for the portfolio lab |
| Hardware Spare Management | Documented operationally but not physically implemented |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Architecture

The initial backup design uses **LNX-SRV01** as the centralized automation and backup host.

Network device configurations are collected over SSH through the Management VLAN. Server configuration backups are written to protected directories and may be copied to a secondary storage location.

### Backup Architecture Components

| Component | Host / Platform | Function |
|:----------|:----------------|:---------|
| **Automation Host** | LNX-SRV01 | Runs Python and Netmiko backup scripts |
| **Network Devices** | Routers and Switches | Provide configuration data through SSH |
| **Primary Backup Repository** | LNX-SRV01 local storage | Stores current and historical backup files |
| **Secondary Backup Repository** | External or secondary protected storage | Stores an additional recovery copy |
| **Source Control** | Private Git repository where appropriate | Tracks scripts and sanitized configuration templates |
| **Monitoring Platform** | LibreNMS | Monitors backup host and backup-related service health |
| **Syslog Service** | LNX-SRV01 | Records backup execution and system events |

### Backup Data Flow

```text
Cisco Routers and Switches
           │
           │ SSH / Netmiko
           ▼
      Python Backup Script
           │
           ▼
     Primary Repository
        LNX-SRV01
           │
           ├──────── Verification and Logging
           │
           └──────── Secondary Protected Copy
```

### Architecture Principles

| Principle | Implementation |
|:----------|:---------------|
| **Centralized Collection** | Device backups are collected by one controlled automation host. |
| **Secure Transport** | SSH is used for device communication. |
| **Version Retention** | Historical copies are retained according to policy. |
| **Verification** | Backup files are validated after collection. |
| **Separation** | Backup files are stored separately from production device storage. |
| **Recoverability** | Recovery procedures are tested using controlled lab scenarios. |
| **Confidentiality** | Credentials and secrets are excluded from public repositories. |

> **Availability Note**
>
> LNX-SRV01 is a single backup host in the initial lab. A secondary backup destination is recommended because a backup stored only on the monitored production server does not provide sufficient protection against server failure.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Inventory

### Network Device Backup Inventory

| Hostname | Device Role | Management IP | Backup Method |
|:---------|:------------|:--------------|:--------------|
| **EDGE-R1** | Internet Edge Router | `10.10.99.4` | Python / Netmiko over SSH |
| **CORE-R1** | Primary Core Router | `10.10.99.2` | Python / Netmiko over SSH |
| **CORE-R2** | Secondary Core Router | `10.10.99.3` | Python / Netmiko over SSH |
| **DIST-SW1** | Distribution Switch | `10.10.99.10` | Python / Netmiko over SSH |
| **HR-SW01** | HR Access Switch | `10.10.99.21` | Python / Netmiko over SSH |
| **IT-SW01** | IT Access Switch | `10.10.99.22` | Python / Netmiko over SSH |
| **FIN-SW01** | Finance Access Switch | `10.10.99.23` | Python / Netmiko over SSH |
| **SALES-SW01** | Sales Access Switch | `10.10.99.24` | Python / Netmiko over SSH |
| **SRV-SW01** | Server Access Switch | `10.10.99.25` | Python / Netmiko over SSH |

### Server Backup Inventory

| Hostname | Platform | Protected Data |
|:---------|:---------|:---------------|
| **WIN-SRV01** | Windows Server 2022 | DHCP, DNS, selected system and service configuration |
| **LNX-SRV01** | Ubuntu Server 24.04 LTS | LibreNMS, Syslog, MariaDB, Apache, automation scripts, and scheduled tasks |

### Documentation Backup Inventory

| Item | Protection Method |
|:-----|:------------------|
| Markdown Documentation | Git version control |
| Network Diagrams | Git repository and secondary copy |
| Test Evidence | Organized evidence directory |
| Device Templates | Private repository or protected storage |
| Automation Scripts | Git version control |
| Inventory Files | Protected repository |
| Change Records | Documentation repository |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Policy

### Backup Policy Requirements

| Policy Item | Requirement |
|:------------|:------------|
| Automated Device Backup | Performed daily |
| Pre-Change Backup | Required before major configuration changes |
| Post-Change Backup | Performed after successful validation |
| Manual Emergency Backup | Performed before high-risk troubleshooting |
| Historical Retention | Multiple versions retained |
| Backup Verification | Required after each automated run |
| Secondary Copy | Recommended |
| Credential Protection | Mandatory |
| Public Repository Review | Mandatory before publication |
| Recovery Testing | Performed periodically |

### Retention Policy

| Backup Type | Retention |
|:------------|:----------|
| Daily Backups | 14 days |
| Weekly Backups | 8 weeks |
| Monthly Backups | 12 months |
| Pre-Change Backups | Retained for the duration of the change record |
| Major Milestone Backups | Retained for the project lifecycle |
| Failed or Corrupted Backups | Removed after investigation and documentation |

### Backup Success Criteria

A backup is considered successful only when:

- The target device or server was reachable.
- Authentication succeeded.
- The expected data was collected.
- The output file is not empty.
- The file includes the correct hostname.
- The file includes a valid timestamp.
- The content passes basic validation.
- The backup result is recorded in the execution log.
- Any failure is reported for investigation.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Types

| Backup Type | Purpose | Trigger |
|:------------|:--------|:--------|
| **Scheduled Backup** | Protects current configurations automatically | Daily schedule |
| **Pre-Change Backup** | Provides rollback point before implementation | Before configuration change |
| **Post-Change Backup** | Captures approved stable configuration | After validation |
| **Milestone Backup** | Preserves major implementation stage | Project milestone |
| **Emergency Backup** | Captures current state during incident response | Before high-risk troubleshooting |
| **Server Configuration Backup** | Protects service and system settings | Scheduled or pre-change |
| **Database Backup** | Protects LibreNMS monitoring data | Scheduled |
| **Documentation Backup** | Protects project documents and evidence | Git commit / repository sync |

### Configuration State Model

| State | Description |
|:------|:------------|
| **Baseline** | Approved standard configuration before implementation |
| **Pre-Change** | Configuration immediately before a change |
| **Post-Change** | Configuration after implementation and successful testing |
| **Last Known Good** | Most recent configuration proven to operate correctly |
| **Recovery Candidate** | Backup selected for restoration |
| **Current Running State** | Configuration currently active on the device |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Schedule

### Recommended Schedule

| Backup Activity | Frequency | Timing |
|:----------------|:----------|:-------|
| Network Device Configuration Backup | Daily | Outside active maintenance periods |
| Pre-Change Backup | Per change | Immediately before implementation |
| Post-Change Backup | Per change | After testing and approval |
| LibreNMS Database Backup | Daily | Before log rotation or maintenance |
| Linux Configuration Backup | Weekly | Scheduled maintenance period |
| Windows Service Configuration Backup | Weekly | Scheduled maintenance period |
| Documentation Repository Sync | After approved changes | Following documentation update |
| Recovery Test | Quarterly | Controlled maintenance window |
| Retention Cleanup | Weekly | After successful backup verification |

### Backup Dependencies

| Backup Activity | Dependency |
|:----------------|:-----------|
| Network Device Backup | SSH reachability through VLAN 99 |
| Windows Service Backup | Administrator permissions |
| LibreNMS Database Backup | MariaDB service operational |
| Syslog Configuration Backup | Access to LNX-SRV01 |
| Secondary Copy | Secondary destination available |
| Git Repository Update | Repository access and approved sanitized content |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Storage Design

### Recommended Directory Structure

```text
/opt/network-backups/
├── configs/
│   ├── daily/
│   │   ├── EDGE-R1/
│   │   ├── CORE-R1/
│   │   ├── CORE-R2/
│   │   ├── DIST-SW1/
│   │   └── access-switches/
│   ├── pre-change/
│   ├── post-change/
│   └── milestones/
├── servers/
│   ├── windows/
│   ├── linux/
│   ├── librenms/
│   └── syslog/
├── scripts/
├── inventory/
├── logs/
└── reports/
```

### Storage Standards

| Storage Item | Standard |
|:-------------|:---------|
| Primary Repository | Protected directory on LNX-SRV01 |
| Secondary Repository | Separate protected storage |
| File Ownership | Dedicated automation account |
| Directory Permissions | Least privilege |
| Encryption at Rest | Recommended |
| Backup Logs | Stored separately from configuration files |
| Retention Cleanup | Automated only after successful validation |
| Public GitHub Storage | Sanitized templates only |

### Permissions Model

| Resource | Recommended Access |
|:---------|:-------------------|
| Backup Configuration Files | Backup administrator only |
| Automation Scripts | Network administrators |
| Inventory File | Authorized automation account |
| Execution Logs | Operations and network administrators |
| Recovery Documentation | Network team |
| Credentials | Protected secrets store or environment variables |
| Public Documentation | No confidential configuration data |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## File Naming Convention

Backup files use a standardized naming format to simplify identification, comparison, and recovery.

### Network Device Naming Format

```text
<HOSTNAME>_<BACKUP-TYPE>_<YYYYMMDD>_<HHMMSS>.cfg
```

### Examples

```text
CORE-R1_daily_20260803_020000.cfg
EDGE-R1_pre-change_20260803_143000.cfg
DIST-SW1_post-change_20260803_160500.cfg
```

### Server Backup Naming Format

```text
<HOSTNAME>_<SERVICE>_<BACKUP-TYPE>_<YYYYMMDD>_<HHMMSS>.<extension>
```

### Examples

```text
WIN-SRV01_DHCP_weekly_20260803_030000.xml
WIN-SRV01_DNS_pre-change_20260803_141500.zip
LNX-SRV01_LibreNMS_database_20260803_031500.sql.gz
LNX-SRV01_rsyslog_weekly_20260803_032000.tar.gz
```

### Naming Requirements

| Requirement | Standard |
|:------------|:---------|
| Hostname | Must match documented device hostname |
| Timestamp | Uses `YYYYMMDD_HHMMSS` |
| Backup Type | Daily, weekly, pre-change, post-change, or milestone |
| Extension | Reflects stored content |
| Spaces | Not permitted |
| Special Characters | Limited to hyphens and underscores |
| Time Reference | Based on synchronized enterprise time |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Network Device Backup

Network device configurations are collected over SSH using a dedicated automation account.

### Data Collected

| Data | Purpose |
|:-----|:--------|
| Running Configuration | Captures active configuration |
| Startup Configuration | Confirms saved configuration |
| Device Version | Records software and hardware information |
| Interface Summary | Records interface operational state |
| VLAN Summary | Records switching segmentation |
| Routing Summary | Records route and protocol status |
| HSRP Summary | Records active and standby gateway roles |

### Recommended Commands

```text
show running-config
show startup-config
show version
show ip interface brief
show vlan brief
show interfaces trunk
show ip route
show ip ospf neighbor
show ip bgp summary
show standby brief
```

### Backup Process

| Step | Activity | Expected Result |
|:----:|:---------|:----------------|
| **1** | Load the protected device inventory | Target devices identified |
| **2** | Establish an SSH connection | Authentication succeeds |
| **3** | Collect running configuration | Active configuration returned |
| **4** | Collect startup configuration | Saved configuration returned |
| **5** | Collect operational metadata | Supporting information returned |
| **6** | Save files using naming standard | Files written successfully |
| **7** | Validate file size and content | Backup confirmed usable |
| **8** | Calculate checksum where implemented | File integrity recorded |
| **9** | Record backup result | Execution log updated |
| **10** | Report failed devices | Administrator notified |

### Backup Failure Conditions

A network device backup is considered failed when:

- The device does not respond.
- SSH authentication fails.
- Command execution times out.
- The configuration output is empty.
- The hostname does not match the target inventory.
- The file cannot be written.
- Verification fails.
- The backup is incomplete or corrupted.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Python Automation Design

Python and Netmiko automate device configuration collection and reporting.

### Automation Components

| Component | Purpose |
|:----------|:--------|
| Device Inventory | Defines hostnames, IP addresses, and device types |
| Credential Input | Loads credentials securely |
| Connection Handler | Establishes SSH sessions |
| Command Collector | Executes approved show commands |
| File Writer | Stores backup output |
| Validator | Verifies backup completeness |
| Logger | Records success and failure |
| Retention Handler | Removes expired backups |
| Summary Report | Lists backup status by device |

### Automation Workflow

```text
Load Inventory
      │
      ▼
Load Credentials Securely
      │
      ▼
Connect to Device
      │
      ▼
Collect Configuration
      │
      ▼
Validate Output
      │
      ▼
Save Timestamped File
      │
      ▼
Record Result
      │
      ▼
Generate Summary Report
```

### Automation Security Requirements

- Credentials must not be hard-coded in the script.
- Credentials must not be committed to GitHub.
- Environment variables or a protected secrets file should be used.
- SSH host reachability should be limited to the Management VLAN.
- The automation account should use only the permissions required.
- Backup output must be stored in a protected directory.
- Logs must not reveal passwords or secrets.
- Public screenshots must not display credentials.

### Scheduling

The script may be scheduled using Linux `cron`.

Example schedule:

```text
0 2 * * * /usr/bin/python3 /opt/network-backups/scripts/backup_devices.py
```

This example runs the backup script daily at 02:00.

### Automation Result Status

| Status | Meaning |
|:-------|:--------|
| **SUCCESS** | Backup completed and validated |
| **PARTIAL** | Some commands succeeded but the backup requires review |
| **FAILED** | No usable backup was produced |
| **UNREACHABLE** | Device did not respond |
| **AUTHENTICATION_ERROR** | SSH authentication failed |
| **VALIDATION_ERROR** | File was collected but failed verification |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Windows Server Backup

WIN-SRV01 provides DHCP, DNS, and NTP-related infrastructure services.

### Protected Windows Components

| Component | Backup Requirement |
|:----------|:-------------------|
| DHCP Configuration | Export scopes, reservations, exclusions, options, and leases |
| DNS Configuration | Protect zones and resource records |
| Server Roles | Document installed roles and features |
| Network Configuration | Record IP addressing and gateway configuration |
| NTP Configuration | Record time-service configuration |
| System State | Recommended where supported |
| Event Logs | Export relevant operational logs when required |
| PowerShell Scripts | Store in version-controlled protected storage |

### DHCP Backup

Example PowerShell approach:

```powershell
Export-DhcpServer `
  -ComputerName "WIN-SRV01" `
  -File "C:\Backup\WIN-SRV01_DHCP.xml" `
  -Leases `
  -Force
```

### DHCP Recovery

Example PowerShell approach:

```powershell
Import-DhcpServer `
  -ComputerName "WIN-SRV01" `
  -File "C:\Backup\WIN-SRV01_DHCP.xml" `
  -BackupPath "C:\Windows\System32\dhcp\backup" `
  -Leases `
  -ScopeOverwrite `
  -Force
```

> Commands should be validated in the lab before being used in a recovery event.

### DNS Backup Considerations

DNS recovery depends on the DNS deployment method:

- AD-integrated zones are protected through Active Directory and System State backup.
- Standard primary zones should have their zone files and configuration protected.
- Critical A records should also be documented in the IP Addressing Plan.
- DNS records should be verified after restoration.

### Windows Backup Verification

| Verification Item | Expected Result |
|:------------------|:----------------|
| DHCP export file exists | File present and readable |
| DHCP scopes included | All user VLAN scopes present |
| DHCP options included | Gateway and DNS options present |
| DNS zones present | `verra.local` available |
| DNS records present | Required infrastructure records available |
| Server network settings recorded | Static IP configuration documented |
| Event logs available | Recovery evidence retained |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Linux Server Backup

LNX-SRV01 hosts LibreNMS, Syslog, web services, automation scripts, and related services.

### Protected Linux Components

| Component | Typical Location |
|:----------|:-----------------|
| LibreNMS Application Configuration | `/opt/librenms/` |
| LibreNMS Environment File | Protected application configuration |
| MariaDB Database | Database export |
| Apache Configuration | `/etc/apache2/` |
| Rsyslog Configuration | `/etc/rsyslog.conf`, `/etc/rsyslog.d/` |
| Cron Jobs | User and system cron configuration |
| Network Configuration | Netplan or platform-specific configuration |
| Python Scripts | `/opt/network-backups/scripts/` |
| Device Inventory | Protected inventory directory |
| Backup Logs | `/opt/network-backups/logs/` |

### Linux Configuration Backup Example

```bash
sudo tar -czf LNX-SRV01_system-config_$(date +%Y%m%d_%H%M%S).tar.gz \
  /etc/apache2 \
  /etc/rsyslog.conf \
  /etc/rsyslog.d \
  /etc/netplan \
  /etc/cron.d
```

### Database Backup Example

```bash
mysqldump --single-transaction \
  -u <backup-user> -p \
  librenms | gzip > \
  LNX-SRV01_LibreNMS_database_$(date +%Y%m%d_%H%M%S).sql.gz
```

### Linux Backup Verification

| Verification Item | Expected Result |
|:------------------|:----------------|
| Archive exists | File present |
| Archive readable | Integrity test succeeds |
| Database dump exists | SQL backup present |
| Database dump not empty | Valid file size |
| Scripts included | Automation files present |
| Rsyslog configuration included | Logging settings recoverable |
| Cron schedule included | Automation schedule recoverable |
| Permissions protected | Unauthorized users cannot read backups |

### Recommended Archive Verification

```bash
tar -tzf <backup-file>.tar.gz
gzip -t <database-backup>.sql.gz
```

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Monitoring Platform Backup

LibreNMS is a critical operational platform and requires both application configuration and database protection.

### LibreNMS Backup Components

| Component | Purpose |
|:----------|:--------|
| Database | Devices, ports, event records, alert rules, and platform state |
| Application Configuration | Platform and integration settings |
| Web Server Configuration | Dashboard access |
| Scheduled Polling | Poller and cron operation |
| Alert Rules | Operational notification logic |
| Device Inventory | Monitored infrastructure records |
| Custom Dashboards | Operational visualization |
| Syslog Integration | Event collection configuration |

### Recommended Backup Order

| Step | Activity |
|:----:|:---------|
| **1** | Record LibreNMS version |
| **2** | Export the MariaDB database |
| **3** | Back up LibreNMS configuration |
| **4** | Back up Apache configuration |
| **5** | Back up cron and poller configuration |
| **6** | Back up Syslog integration settings |
| **7** | Validate archive and database dump |
| **8** | Copy to the secondary repository |

### Monitoring Recovery Priority

| Component | Priority |
|:----------|:--------:|
| Database | Critical |
| LibreNMS Configuration | Critical |
| Web Server Configuration | High |
| Poller Schedule | High |
| Alert Rules | High |
| Historical Graph Data | Medium |
| Old Syslog Data | Medium |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Security

Backup files may contain sensitive infrastructure information and must be protected as confidential operational data.

### Security Controls

| Control | Implementation |
|:--------|:---------------|
| Access Control | Backup directories restricted to authorized administrators |
| Secure Collection | SSH used for network device access |
| Read-Only Automation | Backup account limited where platform permits |
| Credential Storage | Environment variables or protected secrets store |
| Encryption at Rest | Recommended for secondary copies |
| Encryption in Transit | Secure transfer method required |
| Logging | Backup actions recorded |
| Integrity | Checksums recommended |
| Public Repository Protection | Raw production configurations prohibited |
| Deletion Control | Retention cleanup limited to authorized processes |

### Sensitive Information

The following information must not be published:

- Enable secrets
- Local administrator password hashes
- SNMP community strings
- SNMPv3 authentication and privacy credentials
- SSH private keys
- Database passwords
- API tokens
- Publicly usable service credentials
- Full unredacted running configurations
- Environment files containing secrets

### GitHub Storage Rules

| Content | Public Repository |
|:--------|:-----------------:|
| Sanitized configuration template | Allowed |
| Python script without credentials | Allowed |
| Example inventory with documentation IPs | Allowed |
| Actual password or secret | Prohibited |
| Unredacted running configuration | Prohibited |
| Backup archive | Prohibited |
| Database dump | Prohibited |
| Recovery procedure | Allowed |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Verification

Backup verification confirms that collected files are usable before they are relied upon for recovery.

### Verification Checklist

| ID | Verification Item | Expected Result | Status |
|:--:|:------------------|:----------------|:------:|
| **BKP-VER-001** | Device reachability | All in-scope devices reachable | ☐ |
| **BKP-VER-002** | SSH authentication | Automation login succeeds | ☐ |
| **BKP-VER-003** | Running configuration collected | Valid configuration file created | ☐ |
| **BKP-VER-004** | Startup configuration collected | Valid saved configuration created | ☐ |
| **BKP-VER-005** | File naming | Naming convention followed | ☐ |
| **BKP-VER-006** | File size | File is not empty | ☐ |
| **BKP-VER-007** | Hostname validation | Correct hostname appears in file | ☐ |
| **BKP-VER-008** | Execution logging | Result recorded | ☐ |
| **BKP-VER-009** | Failed device reporting | Failure identified clearly | ☐ |
| **BKP-VER-010** | Secondary copy | Backup copied successfully | ☐ |
| **BKP-VER-011** | DHCP export | Export file readable | ☐ |
| **BKP-VER-012** | DNS protection | Zone data or System State protected | ☐ |
| **BKP-VER-013** | LibreNMS database | Database dump valid | ☐ |
| **BKP-VER-014** | Linux archive | Archive integrity verified | ☐ |
| **BKP-VER-015** | Recovery test | Selected backup restored successfully | ☐ |

### File Integrity Verification

Example checksum generation:

```bash
sha256sum <backup-file> > <backup-file>.sha256
```

Example checksum verification:

```bash
sha256sum -c <backup-file>.sha256
```

### Verification Failure Response

| Failure | Response |
|:--------|:---------|
| Empty configuration file | Repeat device backup |
| Authentication failure | Verify automation credentials |
| Device unreachable | Investigate management connectivity |
| Invalid archive | Recreate archive |
| Database dump failure | Verify database account and storage |
| Checksum mismatch | Reject the backup and investigate integrity |
| Secondary copy failure | Retain primary copy and retry transfer |
| Repeated failures | Escalate to network or system administrator |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Recovery Strategy

Recovery restores affected infrastructure to a verified operational state using the most appropriate backup.

### Recovery Principles

| Principle | Implementation |
|:----------|:---------------|
| **Assess Before Restore** | Confirm the root cause before replacing configuration |
| **Use Last Known Good** | Select the most recent validated stable backup |
| **Preserve Current State** | Capture the failed state before recovery where possible |
| **Restore Minimum Required Scope** | Avoid unnecessary changes |
| **Validate Dependencies** | Confirm VLANs, routing, services, and management access |
| **Test After Recovery** | Re-run applicable Test Plan cases |
| **Document Recovery** | Record backup used, actions, and results |
| **Protect Evidence** | Retain logs and failed configurations for analysis |

### Recovery Priority

| Priority | Component | Reason |
|:--------:|:----------|:-------|
| **1** | Core Routing and HSRP | Required for enterprise connectivity |
| **2** | Distribution and Server Switching | Required for VLAN and server access |
| **3** | EDGE-R1 and ISP Connectivity | Required for Internet access |
| **4** | DHCP and DNS | Required for endpoint usability |
| **5** | Management Access | Required for administration |
| **6** | Monitoring and Syslog | Required for operational visibility |
| **7** | Historical Reporting | Lower immediate restoration priority |

### Recovery Decision Matrix

| Failure Type | Recovery Action |
|:-------------|:----------------|
| Incorrect configuration change | Restore pre-change configuration |
| Configuration corruption | Restore last known good configuration |
| Device replacement | Apply baseline and latest approved backup |
| Startup configuration missing | Restore approved startup configuration |
| DHCP configuration loss | Import DHCP backup |
| DNS data loss | Restore zone or System State data |
| LibreNMS database loss | Restore database and application configuration |
| Rsyslog configuration loss | Restore protected configuration files |
| Automation script failure | Restore last approved script version |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Network Device Recovery

### Recovery Preconditions

- Root cause has been identified or sufficiently isolated.
- Current configuration has been captured where possible.
- The selected backup has passed verification.
- A rollback or maintenance window has been approved.
- Console access is available for high-risk recovery.
- Required test cases are identified.

### Configuration Recovery Procedure

| Step | Activity |
|:----:|:---------|
| **1** | Identify the affected device and service impact. |
| **2** | Capture the current running configuration. |
| **3** | Select the last known good backup. |
| **4** | Compare current and backup configurations. |
| **5** | Determine whether partial or full restoration is required. |
| **6** | Apply the approved recovery configuration. |
| **7** | Save the restored configuration. |
| **8** | Verify interfaces, VLANs, routing, HSRP, ACLs, and management access. |
| **9** | Re-run applicable Test Plan cases. |
| **10** | Update incident and recovery records. |

### Partial Recovery

Partial recovery is preferred when only one configuration section is affected.

Examples:

- Restore one ACL.
- Restore one OSPF configuration section.
- Restore an HSRP group.
- Restore a trunk allowed-VLAN list.
- Restore SSH or SNMP settings.

### Full Recovery

Full restoration may be required when:

- The configuration is extensively corrupted.
- A replacement device is introduced.
- Startup configuration is lost.
- Multiple dependent services are affected.
- A validated baseline is available.

### Post-Recovery Verification Commands

```text
show ip interface brief
show vlan brief
show interfaces trunk
show spanning-tree
show ip route
show ip ospf neighbor
show ip bgp summary
show standby brief
show access-lists
show ip nat translations
show ip ssh
show logging
ping
traceroute
```

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Server Recovery

### Windows Server Recovery

| Component | Recovery Action |
|:----------|:----------------|
| DHCP | Import the validated DHCP export |
| DNS | Restore DNS data or System State as appropriate |
| NTP | Restore documented time-service configuration |
| Network Settings | Reapply documented static IP configuration |
| Server Roles | Reinstall and configure required roles |
| Event Logs | Preserve and review incident evidence |

### Linux Server Recovery

| Component | Recovery Action |
|:----------|:----------------|
| LibreNMS | Restore application configuration and database |
| MariaDB | Import validated database dump |
| Apache | Restore web server configuration |
| Syslog | Restore rsyslog configuration |
| Automation Scripts | Restore approved Git or backup version |
| Cron Jobs | Restore scheduled tasks |
| Network Settings | Restore documented network configuration |

### Service Recovery Verification

| Service | Verification |
|:--------|:-------------|
| DHCP | Client receives correct lease |
| DNS | Internal and external resolution succeeds |
| NTP | Devices synchronize successfully |
| Syslog | Device messages appear on LNX-SRV01 |
| SNMP | LibreNMS polls devices |
| LibreNMS | Dashboard and devices are available |
| Apache | Monitoring web interface loads |
| MariaDB | Monitoring platform accesses the database |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Disaster Recovery Scenarios

### DR-001 — Core Router Configuration Loss

#### Impact

- Inter-VLAN routing may fail.
- HSRP redundancy may be reduced.
- OSPF routes may be unavailable.

#### Recovery Action

1. Establish console or management access.
2. Apply the approved CORE-R1 or CORE-R2 backup.
3. Verify VLAN interfaces and HSRP groups.
4. Verify OSPF adjacency.
5. Confirm authorized inter-VLAN communication.
6. Save the recovered configuration.

#### Recovery Validation

```text
show standby brief
show ip ospf neighbor
show ip route
ping
```

### DR-002 — Edge Router Configuration Loss

#### Impact

- Internet access unavailable.
- BGP session down.
- NAT translations unavailable.

#### Recovery Action

1. Restore interface addressing.
2. Restore static default route.
3. Restore OSPF and BGP configuration.
4. Restore NAT and perimeter ACL configuration.
5. Verify ISP reachability.
6. Validate Internet access.

#### Recovery Validation

```text
show ip route
show ip ospf neighbor
show ip bgp summary
show ip nat statistics
ping
traceroute
```

### DR-003 — Distribution Switch Configuration Loss

#### Impact

- Access switches become isolated.
- VLAN propagation fails.
- Management connectivity may be lost.

#### Recovery Action

1. Restore VLAN database.
2. Restore trunk interfaces.
3. Restore native and allowed VLAN settings.
4. Restore STP root configuration.
5. Restore management interface.
6. Validate all downstream trunks.

#### Recovery Validation

```text
show vlan brief
show interfaces trunk
show spanning-tree
show ip interface brief
```

### DR-004 — Access Switch Replacement

#### Impact

- One department or server segment loses connectivity.

#### Recovery Action

1. Install a compatible replacement switch.
2. Apply hostname and management configuration.
3. Restore VLAN and trunk settings.
4. Restore access-port assignments.
5. Restore PortFast, BPDU Guard, and Port Security.
6. Add the device back to monitoring.
7. Validate endpoint connectivity.

### DR-005 — DHCP Configuration Loss

#### Impact

- New clients cannot obtain valid addresses.
- Lease renewal may fail.

#### Recovery Action

1. Restore Windows DHCP service.
2. Import the validated DHCP backup.
3. Verify scopes, exclusions, leases, and options.
4. Test DHCP Relay.
5. Renew a client lease in every user VLAN.

### DR-006 — DNS Configuration Loss

#### Impact

- Internal hostname resolution fails.
- Active Directory-related services may be affected.
- External name resolution may fail.

#### Recovery Action

1. Restore DNS service availability.
2. Restore the internal zone and records.
3. Restore Forwarder configuration.
4. Verify DHCP DNS options.
5. Test internal and external resolution.

### DR-007 — LibreNMS Server Failure

#### Impact

- Monitoring dashboard unavailable.
- SNMP polling and alerting stop.
- Centralized operational visibility is reduced.

#### Recovery Action

1. Restore LNX-SRV01 or deploy a replacement server.
2. Restore network addressing.
3. Install required LibreNMS dependencies.
4. Restore the LibreNMS database.
5. Restore application and web configuration.
6. Restore polling and scheduled tasks.
7. Validate devices, graphs, and alerts.

### DR-008 — Backup Repository Failure

#### Impact

- Primary recovery files unavailable.
- Automated backups fail.

#### Recovery Action

1. Isolate the failed storage.
2. Use the secondary protected copy.
3. Rebuild the backup repository.
4. Restore scripts, inventory, and historical files.
5. Re-enable scheduled automation.
6. Perform a complete backup verification run.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Recovery Verification

### Recovery Verification Checklist

| ID | Verification Item | Expected Result | Status |
|:--:|:------------------|:----------------|:------:|
| **RCV-VER-001** | Device management access | SSH access successful | ☐ |
| **RCV-VER-002** | Interface status | Required interfaces up/up | ☐ |
| **RCV-VER-003** | VLAN configuration | Required VLANs present | ☐ |
| **RCV-VER-004** | Trunk operation | Correct VLANs forwarded | ☐ |
| **RCV-VER-005** | STP operation | DIST-SW1 remains root | ☐ |
| **RCV-VER-006** | HSRP operation | Active/Standby roles correct | ☐ |
| **RCV-VER-007** | OSPF operation | Neighbors reach FULL state | ☐ |
| **RCV-VER-008** | BGP operation | ISP peer Established | ☐ |
| **RCV-VER-009** | NAT operation | PAT translations created | ☐ |
| **RCV-VER-010** | ACL operation | Authorized traffic allowed and unauthorized traffic denied | ☐ |
| **RCV-VER-011** | DHCP operation | Correct leases assigned | ☐ |
| **RCV-VER-012** | DNS operation | Internal and external resolution succeeds | ☐ |
| **RCV-VER-013** | NTP operation | Time synchronized | ☐ |
| **RCV-VER-014** | Syslog operation | Logs received centrally | ☐ |
| **RCV-VER-015** | LibreNMS operation | Devices and metrics visible | ☐ |
| **RCV-VER-016** | Backup automation | New backup completes successfully | ☐ |

### Recovery Acceptance Criteria

Recovery is complete only when:

- The affected service is operational.
- Required dependencies are operational.
- Monitoring confirms normal status.
- Relevant test cases pass.
- No critical security controls are missing.
- The restored configuration is saved.
- A new post-recovery backup is created.
- Documentation is updated.
- The incident is formally closed.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Backup Maintenance

### Daily Tasks

| Task | Purpose |
|:-----|:--------|
| Review Backup Report | Identify failed or partial device backups |
| Verify Repository Availability | Confirm backup storage is accessible |
| Investigate Failed Devices | Restore backup coverage |
| Check Available Disk Space | Prevent backup interruption |

### Weekly Tasks

| Task | Purpose |
|:-----|:--------|
| Verify Sample Configuration Files | Confirm backup readability |
| Review Retention Cleanup | Ensure policy compliance |
| Verify Secondary Copy | Confirm additional recovery protection |
| Review Authentication Failures | Detect account or access issues |
| Review Automation Logs | Identify recurring script errors |

### Monthly Tasks

| Task | Purpose |
|:-----|:--------|
| Perform Sample Recovery Test | Validate actual recoverability |
| Audit Backup Inventory | Confirm all devices are protected |
| Review Storage Permissions | Protect confidential backup data |
| Review Script Versions | Maintain approved automation code |
| Validate Windows and Linux Backups | Confirm server data protection |
| Update Recovery Documentation | Reflect infrastructure changes |

### Backup Maintenance Record

| Field | Description |
|:------|:------------|
| Date and Time | When the task was performed |
| Administrator | Person completing the activity |
| Backup Component | Device, server, or service |
| Activity | Backup, verification, cleanup, or recovery test |
| Result | Success, partial, or failed |
| Findings | Issues identified |
| Corrective Action | Resolution applied |
| Evidence | Log, screenshot, checksum, or test result |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Limitations and Future Enhancements

### Current Limitations

| Limitation | Impact |
|:-----------|:-------|
| Single Automation Host | Backup operation depends on LNX-SRV01 |
| Single Primary Repository | Local server failure may affect backup availability |
| Lab Device Limitations | Some platforms may provide incomplete automation support |
| Manual Server Recovery | Some service restoration steps remain manual |
| No Enterprise Backup Platform | Centralized commercial backup software is not deployed |
| Limited Encryption | Encryption at rest may depend on the lab environment |
| No Geographic Recovery Site | Site-wide disaster recovery is not implemented |

### Future Enhancements

| Enhancement | Benefit |
|:------------|:--------|
| Secondary Backup Server | Removes the primary repository single point of failure |
| Encrypted Off-Site Storage | Protects against local server loss |
| Ansible Integration | Supports scalable configuration collection and restoration |
| Git-Based Configuration Comparison | Highlights configuration drift |
| Secrets Management Platform | Protects automation credentials |
| Automated Checksum Validation | Improves backup integrity verification |
| Email Failure Notifications | Provides proactive backup alerts |
| LibreNMS Alert Integration | Detects failed backup services |
| Automated Recovery Testing | Confirms backup usability regularly |
| Immutable Backup Storage | Protects backups against deletion or tampering |
| Formal RPO and RTO Targets | Improves disaster recovery planning |

### Future RPO and RTO Model

| Service | Proposed RPO | Proposed RTO |
|:--------|:-------------|:-------------|
| Core Network Configuration | 24 hours or pre-change | 1 hour |
| Edge Router Configuration | 24 hours or pre-change | 1 hour |
| Access Switch Configuration | 24 hours | 4 hours |
| DHCP and DNS | 24 hours | 2 hours |
| LibreNMS | 24 hours | 4 hours |
| Syslog | 24 hours | 4 hours |
| Project Documentation | After every approved change | 4 hours |

> RPO and RTO values are planning targets for the portfolio environment and should be adjusted for actual business requirements in a production deployment.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Summary

This Backup and Recovery Guide defines the processes used to protect and restore the Secure Enterprise Network Infrastructure.

Cisco router and switch configurations are collected centrally using Python and Netmiko over SSH. Windows Server service data, Linux configuration, LibreNMS data, Syslog settings, automation scripts, and project documentation are also included in the backup scope.

Standardized retention, naming, storage, verification, security, and maintenance policies ensure that backups remain organized and recoverable.

Recovery procedures prioritize Core routing, gateway redundancy, Distribution switching, Internet connectivity, infrastructure services, management access, and monitoring according to their operational impact.

The design supports rapid rollback after failed changes while providing a foundation for future enhancements such as secondary backup servers, encrypted off-site storage, Ansible integration, automated recovery testing, and formal recovery objectives.

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>

## Glossary

| Acronym | Definition |
|:--------|:-----------|
| **ACL** | Access Control List |
| **API** | Application Programming Interface |
| **BGP** | Border Gateway Protocol |
| **DHCP** | Dynamic Host Configuration Protocol |
| **DNS** | Domain Name System |
| **DR** | Disaster Recovery |
| **HSRP** | Hot Standby Router Protocol |
| **NTP** | Network Time Protocol |
| **OSPF** | Open Shortest Path First |
| **RPO** | Recovery Point Objective |
| **RTO** | Recovery Time Objective |
| **SNMP** | Simple Network Management Protocol |
| **SSH** | Secure Shell |
| **Syslog** | System Logging Protocol |
| **VLAN** | Virtual Local Area Network |

<p align="right">
<a href="#contents">⬆️ Back to Contents</a>
</p>
