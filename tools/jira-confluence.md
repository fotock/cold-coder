# Confluence 和 JIRA 使用笔记

## JIRA 饼图中文显示乱码问题

### 方案1

```bash
wget http://js.downcc.com//down2/centosyyb_downcc.com.zip
unzip centosyyb_downcc.com.zip
rpm -ivh --force --nodeps fonts-chinese-3.02-12.el5.noarch.rpm
yum install -y ttmkfdir chkfontpath
service jira stop
service jira start
```

### 方案2

```bash
yum install urw-fonts
service jira stop
#Clear the plugin cache
service jira start
```

## URL 和文件名称相关中文显示问题

### JIRA

1. 编辑 `/var/atlassian/application-data/jira/dbconfig.xml`, 更新 url:

```xml
<url>jdbc:mysql://xxxxxxxx:3306/dbname?useUnicode=true&amp;characterEncoding=UTF8&amp;sessionVariables=default_storage_engine=InnoDB</url>
```

2. 编辑 `/opt/atlassian/jira/bin/setenv.sh`, 更新：

```conf
JVM_SUPPORT_RECOMMENDED_ARGS="-Dfile.encoding=utf-8 -Dsun.jnu.encoding=UTF-8"
```

### Confluence

编辑 `/opt/atlassian/jira/bin/setenv.sh`, 底部添加：

```conf
CATALINA_OPTS="$CATALINA_OPTS -Dfile.encoding=utf-8 -Dsun.jnu.encoding=UTF-8"
```

