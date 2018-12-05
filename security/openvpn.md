# OpenVPN

## 软件特点
- 用于创建加密通道，允许创建的VPN使用公开密钥、电子证书、或者用户名／密码来进行身份验证。
- 大量使用了OpenSSL加密库中的SSLv3/TLSv1协议函数库。
- 能在Mac OS X与Windows、Solaris、Linux、OpenBSD、FreeBSD以及Android和iOS上运行，并包含了许多安全性的功能。
- 并不是一个基于Web的VPN软件，也不与IPsec及其他VPN软件包兼容。
- 服务器回送证书完成握手创建有效加密连接时有可能会被干扰连接，在使用TCP协议模式时握手可能会被连接重置，而使用UDP协议时含有服务器认证证书的数据包可能会被故意丢弃，使其无法创建有效加密连接而连接失败。


## 同类软件对比
|   类型   |   PPTP   |    L2TP/IPSec     |   OpenVPN         |
|---|---|---|---|---|---|
| 加密长度 |   128位   |   256位   |   160位， 256位   |
| 支持平台 |   Windows, Mac, Linux, Android, iOS   |   Windows, Mac, Linux, Android, iOS   |  Windows, Linux, Mac, Android  |
| 安全级别 |   基本加密   |   最高级加密。检查数据完整性并对数据二次加密。   |   最高级加密。用数字证书校验数据。   |
| 传输速度 |   快        |   需要更多的CPU处理。   |   性能最佳的协议。<br>即便对高延迟和远距离的连接，速度也很快。   |
| 优点 | 易于安装<br>大部分OS内置支持 | 正确安装后加密很强<br>正确安装后可较好防止中间人攻击<br>通常不需要安装额外软件 | 易于安装<br>强加密<br>可较好防止中间人攻击<br>防火墙兼容性很好。难以被block |
| 缺点 | 加密不安全<br>不能应对中间人攻击<br>防火墙与路由器兼容差<br>易被网络提供商block   |   安装复杂<br>配置不好的话连接不安全<br>易被网络提供商block   |   要安装额外的客户端软件   |



## 服务器安装 (以 OpenVPN Access Server 在 CentOS 上为例)

服务器要求不高，单核+1G内存足够。网上的非AS版本安装相对太繁琐。推荐直接装Access Server版。

在线向导： https://openvpn.net/quick-start-guide

1. 下载安装对应操作系统(Ubuntu, Debian, Redhat, CentOS)的安装库文件

```bash
wget http://swupdate.openvpn.org/as/openvpn-as-2.5.2-CentOS7.x86_64.rpm
yum install -y net-tools
rpm -i openvpn-as-*.rpm
```

2. 设置管理界面的用户的登录密码

```bash
passwd openvpn
```

缺省配置的OpenVPN服务器搞定！

至此，一般来讲登录客户端下载地址<br>
https://your.ip.address:943  <br>
登录时选择Login, 下载对应的Mac, Android, iOS 客户端和连接配置文件(Connection profile) 就可以用了.

免费版本只支持 2 个用户同时连接.



### 参数管理与配置地址

https://your.ip.address:943/admin

#### TLS Settings
页面所有配置项仅把 TLS 1.2 设置为 On.

#### Network Settings
- Port number 可以修改.

#### Advanced VPN
- Connection Security Refresh: 可以更新为较短的时间，如 120 分钟.

#### Client Settings
- Customize Client Web Server UI: 关闭不需要的项.



## 防火墙规则配置 (可选项)

参考配置: https://www.linode.com/docs/networking/vpn/set-up-a-hardened-openvpn-server

#### /etc/iptables/rules.v4

```conf
*filter

# Allow all loopback (lo) traffic and reject anything
# to localhost that does not originate from lo.
-A INPUT -i lo -j ACCEPT
-A INPUT ! -i lo -s 127.0.0.0/8 -j REJECT
-A OUTPUT -o lo -j ACCEPT

# Allow ping and ICMP error returns.
-A INPUT -p icmp -m state --state NEW --icmp-type 8 -j ACCEPT
-A INPUT -p icmp -m state --state ESTABLISHED,RELATED -j ACCEPT
-A OUTPUT -p icmp -j ACCEPT

# Allow SSH.
-A INPUT -i eth0 -p tcp -m state --state NEW,ESTABLISHED --dport 22 -j ACCEPT
-A OUTPUT -o eth0 -p tcp -m state --state ESTABLISHED --sport 22 -j ACCEPT

# Allow UDP traffic on port 1194.
-A INPUT -i eth0 -p udp -m state --state NEW,ESTABLISHED --dport 1194 -j ACCEPT
-A OUTPUT -o eth0 -p udp -m state --state ESTABLISHED --sport 1194 -j ACCEPT

# Allow DNS resolution and limited HTTP/S on eth0.
# Necessary for updating the server and timekeeping.
-A INPUT -i eth0 -p udp -m state --state ESTABLISHED --sport 53 -j ACCEPT
-A OUTPUT -o eth0 -p udp -m state --state NEW,ESTABLISHED --dport 53 -j ACCEPT
-A INPUT -i eth0 -p tcp -m state --state ESTABLISHED --sport 80 -j ACCEPT
-A INPUT -i eth0 -p tcp -m state --state ESTABLISHED --sport 443 -j ACCEPT
-A OUTPUT -o eth0 -p tcp -m state --state NEW,ESTABLISHED --dport 80 -j ACCEPT
-A OUTPUT -o eth0 -p tcp -m state --state NEW,ESTABLISHED --dport 443 -j ACCEPT

# Allow traffic on the TUN interface so OpenVPN can communicate with eth0.
-A INPUT -i tun0 -j ACCEPT
-A OUTPUT -o tun0 -j ACCEPT

# Log any packets which don't fit the rules above.
# (optional but useful)
-A INPUT -m limit --limit 3/min -j LOG --log-prefix "iptables_INPUT_denied: " --log-level 4
-A FORWARD -m limit --limit 3/min -j LOG --log-prefix "iptables_FORWARD_denied: " --log-level 4
-A OUTPUT -m limit --limit 3/min -j LOG --log-prefix "iptables_OUTPUT_denied: " --log-level 4

# then reject them.
-A INPUT -j REJECT
-A FORWARD -j REJECT
-A OUTPUT -j REJECT

COMMIT
```

#### /etc/iptables/rules.v6

```conf
*filter

-A INPUT -j REJECT
-A FORWARD -j REJECT
-A OUTPUT -j REJECT

COMMIT
```

#### 激活规则

```bash
iptables-restore < /etc/iptables/rules.v4
ip6tables-restore < /etc/iptables/rules.v6
```


