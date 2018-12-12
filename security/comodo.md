# Comodo

性价比相当高。
全球市场占有率很高。不会有客户端或浏览器不信任的情况。

![](https://w3techs.com/diagram/history_overview/ssl_certificate/ms/m)
[来源: https://w3techs.com](https://w3techs.com/technologies/history_overview/ssl_certificate)

## 签发结果包含4个文件

1. AddTrustExternalCARoot.crt
2. COMODORSADomainValidationSecureServerCA.crt
3. COMODORSAAddTrustCA.crt
4. your_domain_com.crt

文件名中带 CA 字符的文件均为签发机构 Comodo 的信息。
仅最后一个为必不可少的 SSL 证书.

## Nginx 部署

配置文件里必须的有两个: .key 文件和 .crt 文件。

```nginx
ssl_certificate          /web/soft/nginx/ssl/abc.com.crt;
ssl_certificate_key      /web/soft/nginx/ssl/abc.com.key;
```

如果将 CA 相关的 crt 内容添加到 your_domain_com.crt 内容后面的话，以上两项就OK了。
否则，需要多加一项配置，并将带 CA 的内容组成一个文件:

```nginx
# 证书链完整验证. Root CA 和 Intermediate 证书
ssl_trusted_certificate /web/soft/nginx/ssl/abc.com.ca-intermediates;
```

更多请参考 "服务器 &gt; Nginx" 章节。

## Apache 部署

三个文件: key, crt, ca (将带 CA 的文件合并成一个 .ca 文件).

```apache
SSLCertificateKeyFile /path/to/domainname.key
SSLCertificateFile /path/to/domainname.crt
SSLCertificateChainFile /path/to/domainname.ca
```

## Tomcat 部署

jks 和 pfx 格式都可以用。需要转化成 .pfx 文件的话, 可参考以下示例:

```xml
<Connector port="8443" acceptCount="100" maxHttpHeaderSize="8192"
    maxThreads="100"  minSpareThreads="10"
    protocol="org.apache.coyote.http11.Http11NioProtocol" 
    enableLookups="false" disableUploadTimeout="true"
    SSLEnabled="true" scheme="https" secure="true" clientAuth="false"
    sslProtocol="TLSv1.2" sslEnabledProtocols="TLSv1.2"
    keystoreFile="conf/your_domain.pfx" keystorePass="123456" />
```

## IIS 部署

需要转化成 .p7b 文件的话.

1. 有openssl的，可使用下面的命令 (缺的证书可以去掉响应行)

```bash
openssl crl2pkcs7 -nocrl \
-certfile your_domain_com.crt \
-certfile AddTrust_External_CA_Root.crt \
-certfile COMODO_RSA_Certification_Authority.crt.crt \
-certfile COMODORSADomainValidationSecureServerCA.crt \
-out your_domain_com.p7b
```

2. 在线证书格式转化

支持转化成 Tomcat 的 pfx 格式。

https://www.sslshopper.com/ssl-converter.html
