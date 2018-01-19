# 安全安全安全

> 验证一切用户数据: 包括但不限于 GET、POST、 COOKIE 等参数值。


## A. CSRF / XSRF / 跨站请求伪造
- POST，加token
- 及时清理 Cookie

## B. SQL 入注 (Injection)
- 验证输入
- Prepared

## C. SSL/CA 证书
- 请选择赛门铁克等国际品牌
- Comodo 不错

## D. 服务器安全
- 只开放需要的端口
- 数据库、缓存等服务关闭公网访问
