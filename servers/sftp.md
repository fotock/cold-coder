# Linux SSH/SFTP 服务

## 1. SFTP 用户、目录与权限

```bash
groupadd sftpclients
```

```bash
#! /bin/bash

USERSHORT=user1
FUSER="sftp${USERSHORT}"

SFTP_BASE=/data/sftp
chown root:sftpclients $SFTP_BASE

SFTP_HOME="${SFTP_BASE}/${FUSER}"

mkdir -p "${SFTP_HOME}/client/Result"
#mkdir -p "${SFTP_HOME}/client/clientA"
#mkdir -p "${SFTP_HOME}/client/clientB"
#mkdir -p "${SFTP_HOME}/client/clientC"

useradd -g sftpclients -d "${SFTP_HOME}/client" -s /sbin/nologin ${FUSER}

# 账户HOME目录权限必须给root:组
chown -R root:sftpclients "${SFTP_HOME}"

# 允许用户操作的目录赋权给该用户
chown -R ${FUSER}:sftpclients "${SFTP_HOME}/client"

# 先创建 VUE
mount --bind /data/sftp/VUE/client/${USERSHORT}/Result "${SFTP_HOME}/client/Result"
mount -o remount,bind,ro /data/sftp/VUE/client/${USERSHORT}/Result "${SFTP_HOME}/client/Result"

passwd ${FUSER}
```

## 2. SFTP SSHD Config

/etc/ssh/sshd_config:

```cnf
# 添加
Subsystem       sftp    internal-sftp

# 放在文件末尾
Match Group sftpclients
    ChrootDirectory /data/sftp/%u
    ForceCommand internal-sftp
    PermitTunnel no
    AllowAgentForwarding no
    AllowTcpForwarding no
    X11Forwarding no
    PermitTTY no
```


## 3. 不同SFTP账户之间创建软链接

ln 是不能用的。需要用 mount 创建，类似 ln。
源目录(账户A下)和目标目录(账户B下)要先创建。

``` bash
mount --bind /data/sftp/源账户A/client/Result /data/sftp/目标账户B/client/Result

# 挂载只读
mount -o remount,bind,ro /data/sftp/源账户A/client/Result /data/sftp/目标账户B/client/Result
```

### 更多挂载命令

```bash
# 查看所有挂载
findmnt

# 卸载
umount /data/sftp/目标账户B/client/Result
```

### 参考资源

1. [Understanding Bind Mounts](https://www.baeldung.com/linux/bind-mounts)


## 4. SSH 安全配置/漏洞检测

1. https://book.hacktricks.xyz/network-services-pentesting/pentesting-ssh
2. [ssh-audit 配置](https://www.ssh-audit.com/hardening_guides.html#rhel7)
3. [SSH 安全在线检测](https://www.ssh-audit.com)
4. [GitHub 工具](ttps://github.com/jtesta/ssh-audit)
