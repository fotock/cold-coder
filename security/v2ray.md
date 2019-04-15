# V2Ray

## 一键安装

```bash
#!/bin/bash

bash <(curl -L -s https://install.direct/go.sh)

TMP_UUID=`uuidgen -r`

cat <<EOT > /etc/v2ray/config.json
{
  "inbounds": [{
    "port": 60007,
    "protocol": "vmess",
    "settings": {
      "clients": [{ 
        "id": "${TMP_UUID}",
        "alterId": 32,
        "security": "auto",
        "level": 0
      }]
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  }]
}
EOT

firewall-cmd --zone=public --add-port=60007/udp --permanent
firewall-cmd --zone=public --add-port=60007/tcp --permanent
firewall-cmd --reload
service v2ray restart
service v2ray status

# 自动启动
systemctl enable v2ray
```

## 参考资料

官网: https://www.v2ray.com

Android: https://github.com/2dust/v2rayNG

Mac: https://github.com/yanue/V2rayU

## TSL+域名+证书版

不稳定。

示例配置:

<img src="/assets/v2rayu_tls.png" alt="" style="width:100%; max-width:400px">



## 与BBR Plus结合

增强网络速度.

主要解决：BBR在高丢包率下易失速以及BBR收敛慢的问题。

本脚本只支持KVM不支持ovz
CentOS一键安装脚本（自动安装内核并启用）：

```bash
wget "https://github.com/cx9208/bbrplus/raw/master/ok_bbrplus_centos.sh" && chmod +x ok_bbrplus_centos.sh && ./ok_bbrplus_centos.sh
```

安装后，执行uname -r，显示4.14.90则切换内核成功.
执行lsmod | grep bbr，显示有bbrplus则开启成功.
