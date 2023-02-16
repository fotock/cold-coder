# Nginx

## 请求处理流

<img src="/assets/nginx-request-flow.png" alt="">


## 编译配置

```bash
./configure --with-threads \
 --with-pcre \
 --with-http_ssl_module \
 --with-http_gzip_static_module \
 --with-http_auth_request_module \
 --with-http_mp4_module \
 --with-http_stub_status_module \
 --with-http_v2_module  \
 --with-http_gunzip_module \
 --with-http_realip_module \
 --with-http_slice_module \
 --with-debug \
 --prefix=/web/soft/nginx \
 --http-log-path=/web/log/nginx/access.log \
 --error-log-path=/web/log/nginx/nginx.log \
 --http-client-body-temp-path=/var/tmp/nginx/client \
 --http-proxy-temp-path=/var/tmp/nginx/proxy \
 --http-fastcgi-temp-path=/var/tmp/nginx/fastcgi
```

## 目录结构

```
sbin
  \_ nginx
conf
  \_ nginx.conf
  \_ ...
  \_ ssl
       \_ a.com.ca
       \_ a.com.crt
       \_ a.com.key
  \_ conf.d
       \_ _proxy.conf
       \_ _ssl.conf
       \_ site-abc.com.conf
```

## 配置示例: nginx.conf

```nginx
user  www www;
worker_processes  auto;

error_log  /web/log/nginx/error.log;
error_log  /web/log/nginx/error-notice.log  notice;
error_log  /web/log/error-info.log  info;

events {
    use epoll;
    worker_connections  50000;
    worker_rlimit_nofile 204800;
    multi_accept on;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    index    index.html index.htm index.php;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_user_agent" "$http_referer"';

    log_format  detailed  '"$time_local", "$server_protocol", "$host", $status, $request_time, "$remote_addr", '
        '"$request_method", "$request_uri", "$http_user_agent", "$http_referer", $body_bytes_sent, $gzip_ratio';

    sendfile  on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout  65;
    keepalive_requests 100000;
    client_max_body_size 90m;
    types_hash_max_size 4096;

    gzip  on;
    gzip_vary on;
    gzip_disable "msie6";
    gzip_comp_level 4;
    gzip_min_length  1k;
    gzip_buffers 4 64k;
    gzip_types text/plain application/xml text/html text/css text/js text/xml text/javascript application/javascript application/json application/xml+rss;

    server_tokens off;

    include conf.d/site*;
}
```

## 反向代理参数配置 (conf.d/_proxy.conf)

```nginx
proxy_connect_timeout 300;
proxy_send_timeout 300;
proxy_read_timeout 300;

proxy_buffer_size 64k;
proxy_buffers 4 64k;
proxy_busy_buffers_size 64k;
proxy_temp_file_write_size 256k;
# 所有临时文件总体积大小，磁盘上的临时文件不能超过该配置
proxy_max_temp_file_size 1024m;

proxy_redirect off;
proxy_hide_header Vary;
proxy_ignore_client_abort on;
proxy_http_version 1.1;

proxy_set_header Host $host;
proxy_set_header Referer $http_referer;
proxy_set_header Cookie $http_cookie;
proxy_set_header X-Real-IP $remote_addr;
#proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

## SSL 参数配置 (conf.d/_ssl.conf)

该配置可在证书安全评测网站 https://myssl.com 和 https://www.ssllabs.com/ssltest 获得最高级 A+ 评级。

```nginx
ssl on;
ssl_protocols  TLSv1.2;

# ssl_certificate 证书内容顺序从上至下：服务器证书, 中级证书, 根证书
ssl_certificate          /web/soft/nginx/ssl/abc.com.crt;
ssl_certificate_key      /web/soft/nginx/ssl/abc.com.key;
ssl_dhparam /web/ssl/dh4096.pem;

# 不推荐使用 builtin:1000. 1MB共享缓存可容纳约4000个会话
ssl_session_cache shared:SSL:80m;
ssl_session_timeout 1d;
ssl_session_tickets off;
ssl_buffer_size 64k;
 
#ssl_ciphers ECDHE-PSK:ECDHE-ECDSA:ECDHE-RSA:ECDH:AES:HIGH:!NULL:!aNULL:!EXPORT:!CAMELLIA:!MD5:!PSK:!ADH:!RC4:!DH:!DHE;

#最安全，不支持太旧的浏览器
ssl_ciphers EECDH+AESGCM:EDH+AESGCM;

ssl_prefer_server_ciphers on;

# 用于验证证书链完整验证, ssl_certificate 无 Issuer 信息的时候，此指标被忽略
ssl_stapling on;
ssl_stapling_verify on;

# 可信证书：
# 情形1. 可选. 可合并至.crt文件里，用于验证证书链完整验证. Intermediate 证书下面附上 Root CA 证书。
# 情形2. 用于添加信任非 ssl_certificate 同一机构颁发的证书。只要客户端证书为此证书机构下发的证书都作为可信客户端。
ssl_trusted_certificate /web/soft/nginx/ssl/abc.com.ca-intermediates;

# 有客户端证书需要验证的需求时，可用 ssl_client_certificate 或者 ssl_trusted_certificate
# ssl_client_certificate   /web/cert/intermediate-prod/ca-bundle.pem;
# ssl_verify_client optional;
# ssl_verify_depth 3;


# 获得A+评级必不可少. 15768000 = 6个月
add_header Strict-Transport-Security "max-age=15768000; includeSubdomains; preload" always;

add_header X-Frame-Options "DENY" always;
add_header Referrer-Policy "no-referrer-when-downgrade" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Permissions-Policy "geolocation=(self), microphone=()" always;
add_header Content-Security-Policy "default-src 'self' https:; img-src: https: data: blob:" always;

# 允许内容来源. 需自行按实际情况修改: 如允许百度统计、腾讯统计、公共 css/js CDN等
#add_header Content-Security-Policy "default-src 'self'; script-src 'self' https://ssl.google-analytics.com; img-src 'self' https://ssl.google-analytics.com; style-src 'self' https://fonts.googleapis.com; font-src 'self' https://themes.googleusercontent.com; frame-src 'none'; object-src 'none'";
```

### 说明

第1. 关于 DH params, 可以生成4096位的。（如果嫌麻烦，可以不用它）

```bash
cd /etc/ssl/certs
openssl dhparam -out dhparam.pem 4096
```

```nginx
ssl_dhparam /etc/ssl/certs/dhparam.pem;
```

第2. DNS解析. 就国内来说, 也可以加个你信任的.

```nginx
# resolver <你的 DNS 服务器 IP>;
```

第3. 其他 Apache, Lighttpd, HAProxy 等配置可参考Mozilla官方的推荐配置:

https://mozilla.github.io/server-side-tls/ssl-config-generator

## 典型站点配置 (site-abc.com.conf)

```nginx
upstream upstream {
    keepalive 100;
    server localhost:8080;
}

server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name a.com b.com;

    location /proxy {
        proxy_pass http://upstream;
        include conf.d/_proxy.conf;
    }

    include conf.d/_security.conf;
    include conf.d/_error.conf;
    access_log  /web/log/nginx/a.com.access.log  detailed;
}
```

## 典型 SSL 站点配置 (site-ssl-abc.com.conf)

```nginx
upstream upstreamssl {
    keepalive 100;
    server localhost:8080;
}

server {
    listen [::]:443 http2;
    listen      443 http2;

    server_name abc.com;
    root html;

    include conf.d/_ssl.conf;

    location / {
        try_files $uri @doProxy;
    }

    location @doProxy{
        internal;
        proxy_pass  http://upstreamssl;
        include conf.d/_proxy.conf;
    }

    include conf.d/_security.conf;
    include conf.d/_error.conf;
    access_log  /web/log/nginx/abc.access.log;
}
```

## 安全、防入注、CSRF等 (conf.d/_security.conf)

```nginx
location ~* "(eval.*\()"  { deny all; }
location ~* "(127\.0\.0\.1)"  { deny all; }
location ~* "([a-z0-9]{2000})"  { deny all; }
location ~* "(javascript\:)(.*)(\;)"  { deny all; }
location ~* "(base64_encode)(.*)(\()"  { deny all; }
location ~* "(GLOBALS|REQUEST)(=|\[|%)"  { deny all; }
location ~* "(<|%3C).*script.*(>|%3)" { deny all; }
location ~ "(\\|\.\.\.|\.\./|~|`|<|>|\|)" { deny all; }
location ~ "(\{0\}|\(/\(|\.\.\.|\+\+\+|\\\"\\\")" { deny all; }
location ~ "(~|`|<|>|:|;|%|\\|\s|\{|\}|\[|\]|\|)" { deny all; }
location ~* "(boot\.ini|etc/passwd|self/environ)" { deny all; }
location ~* "(thumbs?(_editor|open)?|tim(thumb)?)\.php" { deny all; }
location ~* "(\'|\")(.*)(drop|insert|md5|select|union)" { deny all; }
location ~* "(https?|ftp|php):/" { deny all; }
location ~* "(=\\\'|=\\%27|/\\\'/?)\." { deny all; }
location ~* "/(\$(\&)?|\*|\"|\.|,|&|&amp;?)/?$" { deny all; }
location ~* "/(=|\$&|_mm|(wp-)?config\.|cgi-|etc/passwd|muieblack)" { deny all; }
location ~* "(&pws=0|_vti_|\(null\)|\{\$itemURL\}|echo(.*)kae|etc/passwd|eval\(|self/environ)" { deny all; }
location ~*  "/(^$|readme|license|example|README|LEGALNOTICE|INSTALLATION|CHANGELOG)\.(txt|html|md)" { deny all; }
location ~* "\.(aspx?|bash|bak?|cfg|cgi|php~|dll|exe|git|hg|ini|jsp|log|mdb|out|sql|svn|swp|tar|rdf)$" { deny all; }
location ~* "/(^$|mobiquo|phpinfo|shell|sqlpatch|thumb|thumb_editor|thumbopen|timthumb|webshell)\.php" { deny all; }

## Block SQL injections
set $block_sql_injections 0;
if ($query_string ~ "union.*select.*\(") { set $block_sql_injections 1; }
if ($query_string ~ "union.*all.*select.*") { set $block_sql_injections 1; }
if ($query_string ~ "concat.*\(") { set $block_sql_injections 1; }
if ($query_string ~ "drop.*(table|database)") { set $block_sql_injections 1; }
if ($query_string ~ "delete.*from") { set $block_sql_injections 1; }
if ($query_string ~ "update.*set") { set $block_sql_injections 1; }
if ($block_sql_injections = 1) { return 403; }
 
## Block file injections
set $block_file_injections 0;
if ($query_string ~ "[a-zA-Z0-9_]=http://") { set $block_file_injections 1; }
if ($query_string ~ "[a-zA-Z0-9_]=(\.\.//?)+") { set $block_file_injections 1; }
if ($query_string ~ "[a-zA-Z0-9_]=/([a-z0-9_.]//?)+") { set $block_file_injections 1; }
if ($block_file_injections = 1) { return 403; }
 
## Block common exploits
set $block_common_exploits 0;
if ($query_string ~ "(eval.*\()") { set $block_common_exploits 1; }
if ($query_string ~ "(alert.*\()") { set $block_common_exploits 1; }
if ($query_string ~ "(prompt.*[\(|%]+)") { set $block_common_exploits 1; }
if ($query_string ~ "(confirm.*[\(|%]+)") { set $block_common_exploits 1; }
if ($query_string ~ "javascript\:") { set $block_common_exploits 1; }
if ($query_string ~ "(<|%3C).*script.*(>|%3E)") { set $block_common_exploits 1; }
if ($query_string ~ "(GLOBALS|REQUEST)(=|\[|\%[0-9A-Z]{0,2})") { set $block_common_exploits 1; }
if ($query_string ~ "proc/self/environ") { set $block_common_exploits 1; }
if ($query_string ~ "mosConfig_[a-zA-Z_]{1,21}(=|\%3D)") { set $block_common_exploits 1; }
if ($query_string ~ "base64_(en|de)code\(.*\)") { set $block_common_exploits 1; }
if ($query_string ~ "\+\+\/\/") { set $block_common_exploits 1; }
if ($query_string ~ "\"\-\"\-") { set $block_common_exploits 1; }
if ($block_common_exploits = 1) { return 403; }
 
## Block spam
set $block_spam 0;
if ($query_string ~ "\b(ultram|unicauca|valium|viagra|vicodin|xanax|ypxaieo)\b") { set $block_spam 1; }
if ($query_string ~ "\b(erections|hoodia|huronriveracres|impotence|levitra|libido)\b") { set $block_spam 1; }
if ($query_string ~ "\b(ambien|blue\spill|cialis|cocaine|ejaculation|erectile)\b") { set $block_spam 1; }
if ($query_string ~ "\b(lipitor|phentermin|pro[sz]ac|sandyauer|tramadol|troyhamby)\b") { set $block_spam 1; }
if ($block_spam = 1) { return 403; }
 
## Block user agents
set $block_user_agents 0;
if ($http_user_agent ~ "Indy Library") { set $block_user_agents 1; }
if ($http_user_agent ~ "libwww-perl") { set $block_user_agents 1; }
if ($http_user_agent ~ "GetRight") { set $block_user_agents 1; }
if ($http_user_agent ~ "GetWeb!") { set $block_user_agents 1; }
if ($http_user_agent ~ "Go!Zilla") { set $block_user_agents 1; }
if ($http_user_agent ~ "Download Demon") { set $block_user_agents 1; }
if ($http_user_agent ~ "Go-Ahead-Got-It") { set $block_user_agents 1; }
if ($http_user_agent ~ "TurnitinBot") { set $block_user_agents 1; }
if ($http_user_agent ~ "GrabNet") { set $block_user_agents 1; }
if ($block_user_agents = 1) { return 403; }

```

## 错误处理 (conf.d/_error.conf)

```conf
error_page 403 /403.html;
location = /403.html {
        internal;
        allow all;
        root   html;
        access_log /var/log/nginx/403.access.log detailed buffer=128k flush=5s;
}

error_page 404 /404.html;
location = /404.html {
        internal;
        allow all;
        root   html;
        access_log /var/log/nginx/404.access.log detailed buffer=128k flush=5s;
}
```

## 日志滚动 (logrotate)

每天日志滚动配置文件 `/etc/logrotate.d/nginx`

```conf
/web/log/nginx/*.log {
    copytruncate
    daily
    dateext
    dateformat .%Y%m%d
    rotate 7
    missingok
    notifempty
    compress
    delaycompress
    sharedscripts
    olddir /web/log/nginx/daily
    postrotate
        /bin/kill -USR1 `cat /run/nginx.pid 2>/dev/null` 2>/dev/null || true
        /web/script/log-compress.sh
    endscript
}
```

查看与调试:

```bash
/usr/sbin/logrotate -dvf /etc/logrotate.d/nginx
```


## 性能测试 (ApacheBench)

### 环境1

阿里云ECS，双核 Intel(R) Xeon(R) Platinum 8163 CPU @ 2.50GHz
ab 与 nginx 在同机进行测试。34737个请求每秒是几次结果中较高的值。

**结果 (较好值)**

```text
Server Software:        nginx/1.12.2
Server Hostname:        127.0.0.1
Server Port:            80

Document Path:          /index.html
Document Length:        3700 bytes

Concurrency Level:      200
Time taken for tests:   0.058 seconds
Complete requests:      2000
Failed requests:        0
Write errors:           0
Total transferred:      7868000 bytes
HTML transferred:       7400000 bytes
Requests per second:    34747.56 [#/sec] (mean)
Time per request:       5.756 [ms] (mean)
Time per request:       0.029 [ms] (mean, across all concurrent requests)
Transfer rate:          133493.06 [Kbytes/sec] received
```

### 环境2

腾讯云，8核 CPU @ 3.2GHz，高性能云盘
ab 与 nginx 在不同服务器。41121 rps是几次结果中最好的值(平均在32k左右)。

**结果 (较好值)**

```log
Server Software:        nginx/1.16.1
Server Hostname:        127.0.0.1
Server Port:            80

Document Path:          /index.html
Document Length:        194 bytes

Concurrency Level:      100
Time taken for tests:   0.486 seconds
Complete requests:      20000
Failed requests:        0
Write errors:           0
Total transferred:      13500000 bytes
HTML transferred:       3880000 bytes
Requests per second:    41121.55 [#/sec] (mean)
Time per request:       2.432 [ms] (mean)
Time per request:       0.024 [ms] (mean, across all concurrent requests)
Transfer rate:          27106.49 [Kbytes/sec] received
```

### 环境3

腾讯云，4核, 8GB, 3.2GHz，SSD云盘
ab 与 nginx 在不同服务器, 4 workers。51538 RPS是几次结果中最好的值。

**结果 (最好值)**

```log
Server Software:        openresty
Server Hostname:        training.tencentads.com
Server Port:            10080

Document Path:          /jkjcha
Document Length:        2 bytes

Concurrency Level:      100
Time taken for tests:   0.097 seconds
Complete requests:      5000
Failed requests:        0
Write errors:           0
Total transferred:      900000 bytes
HTML transferred:       10000 bytes
Requests per second:    51538.95 [#/sec] (mean)
Time per request:       1.940 [ms] (mean)
Time per request:       0.019 [ms] (mean, across all concurrent requests)
Transfer rate:          9059.58 [Kbytes/sec] received
```

## acme 自动安装证书

```bash
.acme.sh/acme.sh --issue --debug -d domain.com -w nginx:/web/soft/nginx/conf/conf.d/domain.com.conf
```

## PHP-FPM 站点配置示例 site-php-fpm.conf

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name a.com b.com;
    root /web/www/a.com;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~* \.php$ {
        fastcgi_index   index.php;
        fastcgi_pass    127.0.0.1:9000;
        include         fastcgi_params;
        fastcgi_param   SCRIPT_FILENAME    $document_root$fastcgi_script_name;
        fastcgi_param   SCRIPT_NAME        $fastcgi_script_name;
    }

    access_log  /web/log/nginx/a.com.access.log  detailed;
}
```

## 示例 Reverse Proxy to Google

### A. 一般配置

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass https://www.google.com;
        proxy_set_header Host www.google.com;
        proxy_set_header Referer https://www.google.com;

        proxy_set_header User-Agent $http_user_agent;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Accept-Encoding "";
        proxy_set_header Accept-Language $http_accept_language;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        sub_filter google.com example.com;
        sub_filter_once off;
    }
}
```

### B. 支持 SSL 的配置

```nginx
server {
    listen 80;
    server_name example.com;
    location / {
        rewrite ^/(.*)$ https://$host$1 permanent;
    }
}

server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate      /etc/ssl/example.crt;
    ssl_certificate_key  /etc/ssl/example.key;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

    location / {
        proxy_pass https://www.google.com;
        proxy_set_header Host www.google.com;
        proxy_set_header Referer https://www.google.com;

        proxy_set_header User-Agent $http_user_agent;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Accept-Encoding "";
        proxy_set_header Accept-Language $http_accept_language;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        sub_filter google.com example.com;
        sub_filter_once off;
    }
}
```

## HTTP 400

Kestrel 后端错误解决:

```conf
proxy_set_header Upgrade $http_upgrade;
```

## ExpressJS页面无法读取cookie

在域名conf文件里设置cookie的samesite和secure信息

```conf
proxy_cookie_path / "/; secure; SameSite=None;";
```
