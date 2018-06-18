# Bash

## 终端颜色处理
```bash
for code in {30..37}; do \
echo -en "\e[${code}m"'\\e['"$code"'m'"\e[0m"; \
echo -en "  \e[$code;1m"'\\e['"$code"';1m'"\e[0m"; \
echo -en "  \e[$code;3m"'\\e['"$code"';3m'"\e[0m"; \
echo -en "  \e[$code;4m"'\\e['"$code"';4m'"\e[0m"; \
echo -e "  \e[$((code+60))m"'\\e['"$((code+60))"'m'"\e[0m"; \
done
```
<img src="/assets/bash_colors.png" alt="" width="220">



- 计算 gz 压缩文件内容行数

```bash
unpigz -c FILE.tar.gz | wc -l
```

- tcpdump 抓包

```bash
tcpdump -i eth1 -n "dst host 127.0.0.1 and src port 80" -X -s 0

# 去掉一些空的包
tcpdump -i eth1 -n "dst host 127.0.0.1 and greater 120" -X
```

- 按内存高低显示 PID 和 进程

```bash
ps aux | awk '{printf "%-6s %3s  %s %s\n", $2, $4, $11, $12}' | sort -k2rn | head -n 10
```

