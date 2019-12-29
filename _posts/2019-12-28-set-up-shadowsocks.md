---
title: "Shadowsocks一键安装脚本"
layout: post
date: 2019-12-28 22:04
guid: urn:uuid:2e6c4840-9718-4b54-ac3d-b9ce36ded216
author: "Lixiang"
tags:
  - Tool
---

CentOS系统安装shadowsock服务端。

### 1、下载shadowsock服务端安装脚本

```shell
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
```

### 2、执行安装

```shell
[root@pegasus ~]# ./shadowsocks-all.sh
Which ××× server you'd select:                            #选择安装版本
1) ×××-Python
2) ×××R
3) ×××-Go
4) ×××-libev
Please enter a number (Default ×××-Python):        #默认安装×××-Python

You choose = ×××-Python

Please enter password for ×××-Python
(Default password: teddysun.com):123456789                    #设置密码

password = 123456789

Please enter a port for ×××-Python [1-65535]
(Default port: 13690):30003                                                #设置端口号

port = 30003

Please select stream cipher for ×××-Python:            #选择加密方法
1) aes-256-gcm
2) aes-192-gcm
3) aes-128-gcm
4) aes-256-ctr
5) aes-192-ctr
6) aes-128-ctr
7) aes-256-cfb
8) aes-192-cfb
9) aes-128-cfb
10) camellia-128-cfb
11) camellia-192-cfb
12) camellia-256-cfb
13) xchacha20-ietf-poly1305
14) chacha20-ietf-poly1305
15) chacha20-ietf
16) chacha20
17) salsa20
18) rc4-md5
Which cipher you'd select(Default: aes-256-gcm):7

cipher = aes-256-cfb

Press any key to start...or Press Ctrl+C to cancel                #按任意键继续，或Ctrl+C退出
```

### 3、安装完毕

```shell
started
Starting ××× success

Congratulations, ×××-Python server install completed!
Your Server IP : xxx.xxx.xxx.xxx                #服务端地址
Your Server Port : 30003                            #服务端端口号
Your Password : 123456789                        #密码
Your Encryption Method: aes-256-cfb        #加密方式

Your QR Code: (For ××× Windows, OSX, Android and iOS clients)
 ss://YWVzLTI1Ni1jZmI6MTIzNDU2Nzg5QDIwNS4xODUuMTIyLjcyOjMwMDAz
Your QR Code has been saved as a PNG file path:
 /root/shadowsocks_python_qr.png

Welcome to visit: https://teddysun.com/486.html
Enjoy it!
```

### 4、启动

```shell
/etc/init.d/shadowsocks-python { start | stop | restart | status }
```

### 5、默认配置文件

`/etc/shadowsocks-python/config.json`

### 6、多用户配置文件（可选）

```shell
{
"server":"0.0.0.0",
"local_address":"127.0.0.1",
"local_port":1080,
"port_password":{
     "8989":"centos",
     "9001":"centos",
     "9002":"centos"},
"timeout":300,
"method":"aes-256-cfb",
"fast_open": false
}
```

FYI:

- [teddysun/shadowsocks_install](https://github.com/teddysun/shadowsocks_install)
- [转自：shadowsock安装(centos)](https://blog.51cto.com/niuduzi/2407002)
