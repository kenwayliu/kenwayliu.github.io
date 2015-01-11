title: Cloudstack 4.2.0 添加KVM主机
date: 2014-01-11 22:16:33
categories: CloudStack
comments: true
tags:
        - IAAS

---

### 一.KVM主机安装kvm环境

```python
[root@testing-kvm01 ~]# cat /etc/redhat-release
CentOS release 6.4 (Final)
[root@testing-kvm01 ~]# yum -y install yum install kvm kmod-kvm qemu kvm-qemu-img virt-viewer virt-manager libvirt vconfig ntp CloudStack-agent
[root@testing-kvm01 ~]# hostname -f
```

### 二.修改libvirtd.conf和libvirtd为以下状态

```python
[root@testing-kvm01 ~]# egrep -v '(^#|^$)'  /etc/libvirt/libvirtd.conf
listen_tls=0
listen_tcp=1
tcp_port="16509"
mdns_adv = 0
auth_tcp="none"
[root@testing-kvm01 ~]# egrep -v '(^#|^$)'  /etc/sysconfig/libvirtd
LIBVIRTD_ARGS=-l
export CGROUP_DAEMON='cpu:/virt'
```
<!--more-->
### 三.修改qemu.conf的vpc_listen

```python
[root@testing-kvm01 ~]# grep "vnc_listen" /etc/libvirt/qemu.conf
# vnc_listen = "0.0.0.0"
# over vnc_listen.
vnc_listen="0.0.0.0"
```

### 四.启动libvirtd
```
[root@testing-kvm01 ~]# /etc/init.d/libvirtd start
```
#### 五.关闭selinux

```python
[root@testing-kvm01 ~]# grep "^SELINUX" /etc/sysconfig/selinux
SELINUX=disabled
SELINUXTYPE=targete
```

六.通过web-gui添加主机

NOTE：碰到的问题
```python
[root@testing-kvm01 ~]# tail -100 /var/log/CloudStack/agent/CloudStack-agent.out
Unable to start agent: Failed to get private nic name
Unable to start agent: Failed to get private nic name
上面的日志报错的原因是/etc/CloudStack/agent/agent.properties的问题。
你要保证里面的对的netword-device的名称要和kvm主机上面的一致否则会报错。
```
