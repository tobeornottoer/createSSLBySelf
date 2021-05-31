# createSSLBySelf
利用openssl自建ssl证书
1. 创建CA证书密钥
    > openssl genrsa -des3 -out ca.key 2048
2. 去除CA证书密钥密码
    > openssl rsa -in ca.key -out ca.key
3. 生成CA证书
    > openssl req -sha256 -new -x509 -days 3650 -key ca.key -out ca.crt -subj "/C=CN/ST=GD/L=SZ/O=ljw/OU=wifi/CN=*.7ge.icu"
4. 创建服务器证书密钥
    > openssl genrsa -des3 -out server.key 2048
5. 去除服务器证书密钥
    > openssl rsa -in server.key -out server.key
6. 生成服务器csr文件
    > openssl req -new -sha256 -key server.key -subj "/C=CN/ST=GD/L=SZ/O=ljw/OU=wifi/CN=*.7ge.icu" -reqexts SAN -config <(cat /etc/pki/tls/openssl.cnf <(printf "[SAN]\nsubjectAltName=DNS:*.7ge.icu")) -out server.csr
7. 生成服务器证书
    > openssl ca -in server.csr -md sha256 -days 3650 -keyfile ca.key -cert ca.crt -extensions SAN -config <(cat /etc/pki/tls/openssl.cnf <(printf "[SAN]\nsubjectAltName=DNS:*.7ge.icu")) -out server.crt
8. 谷歌浏览器-设置-管理证书-在“隐私设置和安全性”中的“管理证书”中，“受信任的根证书颁发机构”导入上面生成的ca.crt证书，这样来自自建服务器证书在谷歌浏览器中就不会显示红色，无效。
