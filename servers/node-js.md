# Node.js

## 已经使用的几家公司

- Amazon
- Netflix
- Paypal
- Github
- LinkedIn
- Yahoo
- Shutterstock
- Ebay
- Walmart
- Flickr
- Uber

## 常用框架

### Express

### Koa

## 管理工具

### forever

### pm2

```bash
npm i -g pm2

# express.js
pm2 start bin/www

# common Node.js
pm2 start <app>

pm2 stop     <app_name|id|'all'|json_conf>
pm2 restart  <app_name|id|'all'|json_conf>
pm2 delete   <app_name|id|'all'|json_conf>
pm2 monit
pm2 list
pm2 status
```

### Total.js