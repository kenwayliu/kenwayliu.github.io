title: vMware模板重封装
date: 2014-06-08 11:30
categories: CloudStack
comments: true
tags:
        - IAAS

---

1. 现在需要从vMware原生平台迁入vm到CloudStack 4.2.1中,我这边只拿到了ova的模板,为了验证模板中的硬件配置是否符合CloudStack要求(1.导出时取消所有usb设备连接 2.保证cd/dvd的连接是当前客户端设备 3.顺便看下scsi控制器的格式).
<!--more-->
2. 如果发现ova模板不符合规格,可参考以下两种方法.
	-  通过本地vMware原生平台部署该模板为虚拟机,移除相关的外置设备,重新导出为ova模板
	-  由于ova的模板是一个 gzip的压缩包,我们可以解压此文件,
	```ruby
    ##查看文件类型
    LT import # file tchost_import_.ova 
	tchost_import_.ova: gzip compressed data, from Unix, last modified: Wed May  7 10:00:54 2014
    ##解包ova文件
	LT import # tar xfz tchost_import_.ova
	##查看解压后的文件
    LT import # ls
	tchost_import_.ova  tchost_import_ovf_8-disk1.vmdk  tchost_import_ovf_8.mf  	tchost_import_ovf_8.ovf
	```
       -  .vmdk 磁盘文件
       -  .mf   存放vmdk 和ovf的 sha1校验信息
       -  .ovf  存储该虚拟机的配置信息
			-  这时我们就需要修改这个文件了,不知道修改什么位置的可以参考一个正常ova模板的.ovf文件
