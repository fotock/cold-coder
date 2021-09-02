# CentOS

目前在用7。

## 最大进程数和最大文件打开数修改

1. 修改`/etc/security/limits.conf`文件

```conf
*   soft    nofile  204800
*   hard    nofile  204800
*   soft    nproc   204800
*   hard    nproc   204800
```

2.修改`/etc/security/limits.d/xx-nproc.conf`文件

```conf
*   soft   nproc   204800
*   hard   nproc   204800
```

3.更新系统

```bash
yum update -y
yum groupinstall "Development Tools" "Development Libraries" "Base" "System Tools"
yum -y install gcc gcc-c++ cmake bison ncurses-devel openssl openssl-devel pcre pcre-devel libtool-ltdl-devel libssh2 libssh2-devel libxml2 libxml2-devel libcurl libcurl-devel zlib-devel libmcrypt libmcrypt-devel libevent libevent-devel java-1.8.0-openjdk java-1.8.0-openjdk-devel libwebp libwebp-devel libXpm libXpm-devel libpng libjpeg libpng-devel libjpeg-devel openjpeg openjpeg-devel openjpe  openjpeg2 openjpeg2-devel openjpeg2-libs libwebp libwebp-devel libxslt-devel libunwind gd ImageMagick ImageMagick-devel recode recode-devel mhash mcrypt libaio freetype* redis GeoIP GeoIP-devel libzip libzip-devel perl-devel perl-ExtUtils-Embed 
```

reboot

## 目录结构

```conf
/web
    /backup
    /cron
    /data
    /log
    /script
    /www
```
