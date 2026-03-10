## Version Information

**Version Number**: 23.5.1

**Release Date**: November 7, 2025

**Version Upgrade**: Versions v23.1.2.100 and later can be directly upgraded to the current version.

## Version Positioning

YashanDB Cloud Manager V23.5 is positioned as a Long Term Support (LTS) version. This version officially supports YashanDB V23.5 and provides comprehensive operational capabilities for YashanDB in three forms: standalone, distributed, and YAC, including monitoring alerts, inspection management, tablespace management, privilege management, session management, backup recovery, lock management, log analysis, slow SQL, and other modules functionality.

## Feature Updates

### Monitoring and Alert Management

#### Monitoring Dashboard Management

* Supports creating, renaming, and deleting monitoring dashboards.
* Supports creating, editing, and deleting charts, providing both single and multiple selection options, with the ability to view chart names, descriptions, monitoring indicators, and types.
* Provides chart grouping functionality to facilitate users in categorizing related charts for unified management and analysis.

#### Enhanced Monitoring Capabilities

* Supports monitoring of YAC cluster CR statistics, database process and thread counts, session connection types, instance archive size trends, and process file descriptor counts.
* Supports monitoring specific wait events and displaying event details.
* Supports calculating server hardware indicator monitoring, covering server vendors, power supplies, fans, network cards, storage (NVME), CPU, and memory information.
* Supports YFS hardware monitoring, providing disk overview and detail pages, displaying disk group names, types, total space, remaining space, AU Size, redundancy, status, paths, usage rates, and IOPS data.
* New monitoring indicators added: open files, max user processes, max memory size, stack size, VM utilization, etc.

#### Alert Functionality Optimization

* The custom alert notification script adds an IP field.
* Alert list display optimization supports user-defined monitoring types and time types, with the previous alert information hidden by default.
* Adjusts the default display order of the alert list to support viewing detailed information, such as alert ID, object, content, suggestions, occurrence time, duration, level, status, and operations.
* Default alert strategy supports custom removal of database instance objects and triggers alerts on remaining objects.
* New example labels added to the description when creating alert items.
* Supports real-time changes to alert suppression status and records the suppression timeline.
* SMS and email alert templates support custom configurations, allowing flexible arrangement of alert notification content.

### Resource and SQL Management

#### Resource Monitoring Management Page Optimization

* The resource monitoring page is consolidated. The original separate host monitoring, YashanDB monitoring, and TOPSOL detail pages are merged into a unified resource monitoring interface for centralized management and viewing.
* Monitoring dimensions are refined: YashanDB monitoring is carried out from four dimensions: performance, SQL, storage and buffer, and database processes. Host monitoring covers performance and host process dimensions.

#### SQL Diagnosis and Optimization Functionality

* SQL diagnosis optimization: Supports diagnosing SQL statements of the index recommendation type and displays a list of optimization suggestions.
* Smart optimization suggestions: When SQL execution time exceeds the set threshold, the system automatically displays optimization objects, optimization types, response time ratios, diagnostic reasons, and specific optimization suggestions.

#### SQL Outline Management

* Outline information display: Provides a visual interface for SQL Outline related information.
* Supports creation, editing, deletion, enabling/disabling, binding, and unbinding operations for outlines, and supports viewing binding records from the past 30 days.

### Multi-Tenant Management

* Supports CDB and PDB database logins, can host multi-tenant mode databases.
* Provides tenant list display, supporting operations such as adding, deleting, starting, stopping, and restarting PDBs.
* Realizes resource monitoring of CDB and PDB.
* Supports user and role management for CDB and PDB, session management, database auditing, reliability solutions, performance reports and snapshot management, lock and transaction analysis, slow SQL analysis, and other functionalities.
* Provides visualization of the multi-tenant mode topology to enhance visual effects.

### Backup and Recovery Enhancement

* Supports packaging database backups and archive log files into a single file in tar format for centralized viewing and management.
* In distributed, YAC, and cascade standby modes, supports custom restoration paths during recovery to simplify operations.
* Common operations visualization: Supports visual operations for backing up archive log files, specifying restoration paths for standalone database recovery, and backing up and recovering standalone database tablespaces, reducing operational complexity.
* Supports pre-check of disk space before database recovery to ensure operational reliability.
* Allows cancellation of ongoing or unexecuted backup tasks to further enhance system usability.
* Supports alerting for backup task failures, improving operational capabilities.

### Cluster and High Availability Management

* Supports ECS scaling.
* Adapts to distributed memory database architecture.
* Supports hosting independent cascade backup nodes, allowing optional disabling of self-election mode during primary/standby switching in replication groups.
* Supports visual functionality for cluster database expansion, enhancing operational efficiency for YAC.
* Supports visual rolling upgrades for databases, adapting to local upgrades and one-click upgrades for high-availability deployment architecture.

### User Experience Optimization

* Menu bar parent page-related lists support saving filter conditions at the browser session level.
* The dashboard supports displaying Chinese aliases for databases.
* Supports monitoring database processes under non-ycm-agent users.
* Supports changing YASOM user passwords and provides functionality for privilege downgrade assignments.
* Supports configuring the browser to not save passwords.
* Implements white screen reinstalls and restart operations for the agent.

## Specification Configuration Limits

### Requirements for Managed Databases

Supports managing up to 10 sets of databases with no restrictions on deployment specifications and forms.

### Server Configuration

|Managed Database Scale |Minimum Server Configuration |
| -------------- | ----------------------- |
| Less than 10 instances   | 4 cores, 4G memory, 50G disk    |
| 10-100 instances        | 8 cores, 16G memory, 100G disk   |
| More than 100 instances | 16 cores, 32G memory, 500G disk  |

### Operating System Requirements

|Server Type |Operating System |Supported Versions |
| ----------- | -------- | --------------------------------------- |
| x86_64      | CentOS   | 7.9.2009                                |
| x86_64      | Ubuntu   | 20.04.1                                 |
| ARM aarch64 | Kylin            | Kylin Linux Advanced Server V10 (Lance)      |

### Browser Requirements

The client supports access via Chrome, Firefox, and Microsoft Edge browsers, and using the current newer versions is recommended.

|Browser |Supported Versions |
| -------------- | ------------- |
| Google Chrome  | Version 88 and above   |
| Firefox        | Version 78 and above    |
| Microsoft Edge | Version 88 and above   |

## Version Compatibility

YashanDB Cloud Manager v23.5's full platform functionality has been adapted for YashanDB versions v22.2, v23.1, v23.2, v23.3, v23.4, and v23.5, except for the multi-tenant mode in v23.5, which has not been fully adapted; all other deployment forms are supported for management.

## Version Constraints

|Product Functionality |Functionality Limitations |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Database Management                     | Supports managing a maximum of 10 business systems.                                                                                                                   |
|                                         | Does not support simultaneously managing database instances created by different users on the same host.                                                              |
| Remote YCM Management | * One local YCM only supports linking to one remote YCM, and the linkage is bidirectional.<br/>* Two remote linked YCM cannot manage the same host. |
| API Key Management                      | * Each user can create a maximum of 2 access keys, with their API privileges consistent with the creating user.<br/>* Users can only view and edit their created keys.  |
| Backup Strategy                         | Point-in-time recovery (PITR) does not currently support distributed forms.                                                                                            |
| Host Management                         | Currently does not support adding hosts in container form.                                                                                                         |
| Platform Data Backup and Recovery       | * The platform data recovery process will restart related processes; please ensure backups and recoveries occur during periods without task execution.<br/>* The target address for backups must have more than 2G of free space; if backing up ycm-agent, it needs to be greater than 1G. |

## Issues Fixed

1. Fixed the issue where custom list settings were not saved.
2. Fixed the intermittent breakpoint issue with process monitoring icons.
3. Fixed the possible deployment failure issue of the management platform on certain operating systems.
4. Fixed the issue of incompatible exporter information in exception collection.
5. Fixed the intermittent timeout issue on database task removal.
6. Fixed the abnormal error entering the detail page during the database creation process.
7. Fixed the duplicate result issue in the slow SQL execution plan list.
8. Fixed the issue of residual deleted alert items in high availability management platform after primary/standby switching.
9. Fixed the issue of upgrade failure when decompressing the management platform to the installation directory.
10. Fixed the issue of residual alerts during management platform upgrade in abnormal scenarios.
11. Fixed the inconsistency issue between snapshot generation time displayed on the management platform and that in reports.
12. Fixed the intermittent issue of the database hosting list not updating automatically.
13. Fixed the issue of the metric selection box in the suppression rule search functionality being ineffective.
14. Fixed the intermittent failure issue of the delete database tablespace task.
15. Fixed the issue of managed database tasks failing in abnormal scenarios.