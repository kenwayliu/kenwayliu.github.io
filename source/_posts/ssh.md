title: ssh基础篇
date: 2013-03-07 21:23
categories: Linux Secure
comments: true
tags:
        - 系统安全

---

SSH-安全登录服务,主要用于远程登录管理
1.监听tcp的22端口
2.相关软件包openssh-cline,openssh-server-5.3p1-81.el6.x86_64
3.配置文件位置在/etc/ssh中,ssh_config是客户端配置文件,sshd_config是服务端配置文件,默认通过公私钥的方式加密
当客户端连接时会在本地家目录生成一个公钥


当我们要同时分发文件到多台服务器的时候当我们一台一台登录的时候比较麻烦.这是我们可以通过生成公私钥的方式,把公钥分发到每一台host,这样我们不用每次输入密码了,一下是步骤

```ruby
ssh-keygen   生成密钥
ssh-copy-id  -i  ~/.ssh/*.pub user@host  把生成的公钥传到客户端,这样就不用每次数据密码了.
```

ssh常用的命令:

```ruby

ssh user@host     登录远程主机
ssh user@host   "command"   本地操作远程主机执行命令
ssh user@host   "command" > /tmp/     把远程服务器执行的结果重定向到本地tmp目录下
tar cf - dir | ssh IP "cat - > /tmp/dir1.tar"   把本地的文件或者目录传输到远程服务器,并且是打包的
ssh IP "tar xzf - -C /tmp" < /dir/dd.tar.gz   把本地的tar包解压到远程目录
tar cf - . |ssh root@host "tar xf - -C /tmp"  把本地的数据打包传输到远程服务器,所有的目录树结构和权限是一样的
```

如果想批量执行可以这么操作

```ruby
cat <<END |ssh IP
>service httpd restart  重启httpd服务
>sleep 2                     停顿2s
>service sshd restart    重启sshd服务
>ENDF                          退出并执行
```
ssh转发功能,解决不加密的服务进行转发

ssh IP -L 2025:localhost:25    通过本地2025端口去连接远程的25号端口


远程执行本地脚本
ssh  user@localhost bash < /tmp/shell.sh

