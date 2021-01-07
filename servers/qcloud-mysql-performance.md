# MySQL 高可用云数据库与本地数据库性能比较

## 测试工具 Sysbench 1.1.0

[腾讯云文档](https://cloud.tencent.com/developer/article/1613732)

```bash
yum -y install  git
git clone https://github.com/akopytov/sysbench.git
yum -y install make automake libtool pkgconfig libaio-devel
yum -y install mariadb-devel
cd sysbench
./autogen.sh
./configure
make -j
make install
```

## 测试方法

sysbench 测试分为三步，分别(prepare -> run -> cleanup)：

1.准备数据 (prepare)

```bash
sysbench ./oltp_read_write.lua --mysql-host=IP --mysql-port=3306  --mysql-user=root --mysql-password='密码'  --mysql-db=test  --tables=4 --table-size=100000  --threads=128  prepare
```

2.测试数据(run)

```bash
sysbench ./oltp_read_write.lua --mysql-host=IP --mysql-port=3306  --mysql-user=root --mysql-password='密码'  --mysql-db=test  --tables=4 --table-size=100000  --threads=128 --report-interval=3  --time=30 run
```

3.清理测试数据(cleanup)

```bash
sysbench ./oltp_read_write.lua --mysql-host=IP --mysql-port=3306  --mysql-user=root --mysql-password='密码'  --mysql-db=test --tables=4 --table-size=100000  --threads=128  cleanup
```

## 测试1: 腾讯云高可用 MySQL 云数据库

- **配置** 4核心，8G内存(NVMe, 240k IOPS)
- **版本**  MySQL 5.6

```log
SQL statistics:
    queries performed:
        read:                            855134
        write:                           244324
        other:                           122162
        total:                           1221620
    transactions:                        61081  (1450.96 per sec.)
    queries:                             1221620 (29019.17 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      **1450.9584**
    time elapsed:                        42.0970s
    total number of events:              61081

Latency (ms):
         min:                                    **8.62**
         avg:                                   88.18
         max:                                  341.59
         95th percentile:                      179.94
         sum:                              5386021.08

Threads fairness:
    events (avg/stddev):           477.1953/49.80
    execution time (avg/stddev):   42.0783/0.01
```

## 测试2: 本地网络增强型云主机

**配置** 3.2GHz/8核/16G

### 版本1: MySQL 5.5

my.cnf 基本为缺省配置, 仅添加 skip-name-resolve.

```log
SQL statistics:
    queries performed:
        read:                            3258556
        write:                           931016
        other:                           465508
        total:                           4655080
    transactions:                        232754 (5538.14 per sec.)
    queries:                             4655080 (110762.77 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      **5538.1386**
    time elapsed:                        42.0275s
    total number of events:              232754

Latency (ms):
         min:                                    2.07
         avg:                                   23.10
         max:                                  560.67
         95th percentile:                       41.10
         sum:                              5377114.37

Threads fairness:
    events (avg/stddev):           1818.3906/40.18
    execution time (avg/stddev):   42.0087/0.01
```

### 版本2: MySQL 8.0.21

my.cnf 配置 skip-name-resolve, skip-log-bin.

- 1 读写综合 oltp_read_write.lua

```log
SQL statistics:
    queries performed:
        read:                            2737490
        write:                           782140
        other:                           391070
        total:                           3910700
    transactions:                        195535 (5427.54 per sec.)
    queries:                             3910700 (108550.87 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      **5427.5435**
    time elapsed:                        36.0264s
    total number of events:              195535

Latency (ms):
         min:                                    2.20
         avg:                                   23.57
         max:                                  178.39
         95th percentile:                       33.12
         sum:                              4608196.99

Threads fairness:
    events (avg/stddev):           1527.6172/15.36
    execution time (avg/stddev):   36.0015/0.00
```

- 2 写入 oltp_write_only.lua

```log
SQL statistics:
    queries performed:
        read:                            0
        write:                           3171308
        other:                           1585654
        total:                           4756962
    transactions:                        792827 (**18867.98** per sec.)
    queries:                             4756962 (113207.90 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      **18867.9825**
    time elapsed:                        42.0197s
    total number of events:              792827

Latency (ms):
         min:                                    0.26
         avg:                                    6.78
         max:                                  140.07
         95th percentile:                       11.45
         sum:                              5375043.45

Threads fairness:
    events (avg/stddev):           6193.9609/124.82
    execution time (avg/stddev):   41.9925/0.00
```

- 3 插入 oltp_insert.lua

```log
SQL statistics:
    queries performed:
        read:                            0
        write:                           2592925
        other:                           0
        total:                           2592925
    transactions:                        2592925 (**61710.58** per sec.)
    queries:                             2592925 (61710.58 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      **61710.5754**
    time elapsed:                        42.0175s
    total number of events:              2592925

Latency (ms):
         min:                                    0.05
         avg:                                    2.07
         max:                                  301.90
         95th percentile:                       14.73
         sum:                              5370738.69

Threads fairness:
    events (avg/stddev):           20257.2266/650.96
    execution time (avg/stddev):   41.9589/0.01
```

- 4 只读 oltp_read_only.lua

```log
SQL statistics:
    queries performed:
        read:                            4916310
        write:                           0
        other:                           702330
        total:                           5618640
    transactions:                        351165 (8356.19 per sec.)
    queries:                             5618640 (133699.09 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      **8356.1929**
    time elapsed:                        42.0245s
    total number of events:              351165

Latency (ms):
         min:                                    1.17
         avg:                                   15.31
         max:                                   99.62
         95th percentile:                       20.74
         sum:                              5375894.91

Threads fairness:
    events (avg/stddev):           2743.4766/100.59
    execution time (avg/stddev):   41.9992/0.01
```

- 5 更新索引字段 oltp_update_index.lua

```log
SQL statistics:
    queries performed:
        read:                            0
        write:                           2144942
        other:                           0
        total:                           2144942
    transactions:                        2144942 (59561.59 per sec.)
    queries:                             2144942 (59561.59 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      **59561.5862**
    time elapsed:                        36.0122s
    total number of events:              2144942

Latency (ms):
         min:                                    0.06
         avg:                                    2.15
         max:                                  170.35
         95th percentile:                       10.46
         sum:                              4606588.49

Threads fairness:
    events (avg/stddev):           16757.3594/332.18
    execution time (avg/stddev):   35.9890/0.00
```

- 6 更新非索引字段 oltp_update_non_index.lua

```log
SQL statistics:
    queries performed:
        read:                            0
        write:                           2612775
        other:                           0
        total:                           2612775
    transactions:                        2612775 (72550.94 per sec.)
    queries:                             2612775 (72550.94 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      **72550.9427**
    time elapsed:                        36.0130s
    total number of events:              2612775

Latency (ms):
         min:                                    0.05
         avg:                                    1.76
         max:                                  221.17
         95th percentile:                        9.39
         sum:                              4606005.36

Threads fairness:
    events (avg/stddev):           20412.3047/477.17
    execution time (avg/stddev):   35.9844/0.00
```
