## Server Directory Planning

Before installing the YashanDB server, it is necessary to plan the following directories and authorize the installation user.

The installation user must possess full privileges for all the following directories.

### Installation Directory

YashanDB adopts a clustered multi-server parallel installation mode. Only one installation directory needs to be created on one server for downloading and unpacking the software package.

In this installation manual, the installation directory is planned under /home/yashan, to be created by the yashan user during [software package download](Software Packages Download.html#Downloadingpackage).

### HOME Directory and DATA Directory

All YashanDB instance nodes must plan for the HOME directory and DATA directory, and the installation user must have full privileges for the corresponding directories.

- **HOME Directory**: The product directory of YashanDB, used to store the commands provided by YashanDB, libraries required for database operation, and various key components. During the installation process, this directory will serve as the value for the --install-path parameter. After installation, YashanDB will generate $YASDB_HOME in this directory according to the rules.

- **DATA Directory**: The data directory of YashanDB, used to store various system data files, some log files, and configuration files. User data is also stored in this directory by default. However, for YAC, all data files and redo files must be stored on shared storage, and the DATA directory will only be used to store instance operation-related configuration files, log files, and other data. During the installation process, this directory will serve as the value for the --data-path parameter. After installation, YashanDB will generate $YASDB_DATA in this directory according to the rules.

In this installation manual, the example values for the HOME directory and DATA directory are planned under /data/yashan. The respective paths can be created and authorized using the following commands:

```shell
# Create /data/yashan directory
# cd /
# mkdir -p data/yashan

# Authorization can be done directly based on the parent directory
# chown -R yashan:yashan /data/yashan

# Create HOME directory, the complete HOME directory is /data/yashan/yasdb_home
# su yashan
$ cd /data/yashan
$ mkdir yasdb_home

# Create DATA directory, the complete DATA directory is /data/yashan/yasdb_data
$ mkdir yasdb_data
```

<span id="run_log_path" name="run_log_path" class="yaslink"></span>
### Run Log Directory

The run log directory of YashanDB is used to store log files generated during database operation, including run.log and slow.log of the database, along with the logs of yasom and yasagent.

To avoid interference between running logs and data file IO, it is recommended to configure the database run log directory on a different physical disk than the DATA directory. Additionally, the run log directory can be planned according to the following scenarios:

- Unified storage as planned

    1. Plan a directory to store run.log, slow.log, and the logs of yasom and yasagent.
    
    2. During the installation process, this directory will serve as the value for the --data-path parameter.

- Separate storage as planned

    1. Plan different directories to store run.log, slow.log, and the logs of yasom and yasagent separately.
    
    2. During the installation process, one of these directories (for example, the planned directory for the logs of yasom and yasagent) can be set as the value for the --log-path parameter. After generating the configuration file {cluster name}.toml, modify the relevant parameters of the other two types of log directories as needed before proceeding with subsequent installation steps, or modify the corresponding configuration parameters to adjust the storage directory for run.log and slow.log after installation is complete.

- Use default mechanism

    - If the run log directory is not planned, YashanDB will create a subdirectory log under the HOME directory by default (according to this manual's example, it would be /data/yashan/yasdb_home/log) to store run.log, slow.log, and the logs of yasom and yasagent.
    
    - There is no need to specify the --log-path parameter during the installation process.

<span id="lun" name="lun" class="yaslink"></span>

## Binding LUN (YAC Deployment)

This step applies only to YAC Deployment scenarios using shared storage; other scenarios may be skipped.

The actual deployment environment may differ from the example; please adjust based on the example, and do not directly use the following configuration in a production environment. For primary/standby cluster deployment, LUN binding must be planned and executed separately for the primary and standby clusters.

YashanDB requires shared storage to be planned as two types of disks, and all disks should be bound to the same directory, which will serve as the value for the --disk-found-path parameter. In this document, the default value `/dev/yfs` is used as an example.

- **System Disk**
    
    Used for cluster management, only 1, 3, or 5 disks of the same size are supported. Each disk must have a capacity of at least 1G (when the number of system disks is 1 and the AU Size is configured as 32M, the system disk size must be at least 2G). The absolute path of this type of disk will serve as the value for the --system-data parameter, separated by commas for multiple disks, e.g., `--system-data /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2`.

- **Data Disk**

    Used to save the cluster business data, one or more disks of the same size are required. Please plan the quantity and capacity according to actual business needs. The absolute path of this type of disk will serve as the value for the --data parameter, separated by commas for multiple disks, e.g., `--data /dev/yfs/data0,/dev/yfs/data1,/dev/yfs/data2`.

This document does not provide operational guidance for network configuration and storage division of shared storage; please follow the installation instructions provided by the shared storage vendor. This document assumes 3 system disks + 1 data disk. Four LUNs have been partitioned on shared storage and mounted via multipath, planned as follows:

|LUN |Disk Type |Planned Binding Path (Example Value) |
| ----------------------- | -------- | ---------------------- |
| /dev/mapper/lun03-sys0  | System Disk | /dev/yfs/sys0          |
| /dev/mapper/lun04-sys1  | System Disk | /dev/yfs/sys1          |
| /dev/mapper/lun05-sys2  | System Disk | /dev/yfs/sys2          |
| /dev/mapper/lun01-data0 | Data Disk | /dev/yfs/data0         |

The binding directory (/dev/yfs) and disk names (data0, sys0, etc.) are sample values and should be adjusted based on actual business needs. However, while naming, please pay attention to:

- The absolute path length of the bound disk (e.g., `/dev/yfs/sys0`) must not exceed 31 bytes.
- The binding directory and the absolute paths of the disks will serve as the corresponding parameter values for the `yasboot package ce gen` command. If modifications are necessary, please use the actual values when executing `yasboot package ce gen`.

Please execute the following LUN binding operations as the root user on all servers in the cluster:

### Method 1: Binding via Multipath

#### Step 1: Generate Configuration File

1. Create a script named `genDevRuleByUUID.sh` in the root directory:

    ```shell
    # cd ~
    # vi genDevRuleByUUID.sh
    ```

2. Enter the following content, modify the `DEVICES` paths according to the deployment environment, specify the binding names for each device corresponding to the `DEVICES` parameter, and modify `YFS_DISK_DIR` for the binding directory. Save and exit:

    ```shell
    #/bin/bash

    # Please modify to each device path
    DEVICES=("/dev/mapper/lun01-data0" "/dev/mapper/lun03-sys0" "/dev/mapper/lun04-sys1" "/dev/mapper/lun05-sys2")
    # Please specify the binding names for these devices in the order of the DEVICES array, it is recommended to use names related to the business
    TARGETS=("data0"                   "sys0"                   "sys1"                   "sys2")
    # Specify the binding directory
    YFS_DISK_DIR="yfs"

    # Please keep as default, ensure the yashan user exists
    USER="yashan"
    GROUP="yashan"
    UGROUP="$USER:$GROUP"

    if [ ${#TARGETS[@]} != ${#DEVICES[@]} ]
    then
        echo "DEVICES and TARGETS count does not match"
        exit 1
    fi

    if [ ${#TARGETS[@]} == 0 ]
    then
        echo "no targets found"
        exit 1
    fi

    curDir=$(cd "$(dirname "$0")";pwd)
    rm -f $curDir/yashan-device-rule.rules

    count=${#TARGETS[@]}

    for ((i = 0; i < $count; i ++))
    do
        device=${DEVICES[$i]}
        target_name=${TARGETS[$i]}
        target="${YFS_DISK_DIR}/${target_name}"
        
        str1="$device:`udevadm info --query=all --name=$device | grep DM_UUID`"
        str2=${str1#*=}
        echo "KERNEL==\"dm-*\",ENV{DM_UUID}==\"${str2}\",SYMLINK+=\"$target\",OWNER=\"$USER\",GROUP=\"$GROUP\",MODE=\"0660\",RUN+=\"/bin/sh -c 'chown -R $UGROUP $target'\""  >> $curDir/yashan-device-rule.rules
    done
    ```

3. Execute the script to generate the configuration file:

    ```shell
    # sh ./genDevRuleByUUID.sh
    ```

    After successful execution of the script, a binding configuration file named `yashan-device-rule.rules` will be generated in the current directory. The content is as follows, actual deployment may differ:

    ```shell
    # cat yashan-device-rule.rules
    KERNEL=="dm-*",ENV{DM_UUID}=="mpath-36d039ea000a2231f0000e7d9668d7b4a",SYMLINK+="yfs/data0",OWNER="yashan",GROUP="yashan",MODE="0660",RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
    KERNEL=="dm-*",ENV{DM_UUID}=="mpath-36d039ea000a2231f0000e7dd668d7b68",SYMLINK+="yfs/sys0",OWNER="yashan",GROUP="yashan",MODE="0660",RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/sys0'"
    KERNEL=="dm-*",ENV{DM_UUID}=="mpath-36d039ea000a233b90000e358668d7caf",SYMLINK+="yfs/sys1",OWNER="yashan",GROUP="yashan",MODE="0660",RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/sys1'"
    KERNEL=="dm-*",ENV{DM_UUID}=="mpath-36d039ea000a233b90000e35a668d7cbf",SYMLINK+="yfs/sys2",OWNER="yashan",GROUP="yashan",MODE="0660",RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/sys2'"
    ```

    SYMLINK is the alias for the bound device, verify if it matches the expected binding target.

#### Step 2: Deploy Configuration File

Execute the following command to deploy the binding configuration file:

```shell
# cp yashan-device-rule.rules /etc/udev/rules.d/yashan-device-rule.rules
```

#### Step 3: Reboot the Server

A server reboot is required for the binding configuration file to take effect:

```shell
# systemctl reboot
```

#### Step 4: Check Binding Results

1. Execute the following command to confirm all bindings have taken effect, with the user and group set to `yashan`:

    ```shell
    # ls -l /dev/yfs/*
    lrwxrwxrwx 1 yashan yashan 7 Jul 11 14:44 data0 -> ../dm-5
    lrwxrwxrwx 1 yashan yashan 7 Jul 11 14:44 sys0 -> ../dm-7
    lrwxrwxrwx 1 yashan yashan 7 Jul 11 14:44 sys1 -> ../dm-4
    lrwxrwxrwx 1 yashan yashan 7 Jul 11 14:44 sys2 -> ../dm-6
    ```

    And verify that the user and group of the devices point to `yashan`, and the privilege is `0660`:

    ```shell
    # ls -l /dev/dm-4
    brw-rw-rw- 1 yashan yashan 253, 4 Jul 11 14:44 /dev/dm-4

    # Other devices similar
    ```

2. Execute the following command on all servers in the cluster to confirm that **all** bound multipath WWIDs are consistent across servers.

    ```shell
    # Compare the output results from each server for consistency
    # /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/data
    # /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys0
    # /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys1
    # /lib/udev/scsi_id --whitelisted  --device=/dev/yfs/sys2
    ```
    If the WWID corresponding to the same path differs on two servers, it indicates that their mounted shared storage is not the same LUN and network administrators should be contacted for resolution.

<span id="modify_multipath_conf" name="modify_multipath_conf" class="yaslink"></span>
#### Step 5 (Optional): Modify Multipath Configuration File

Before using IO Fencing functionality based on SCSI persistent reservations, this operation must be completed on each server in YAC.

1. Ensure the mpathpersist tool is installed.

    ```shell
    # mpathpersist -h
    ```

2. Modify the /etc/multipath.conf file on each server to add the "reservation_key file" item to the "defaults" section.

    Example of an incomplete file content; only add options as instructed, and do not modify other configuration items.

    ```shell
    defaults {
        reservation_key file
    }
    ```

### Method 2: Binding via SCSI Device WWID
>**Caution**:
>
> - It is recommended to use the multipath method to bind storage devices. Using WWID binding does not meet high availability requirements and is not advised for production environments.
> - When using WWID to bind storage devices, there should only be one device with the same WWID under the "/dev" directory; otherwise, the binding will fail.

The operation for generating configuration files in "Step 1: Generate Configuration File" differs from that of using multipath binding. The operations in "Step 5 (Optional): Modify Multipath Configuration File" do not need to be executed, while other steps remain the same.

#### Generate Configuration File
1. Create a script file named `genDevRuleByWWID.sh` in the root directory:

    ```shell
    # cd
    # vi genDevRuleByWWID.sh
    ```

2. Enter the following content and save it:

    ```shell
    #/bin/bash

    # Please modify to each device path
    DEVICES=("/dev/sdc" "/dev/sdd" "/dev/sde" "/dev/sdf")
    # Please specify the binding names for these devices in the order of the DEVICES array, it is recommended to use names related to the business
    TARGETS=("data0"     "sys0"    "sys1"     "sys2")
    # Specify the binding directory
    YFS_DISK_DIR="yfs"

    # Please keep as default, ensure the yashan user exists
    USER="yashan"
    GROUP="yashan"
    UGROUP="$USER:$GROUP"

    if [ ${#TARGETS[@]} != ${#DEVICES[@]} ]
    then
        echo "DEVICES and TARGETS count does not match"
        exit 1
    fi

    if [ ${#TARGETS[@]} == 0 ]
    then
        echo "no targets found"
        exit 1
    fi

    curDir=$(cd "$(dirname "$0")";pwd)
    rm -f $curDir/yashan-device-rule.rules

    count=${#TARGETS[@]}

    for ((i = 0; i < $count; i ++))
    do
        device=${DEVICES[$i]}
        target_name=${TARGETS[$i]}
        target="${YFS_DISK_DIR}/${target_name}"
        
        echo "KERNEL==\"sd*\", SUBSYSTEM==\"block\", PROGRAM==\"/usr/lib/udev/scsi_id -g -u -d /dev/\$parent\", RESULT==\"`/usr/lib/udev/scsi_id -g -u -d $device`\",SYMLINK+=\"$target\",OWNER=\"$USER\", GROUP=\"$GROUP\", MODE=\"0660\", RUN+=\"/bin/sh -c 'chown -R $UGROUP /dev/$target'\"" >> $curDir/yashan-device-rule.rules
    done
    ```

3. Execute the script to generate the configuration file:

    ```shell
    # sh ./genDevRuleByWWID.sh
    ```

    After successful execution of the script, a binding configuration file named `yashan-device-rule.rules` will be generated in the current directory. The content is as follows:

    ```shell
    # cat yashan-device-rule.rules
    KERNEL=="sd*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d /dev/$parent", RESULT=="36a01c8d100cd2c1b777f88dd000002b4",SYMLINK+="yfs/data0",OWNER="yashan", GROUP="yashan", MODE="0660", RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
    KERNEL=="sd*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d /dev/$parent", RESULT=="36a01c8d100cd2c1b777f88be000002b3",SYMLINK+="yfs/sys0",OWNER="yashan", GROUP="yashan", MODE="0660", RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
    KERNEL=="sd*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d /dev/$parent", RESULT=="36a01c8d100cd2c1b777f88be000002b5",SYMLINK+="yfs/sys1",OWNER="yashan", GROUP="yashan", MODE="0660", RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
    KERNEL=="sd*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d /dev/$parent", RESULT=="36a01c8d100cd2c1b777f88be000002b6",SYMLINK+="yfs/sys2",OWNER="yashan", GROUP="yashan", MODE="0660", RUN+="/bin/sh -c 'chown -R yashan:yashan /dev/yfs/data0'"
    ```
