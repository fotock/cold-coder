# Gogs Git 代码管理网站

## 二进制安装

```bash
useradd git
passwd git
```

## 路径与自动启动

安装到 `/home/git` 下. 设置服务：

``` bash
cp /home/git/gogs/scripts/systemd/gogs.service /lib/systemd/system
```

自动启动

```bash
systemctl daemon-reload
systemctl start gogs.service
systemctl enable gogs.service
```

## 详细说明

https://www.vultr.com/docs/how-to-install-gogs-0-11-53-on-centos-7
