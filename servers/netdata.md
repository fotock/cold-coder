# Netdata
分布式实时性能与健康监测系统。可实时监测CPU、内存、硬盘、网络、各类应用的运行情况。
适用于大部分非 Windows 服务器.

<img src="https://cloud.githubusercontent.com/assets/2662304/14092712/93b039ea-f551-11e5-822c-beadbf2b2a2e.gif" alt="" >

## Github 地址:
https://github.com/firehol/netdata

## 安装
1. 下载安装
  找到最新版本，下载zip文件安装
2. 更多安装方式
  参见 https://github.com/firehol/netdata/wiki/Installation

## 配置 netdata.conf
CentOS 下缺省位于 /etc/netdata

### 1. 注册中心 (registry) 配置
```conf
[global]
        hostname = 你的主机名
        history = 3996
        update every = 5
[registry]
        enabled = yes
        registry to announce = http://注册中心服务器IP:19999
```
### 2. 非注册中心配置
```conf
[global]
        hostname = 你的主机名
        history = 3996
        update every = 5
[registry]
        enabled = no
        registry to announce = http://注册中心服务器IP:19999
```

## 其他可监测的服务
- Apache 和 lighttpd
- Nginx
- Tomcat
- MySQL
- Postgres
- Redis
- Mongodb
- Memcached
- Squid
- HAproxy
- varnish
- PHP-FPM
- IPFS
- Postfix 邮件服务

## 自定义的监测面板
/usr/share/netdata/web/center.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>服务监控中心</title>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        body>h1 {
            color:#888; 
            text-align:center;
            font-weight:200; 
            font-family:'helvetica neue', 'PingFang SC',Arial;
        }
        .loading:before {
            display:block; 
            content:"加载中..."; 
            color:#aaa; 
            font-size:4vw; 
            text-align:center;
            margin-top:40vh;
            font-weight:200;
        }
        .server {
            text-align:right;
            padding:9px 0;
            position:relative; 
            border-bottom:.5px solid rgba(0,0,0,.4);
        }
        .w12{display:inline-block;min-width:30%;}
        .w13{display:inline-block;min-width:27%;}
        .w14{display:inline-block;min-width:17%;}
        .hostname {
            display:inline-block;
            padding:10px; 
            line-height:190px;
            position:absolute;
            top:0;
            left:0;
        }
        .hidden {display:none;}
        a:link, a:visited {color:#888; font-size:1.5em;font-weight:200;}
        span.netdata-legend-name,span.netdata-legend-title-time {font-weight:normal;}
    </style>
</head>
<script>
var eths = { 'abc': 'eth0' };

var showByServer = function(u) {

   var html = '<div class="server">'
        + '<div class="hostname"><a href="'+u.url+'index.html" title="">' + (u.name) + '</a></div>'
        + '<div class="w14">'
        + '<div data-netdata="system.ram" '
            + 'data-host="'+ u.url +'" '
            + 'data-colors="#4e4 #e44 #ee4 #e4e #44e" '
            + 'data-chart-library="dygraph" '
            + 'data-width="100%" '
            + 'data-height="190" '
            + 'data-title="内存" '
            + 'data-update_every="10">'
        + '</div>'
        + '</div>'

        + '<div class="w13">'
        + '<div data-netdata="net.'+ (eths[u.name]||'eth1') +'" '
            + 'data-host="'+ u.url +'" '
            + 'data-chart-library="dygraph" '
            + 'data-colors="#4e4 #e44 #ee4 #e4e" '
            + 'data-title="公网'+ (eths[u.name]||'eth1') +'" '
            + 'data-decimal-digits="0" '
            + 'data-units="kb/s" '
            + 'data-width="100%" '
            + 'data-height="190">'
        + '</div>'
        + '</div>'

        + '<div class="w13">'
        + '<div data-netdata="system.cpu" '
            + 'data-host="'+ u.url +'" '
            + 'data-chart-library="dygraph" '
            + 'data-width="100%" '
            + 'data-height="190px" '
            + 'data-units="%" '
            + 'data-title="处理器" '
            + 'data-dygraph-valuerange="[0,20]" '
            + 'data-update_every="5" '
            + 'role="application">'
        + '</div>'
        + '</div>'

        + '<div class="w14">'
        + '<div data-netdata="disk_space._" '
            + 'data-host="'+ u.url +'" '
            + 'data-colors="#4e4 #e44 #ee4 #e4e" '
            + 'data-chart-library="dygraph" '
            + 'data-width="100%" '
            + 'data-height="190px" '
            + 'data-update_every="10" '
            + 'data-title="磁盘">'
        + '</div>'
        + '</div>'
    + '</div>';

    document.getElementById('idBody').insertAdjacentHTML('beforeend', html);    
}

var netdataTheme = 'slate'; // this is dark
var netdataRegistryCallback = function(machines_array) {
    if(machines_array) {
        function name_comparator_desc(a, b) {
            if (a.name > b.name) return -1;
            if (a.name < b.name) return 1;
            return 0;
        }

        var tmp = [];
        for (i=0; i<machines_array.length; i++) {
            var el = machines_array[i];
            tmp.push(el);
        }
        var machines = tmp.sort(name_comparator_desc);
        var len = machines.length;
        while(len--) {
            var u = machines[len];
            showByServer(u);
            NETDATA.updatedDom();
        }
    }
    document.getElementById('idBody').className="";
    document.getElementById('headline').className="";
};
</script>
<script type="text/javascript" src="dashboard.js?v20170724-1"></script>
<body id="idBody" class="loading">
  <h1 id="headline" class="hidden">服务监控中心</h1>
</body>
</html>
```