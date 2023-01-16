# 文件加密与解密

## OpenSSL 方式 (支持大文件方案)

### 代码

```bash
#生成证书 (如需密码，去掉 -nodes 参数)
openssl req -x509 -nodes -days 365000 -newkey rsa:8192  -keyout privatekey.pem  -out publickey.pem

#加密 (如果支持，优先使用gcm)
openssl smime -encrypt -aes-256-gcm -binary -outform DER -in largeFile.tgz -out largeFile_enc.tgz publickey.pem

#解密

#无密码:
openssl smime -decrypt -binary -inform DER -inkey privatekey.pem -in largeFile_enc.tgz -out largeFile_dec.tgz

#有密码:
openssl smime -decrypt -binary -inform DER -inkey privatekey.pem -in largeFile_enc.tgz -out largeFile_dec.tgz -passin pass:YOUR_PASSWORD
```
### 性能

MacOSX 2021(M1)，文件大小 476MB。加密用时 2.9s，解密用时 2.6s。

### Mac bash

``` bash
#! /bin/bash

perl -MTime::HiRes=time -e 'printf "%.9f\n", time'

openssl  smime  -encrypt -aes256 -binary -outform DER -in a.mkv -out  a_enc.mkv  publickey.pem

perl -MTime::HiRes=time -e 'printf "%.9f\n", time'

# openssl  smime -decrypt -binary -inform DER -inkey privatekey.pem -in a_enc.mkv -out a_dec.mkv
openssl  smime -decrypt -binary -inform DER -inkey privatekey.pem -in a_enc.mkv -out a_dec.mkv -passin pass:YOUR_PASSWORD

perl -MTime::HiRes=time -e 'printf "%.9f\n", time'
```
