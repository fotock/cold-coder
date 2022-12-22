# OpenSSL 生成自签名的服务器端和客户端证书

## 创建证书

### 1 创建CA根证书

```bash
openssl genrsa -out ca-key.pem 4096
openssl req -new -out ca-req.csr -key ca-key.pem 
openssl x509 -req -in ca-req.csr -out ca-cert.pem -signkey ca-key.pem -days 3650
```

- CA根证书的Common Name可填写为 root. 
- 所有客户端和服务器端的证书Common Name字段需要填写域名或者ip，但不能与根证书的这个字段一样。
- 所有客户端和服务器端的证书Country, State and Organization Name 必须与 CA 根证书一致。

### 2 创建 Server证书

```bash
openssl genrsa -out server-key.pem 4096
openssl req -new -out server-req.csr -key server-key.pem
openssl x509 -req -in server-req.csr -out server-cert.pem -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -days 3650
openssl pkcs12 -export -in server-cert.pem -inkey server-key.pem -out server.p12
```

### 3 创建 Client 证书

```bash
openssl genrsa -out client-key.pem 4096
openssl req -new -out client-req.csr -key client-key.pem
openssl x509 -req -in client-req.csr -out client-cert.pem -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -days 3650
openssl pkcs12 -export -in client-cert.pem -inkey client-key.pem -out client.p12
```

### Nginx配置

```conf
ssl_certificate /etc/nginx/certs/server.crt;
ssl_certificate_key /etc/nginx/certs/server.key;
ssl_client_certificate /etc/nginx/certs/ca.crt; 
ssl_verify_client on;
```

## 可用性验证

```bash
openssl s_client -connect domain.com:443 -state -debug -cert client.pem -key client.key
```

## 参考资料

1. [Setup & verify mutual TLS authentication (MTLS) with openssl](https://www.golinuxcloud.com/mutual-tls-authentication-mtls/)
2. [OpenSSL create certificate chain with Root & Intermediate CA](https://www.golinuxcloud.com/openssl-create-certificate-chain-linux/)
3. [OpenSSL: Generate ECC certificate](https://www.golinuxcloud.com/openssl-generate-ecc-certificate/)
4. [Gist](https://gist.github.com/welshstew/536e6b77f40e890c01a52b9172e84c11#file-generate-certificates-sh)
5. [medium](https://mcilis.medium.com/how-to-create-a-self-signed-client-certificate-with-openssl-c4af9ac03e99)
