# Node.js

## 已经使用的几家公司

- Amazon
- Netflix
- Paypal
- Github
- LinkedIn
- Yahoo
- Shutterstock
- Ebay
- Walmart
- Flickr
- Uber

## 常用框架

### Express

腾讯云8核3.2G，内存 16G。

pm2管理6实例，带实际应用插件session/middleware/csrf/sequelizer等等，{hello:'world'}性能:

```log
Document Path:          /xn/helloworld
Document Length:        17 bytes

Concurrency Level:      100
Time taken for tests:   0.346 seconds
Complete requests:      4000
Failed requests:        0
Write errors:           0
Total transferred:      1012000 bytes
HTML transferred:       68000 bytes
Requests per second:    11550.04 [#/sec] (mean)
Time per request:       8.658 [ms] (mean)
Time per request:       0.087 [ms] (mean,...)
Transfer rate:          2853.67 [Kbytes/sec] received
```

node启动，单实例，全新纯粹expressjs框架，{hello:'world'}性能:

```log
Document Path:          /xn
Document Length:        17 bytes

Concurrency Level:      100
Time taken for tests:   0.238 seconds
Complete requests:      4000
Failed requests:        0
Write errors:           0
Total transferred:      896000 bytes
HTML transferred:       68000 bytes
Requests per second:    16811.53 [#/sec] (mean)
Time per request:       5.948 [ms] (mean)
Time per request:       0.059 [ms] (mean, across all concurrent requests)
Transfer rate:          3677.52 [Kbytes/sec] received
```

pm2启动4个实例，全新纯粹expressjs框架，{hello:'world'}性能:


```log
Document Path:          /xn
Document Length:        17 bytes

Concurrency Level:      100
Time taken for tests:   0.130 seconds
Complete requests:      4000
Failed requests:        0
Write errors:           0
Total transferred:      896000 bytes
HTML transferred:       68000 bytes
Requests per second:    30685.20 [#/sec] (mean)
Time per request:       3.259 [ms] (mean)
Time per request:       0.033 [ms] (mean, across all concurrent requests)
Transfer rate:          6712.39 [Kbytes/sec] received
```

### Koa

## 管理工具

### forever

### pm2

```bash
npm i -g pm2

# express.js
pm2 start bin/www

# common Node.js
pm2 start <app>

pm2 stop     <app_name|id|'all'|json_conf>
pm2 restart  <app_name|id|'all'|json_conf>
pm2 delete   <app_name|id|'all'|json_conf>
pm2 monit
pm2 list
pm2 status
```

#### pm2 开机启动/禁用

```bash
pm2 startup

pm2 unstartup systemd
```

#### pm2 启动文件

echosystem.config.js, echosystem.dev.config.js

```js
module.exports = {
    apps: [{
        name: 'project1',
        script: 'bin/www',
        args: 'one two',
        instances: 2,
        out_file: "log/pm2-out.log",
        error_file: "log/pm2-error.log",
        autorestart: false,
        watch: false,
        watch_delay: 1000,
        ignore_watch: ["node_modules", "public", "log", ".git"],
        watch_options: {
            "followSymlinks": false,
            dot: true
        },
        max_memory_restart: '256M',
        env: {
            PORT: 8080,
            NODE_ENV: 'production'
        }
    }]
};
```

### Total.js