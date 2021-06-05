---
title: "syslog-ng安装及简单实例"
tags: ["syslog-ng"]
categories: ["日志"]
date: 2021-06-05T09:00:01+08:00
toc: true
draft: false
---

## 背景
之前在一家物流公司，产品还没上线，应用程序跑在开发环境和测试环境。辛苦了几天搭建了一套日志系统ELK，满心欢愉的向开发和测试推广ELK，可惜没过几天，开发就向我提出意见了，说在Kibaba上看日志太不爽了，上面的实时日志流要等两三秒才出来，太不实时，等得难受。此时，我的心很受伤，不过想了一下，好像这也是个问题，确实用户体验不太好，开发环境和测试环境会经常调试，对实时性的要求比较高。ELK擅长查询和统计，更适合用在生产环境上，要求像在linux上一样的查看实时日志确实有点难为它。  
  
调研了一下，要达到的目的有3个，第一，要求实时；第二，日志整合在一台服务器上；第三，最好能跟linux上查看一样。好吧，合理的痛点需求至高无上，于是开始着手研究syslog-ng。
  
## 简介
syslog是Linux系统默认的日志守护进程，默认的syslog配置文件是/etc/syslog.conf文件。syslog守护进程是可配置的，它允许人们为每一种类型的系统信息精确地指定一个存放地点。比较 syslog ，syslog-ng 具有众多高级的功能：更好的网络支持，更加方便的配置，集中式的网络日志存储，并且更具有弹性。比如，使用syslogd时，所有的iptables日志与其他内核日志一起全部存储到了kern.log文件里。Syslog-ng则可以让你有选择性的将iptables部分分出到另外的日志文件中。Syslogd仅能使用UDP协议，Syslog-ng 可以使用UDP和TCP协议。所以我们可以在加密的网络隧道中传输日志到集中日志服务器。
  
syslog-ng的一个设计原则就是建立更好的消息过滤粒度。syslog-ng能够进行基于内容和优先权/facility的过滤。另一个设计原则是更容易进行不同防火墙网段的信息转发，它支持主机链，即使日志消息经过了许多计算机的转发，也可以找出原发主机地址和整个转发链。最后的一个设计原则就是尽量使配置文件强大和简洁。syslog-ng作为syslog的替代工具，可以完全替代syslog的服务，并且通过定义规则，实现更好的过滤功能。

## 模式
syslog-ng模式有三种：客户端模式、服务端模式、中继模式。
* 客户端模式（client mode）：
* 服务端模式（server mode）:
* 中继模式（Relay mode）：

## 安装
### 1、安装
```
[root@syslog-ng ~]# yum install syslog-ng -y      # 安装
[root@syslog-ng ~]# systemctl start syslog-ng.service    # 启动
[root@syslog-ng ~]# systemctl enable syslog-ng.service   # 开机启动
```
### 2、工作目录
配置目录
```
[root@syslog-ng syslog-ng]# pwd
/etc/syslog-ng
[root@syslog-ng syslog-ng]# ls
conf.d  patterndb.d  scl.conf  syslog-ng.conf
```
syslog-ng.conf 为主配置文件，它主要分为 5 段：
```
options {  };    # 全局配置
source s_name {  };    # 日志源，可以是本地也可以是远程主机
filter f_name {  };    # 过滤规则
destination d_name {  };    # 日志输出，可以是本地也可以是远程主机
log { source(s_name); filter(f_name); destination() };    #此段将来源、过滤、目的，连接起来并且告诉syslog-ng如何处理日志
```

## 简单实例
### 1、实例描述
本实例才用syslog-ng的C/S架构，客户端和服务端都要安装syslog-ng。接收远程客户端应用日志（dev-api.log、dev-conf.log）保存在本机指定位置提供查看。

### 2、安装
在服务端和客户端都安装
```
[root@syslog-ng ~]# yum install syslog-ng -y 
```

### 3、服务端操作
```
[root@syslog-ng ~]# cd /etc/syslog-ng/
[root@syslog-ng syslog-ng]# cp syslog-ng.conf syslog-ng.conf_bak
```

```
[root@syslog-ng syslog-ng]# vim syslog-ng.conf
@version:3.5
@include "scl.conf"

# syslog-ng configuration file.
#
# This should behave pretty much like the original syslog on RedHat. But
# it could be configured a lot smarter.
#
# See syslog-ng(8) and syslog-ng.conf(5) for more information.
#
# Note: it also sources additional configuration files (*.conf)
#       located in /etc/syslog-ng/conf.d/

options {
    flush_lines (0);    # 0 为不缓存
    time_reopen (10);     # 对于死连接，到达多少秒，会重新连接
    log_fifo_size (1000);    # 输出队列的行数
    chain_hostnames (off);    # 是否打开主机名链功能，打开后可在多网络段转发日志时有效
    use_dns (no);    # 是否打开DNS查询功能，应使用防火墙保护使用syslog-ng的节点安全，并确认所有主机都是可以通过dns解释的，否则请关闭该选项
    use_fqdn (no);    # 是否使用完整的域名
    create_dirs (no);     # 当指定的目标目录不存在时，是否创建该目录
    keep_hostname (yes);     # 是否保留日志消息中保存的主机名称，否时，总是使用来源主机来作重写日志的主机名
};

# 配置多个不同项目的日志时，端口一定不能配置成一样的，否则多个日志会被传输到同一个日志文件里。默认端口是514，这里多个项目，自定义了端口510和511
source s-dev-api { udp(ip(0.0.0.0) port(510)); };    
source s-dev-conf { udp(ip(0.0.0.0) port(511)); };

# 将日志保存在/data/logs/dev/下
destination d-dev-api { file("/data/logs/dev/dev-api.log"); };
destination d-dev-conf { file("/data/logs/dev/dev-conf.log"); };

log { source(s-dev-api);  destination(d-dev-api); };
log { source(s-dev-api);  destination(d-dev-conf); };

# Source additional configuration files (.conf extension only)
@include "/etc/syslog-ng/conf.d/*.conf"
```

启动
```
[root@syslog-ng syslog-ng]# systemctl restart syslog-ng.service
[root@syslog-ng syslog-ng]# systemctl enable syslog-ng.service
```

### 4、客户端操作
```
[root@webapps ~]# vim /etc/syslog-ng/syslog-ng.conf 

@version:3.5
@include "scl.conf"

# syslog-ng configuration file.
#
# This should behave pretty much like the original syslog on RedHat. But
# it could be configured a lot smarter.
#
# See syslog-ng(8) and syslog-ng.conf(5) for more information.
#
# Note: it also sources additional configuration files (*.conf)
#       located in /etc/syslog-ng/conf.d/

options {
    flush_lines (0);
    time_reopen (10);
    log_fifo_size (1000);
    chain_hostnames (off);
    use_dns (no);
    use_fqdn (no);
    create_dirs (no);
    keep_hostname (yes);
};

# 采集本机的日志
source s-dev-api { file("/data/webapps/dev-api/log/dev-api.log"); };
source s-dev-conf { file("/data/webapps/dev-conf/log/dev-conf.log"); };

# 将采集到的日志传输到服务端
# 192.168.1.10为服务端的IP，端口和服务端配置里的端口一一对应
destination d-dev-api { udp(192.168.1.10 port(510)); };
destination d-dev-conf { udp(192.168.1.10 port(511)); };

log { source(s-dev-api); destination(d-dev-api); };
log { source(s-dev-conf); destination(d-dev-conf); };


# Source additional configuration files (.conf extension only)
@include "/etc/syslog-ng/conf.d/*.conf"


# vim:ft=syslog-ng:ai:si:ts=4:sw=4:et:
```

启动
```
[root@webapps ~]# systemctl restart syslog-ng.service
[root@webapps ~]# systemctl enable syslog-ng.service
```

### 5、测试
在客户端制造日志
```
[root@webapps ~]# echo 11111111111 >> /data/webapps/dev-api/log/dev-api.log
[root@webapps ~]# echo 22222222222 >> /data/webapps/dev-api/log/dev-api.log
```

在服务端验证
```
[root@syslog-ng ~]# tail -f /data/logs/dev/dev-api.log
Jul 15 14:21:17 webapps 11111111111
Jul 15 14:21:17 webapps 22222222222 
```

可以看到日志大致有3列：  
第一列为服务端接传输到这个日志文件的时间（Jul 15 14:21:17）；  
第二列为客户端的主机名称（webapps）；  
第三列为客户端的应用日志。  

## 日志分割及归档
用shell脚本进行每天日志分割，并进行归档。
```shell script
#! /bin/bash
# 名称： /opt/syslog-ng-split.sh
# 用法： 
# 1、  chmod +x /opt/syslog-ng-split.sh
# 2、  0 0 * * * /opt/syslog-ng-split.sh
# 注释
# 1、日志名字必须以 "$ENV-" 开头
# 2、存放日志的目录根据变量“LOG_DIR”自定义

DATE=`date -d yesterday +%Y%m%d`
LOG_DIR=/data/logs

# 检查日志目录是否存在，没有退出
if [ ! -d $LOG_DIR ];then
  echo "$LOG_DIR , Directory does not exist!"
  exit 1
fi

# 检查是否有 yesterday 和 history目录，如果没有就创建
for ENV in uat dev
do
  if [ ! -d "$LOG_DIR/$ENV" ];then
    echo "$LOG_DIR/$ENV , Directory does not exist!"
    continue
  fi

  cd $LOG_DIR/$ENV/

  if [ ! -d yesterday ] ;then
    mkdir yesterday
  fi
  if [ ! -d history ];then
    mkdir history
  fi
# 删除yesterday目录下日志，等待前一天日志进入
  rm -rf $LOG_DIR/$ENV/yesterday/*
# 日志移动到 yesterday 目录
  mv ${ENV}-* yesterday/
# 在 yesterday目录下打包压缩
  cd $LOG_DIR/$ENV/yesterday
  tar -zcvf ${ENV}_${DATE}.tar.gz $ENV-*
# 将压缩包移动到 history 目录
  mv ${ENV}_${DATE}.tar.gz $LOG_DIR/$ENV/history/
done
```




