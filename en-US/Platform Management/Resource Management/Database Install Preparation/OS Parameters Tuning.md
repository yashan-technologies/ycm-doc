To ensure the performance of YashanDB, it is recommended to adjust the following operating system parameters for all servers running YashanDB. Especially during Distributed Deployment, if the following adjustments are not executed, extreme scenarios may occur in the future where business operations exhaust system resources, potentially resulting in service exits, business execution stalls, and errors.

## Set Server Hostname (YAC)

**This operation must be performed if deploying a YashanDB YAC.**

The server name requirements are as follows:
- The name must consist of letters, numbers, hyphens (-), and underscores (_), and must start with a letter, with a length of [4,64] characters.
- Server names within the same YashanDB YAC must be unique.
- It is recommended to run only one instance on each server, and if a server needs to run multiple instances, the server name must be set to [3,63] characters.

The procedure is as follows:

1. Execute the following command to view the hostname.
    ```shell
    # hostnamectl
    ```

2. Execute the following command to set the hostname as needed.
    ```shell
    # hostnamectl set-hostname instance1
    ```

## Disable Swap Partition

Use `sysctl -a | grep swappiness` to check the current status. It is recommended to set it to 0 to reduce memory swapping and avoid impacting system performance.

```bash
# sysctl -w vm.swappiness=0
# echo "vm.swappiness=0">> /etc/sysctl.conf
```

## Adjust Automatic Allocation of Local Port Range

Use `cat /proc/sys/net/ipv4/ip_local_port_range` to check the available local port range. The lower limit is recommended to be greater than 30000 to reduce port conflicts.

```bash
# sysctl -w net.ipv4.ip_local_port_range='32768 60999' 
# echo "net.ipv4.ip_local_port_range=32768 60999" >> /etc/sysctl.conf
```

This operation is also effective for IPv6.

## Adjust Process VMA Limit

Use `sysctl -a | grep vm.max_map_count` to check the current high value. It is recommended to be greater than 2000000 to increase the VMA size that the database process can hold.

```bash
# sysctl -w vm.max_map_count=2000000
# echo "vm.max_map_count=2000000" >> /etc/sysctl.conf 
```

## Adjust Resource Limit Values

Adjust some resource limit values (use `ulimit -a` to view all resource limit values) to recommended values or above. Detailed information is shown in the table below, where `yashan` is an example of the planned database installation user (specific username should be based on actual planning).

|Resource Item |Recommended Value |COMMAND |Resource Item Description |
| ------------------ | --------- | --------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| open files          | 1048576           | ulimit -n 1048576<br>vi /etc/security/limits.conf add:<br>`yashan soft nofile 1048576`<br>`yashan hard nofile 1048576`   | Number of open files per process   |
| max user processes   | 1048576           | ulimit -u 1048576<br>vi /etc/security/limits.conf add:<br/>`yashan soft nproc 1048576`<br/>`yashan hard nproc 1048576` | Maximum user process count |
| max memory size     | unlimited         | ulimit -m unlimited<br>vi /etc/security/limits.conf add:<br/>`yashan soft rss unlimited`<br/>`yashan hard rss unlimited` | Maximum resident set size |
| stack size          | 8192              | ulimit -s 8192<br>vi /etc/security/limits.conf add:<br/>`yashan soft stack 8192`<br/>`yashan hard stack 8192`            | Maximum stack space for each process |

## Configure Huge Pages Memory

YashanDB utilizes huge pages memory to enhance memory access efficiency. Standard huge pages memory is pre-allocated at system startup, while transparent huge pages are dynamically allocated at runtime, which may cause runtime errors and lead to YashanDB being terminated.

It is recommended to configure the operating system during installation to enable huge pages memory and disable the Transparent HugePages option. After the YashanDB service is installed, if huge pages memory is to be used, please set the database configuration parameter `USE_LARGE_PAGES` to TRUE or ONLY.

For more details about huge pages memory, please refer to the [Linux Kernel Documentation](https://www.kernel.org/doc/html/latest/admin-guide/mm/hugetlbpage.html).

### Enable Huge Pages Memory
1. Execute the following command to check the current huge pages memory configuration: (the echoed information may vary)
   
   ```bash
   # grep -i huge /proc/meminfo
   AnonHugePages:         0 kB
   HugePages_Total:       0
   HugePages_Free:        0
   HugePages_Rsvd:        0
   HugePages_Surp:        0
   Hugepagesize:       2048 kB
   ```
   - AnonHugePages: Total amount of memory currently used by transparent huge pages, referenced for disabling in this document.
   - HugePages_Total: Total number of pages in the huge pages pool.
   - HugePages_Free: Number of free pages in the huge pages pool.
   - HugePages_Rsvd: Number of pages reserved by the system. Indicates that the application has requested the corresponding number of huge pages, but since the application has not accessed these pages, the system has not truly allocated them from physical memory. The application can still request `HugePages_Free - HugePages_Rsvd` additional huge pages.
   - HugePages_Surp: When `nr_overcommit_hugepages` is enabled, the system allows swapping out some huge pages, allowing the application to request more pages than the capacity of the huge pages pool. This may have a negative impact on performance if the application frequently accesses huge pages.
   - Hugepagesize: The size of a huge page, which varies depending on the platform; x86 platforms support 2M and 1G, usually at 2M.

   If `HugePages_Total = 0`, `HugePages_Free = 0`, or `HugePages_Free - HugePages_Rsvd < YashanDB requirement`, the system configuration needs to be modified to enable huge pages memory or increase the capacity of the huge pages memory pool.

2. Estimate the huge pages memory requirement by first estimating YashanDB's huge pages memory requirement:

   |Configuration Parameter |Default Value |
   | ------------------- | ------ |
   | DATA_BUFFER_SIZE    | 32M    |
   | REDO_BUFFER_SIZE    | 8M     |
   | VM_BUFFER_SIZE      | 16M    |
   | SHARE_POOL_SIZE     | 64M    |
   | LARGE_POOL_SIZE     | 8M     |
   | DBWR_BUFFER_SIZE    | 4M     |
   | WORK_AREA_POOL_SIZE | 16M    |
   | AUDIT_QUEUE_SIZE    | 16M    |

   Calculate the total memory required for YashanDB, `UserSize`, which is the sum of all the aforementioned configuration items (items not configured are calculated using the default value). If planning to use MMS tablespace, estimate the total memory required for MMS tablespace as `MMSSize`.

   - YashanDB requires at least `LargePageSize = (UserSize + MMSSize + 64M) * 1.2` huge pages memory space.

   - The number of huge pages required for YashanDB is `HugePages = Ceil(LargePageSize / Hugepagesize)`.

   > **Caution**:
   > 
   > Allocating all memory to huge page management may prevent the system from starting or cause other issues. **Please use with caution.**
   > 
   > Ensure that the system memory capacity is sufficient to support the values configured for huge pages.

3. Enable huge pages memory and modify relevant configurations based on the estimated values:
   
   This document takes a huge page size of 2M and requests 16 huge pages (for a planned 32M huge pages memory) as an example. Production environment configurations should be based on actual planning.
   
   Edit the `/etc/sysctl.conf` file:
   
   ```bash
   # vi /etc/sysctl.conf
   ```
   
   Append the following configuration:
   ```text
   vm.nr_hugepages=16
   vm.nr_overcommit_hugepages=0
   ```

4. Reboot the system for changes to take effect.
   ```bash
   # reboot
   ```
   
### Disable Transparent Huge Pages

1. Some operating systems have the transparent huge pages option enabled by default. Execute the following command to confirm:

   ```shell
   ## Red Hat Enterprise Linux kernel
   # cat /sys/kernel/mm/redhat_transparent_hugepage/enabled

   ## Other kernels
   # cat /sys/kernel/mm/transparent_hugepage/enabled
   ```

   - If it displays `[always] madvise never` or `always [madvise] never`, it indicates that transparent huge pages are enabled.

   - If it displays `always madvise [never]`, it indicates that transparent huge pages are disabled.

2. Execute `vi /etc/default/grub`, and add or modify the parameter `transparent_hugepage=never` in `GRUB_CMDLINE_LINUX`.
   
   ```text
      GRUB_TIMEOUT=5
      GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
      GRUB_DEFAULT=saved
      GRUB_DISABLE_SUBMENU=true
      GRUB_TERMINAL_OUTPUT="console"
      GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet transparent_hugepage=never"
      GRUB_DISABLE_RECOVERY="true"
   ```
3. Check the current boot type of the system with the following command:
   ```bash
   # [ -d /sys/firmware/efi ] && echo UEFI || echo BIOS
   ```

   The paths for the startup files for the two types of boot are:
   - BIOS：`/boot/grub2/grub.cfg`

   - UEFI: `/boot/efi/EFI/<distro_name>/grub.cfg`, where `distro_name` is the name of the system distribution, such as `ubuntu`, `fedora`, `debian`, etc.

4. Execute the `grub2-mkconfig` command to reconfigure `grub.cfg`.

   ```bash
   ## BIOS boot
   # grub2-mkconfig -o /boot/grub2/grub.cfg
   ## UEFI boot
   # grub2-mkconfig -o /boot/efi/EFI/<distro_name>/grub.cfg
   ```
5. Restart the operating system to make the configuration permanent.
   ```bash
   # reboot
   ```
6. Verify that transparent huge pages are disabled:
   ```bash
   ## Red Hat Enterprise Linux kernel
   # cat /sys/kernel/mm/redhat_transparent_hugepage/enabled

   ## Other kernels
   # cat /sys/kernel/mm/transparent_hugepage/enabled
   ```
   It should display `always madvise [never]`.

## Enable Core Files (Optional)

When setting up a testing or experimental environment, it is recommended to enable core file generation (use `ulimit -c` to check the current status) to facilitate problem diagnosis.

In a production environment, **it is not recommended** to enable this switch to avoid impacting system efficiency.

```bash 
# ulimit -c unlimited 
# vi /etc/security/limits.conf

## Add the following information and save, where `yashan` is an example of the planned database installation user; the specific username should be based on actual planning
yashan soft core unlimited
yashan hard core unlimited
```
