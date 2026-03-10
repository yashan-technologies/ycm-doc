To ensure the normal installation and operation of YashanDB, please configure the required dependencies in all server environments according to the following sources and minimum version requirements.

## Dependency List

The dependencies required for the database operation are shown in the table below.

|Name |Minimum Version Requirement |Open Source License |Source Installation Link |Source Provider |
| ---------- | ------ | ------------------------------------------------------------------ | --------------------------------------------- | -------------------- |
| openssl    | 1.0.0  | [Apache-2.0](https://github.com/openssl/openssl/blob/OpenSSL_1_1_1/LICENSE)      | [https://github.com/openssl/openssl](https://github.com/openssl/openssl)            | openssl              |
| lz4        | 1.8.3  | [BSD 2-Clause](https://github.com/lz4/lz4/blob/v1.9.3/LICENSE)        | [https://github.com/lz4/lz4](https://github.com/lz4/lz4)               | lz4                  |
| zlib       | 1.2.7 | [zlib license](https://zlib.net/zlib_license.html)                    | [https://github.com/madler/zlib](https://github.com/madler/zlib)           | Mark Adler           |
| zstd       | 1.4.4  | [BSD License](https://github.com/facebook/zstd/blob/v1.5.2/LICENSE)   | [https://github.com/facebook/zstd](https://github.com/facebook/zstd)         | Facebook             |
| gmssl      | 3.1.1                      | [Apache-2.0](https://gitee.com/yashan_tech/GmSSL/blob/v3.1.1/LICENSE) | [https://gitee.com/yashan_tech/GmSSL](https://gitee.com/yashan_tech/GmSSL) | Shenzhen Yashan Technology Co., Ltd. |
| bitshuffle | 0.5.1                      | [MIT](https://gitee.com/yashan_tech/bitshuffle/blob/0.5.1/LICENSE) | [https://gitee.com/yashan_tech/bitshuffle](https://gitee.com/yashan_tech/bitshuffle) | Shenzhen Yashan Technology Co., Ltd. |

> **Note**:
>
> gmssl and bitshuffle are embedded in the YashanDB installation package and do not need to be installed manually.

The dependencies required for the database operation and maintenance tools are shown in the table below.

|Name |Version |Open Source License |Source Installation Link |Source Provider |
| ----- | ------ | ---------------------------------------------------------------- | ------------------------------------------- | ---------- |
| monit | 5.28.0 | [AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.html)              | [https://bitbucket.org/tildeslash/monit](https://bitbucket.org/tildeslash/monit) | Tildeslash |
| fio   | 3.34   | [GPL-2.0](https://github.com/axboe/fio/blob/fio-3.34/MORAL-LICENSE) | [https://github.com/axboe/fio](https://github.com/axboe/fio)           | Jens Axboe |
| iperf | 2.0.13 | [BSD License](https://github.com/esnet/iperf/blob/master/LICENSE)   | [https://github.com/esnet/iperf](https://github.com/esnet/iperf)         | ESnet      |

> **Note**:
>
> The dependencies required for the database operation and maintenance tools are all embedded in the YashanDB installation package and do not need to be installed manually.

## Check and Install Dependencies

The following commands serve as examples for CentOS 7.6 and KylinOS V10.

### libcrypto.so and libssl.so

- **Source**: openssl

- **Version Requirement**: 1.0.0 or above. The versions of libcrypto.so and libssl.so must be consistent. However, openssl 1.1.1 or above is required in the following scenarios:
  
  - Using key management functionality
  
  - Using national secret algorithms (SM2, SM3, SM4, etc.), such as TDE, backup set encryption, PL source encryption, user password encryption, encryption/decryption related built-in functions (CRYPT_ENCRYPT, CRYPT_DECRYPT, CRYPT_HASH, CRYPT_HMAC, CRYPT_SIGN, CRYPT_VERIFY, etc.)

- **Check Method**: `ldconfig -p | grep -E "libcrypto.so|libssl.so"`

- **Installation Method**: `yum install openssl openssl-devel`

> **Caution**:
>
> If, after installing openssl, the operating system still does not have libcrypto.so or libssl.so, you need to manually create a symbolic link.

Example:

```shell
## Check for libcrypto.so
# ldconfig -p | grep libcrypto.so
        libcrypto.so.10 (libc6,x86-64) => /lib64/libcrypto.so.10
## If the query result does not include libcrypto.so, create a symbolic link for libcrypto.so
# ln -s /lib64/libcrypto.so.10 /lib64/libcrypto.so

## Check for libssl.so
# ldconfig -p | grep libssl.so
        libssl.so.10 (libc6,x86-64) => /lib64/libssl.so.10
## If the query result does not include libssl.so, create a symbolic link for libssl.so
# ln -s /lib64/libssl.so.10 /lib64/libssl.so

## After creating the symbolic links, refresh the dynamic library
# ldconfig
```

### liblz4.so

- **Source**: lz4

- **Check Method**: `ldconfig -p | grep liblz4.so`

- **Installation Method**: `yum install lz4`

### libz.so

- **Source**: zlib

- **Check Method**: `ldconfig -p | grep libz.so`

- **Installation Method**: `yum install zlib`

### libzstd.so

- **Source**: zstd

- **Check Method**: `ldconfig -p | grep libzstd.so`

- **Installation Method**: `yum install libzstd`