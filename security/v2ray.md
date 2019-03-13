# V2Ray

安装脚本

```bash
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
```
