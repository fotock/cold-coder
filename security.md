# 安全安全安全

> 验证一切用户数据: 包括但不限于 GET、POST、 COOKIE 等参数值。

<br>

公网服务要经得起各种测试和攻击。从日志里可以分析各种攻击。
腾讯的项目一般都要求可经受一天几百万次的安全检测请求。

<br>

#### A. CSRF / XSRF / 跨站请求伪造
- POST，加token
- 及时清理 Cookie


#### B. SQL 入注 (Injection)
- 给予最小权限
- 验证输入
- Prepared


#### C. 操作系统安全
- 及时更新软件
- 防火墙。只开放需要的端口
- 数据库、缓存等服务关闭公网访问
- 及时备份


#### D. SSL / CA 证书
IIS 需要在所在的 Windows 服务器上生成 CSR。

- 请选择赛门铁克等国际品牌
- Comodo 性价比很高
- 证书格式在线转换
  https://www.sslshopper.com/ssl-converter.html
- 证书状态检查/安全评估
  https://myssl.com
- Windows 服务器 SSL/TLS 配置
  https://www.nartac.com/Products/IISCrypto
