# OpenSSL 生成自签名的服务器端和客户端证书

## 1 创建CA证书

```bash
openssl genrsa -out ca-key.pem 4096
openssl req -new -out ca-req.csr -key ca-key.pem 
openssl x509 -req -in ca-req.csr -out ca-cert.pem -signkey ca-key.pem -days 3650
```

## 2 创建 Server证书

```bash
openssl genrsa -out server-key.pem 4096
openssl req -new -out server-req.csr -key server-key.pem
openssl x509 -req -in server-req.csr -out server-cert.pem -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -days 3650
openssl pkcs12 -export -in server-cert.pem -inkey server-key.pem -out server.p12
```

## 2 创建 Client 证书

```bash
openssl genrsa -out client-key.pem 4096
openssl req -new -out client-req.csr -key client-key.pem
openssl x509 -req -in client-req.csr -out client-cert.pem -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -days 3650
openssl pkcs12 -export -in client-cert.pem -inkey client-key.pem -out client.p12
```
