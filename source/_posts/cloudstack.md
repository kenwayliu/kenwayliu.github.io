title: Cloudstack 4.2.0 介绍
date: 2013-12-10 22:16:33
categories: CloudStack
comments: true
tags:
        - IAAS

---

## 一.CloudStack组成部分讲解：

1. Zone：Zone 对应于现实中的一个数据中心，它是 CloudStack 中最大的一个单元。
1. Pod：Pod 对应着一个机架。同一个 pod 中的机器在同一个子网（网段）中。
1. Cluster：Cluster 是多个主机组成的一个集群。同一个 cluster 中的主机有相同的硬件，相同的 Hypervisor，和共用同样的存储。同一个 cluster 中的虚拟机，可以实现无中断服务地从一个主机迁移到另外一个上。
1. Host：Host 就是运行虚拟机（VM）的主机。
1. 即从包含关系上来说，一个 zone 包含多个 pod，一个 pod 包含多个 cluster，一个 cluster 包含多个 host。
1. CloudStack 中存在两种存储：
1. Primary storage：一级存储与 cluster 关联，它为该 cluster 中的主机的全部虚拟机提供磁盘卷。一个 cluster 至少有一个一级存储，且在部署时位置要临近主机以提供高性能。
1. Secondary storage：二级存储与 zone 关联，它存储模板文件，ISO 镜像和磁盘卷快照。
1. 模板：可以启动虚拟机的操作系统镜像，也包括了诸如已安装应用的其余配置信息。
1. ISO 镜像：包含操作系统数据或启动媒质的磁盘镜像。
1. 磁盘卷快照：虚拟机数据的已储存副本，能用于数据恢复或者创建新模板

<!--more-->
## 二.安装 CloudStack

环境准备
一个完整的 CloudStack 环境包括两部分：
- 管理服务器（Management Server）
- 虚拟机管理器 (Hypervisor)
- Centos6.4
- CloudStack4.2

## 三.具体配置

```ruby
1.配置yum,检测网络,hostname,安装ntp,安装manager
[CloudStack]
name=CloudStack
baseurl=http://CloudStack.apt-get.eu/rhel/4.2/
enabled=1
gpgcheck=0
[root@CloudStack /]# hostname -f
cloud-agent.test.com
[root@CloudStack /]# ping -c 3 www.CloudStack.org
PING www.apache.org (192.87.106.229) 56(84) bytes of data.
64 bytes from aurora-2012.apache.org (192.87.106.229): icmp_seq=1 ttl=45 time=396 ms
64 bytes from aurora-2012.apache.org (192.87.106.229): icmp_seq=2 ttl=45 time=396 ms
64 bytes from aurora-2012.apache.org (192.87.106.229): icmp_seq=3 ttl=45 time=396 ms
--- www.apache.org ping statistics ---
5 packets transmitted, 3 received, 40% packet loss, time 4644ms
rtt min/avg/max/mdev = 396.281/396.515/396.858/0.570 ms
[root@CloudStack /]# yum -y install CloudStack-management
2.安装配置mysql数据库
[root@CloudStack /]# yum -y instal mysql-server
[root@CloudStack /]# vi /etc/my.cnf
添加以下参数
innodb_rollback_on_timeout=1
innodb_lock_wait_timeout=600
max_connections=350
log-bin=mysql-bin
binlog-format = 'ROW'
[root@CloudStack /]# /etc/init.d/mysqld start  #初始化mysql
[root@CloudStack /]# mysql_secure_installation #加固mysql
3.设置selinux
[root@CloudStack /]# setenforce 0
[root@CloudStack /]# getenforce
Permissive
[root@CloudStack /]# sed -i 's/SELINUX=*/SELINUX=Permissive/' /etc/sysconfig/selinux
4.导入数据到mysql，启动manager
[root@CloudStack /]#CloudStack-setup-databases cloud:<dbpassword>@localhost \  #设置cloud的dbpassword
--deploy-as=root:<password> \   #通过mysql的root用户去导入
-e <encryption_type> \
-m <management_server_key> \
-k <database_key> \
-i <management_server_ip>
[root@CloudStack /]# CloudStack-setup-management #启动manager和设置iptbles等环境
5.如果要初始化设置可以执行下列语句
mysql> drop database cloud; drop database cloud_usage;
然后在重新执行以上操作
6. 创建主存储和二级存储
[root@CloudStack /]# yum -y install nfs-utils
[root@CloudStack /]# cat /etc/exports
/export *(rw,async,no_root_squash)
[root@CloudStack export]# exportfs -a #生效/etc/export
[root@CloudStack export]# exportfs -v #查看导出目录
/export       <world>(rw,async,wdelay,no_root_squash,no_subtree_check)
[root@CloudStack /]# mkdir /export/{primary,secondary} -p  #创建目录
[root@CloudStack /]# cat >> /etc/sysconfig/nfs <<ENF   #编辑/etc/sysconfig/nfs
> LOCKD_TCPPORT=32803
> LOCKD_UDPPORT=32769
> MOUNTD_PORT=892
> RQUOTAD_PORT=875
> STATD_PORT=662
> STATD_OUTGOING_PORT=2020
> ENF
[root@CloudStack /]#vim /etc/sysconfig/iptables  #添加一下防火墙策略
-A INPUT -s <NETWORK> -m state --state NEW -p udp --dport 111 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p tcp --dport 111 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p tcp --dport 2049 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p tcp --dport 32803 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p udp --dport 32769 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p tcp --dport 892 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p udp --dport 892 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p tcp --dport 875 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p udp --dport 875 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p tcp --dport 662 -j ACCEPT
-A INPUT -s <NETWORK> -m state --state NEW -p udp --dport 662 -j ACCEPT
[root@CloudStack /]# /etc/init.d/nfs restart && /etc/init.d/mysqld restart /etc/init.d/iptables restart
5.通过http://hostname:8080/client  访问
默认用户名：admin
密码：password
后期安装agent端和相关配置
```
