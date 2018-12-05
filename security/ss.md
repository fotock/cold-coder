# ss

## Shadowsocks-libev 版安装
```bash
wget --no-check-certificate -O shadowsocks-libev.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-libev.sh
chmod +x shadowsocks-libev.sh
./shadowsocks-libev.sh 2>&1 | tee shadowsocks-libev.log
```

### 使用插件启动:
#### 安装 simple obfs
```bash
yum install gcc autoconf libtool automake make zlib-devel openssl-devel asciidoc xmlto

git clone https://github.com/shadowsocks/simple-obfs.git
cd simple-obfs
git submodule update --init --recursive
./autogen.sh
./configure && make
make install
```

启动

```bash
ss-server -c /etc/shadowsocks-libev/config.json --plugin obfs-server --plugin-opts "obfs=http" &
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

