# 安全安全安全

公网服务要经得起各种测试和攻击。从日志里可以分析各种攻击。
腾讯的项目一般都要求可经受一天几百万次的安全检测请求。

> 安全策略、网络安全、漏洞管理、数据安全、渗透测试、社会工程学，各个方面的安全指南，参见[PurpleSec](https://purplesec.us/learn)。

- 信息安全策略模板 （30个）

  https://purplesec.us/resources/cyber-security-policy-templates/
  
  
> 认证/验证一切来源数据: 包括但不限于 GET、POST、 COOKIE 等参数值。

## CIS控制/Center for Information Security

保护企业信息免受网络威胁的最佳实践。

官方网站：https://www.cisecurity.org

控制/实施文档：[CIS控制7.1版实施组1.2 中文版](/assets/CIS控制7.1版实施组1.2.xlsx)


## 基础设施安全/Infrastructure Security

基础设施渗透测试系列涵盖了渗透测试执行标准所描述的基础设施渗透测试的所有阶段 。

- 情报收集：在组织的信息收集阶段执行的技术步骤，并确定攻击面区域。
- 漏洞分析：利用metasploit-fu、nmap等工具探索一台机器不同端口上运行的不同服务。
- Exploitation：在破坏域用户凭据后可以使用的枚举方法和在破坏管理凭据后使用的远程代码执行方法。
- Post Exploitation ：获取管理远程 shell 后收集凭据的不同方法。此外，本节还介绍了执行后期开发以对 C 级管理人员产生高影响。
- 报告 ：在渗透测试成功后自动编写报告的开源方法。
- 配置审查 ：对交换机、路由器、防火墙和端点设备执行配置审查的方法。

https://bitvijays.github.io/index.html

## 渗透测试/Penetration Test

https://purplesec.us/types-penetration-testing/

## CSRF / XSRF / 跨站请求伪造

- POST，加token
- 及时清理 Cookie


## SQL 入注 (Injection)

- 给予最小权限
- 验证输入
- Prepared

## 操作系统安全

- 及时更新软件
- 只开放需要的端口. 可能的话,修改默认端口
- 及时备份

## 服务安全

- 数据库、缓存、集群服务等内部服务, 关闭公网访问
- 有成为别人的挖坑设备的风险的服务:
  1. Redis. 需要连接密码验证, 禁止公网访问.
  2. Jenkins. 更新成最新版本.

## SSL / CA 证书

IIS 需要在所在的 Windows 服务器上生成 CSR。

- 请选择赛门铁克等国际品牌

- Comodo 性价比很高

- 证书格式在线转换

  https://www.sslshopper.com/ssl-converter.html

- 证书状态检查/安全评估 (国内)

  https://myssl.com

- 证书状态检查/安全评估 (国外)

  https://www.ssllabs.com/ssltest

- Windows 服务器 SSL/TLS 配置

  https://www.nartac.com/Products/IISCrypto

- 推荐证书安全配置 (Apache, Nginx, Lighttpd, HAProxy)

  https://mozilla.github.io/server-side-tls/ssl-config-generator

- DDOS 攻击实时检测

  http://map.norsecorp.com
  
