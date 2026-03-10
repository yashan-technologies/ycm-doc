## Package List

Please obtain the software packages needed for your usage scenario from our technical support, including the following four types:

- YashanDB software packages installed on the server by the DBA.
- Client software packages used to connect to YashanDB, installed locally by the users who need to use them.
- Driver software packages for YashanDB interface program development, selected for installation by application developers as needed.

The list of all software packages provided by YashanDB is as follows: (xx.xx is the software version number)

|Software Package Name |Description |
| ------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| yashandb-xx.xx-linux-x86_64.tar.gz<br>yashandb-xx.xx-linux-aarch64.tar.gz                                                    | YashanDB installation package, suitable for database installation and deployment on Linux x86_64/arm64 platforms.               |
| yashandb-image-personal-xx.xx-linux-x86_64.tar.gz<br>yashandb-image-personal-xx.xx-linux-aarch64.tar.gz                      | YashanDB image package, suitable for database installation and deployment using Docker on Linux x86_64/arm64 platforms.        |
| yashandb-client-xx.xx-linux-x86_64.tar.gz<br>yashandb-client-xx.xx-linux-aarch64.tar.gz                                      | YashanDB client installation package, suitable for Linux x86_64/arm64 platforms.                                                |
| yashandb-client-xx.xx-1.el7.centos.x86_64.rpm<br>yashandb-client-xx.xx-1.el7.aarch64.rpm                                     | YashanDB client installation package, suitable for CentOS Linux x86_64/arm64 platforms.                                            |
| yashandb-client-xx.xx-windows-amd64.zip                                                                                       | YashanDB client installation package, suitable for Windows platforms.                                                              |
| yashandb-odbc-xx.xx-linux-x86_64.tar.gz<br>yashandb-odbc-xx.xx-linux-aarch_64.tar.gz<br>yashandb-odbc-xx.xx-windows-x86_64.zip | YashanDB ODBC driver installation package, suitable for Linux x86_64/arm64 and Windows platforms.                                     |
| yashandb-oci-xx.xx-linux-x86_64.tar.gz<br>yashandb-oci-xx.xx-linux-aarch64.tar.gz<br>yashandb-oci-xx.xx-windows-amd64.zip    | YashanDB OCI driver installation package, suitable for Linux x86_64/arm64 and Windows platforms.                                     |
| yasdb-x.x.x-py3-none-any.whl                                                                                                   | YashanDB Python driver installation package, suitable for all platforms.                                                            |
| yaspy-xx.xx-cp36-cp36m-linux-x86_64.whl<br>yaspy-xx.xx-cp36-cp36m-linux-aarch_64.whl<br>yaspy-xx.xx-cp36-cp36m-win_amd64.whl | YashanDB Python driver installation package, suitable for Linux x86_64/arm64 and Windows platforms.                                    |
| yashandb-jdbc-xx.xx.jar<br/>yashandb-jdbc-xx.xx-jre6.jar                                                                     | YashanDB JDBC driver installation package, suitable for all platforms.                                                              |
| yashandb-dotnet-xx.xx.zip                                                                                                      | YashanDB ADO.NET driver installation package, suitable for all platforms.                                                          |
| yashandb-go-xx.zip                                                                                                             | YashanDB Go driver installation package, suitable for Linux x86_64/arm64 and Windows platforms.                                      |

<span id="Downloadingpackage" name="Downloadingpackage" class="yaslink"></span>
## Package Download

Starting from this step, as well as for all subsequent server installation steps, the database installation user will perform the operations. Please first switch to the yashan user or log in to the server directly as the yashan user.

For the ease of presenting the installation and deployment process of YashanDB, we will uniformly use the installation package `yashandb-{version number}-linux-x86_64.tar.gz` as an example.

YashanDB supports downloading the software package on any server and initiating parallel installations on all other servers. This installation manual takes 192.168.1.2 as the installation server, and the following steps are executed on 192.168.1.2.

```shell
# su yashan
```

**1. Create install directory**

Create an empty directory under the yashan user's home directory to serve as the storage path for the installation package.

```shell
$ cd
$ mkdir install
```

**2. Download the software package**

Download the software package to the /home/yashan/install directory and extract the package:

```shell
 $ cd /home/yashan/install
 $ tar zxf yashandb-{version number}-linux-x86_64.tar.gz
```
