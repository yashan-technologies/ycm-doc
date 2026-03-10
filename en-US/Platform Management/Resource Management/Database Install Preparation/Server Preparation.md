## Server Configuration

The table below lists the recommended configurations and minimum requirements for server hardware and operating systems needed to install YashanDB. For all compatible certified servers and operating systems, please refer to the [YashanDB Compatibility List](https://yashandb.com/compatible_certified_partner).

> **Note**:
> 
> The YashanDB installation manual lists general steps and commands applicable to the environments listed in the table below. If there are slight differences in actual steps, commands, etc., in other compatible environments, it is recommended to refer to the official guidance for the corresponding operating system or contact the system service provider for resolution.

|Item |Recommended Configuration |Minimum Configuration |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| Operating System | CentOS 7.6 and above<br/>openEuler 22.03 LTS<br/>KylinOS V10 (ARM architecture: V10 SP3 2303 kernel 52.33 recommended; HaiGuang CPU: V10 SP3 2303 kernel 52.45 recommended) | If large page memory is used, the Linux kernel version must be 2.6 or above |
| CPU        | X86_64, ARM64<br>Hyper-Threading, Non-Hyper-Threading<br>Recommended 4C or above; YAC recommend 8C or above                         | CPU cores > 2                                     |
| Memory     | Recommended 16G or above; YAC recommends 32G or above                                                                                  | > 4G                                              |
| Disk       | SSD recommended<br>Recommended storage space 50G or above                                                                                         | > 4G                                              |
| File System | ext4 or xfs                                                                                                                                       | Cannot be tmpfs or devtmpfs                        |
| Network    | Gigabit Ethernet or above, supporting TCP and UDP; 10GE required internally for YAC, excluding RDMA                                   | -                                                 |
| Network Card | It is recommended to use more than 2 private network cards and use Linux network bonding technology to provide redundancy, configuring one IP address | -                                                 |

> **Caution**:
>
> If YashanDB is installed using a virtual machine, the virtual machine's hard disk must be set to independent-permanent mode (for example, in VMware, check the mode as **[Independent]** - **[Permanent]** in the virtual machine's **[Hard Disk Advanced Settings]**; specific configuration methods should follow the actual virtual machine platform). Otherwise, disk file corruption may occur in scenarios such as power outages.

## Shared Storage Configuration

|Item |Recommended Configuration |Minimum Configuration |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| Disk       | Supports both SSD and HDD, brands and materials should not be mixed                                                                                                                                                     | -                     |
| LUN        | 4 or 6 or more: <br/>3 or 5 system disks (1G or above; if there is only 1 system disk and the AU Size is configured to 32M, the size must be at least 2G).<br/> Other disks are data disks (50G or above)                | -                     |
| Interface   | Storage device and operating system must support Direct IO, supporting 512-byte memory alignment and IO size alignment.<br>Storage device supports reading and writing 512-byte multiples with `pread64` and `pwrite64`. <br /> i.e., supports reading and writing the following sizes: 512 bytes, 1024 bytes, 1536 bytes...32M | -                     |
| Protocol   | For using I/O Fencing based on SCSI persistent reservation, SCSI-3 protocol (or above) and SPC-3 command set (SCSI Primary Commands-3, or above) must be supported, as well as type 5 persistent reservation (Write Exclusive – Registrants Only) | -                     |

> **Note**:
>
> The YAC supports two methods of I/O Fencing. The use of I/O Fencing based on SCSI persistent reservations is recommended, but this method requires certain capabilities from the shared storage. During the installation process, the fenceScsiCheck script will automatically check if the shared storage and other devices/environments meet the requirements.
>
> When the fenceScsiCheck script detects errors, it does not block the installation, but the I/O Fencing method for YAC will be configured as on-the-way I/O protection algorithm. If it is still necessary to use I/O Fencing based on SCSI persistent reservation, please make adjustments based on the corresponding error prompts.

## Deployment Scale

**Standalone Primary/Standby Deployment**

The minimum configuration for standalone deployment is 1 server, but a high-availability environment cannot be built.

Multiple servers can be used to build a primary/standby high-availability environment.

**YAC Deployment**

YAC Deployment configures 2 servers + 1 shared storage (optional) to build a high-availability multi-instance database within the cluster.

For performance considerations, it is recommended to select shared storage in the production environment.

Multiple servers can be used to build a primary/standby high-availability cluster.

**Distributed Deployment**

The minimum configuration for distributed deployment is 1 server, with MN, CN, and DN deployed on the same server.

At least 3 servers can be used to build a high-availability environment in the MN/DN primary/standby form and CN multi-active form.

## Example Server List for Installation

The following lists the server information used in the installation examples. All servers are X86_64 architecture and have installed CentOS 7.6 operating system. The network planning does not differentiate between public and private networks.

In the production environment, please reasonably plan network segments according to actual needs, and it is recommended to divide mutually independent and isolated public and private network segments.

### Standalone Single Database Deployment

In standalone single database deployment, YashanDB will be installed on a single server without a standby database.

|Server Name |Server IP |Role |
| ---------- | ----------- | ---- |
| Server 1    | 192.168.1.2  | Primary   |

### Standalone Primary/Standby Deployment

Standalone primary/standby deployment will take three servers that serve as primary, standby, and standby roles as an example to build a high-availability environment with one primary and two standbys.

|Server Name |Server IP |Role |
| ---------- | ----------- | ---- |
| Server 1    | 192.168.1.2  | Primary   |
| Server 2    | 192.168.1.3  | Standby   |
| Server 3    | 192.168.1.4  | Standby   |

### Standalone Cascade Standby Deployment

Standalone cascade standby deployment will take three servers that serve as primary, standby, and cascade standby roles as an example to build a high-availability environment of one primary/one standby and one cascade standby.

|Server Name |Server IP |Role |
| ---------- | ----------- | ------ |
| Server 1    | 192.168.1.2  | Primary   |
| Server 2    | 192.168.1.3  | Standby   |
| Server 3    | 192.168.1.4  | Cascade Standby |

### Dual Replication Group Primary/Standby Deployment

Dual replication group primary/standby deployment will use six servers as an example, with the primary group (1 primary and 2 standbys per node) and the backup group (the first node as standby, the other nodes as cascade standbys) to construct a high-availability environment with dual replication across two locations.

|Server Name |Server IP |Role |
| ---------------- | ------------ | ------------------------ |
| Server 1 (Data Center A) | 192.168.1.2  | Primary group: 1-1:1 (Primary)     |
| Server 2 (Data Center A) | 192.168.1.3  | Primary group: 1-2:2 (Synchronous Standby) |
| Server 3 (Data Center A) | 192.168.1.4  | Primary group: 1-3:3 (Synchronous Standby) |
| Server 4 (Data Center B) | 192.168.10.5 | Backup group: 2-1:4 (Asynchronous Standby) |
| Server 5 (Data Center B) | 192.168.10.6 | Backup group: 2-2:5 (Cascade Standby) |
| Server 6 (Data Center B) | 192.168.10.7 | Backup group: 2-3:6 (Cascade Standby) |

### YAC Deployment

YAC Deployment uses two servers + one shared storage as an example to build a typical YAC deployment with dual instances and a single database.

Servers:

|Server Name |Server IP |Role |
| ---------- | ----------- | ----------- |
| Server 1    | 192.168.1.2  | Database Instance 1 |
| Server 2    | 192.168.1.3  | Database Instance 2 |

Shared Storage: (4 LUNs have been allocated according to configuration requirements and mounted through multipathing)

|LUN Name |LUN Path |Role |
| ------- | ----------------------- | ------ |
| LUN1     | /dev/mapper/lun03-sys0  | System Disk |
| LUN2     | /dev/mapper/lun03-sys1  | System Disk |
| LUN3     | /dev/mapper/lun03-sys2  | System Disk |
| LUN4     | /dev/mapper/lun01-data0 | Data Disk |

### Primary/Standby Cluster Deployment

Primary/standby cluster deployment uses four servers + two shared storage as an example to build a dual-instance YAC Deployment.

Servers:

|Server Name |Server IP |Role |
| ---------- | ------------ | ----------------- |
| Server 1    | 192.168.1.2   | Primary Cluster Database Instance 1 |
| Server 2    | 192.168.1.3   | Primary Cluster Database Instance 2 |
| Server 3    | 192.168.10.4  | Backup Cluster Database Instance 1 |
| Server 4    | 192.168.10.5  | Backup Cluster Database Instance 2 |

Shared Storage: (8 LUNs have been allocated according to configuration requirements and mounted through multipathing)

|Shared Storage |LUN Name |LUN Path |Role |
| --------- | ------- | ----------------------- | ------------- |
| Shared Storage 1 | LUN1    | /dev/mapper/lun03-sys0  | Primary Cluster System Disk 1 |
| Shared Storage 1 | LUN2    | /dev/mapper/lun03-sys1  | Primary Cluster System Disk 2 |
| Shared Storage 1 | LUN3    | /dev/mapper/lun03-sys2  | Primary Cluster System Disk 3 |
| Shared Storage 1 | LUN4    | /dev/mapper/lun01-data0 | Primary Cluster Data Disk  |
| Shared Storage 2 | LUN1'   | /dev/mapper/lun04-sys0  | Backup Cluster System Disk 1 |
| Shared Storage 2 | LUN2'   | /dev/mapper/lun04-sys1  | Backup Cluster System Disk 2 |
| Shared Storage 2 | LUN3'   | /dev/mapper/lun04-sys2  | Backup Cluster System Disk 3 |
| Shared Storage 2 | LUN4'   | /dev/mapper/lun02-data0 | Backup Cluster Data Disk  |

### Distributed Deployment

Example 1: Distributed Deployment takes three servers, including 1 MN group (MN node 1 primary and 2 standbys), 2 CN, and 1 DN group (DN node 1 primary and 1 standby) as the example environment.

|Server Name |Server IP |Role |
| ---------- | ----------- | ------------------------------------- |
| Server 1    | 192.168.1.2  | MN: 1-1 (Primary), DN: 3-2 (Standby) |
| Server 2    | 192.168.1.3  | MN: 1-2 (Standby), CN: 2-1            |
| Server 3    | 192.168.1.4  | MN: 1-3 (Standby), CN: 2-2, DN: 3-1 (Primary) |

Example 2: Distributed Deployment takes three servers, including 1 MN group, 2 CN, and 1 DN group (both DN group and MN group are 1 primary and 2 standbys) as the example environment.

|Server Name |Server IP |Role |
| ---------- | ----------- | ------------------------------------- |
| Server 1    | 192.168.1.2  | MN: 1-1 (Primary), DN: 3-2 (Standby) |
| Server 2    | 192.168.1.3  | MN: 1-2 (Standby), CN: 2-1, DN: 3-3 (Standby) |
| Server 3    | 192.168.1.4  | MN: 1-3 (Standby), CN: 2-2, DN: 3-1 (Primary) |