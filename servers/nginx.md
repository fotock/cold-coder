# Nginx

#### 请求处理流
<img src="/assets/nginx-request-flow.png" alt="">


#### configure
```bash
./configure --with-threads  --with-pcre --with-http_ssl_module --with-http_gzip_static_module --with-http_auth_request_module  --with-http_mp4_module --with-http_stub_status_module --with-http_v2_module  --with-http_gunzip_module --with-http_realip_module --with-debug --prefix=/web/soft/nginx --http-log-path=/web/log/nginx/access.log --error-log-path=/web/log/nginx/nginx.log --user=www --group=www --http-client-body-temp-path=/var/tmp/nginx/client --http-proxy-temp-path=/var/tmp/nginx/proxy --http-fastcgi-temp-path=/var/tmp/nginx/fastcgi
```

### 目录结构
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

#### nginx.conf

```nginx
user  www;
worker_processes  auto;
worker_rlimit_nofile 100000;

error_log  /web/log/nginx/error.log;
error_log  /web/log/nginx/error-notice.log  notice;
error_log  /web/log/error-info.log  info;

events {
    use epoll;
    worker_connections  10000;
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
    server_tokens off;

    gzip  on;
    gzip_disable "msie6";
    gzip_comp_level 6;
    gzip_min_length  500;
    gzip_buffers 16 8k;
    gzip_types text/plain application/xml text/css text/js text/xml text/javascript application/javascript application/json application/xml+rss;

    include conf.d/site*;
}
```

#### conf.d/_proxy.conf
```nginx
proxy_connect_timeout 300;
proxy_send_timeout 300;
proxy_read_timeout 300;

proxy_buffer_size 64k;
proxy_buffers 4 32k;
proxy_busy_buffers_size 64k;

proxy_temp_file_write_size 64k;
proxy_redirect off;
proxy_hide_header Vary;
proxy_ignore_client_abort on;
proxy_http_version 1.1;

proxy_set_header Connection "";
proxy_set_header Accept-Encoding '';
proxy_set_header Host $host;
proxy_set_header Referer $http_referer;
proxy_set_header Cookie $http_cookie;
proxy_set_header X-Real-IP $remote_addr;
#proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

#### SSL 配置 (conf.d/_ssl.conf)
该配置可在证书安全评测网站 https://myssl.com 和 https://www.ssllabs.com/ssltest 获得最高级 A+ 评级。
```nginx
ssl on;
ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;

ssl_certificate          /web/soft/nginx/ssl/abc.com.crt;v
ssl_certificate_key      /web/soft/nginx/ssl/abc.com.key;

ssl_session_cache  builtin:1000  shared:SSL:10m;

ssl_ciphers "EECDH+ECDSA+AESGCM:EECDH+aRSA+AESGCM:EECDH+ECDSA+SHA512:EECDH+ECDSA+SHA384:EECDH+ECDSA+SHA256:EECDH+AESGCM:ECDH+AES256:AES256+EECDH:AES256+EDH:RSA+AESGCM:!aNULL:!eNULL:!LOW:!RC4:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS";
ssl_prefer_server_ciphers on;

ssl_stapling on;
ssl_stapling_verify on;
ssl_trusted_certificate /web/soft/nginx/ssl/abc.com.ca;

add_header X-Frame-Options DENY;
add_header X-XSS-Protection "1; mode=block";
add_header X-Content-Type-Options nosniff;
add_header Strict-Transport-Security "max-age=31536000; includeSubdomains";
```

#### site-php-fpm.conf
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

#### site-upstream.conf
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

    access_log  /web/log/nginx/a.com.access.log  detailed;
}
```

#### site-ssl-proxy.conf
```nginx
server {
    listen [::]:443 ssl http2;
    listen      443 ssl http2;
    server_name abc.com;

    root html;
    access_log  /web/log/nginx/abc.access.log;

    include conf.d/_ssl.conf;

    location / {
        try_files $uri @doProxy;
    }

    location @doProxy{
        internal;
        proxy_pass  http://10.28.53.13:10880;
        include conf.d/_proxy.conf;
    }
}
```

#### 性能测试 (ApacheBench)
环境:
阿里云ECS，双核 Intel(R) Xeon(R) Platinum 8163 CPU @ 2.50GHz
ab 与 nginx 在同机进行测试。34737个请求每秒是几次结果中较高的值。

<br>
结果（较好值)
```
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