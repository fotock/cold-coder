# OpenSSL

> 一切为了安全

## 加密/校验算法的安全性列表

https://ciphersuite.info/cs/?singlepage=true&sort=sec-desc

## SSL/TLS 部署最佳实践

https://github.com/ssllabs/research/wiki/SSL-and-TLS-Deployment-Best-Practices

## 网站安全综合评分

https://www.ssllabs.com/ssltest

## OpenSSL Cookbook

https://www.feistyduck.com/library/openssl-cookbook/online

## OpenSSL 生成证书与使用证书的版本一致性

新版本的OpenSSL可能会摒弃一些不安全的Ciphers。如果生成证书时用的版本和使用时的版本差别过大，就有可能出现无法解证书或需要加legacy之类的选项，给使用造成麻烦。例如，OpenSSL2使用RC2是没有问题的，但到了OpenSSL3，就不再使用该Cipher了。

有类似问题的证书最好进行转换为新的Cipher再使用。例如下面这样的 .p12 证书：

```bash
openssl pkcs12 -in old-cipher-cert.p12 -nodes -legacy -out new-cert.tmp
openssl pkcs12 -in new-cert.tmp -export -out new-cipher-cert.p12
```

## 性能测试 (单核, PV客户网关)

**测试1: 腾讯云CVM S5, 4核4G**

CPU: Intel(R) Xeon(R) Platinum 8361HC CPU @ 2.60GHz

```bash
openssl speed rsa
version: 3.0.7
...
                  sign    verify    sign/s verify/s
rsa  512 bits 0.000042s 0.000002s  23833.5 419624.3
rsa 1024 bits 0.000096s 0.000006s  10375.1 168491.4
rsa 2048 bits 0.000662s 0.000019s   1511.5  53579.3
rsa 3072 bits 0.001947s 0.000039s    513.7  25704.3
rsa 4096 bits 0.004353s 0.000067s    229.7  14897.3
rsa 7680 bits 0.040282s 0.000227s     24.8   4413.7
rsa 15360 bits 0.214255s 0.000894s     4.7   1118.5
```

一般安全证书为2048位，单核签名(sign)每秒1500次。大部分连接只需验证(verify)。

**测试2: Macbook M1 Max**

CPU 频率: 2.06 ~ 3.22 GHz

```bash
openssl speed rsa
LibreSSL 3.3.6
...
                  sign    verify    sign/s verify/s
rsa  512 bits 0.000139s 0.000004s   7174.8 226435.5
rsa 1024 bits 0.000472s 0.000009s   2117.7 105570.5
rsa 2048 bits 0.001873s 0.000028s    534.0  35268.8
rsa 4096 bits 0.009487s 0.000106s    105.4   9415.3
```

笔记本电脑确实弱。

