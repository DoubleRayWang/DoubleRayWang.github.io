---
layout: post
title: "CentOS配置Docker API TLS认证"
date: 2020-04-03 10:10:10 +0800
categories: 杂记
tags: Docker
---

我们利用Portainer来管理docker环境和用Jenkins来自动构建和部署docker时的远程管理都会使用到Docker API，通常我们只是开启了没有安全保护的2375（通常）端口，这个比较危险，会导致远程劫持攻击。那么我们就需要配置TLS认证的2376（通常）端口。<!-- more -->

下面我们针对CentOS系统进行配置：

一、利用系统自带的openssl生成相应的服务端和客户端证书
具体过程可参考：https://docs.docker.com/engine/security/https/

我们利用脚本自动生成，这样非常便捷，脚本（auto-tls-certs.sh）如下：

```shell script
#!/bin/bash
# 
# -------------------------------------------------------------
# 自动创建 Docker TLS 证书
# -------------------------------------------------------------
# 以下是配置信息
# Config start
IP="39.39.139.39"
PASSWORD="123456"
COUNTRY="CN"
STATE="Beijing"
CITY="Beijing"
ORGANIZATION="COM"
ORGANIZATIONAL_UNIT="Dev"
COMMON_NAME="$IP"
EMAIL="COM@china.cn"
# Config end
# 生成 CA 密钥
if [[ ! -f ca-key.pem ]]; then
    echo " - 生成 CA 密钥"
    openssl genrsa -aes256 -passout "pass:$PASSWORD" -out "ca-key.pem" 4096
fi
# 生成 CA
if [[ ! -f ca.pem ]]; then
    echo " - 生成 CA"
    openssl req -new -x509 -days 365 -key "ca-key.pem" -sha256 -out "ca.pem" -passin "pass:$PASSWORD" -subj "/C=$COUNTRY/ST=$STATE/L=$CITY/O=$ORGANIZATION/OU=$ORGANIZATIONAL_UNIT/CN=$COMMON_NAME/emailAddress=$EMAIL"
fi
# 生成服务器密钥 & 服务器证书
if [[ ! -f server-key.pem ]]; then
    echo " - 生成服务器密钥"
    openssl genrsa -out "server-key.pem" 4096
fi
if [[ ! -f server.csr ]]; then
     openssl req -subj "/CN=$COMMON_NAME" -sha256 -new -key "server-key.pem" -out server.csr
fi
if [[ ! -f server-cert.pem ]]; then
    echo " - 生成服务器证书"
    echo "subjectAltName = IP:$IP,IP:127.0.0.1" >> extfile.cnf
    echo "extendedKeyUsage = serverAuth" >> extfile.cnf
    openssl x509 -req -days 365 -sha256 -in server.csr -passin "pass:$PASSWORD" -CA "ca.pem" -CAkey "ca-key.pem" -CAcreateserial -out "server-cert.pem" -extfile extfile.cnf
fi
rm -f extfile.cnf
# 生成客户端证书
if [[ ! -f key.pem ]]; then
    openssl genrsa -out "key.pem" 4096
fi
if [[ ! -f cert.pem ]]; then
    openssl req -subj '/CN=client' -new -key "key.pem" -out client.csr
    echo extendedKeyUsage = clientAuth >> extfile.cnf
    openssl x509 -req -days 365 -sha256 -in client.csr -passin "pass:$PASSWORD" -CA "ca.pem" -CAkey "ca-key.pem" -CAcreateserial -out "cert.pem" -extfile extfile.cnf
fi
chmod -v 0400 "ca-key.pem" "key.pem" "server-key.pem"
chmod -v 0444 "ca.pem" "server-cert.pem" "cert.pem"
# 打包客户端证书
echo " - 打包客户端证书为 tls-client-certs.tar.gz"
mkdir -p "tls-client-certs"
cp -f "ca.pem" "cert.pem" "key.pem" "tls-client-certs/"
cd "tls-client-certs"
tar zcf "tls-client-certs.tar.gz" *
mv "tls-client-certs.tar.gz" ../
cd ..
rm -rf "tls-client-certs"
# 拷贝服务端证书
mkdir -p /etc/docker/certs.d
cp -f "ca.pem" "server-cert.pem" "server-key.pem" /etc/docker/certs.d/
# 清理
rm -vf client.csr server.csr extfile.cnf ca.srl server-cert.pem server-key.pem cert.pem
echo "Connect to server via docker-cli:"
echo "docker -H $IP:2376 --tlsverify --tlscacert ~/.docker/ca.pem --tlscert ~/.docker/cert.pem --tlskey ~/.docker/key.pem ps -a"
# 客户端使用 cURL 连接
echo "Connect to server via curl:"
echo "curl --cacert ~/.docker/ca.pem --cert ~/.docker/cert.pem --key ~/.docker/key.pem https://$IP:2376/containers/json"
echo -e "\e[1;32mAll be done.\e[0m"
```

执行脚本前可能由于脚本是在windows端写地，会报一个错 $’\r’: command not found；所以先对脚本执行以下这个命令 ：

`dos2unix auto-tls-certs.sh`

然后再执行命令

`sh ./auto-tls.certs.sh`

对脚本中的变量进行修改并执行后，自动会创建好tls证书，服务器的证书会自动move到/etc/docker/certs.d/目录下；

客户端的证书在运行脚本的目录下，同时还自动打好了一个.tar.gz的包，很方便，可以下载到本地，配置时直接对应上传即可。

二、配置Docker服务
在 /usr/lib/systemd/system 目录下找到 文件 docker.service

原来的ExecStart项为：

> ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

修改为：

> ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H unix:///var/run/docker.sock -D -H tcp://0.0.0.0:2376   \
		 --tlsverify --tlscacert=/etc/docker/certs.d/ca.pem   \
	        --tlscert=/etc/docker/certs.d/server-cert.pem     \
		--tlskey=/etc/docker/certs.d/server-key.pem

然后执行命令重启docker服务：

`systemctl daemon-reload`
`systemctl restart docker`

成功重启后，服务端的配置就结束了(成功与否的检测方法在自动化脚本中有说明，也可以直接进行下一步)。

 

三、配置Portainer远程TLS连接

<img src="/styles/images/Portainer-tls.png" />

证书对应选择：

TLS CA certificate：ca.pem

TLS certificate：cert.pem

TLS key：key.pem

这样就完成了。

