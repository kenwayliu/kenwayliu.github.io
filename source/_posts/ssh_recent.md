title: 远程密令开启ssh服务
date: 2013-03-10 22:00:33
categories: Linux Secure
comments: true
tags:
        - 系统安全

---

- linux服务器，我们一般是通过ssh通道远程管理，这就需要我们开启ssh端口，如22。但开启端口有被暴力破解的风险，你会说可以设置复杂的密码或使用证书避免。就算破解不了密码，但openssh也可能会有漏洞，你会说可以更改ssh端口，但还是有可能被扫描出来。还有一种选择，我们可以只允许指定IP访问ssh，通过vpn登录管理服务器，但局限很明显，万一紧急情况vpn登录不上去了怎么办。下面给出一种个人觉得比较满意的解决方案，即使用iptables的recent模块，通过密令临时开启ssh端口。当然，密令需要保管好，防止外泄。


1、iptables规则设定

```ruby
#指定78字节的icmp数据包(包含IP头部20字节，ICMP头部8字节)通过被加入sshopen列表。
iptables -A INPUT -p icmp --icmp-type 8 -m length --length 78 -m recent --set --name sshopen --rsource -j ACCEPT
#检查sshopen列表是否存在你的来源IP，如果存在，即从第一次使用密令开始15秒钟内开启ssh端口22,超过15秒端口自动关闭，不再允许新连接，已连接的不会断开。
iptables -A INPUT -p tcp --dport 22 --syn -m recent --rcheck --seconds 15 --name sshopen --rsource -j ACCEPT

```
2、通过icmp包开启ssh

```ruby
linux下:ping -s 50 host
windows下:ping -l 50 host
```
<!--more-->
3、iptables example

```ruby

-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT
-A INPUT -p tcp -m tcp --dport 123 -j ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -m length --length 50 -m recent --set --name sshopen --rsource -j ACCEPT
-A INPUT -p tcp -m tcp --dport 22 --syn -m recent --rcheck --seconds 15 --name sshopen --rsource -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 11 -j ACCEPT
-A OUTPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A OUTPUT -o lo -j ACCEPT
-A OUTPUT -p tcp -m tcp --dport 80 -j ACCEPT
-A OUTPUT -p tcp -m tcp --dport 443  -j ACCEPT
-A OUTPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT
-A OUTPUT -p icmp -m icmp --icmp-type 11 -j ACCEPT

```

