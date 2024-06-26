# OpenSSL 生成自签名的服务器端和客户端证书

## 第三方证书

从Digicert等机构购买的 DV 证书，Subject 信息只含有 Common Name，也就是你申请的域名，不含有 Country, State and Organization Name等信息。

如需此信息在证书里显示，需要购买OV/EV证书。

## 自建证书

openssl.cnf 参见 [cnf示例或参考资料1/2](openssl-cnf-for-ca-client-server.md)

使用方法1或2.

### 1.1 创建CA根证书

```bash
openssl genrsa -out root-key.pem 8192
openssl req -sha256 -new -x509 -days 3650 -outform PEM -key root-key.pem -out root-cert.pem
```

- CA根证书的Common Name可填写为 MyCompany Root CA. 

### 1.2 创建CA中间证书

[Create Certificate Chain (bundle)](https://www.golinuxcloud.com/openssl-create-certificate-chain-linux/)

```bash
mkdir intermediate
cd intermediate

touch index.txt
echo 01 > serial
mkdir newcerts
echo 01 > crlnumber

openssl genrsa (-aes256) -out ca-key.pem 4096
openssl req -new -sha256 -out ca-req.csr -key ca-key.pem -config openssl.cnf -extensions v3_ca
openssl ca -config openssl.cnf -extensions v3_intermediate_ca -notext -batch -in ca-req.csr -out ca-cert.pem -keyfile ../root/root-key.pem -cert ../root/root-cert.pem

openssl x509 -in ca-cert.pem -out ca-cert.pem -outform PEM
cat ca-cert.pem ../root/root-cert.pem > ca-bundle.pem
```

### 2 直接创建CA根证书 (不使用中间证书)

```bash
openssl genrsa -aes256 -out ca-key.pem 4096
openssl req -new -sha256 -out ca-req.csr -key ca-key.pem -config openssl.cnf -extensions v3_ca
openssl x509 -sha256 -req -in ca-req.csr -out ca-cert.pem -signkey ca-key.pem -days 3650 -outform PEM  -extensions v3_ca
```

- 所有客户端和服务器端的证书Common Name字段需要填写域名或者ip，但不能与根证书的这个字段一样。
- 所有客户端和服务器端的证书Country, State and Organization Name 必须与 CA 根证书一致。

### 3 创建 Server证书

cat server_cert_ext.cnf 
```conf
basicConstraints = CA:FALSE
nsCertType = server
nsComment = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer:always
keyUsage = digitalSignature, keyEncipherment, dataEncipherment, nonRepudiation
extendedKeyUsage = serverAuth
```

```bash
openssl genrsa -aes256 -out server-key.pem 4096
openssl req -new -sha256 -out server-req.csr -key server-key.pem -config openssl.cnf
openssl x509 -sha256 -req -in server-req.csr -out server-cert.pem -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -days 3650 -outform PEM -extfile server_cert_ext.cnf
openssl pkcs12 -export -in server-cert.pem -inkey server-key.pem -out server.p12
```

Nodejs 作为server端时，要移除server-key的密码。

### 4 创建 Client 证书

cat client_cert_ext.cnf 
```conf
basicConstraints = CA:FALSE
nsCertType = client, email
nsComment = "OpenSSL Generated Client Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment, dataEncipherment
extendedKeyUsage = clientAuth
```

```bash
openssl genrsa -aes256 -out client-key.pem 4096
openssl req -new -sha256 -out client-req.csr -key client-key.pem -config openssl.cnf -extensions v3_req
openssl x509 -sha256 -req -in client-req.csr -out client-cert.pem -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -days 3650 -outform PEM -extfile client_cert_ext.cnf
openssl pkcs12 -export -in client-cert.pem -inkey client-key.pem -out client.p12
```
Nginx 作为client端时，要移除client-key的密码。

### Nginx配置

```conf
ssl_certificate /etc/nginx/certs/server.crt;
ssl_certificate_key /etc/nginx/certs/server.key;
ssl_client_certificate /etc/nginx/certs/ca.crt; 

ssl_verify_client optional;
ssl_verify_depth 1;

location @doProxy{
  proxy_pass  http://upstream;
                
  proxy_set_header SSL-ClientIDN $ssl_client_i_dn;
  proxy_set_header SSL-ClientSDN $ssl_client_s_dn;
  proxy_set_header SSL-ClientSerial $ssl_client_serial;
  proxy_set_header SSL-Verify $ssl_client_verify;
}
```

## openssl 命令

```bash
#可用性验证
openssl s_client -connect domain.com:443 -state -debug -cert client.pem -key client.key

#curl查看证书和连接
curl --key client-key.pem --cert client-cert.pem --cacert ca-cert.pem https://xxx/xxx -v

#导出无需密码的key
openssl rsa -in server-key.pem -out server-key-nopass.pem

#查看证书信息
openssl x509 -in server.crt -noout -text

#查看所有信息
openssl pkcs12 -info -in in.p12 -nodes

#导出证书
openssl pkcs12 -in in.p12 -out cert.pem -nokeys

#导出秘钥
openssl pkcs12 -in in.p12 -out key.pem -nodes -nocerts

#格式转换
openssl pkcs12 -export -in client-cert.pem -inkey client-key.pem -out client.p12
```

## 参考资料
1. [OpenSSL create certificate chain with Root & Intermediate CA](https://www.golinuxcloud.com/openssl-create-certificate-chain-linux/)
2. [Create client certificate & server certificate](https://www.golinuxcloud.com/openssl-create-client-server-certificate/)
3. [Setup & verify mutual TLS authentication (MTLS) with openssl](https://www.golinuxcloud.com/mutual-tls-authentication-mtls/)
4. [OpenSSL: Generate ECC certificate](https://www.golinuxcloud.com/openssl-generate-ecc-certificate/)
5. [Gist](https://gist.github.com/welshstew/536e6b77f40e890c01a52b9172e84c11#file-generate-certificates-sh)
6. [medium](https://mcilis.medium.com/how-to-create-a-self-signed-client-certificate-with-openssl-c4af9ac03e99)
