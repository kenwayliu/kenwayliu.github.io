title: Screen
date: 2013-06-07 19:44:33
categories: Linux Tools
comments: true
tags:
        - Linux常用工具

---



#### 一.screen简介

1.用于后台不间断操作,常用与测试.数据临时备份等相关的操作


#### 二.screen vs nohub


nohub 执行命令后是直接丢到后台运行,退出后无法通过父进程链接

如果我们使用screen可以随时登录和登出当前shell
<!--more-->

#### 三.screen安装


install for centos
```ruby
   yum -y install screen
```

install for ubuntu

```ruby
   apt-get install screen
```

#### 四.screen常用参数

```python
   screen                  直接进入一个bash,这样的缺点是有太多的screen会混乱,不知道每个进程的目的
   screen -S sockname      指定当天screen运行的sock名字,用户方便区分
   screen -ls              列出当前所有的screen,状态有(Attached|Detached)
   screen -r PID|SOCKNAME  登录指定的screen
   screen -d scockname|pid 当通过screen -ls查看状态为Attached的时候可以通过此命令终止当前的链接
```

#### 五.screen 窗体快捷键

```ruby
   ctrl+a d 退出当前session,进程不中断
   ctrl+a c 在窗体中创建另一个窗体
   ctrl+a n 切换到下一个窗体
   ctrl+a p 切换到上一个窗体

```

