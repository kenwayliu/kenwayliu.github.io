title: Cloudstack 4.2.1-模板注册问题
date: 2014-04-17
categories: CloudStack
comments: true
tags:
        - IAAS
---



1.当我们在高级网络(通过VLAN的方式隔离)中注册模板的时候可能会在模板注册的时候提示

```ruby
		状态: connection refused
        
        碰到这个文件需要修改全局变量参数
        	secstorage.allowed.internal.sites  在后面输入二级存储的IP就可以, xx.xx.xx.xx/32 就可以了,如果只写IP地址是不可以的
        
```
