# 安全安全安全

> 认证/验证一切来源数据: 包括但不限于 GET、POST、 COOKIE 等参数值。

<br>

公网服务要经得起各种测试和攻击。从日志里可以分析各种攻击。
腾讯的项目一般都要求可经受一天几百万次的安全检测请求。


## A. CSRF / XSRF / 跨站请求伪造

- POST，加token
- 及时清理 Cookie


## B. SQL 入注 (Injection)

- 给予最小权限
- 验证输入
- Prepared


## C. 操作系统安全

- 及时更新软件
- 只开放需要的端口. 可能的话,修改默认端口
- 及时备份

## D. 服务安全

- 数据库、缓存、集群服务等内部服务, 关闭公网访问
- 有成为别人的挖坑设备的风险的服务:
  1. Redis. 需要连接密码验证, 禁止公网访问.
  2. Jenkins. 更新成最新版本.

## E. SSL / CA 证书

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
