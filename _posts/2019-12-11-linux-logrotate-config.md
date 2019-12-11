---
title: "Linux Logrotate配置"
layout: post
date: 2019-12-11 22:18
guid: urn:uuid:c22db702-abe3-4f6d-8f1e-9984c2ffdbdf
author: "Lixiang"
tags:
  - Tool
  - Linux
---

Logrotate 程序是一个日志文件管理工具。用于分割日志文件，压缩转存、删除旧的日志文件，并创建新的日志文件。

Logrotate是基于CRON来运行的，其脚本是/etc/cron.daily/logrotate，日志轮转是系统自动完成的。

配置文件一般放在 `/etc/logrotate.d/`文件夹下

举例配置格式如下

```shell
vim /etc/logrotate.d/nginx
```

```shell
/var/log/weblog/*.log {
    daily         //指定转储周期为每天
    compress      //通过gzip 压缩转储以后的日志
    rotate 7      //保存7天的日志
    missingok     //如果日志文件丢失，不要显示错误
    notifempty    //当日志文件为空时，不进行轮转
    dateext       //使用当期日期作为命名格式，exp: nginx_access.log-20190120
    sharedscripts //运行postrotate脚本
    postrotate    //执行的指令
        if [ -f /run/nginx.pid ]; then
            kill -USR1 `cat /run/nginx.pid`
        fi
    endscript  //结束指令
}
```

Logrotate可以由自动或者手动触发日志轮转：

```shell
logrotate -f /etc/logrotate.d/nginx
```

FYI:

- [Linux日志切割工具Logrotate配置详解](https://www.centos.bz/2019/07/linux%e6%97%a5%e5%bf%97%e5%88%87%e5%89%b2%e5%b7%a5%e5%85%b7logrotate%e9%85%8d%e7%bd%ae%e8%af%a6%e8%a7%a3/)
