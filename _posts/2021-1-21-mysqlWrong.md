---
layout: post
title: mysql8一些错误
date: 2021-1-21
Author: halin
tag: [mysql,learning]
conmment: false

---
更新mysql8.0遇到一些问题和解决方法
<!-- more -->
## mysql8修改密码
  - ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password'; 

## 错误：java.sql.SQLException: Unable to load authentication plugin ‘caching_sha2_password‘

  - 原因是5.x版本和8.x版本区别：
    - 5.7版本是：default_authentication_plugin=mysql_native_password
    - 8.x版本就是：default_authentication_plugin=caching_sha2_password
  - 解决方法
    - my.ini 中的配置改成5.7
    - 更新mysql-connector-java驱动

## 错误：Public Key Retrieval is not allowed

  - 如果用户使用了 sha256_password 认证，密码在传输过程中必须使用 TLS 协议保护，但是如果 RSA 公钥不可用，可以使用服务器提供的公钥；可以在连接中通过 ServerRSAPublicKeyFile 指定服务器的 RSA 公钥，或者AllowPublicKeyRetrieval=True参数以允许客户端从服务器获取公钥；但是需要注意的是 AllowPublicKeyRetrieval=True可能会导致恶意的代理通过中间人攻击(MITM)获取到明文密码，所以默认是关闭的，必须显式开启
  - 解决方法：在连接url后面添加：allowPublicKeyRetrieval=true

## 警告：Establishing SSL connection without server's identity verification is not recommend

  - MySQL8中需要指明是否进行SSL连接
  - 解决方法：在连接url后面添加：useSSL=true