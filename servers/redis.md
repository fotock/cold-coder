# Redis

## 源码安装最新版

```bash
wget https://download.redis.io/releases/redis-6.2.1.tar.gz
tar xzf redis-6.2.1.tar.gz
cd redis-6.2.1
make
```

## 路径

- 将 redis.conf 文件拷贝到 `/etc` 目录；
- 进入 `src` 目录下. 将所有可执行文件拷贝到 `/usr/bin` 目录。

## 修改配置信息

编辑 `/etc/redis.conf`, 按需修改数据目录等参数，并将 supervised 前面的注释去掉

```conf
supervised auto
```

## 创建服务

创建 `/usr/lib/systemd/system/redis.service` 文件。内容：

```conf
[Unit]
Description=redis
After=network.target

[Service]
Type=forking
PIDFile=/var/run/redis.pid
ExecStart=/usr/bin/redis-server /etc/redis.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

## 重载配置

```bash
sudo systemctl daemon-reload
```

## 使用

```bash
systemctl start redis
systemctl restart redis
systemctl stop redis
```

## 自动启动

```bash
systemctl enable redis
systemctl disable redis
```