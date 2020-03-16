# Shadowsocks

## 免费SS服务器列表

- https://shadowsockser.com/free-shadowsocks-server-list

## Shadowsocks-libev 版安装

```bash
wget --no-check-certificate -O shadowsocks-libev.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-libev.sh
chmod +x shadowsocks-libev.sh
./shadowsocks-libev.sh 2>&1 | tee shadowsocks-libev.log
```

### 使用插件启动

原网页: https://github.com/shadowsocks/simple-obfs

#### 安装 simple obfs

```bash
yum install -y gcc autoconf libtool automake make zlib-devel openssl-devel asciidoc xmlto

git clone https://github.com/shadowsocks/simple-obfs.git
cd simple-obfs
git submodule update --init --recursive
./autogen.sh
./configure && make
make install
```

```conf

```

启动

```bash
systemctl start shadowsocks

# 自动启动
systemctl enable shadowsocks

#ss-server -c /etc/shadowsocks-libev/config.json --plugin obfs-server --plugin-opts "obfs=tls" &
```

### 卸载

```bash
./shadowsocks-libev.sh uninstall
```

### 控制

启动：/etc/init.d/shadowsocks start
停止：/etc/init.d/shadowsocks stop
重启：/etc/init.d/shadowsocks restart
查看状态：/etc/init.d/shadowsocks status

## 与BBR Plus结合

增强网络速度.

主要解决：BBR在高丢包率下易失速以及BBR收敛慢的问题。

本脚本只支持KVM不支持ovz.

CentOS一键安装脚本（自动安装内核并启用）：

```bash
wget "https://github.com/cx9208/bbrplus/raw/master/ok_bbrplus_centos.sh" && chmod +x ok_bbrplus_centos.sh && ./ok_bbrplus_centos.sh
```

安装后，执行uname -r，显示4.14.90则切换内核成功.
执行lsmod | grep bbr，显示有bbrplus则开启成功.

## 推荐直接一键安装streisand

DNS-over-HTTPS (cloudfare) 要禁用。Linode目前不支持。 (2020-03-16)