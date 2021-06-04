# MySQL

## CentOS 7 安装 MySQL 8 (使用国内镜像)

```bash
wget http://mirrors.ustc.edu.cn/mysql-ftp/Downloads/MySQL-8.0/mysql-community-server-8.0.21-1.el7.x86_64.rpm
wget http://mirrors.ustc.edu.cn/mysql-ftp/Downloads/MySQL-8.0/mysql-community-client-8.0.21-1.el7.x86_64.rpm
wget http://mirrors.ustc.edu.cn/mysql-ftp/Downloads/MySQL-8.0/mysql-community-common-8.0.21-1.el7.x86_64.rpm
wget http://mirrors.ustc.edu.cn/mysql-ftp/Downloads/MySQL-8.0/mysql-community-libs-8.0.21-1.el7.x86_64.rpm
wget http://mirrors.ustc.edu.cn/mysql-ftp/Downloads/MySQL-8.0/mysql-community-devel-8.0.21-1.el7.x86_64.rpm

rpm -ivh mysql-community-libs-8.0.21-1.el7.x86_64.rpm
rpm -ivh mysql-community-common-8.0.21-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.21-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.21-1.el7.x86_64.rpm
rpm -ivh mysql-community-devel-8.0.21-1.el7.x86_64.rpm

# cnf 配置
vi /etc/my.cnf

chown -R mysql:mysql /web/data/mysql/
chown -R mysql:mysql /web/log/mysql/
mysqld --initialize --user=mysql
# get root password
vi /web/log/mysql/mysqld.log

systemctl start mysqld.service
systemctl enable mysqld.service

mysql -uroot -p

# 修改密码
alter user 'root'@'localhost'IDENTIFIED BY 'YourNewPass@123';

#授权
# create user 'dbuser'@'%' identified by 'dbpass';
create user 'dbuser'@'%' identified with mysql_native_password by 'xxxxx';
grant all privileges on db.* to 'dbuser'@'%';
flush privileges;
```

## [MySQL 高可用云数据库与本地数据库性能比较](qcloud-mysql-performance.md)

## [MySQL 8.0 之 Insert 性能优化](mysql-insert-optimize.md)

### /etc/my.cnf

```conf
[mysqld]
skip_name_resolve = 1
skip-log-bin

#sysbench 256 threads benchmark
max_connections = 1024
max_user_connections = 256
open_files_limit = 65535
innodb_open_files = 20480

# session memory settings #
read_buffer_size = 4M
read_rnd_buffer_size = 8M
sort_buffer_size = 4M
join_buffer_size = 4M
#tmp_table_size = 32M
thread_cache_size = 256
key_buffer_size = 512M

# log settings #
slow_query_log = 1
slow_query_log_file = /web/log/mysql/slow.log
log_queries_not_using_indexes = 1
log_slow_admin_statements = 1
log_slow_slave_statements = 1
long_query_time = 2

# innodb settings #
innodb_buffer_pool_size = 2048M
innodb_log_file_size = 256
innodb_log_buffer_size = 32M
innodb_flush_log_at_trx_commit = 2
innodb_io_capacity = 500
innodb_write_io_threads = 32
innodb_read_io_threads = 32
#innodb_file_per_table = 1
innodb_stats_on_metadata = 0


# Remove the leading "# " to disable binary logging
# Binary logging captures changes between backups and is enabled by
# default. It's default setting is log_bin=binlog
# disable_log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
#
# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password

datadir=/web/data/mysql
socket=/var/lib/mysql/mysql.sock

log-error=/web/log/mysql/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```
