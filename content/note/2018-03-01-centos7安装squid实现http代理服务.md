---
title: centos7安装squid实现http代理服务
author: gXcloud
date: '2018-03-01'
slug: centos7-squid-http
categories:
  - 爬虫
tags:
  - linux
  - vpn
  - http
---


> 在centos7环境搭建带认证功能的squid代理服务



### yum安装

squid实现代理功能，httpd-tools文件加密

```
yum -y install squid
yum -y install httpd-tools
```

### squid的使用参数

进程启动、状态控制

```Shell
systemctl enable squid
systemctl status/restart/stop squid
systemctl status squid
● squid.service - Squid caching proxy
   Loaded: loaded (/usr/lib/systemd/system/squid.service; enabled; vendor preset: disabled)
   Active: active (running) since 三 2018-02-28 05:56:10 CST; 23h ago
  Process: 4889 ExecStop=/usr/sbin/squid -k shutdown -f $SQUID_CONF (code=exited, status=0/SUCCESS)
  Process: 4898 ExecStart=/usr/sbin/squid $SQUID_OPTS -f $SQUID_CONF (code=exited, status=0/SUCCESS)
  Process: 4892 ExecStartPre=/usr/libexec/squid/cache_swap.sh (code=exited, status=0/SUCCESS)
 Main PID: 4900 (squid)
   CGroup: /system.slice/squid.service
           ├─4900 /usr/sbin/squid -f /etc/squid/squid.conf
           ├─4902 (squid-1) -f /etc/squid/squid.conf
           ├─4903 (logfile-daemon) /var/log/squid/access.log
           └─4907 (basic_ncsa_auth) /etc/squid/passwd
```

版本信息、命令行参数

```Shell
# squid -v
Squid Cache: Version 3.5.20
Service Name: squid
configure options:  '--build=x86_64-redhat-linux-gnu' '--host=x86_64-redhat-linux-gnu' '--program-prefix=' '--prefix=/usr' '--exec-prefix=/usr' '--bindir=/usr/bin' '--sbindir=/usr/sbin' '--sysconfdir=/etc' '--datadir=/usr/share' '--includedir=/usr/include' '--libdir=/usr/lib64' '--libexecdir=/usr/libexec' '--sharedstatedir=/var/lib' '--mandir=/usr/share/man' '--infodir=/usr/share/info' '--disable-strict-error-checking' '--exec_prefix=/usr' '--libexecdir=/usr/lib64/squid' '--localstatedir=/var' '--datadir=/usr/share/squid' '--sysconfdir=/etc/squid'

# squid -h
Usage: squid [-cdhvzCFNRVYX] [-n name] [-s | -l facility] [-f config-file] [-[au] port] [-k signal]
       -a port   Specify HTTP port number (default: 3128).
       -d level  Write debugging to stderr also.
       -f file   Use given config-file instead of
                 /etc/squid/squid.conf
       -h        Print help message.
       -k reconfigure|rotate|shutdown|restart|interrupt|kill|debug|check|parse
                 Parse configuration file, then send signal to
                 running copy (except -k parse) and exit.
       -n name   Specify service name to use for service operations
                 default is: squid.
       -s | -l facility
                 Enable logging to syslog.
       -u port   Specify ICP port number (default: 3130), disable with 0.
       -v        Print version.
       -z        Create missing swap directories and then exit.
       -C        Do not catch fatal signals.
       -D        OBSOLETE. Scheduled for removal.
       -F        Don't serve any requests until store is rebuilt.
       -N        No daemon mode.
       -R        Do not set REUSEADDR on port.
       -S        Double-check swap during rebuild.
       -X        Force full debugging.
       -Y        Only return UDP_HIT or UDP_MISS_NOFETCH during fast reload.
```

查看日志

```Shell
tail -f /var/log/squid/access.log
```

### 添加认证

```
#生成密码文件
touch /etc/squid/passwd && chown squid /etc/squid/passwd
#添加认证用户
htpasswd /etc/squid/passwd user
```

### squid配置

匹配认证用户

```Shell
vi /etc/squid/squid.conf

auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Squid Basic Authentication
auth_param basic credentialsttl 2 hours
acl auth_users proxy_auth REQUIRED
http_access allow auth_users
```

非透明代理

```shell
vi /etc/squid/squid.conf

request_header_access Via deny all
request_header_access X-Forwarded-For deny all
request_header_access From deny all
```

### 代理测试

wget命令行

```Shell
wget -e "https_proxy=https://user:passwd@106.105.95.26:3128/" https://www.baidu.com/
```

Python requests示例

```python
import requests

proxies = {'http': 'http://106.105.95.26:3128', 'https': 'https://106.105.95.26:3128'}
proxies = {'http': 'http://user:passwd@106.105.95.26:3128', 'https': 'https://user:passwd@106.105.95.26:3128'}
resp = requests.get('http://httpbin.org/ip', proxies=proxies)
print(resp.json())
```



### 备注：

直接使用这种方式即是在墙外服务器搭建squid的http/https代理服务，也不能实现代理访问墙外网站，还需要进一步加密请求信息等



------

[^1]: [how-to-install-and-configure-squid-proxy-on-centos-7](https://hostpresto.com/community/tutorials/how-to-install-and-configure-squid-proxy-on-centos-7/)
[^2]: [SQUID正向代理实现翻墙访问FACEBOOK/GOOGLE](https://putty.biz/181)
[^3]: [使用squid为爬虫添加二级代理](https://rookiefly.cn/detail/192)
[^4]: [搭建需要身份认证的 Squid 代理](https://maoxian.de/2016/06/1415.html)

