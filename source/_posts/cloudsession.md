title: Cloudstack 4.2.1 session保持
date: 2014-05-05 19:23:33
categories: CloudStack
comments: true
tags:
        - IAAS
---

由于CloudStack的保持时间很短,当我们登录到CloudStack-wei-ui后,默认30min内没有操作可以会与服务器断开,这时我们就需要重新登录,为了延长session的保持时间我们需要修改/etc/CloudStack/managerment/web.xml这个配置文件 481行,之前的值是30min我们可以改为1440
min(一天),然后需要重新启动CloudStack-managerment服务就可以了.

```ruby
 480     <session-config>
 481         <session-timeout>1440</session-timeout>
 482     </session-config>
 483 
```
