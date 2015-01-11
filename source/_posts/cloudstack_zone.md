title: Cloudstack 4.2.1+vMware Hypervisor 重装
date: 2014-06-08 12:10
categories: CloudStack
comments: true
tags:
        - IAAS

---

1. 现在我们需要重新部署一遍CloudStack,就是删除所有的数据,因为删除的时候出现了很多错误.
<!--more-->
	- 禁用区域,通过CloudStack-ui删除所有的虚拟机(业务+系统+vRoute)
    - 删除注册的模板
    - 删除数据库
    - 通过vsphere登录到vcenter中删除对应的主存储中的所有数据
    - 查看虚拟机和模板是否有未删除的模板,如果未删除的系统模板,重新初始化的时候会提示找不到模板文件.
    - 删除主机对应的虚拟网络中的网卡配置接口
    - 修改数据中心cloud.zone 为flase,如果不修改,第二次添加的时候会提示该数据中心已被添加
