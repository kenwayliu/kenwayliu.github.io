title: Python 通过网站search功能监控网站内容更新
date: 2014-05-09 15:24:33
categories: Python
comments: true
tags:
        - 脚本语言
---

接到需求帮朋友监控一个信息网站,当该网站有相关的数据更新的时候发送信息到指定邮箱.
下面是相关的python脚本,用到了httplib, time , sys ,smtplib模块
<!--more-->
```python
#!/usr/bin/env python
#coding=utf8
import httplib, time
import sys
import smtplib
reload(sys)
sys.setdefaultencoding('utf8')
from email.mime.text import MIMEText
mailto_list=['sendmail@126.com']
mail_host="smtp.sina.com"
mail_user="sendmailaccount"
mail_pass="sendmailpassword"
mail_postfix="sina.com"
sec=60
conn = httplib.HTTPConnection("网站地址")
conn.request("GET", "搜索后的网站地址|/caigou/kw-%E6%B1%A1%E6%B0%B4%E5%A4%84%E7%90%86%E6%8B%9B%E6%A0%87.html") 
r1 = conn.getresponse()
a = r1.read()
def send_mail(to_list,sub,content):
    me="网站信息已更新"+"<"+mail_user+"@"+mail_postfix+">"
    msg = MIMEText(content,_subtype='html',_charset='utf8')
    msg['Subject'] = sub
    msg['From'] = me
    msg['To'] = ";".join(to_list)
    try:
        s = smtplib.SMTP()
        s.connect(mail_host)
        s.login(mail_user,mail_pass)
        s.sendmail(me, to_list, msg.as_string())
        s.close()
        return True
    except Exception, e:
        print str(e)
        return False

print "The program is starting"
while True:
	time.sleep( sec )
	b = r1.read()
	if a == b:
		print 'good running ,Now it is ' + time.strftime('%Y年%m月%d日,%H:%M:%S')+ '\n请稍后.....'
	else:
		print "网站更新"
		if __name__ == '__main__':
			if send_mail(mailto_list, "网站信息已更新", "对应网站域名"):
        			print "发送成功"
    			else:
        			print "发送失败"
		a = r1.read()
        
```
