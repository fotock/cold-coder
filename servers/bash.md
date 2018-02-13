# Bash

- 计算 gz 压缩文件内容行数

```bash
unpigz -c FILE.tar.gz | wc -l
```

- tcpdump 抓包

```bash
tcpdump -i eth1 -n "dst host 127.0.0.1 and src port 80" -X -s 0
```

- 按内存高低显示 PID 和 进程

```bash
ps aux | awk '{printf "%-6s %3s  %s %s\n", $2, $4, $11, $12}' | sort -k2rn | head -n 10
```

