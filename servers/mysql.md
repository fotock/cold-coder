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
```

## [MySQL 高可用云数据库与本地数据库性能比较](qcloud-mysql-performance.md)

