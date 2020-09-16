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
sysbench ./oltp_read_write.lua --mysql-host=IP --mysql-port=3306  --mysql-user=root --mysql-password='密码'  --mysql-db=test  --tables=4 --table-size=100000  --threads=128 --report-interval=3  --time=42 run
```

3.清理测试数据(cleanup)

```bash
sysbench ./oltp_read_write.lua --mysql-host=IP --mysql-port=3306  --mysql-user=root --mysql-password='密码'  --mysql-db=test --tables=4 --table-size=100000  --threads=128  cleanup
```

## 测试结果

### 高可用 MySQL 云数据库

- **配置** 4核8G
- MySQL 5.6


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
    events/s (eps):                      1450.9584
    time elapsed:                        42.0970s
    total number of events:              61081

Latency (ms):
         min:                                    8.62
         avg:                                   88.18
         max:                                  341.59
         95th percentile:                      179.94
         sum:                              5386021.08

Threads fairness:
    events (avg/stddev):           477.1953/49.80
    execution time (avg/stddev):   42.0783/0.01
```

### 本地网络增强型云主机

**配置** 8核/16G/3.2GHz

#### 版本 MySQL 5.5

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
    events/s (eps):                      5538.1386
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

#### 版本 MySQL 8.0.21

my.cnf 配置 skip-name-resolve, skip-log-bin.

1. 写入性能 oltp_write_only.lua

```log
SQL statistics:
    queries performed:
        read:                            0
        write:                           3171308
        **other**:                           1585654
        total:                           4756962
    transactions:                        792827 (**18867.98** per sec.)
    queries:                             4756962 (113207.90 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

Throughput:
    events/s (eps):                      18867.9825
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

2. 插入性能 oltp_insert.lua

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
    events/s (eps):                      61710.5754
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

