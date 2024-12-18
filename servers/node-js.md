# Node.js

### Express

腾讯云8核3.2G，内存 16G。

1. pm2管理6实例，带实际应用插件session/middleware/csrf/sequelizer等等，{hello:'world'}性能:

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

2. node启动，单实例，全新纯粹expressjs框架，{hello:'world'}性能:

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

3. pm2启动4个实例，全新纯粹expressjs框架，{hello:'world'}性能:


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

## PM2

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

# *修复pm2一直/频繁自动重启*
pm2 update
```

#### pm2 开机启动/禁用

```bash
pm2 startup

pm2 unstartup systemd
```

#### pm2 启动文件

`!!! 尽量将配置放到其他地方，如 config/config.js. 这里的重启可能会不加载env更新 !!!`

echosystem.config.js, echosystem.dev.config.js

```js
module.exports = {
    apps: [{
        name: 'project1',
        script: 'bin/www',
        args: 'one two',
        instances: 2,
        exec_mode: "cluster",
        wait_ready: true,
        listen_timeout: 3000,
        out_file: "log/pm2-out.log",
        error_file: "log/pm2-error.log",
        merge_logs: true,
        time: true,
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

#### pm2 部署/deploy

ecosystem.config.js 里面添加:

```js
module.exports = {
    apps: [{ ... }],
    deploy: {
         production: {
           key: '~/.ssh/ssh.pem',
           user: 'publishuser',
           host: [
             {
               host: 'ip',
               port: '22'
             },
             {
               host: 'ip',
               port: '22'
             },
           ],
           ssh_options: 'StrictHostKeyChecking=no',
           ref: "origin/master",
           repo: 'git@git.name.com:projectname.git',
           path: '/web/www/projectname',
           'post-deploy': 'pm2 reload all'
         }
     }
}
```
待部署服务器主要配置git

```bash
ssh-keygen -t ed25519 -C 'abc@def.com'
```

将 .ssh/id_ed25519.pub 的内容放到git服务器或对应项目的配置中。

测试 git clone git@git.name.com:projectname.git 是否可用。


## Express vs Hyper-Express

Macbook Pro 14, 测试工具 wrk，输出 "hello, world".

#### Hyper-Express

```bash
wrk -t1 -c10000 -d3s http://localhost:43211/
```

```bash
Running 3s test @ http://localhost:43211/
  1 threads and 10000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   766.75us  327.40us   7.49ms   91.76%
    Req/Sec   173.60k    17.90k  189.25k    83.33%
  518049 requests in 3.04s, 43.97MB read
  Socket errors: connect 9750, read 63, write 0, timeout 0
Requests/sec: **170635.32**
Transfer/sec:     14.48MB


                                 VIRTUAL   RESIDENT 
MALLOC ZONE                         SIZE       SIZE 
===========                      =======  ========= 
MallocHelperZone_0x107664000      600.0M      8528K 
DefaultMallocZone_0x107670000     512.0M      2512K 
===========                      =======  ========= 
TOTAL                               1.1G      10.8M
```

Hyper-Express 框架RPS在 15万 到 18万之间。延迟上也明显低很多。

内存占用上，大概是Express的50%。

#### Express

```log
Running 3s test @ http://localhost:43210/
  1 threads and 10000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    14.01ms   17.64ms 876.15ms   99.88%
    Req/Sec    17.88k     1.75k   19.17k    90.00%
  53333 requests in 3.04s, 12.21MB read
  Socket errors: connect 9750, read 80, write 0, timeout 0
Requests/sec:  **17572.26**
Transfer/sec:      4.02MB



                                 VIRTUAL   RESIDENT 
MALLOC ZONE                         SIZE       SIZE 
===========                      =======  ========= 
MallocHelperZone_0x109ee8000        1.2G      16.2M 
DefaultMallocZone_0x109ef4000     512.0M      3408K 
===========                      =======  ========= 
TOTAL                               1.7G      19.5M 
```

Express 框架RPS在 1.7万左右。

纯框架看，Express性能和 Hyper-Express 差距很大。
