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

reboot

## 目录结构

···conf
/web
    /backup
    /cron
    /data
    /log
    /script
    /www
```


