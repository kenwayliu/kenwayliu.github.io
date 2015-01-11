title: 运维常用shell
date: 2014-05-3 22:16:33
categories: Shell
comments: true
tags:
        - 脚本语言

---

1.显示消耗内存/CPU最多的10个进程
`ps aux | sort -nk +4 | tail -10`
`ps aux | sort -nk +3 | tail -10`
 
2.查看Apache的并发请求数及其TCP连接状态
`netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'`
另一种方法：
`netstat -ant|awk '/^tcp/ {print awk $NF}'|sort|uniq -c|sort -nr`
<!--more-->
 
3.找出自己最常用的10条命令及使用次数（或求访问最多的ip数）
`sed -e 's/| //n/g' ~/.bash_history |cut -d  '' -f 1 | sort | uniq -c | sort -nr | head`
 
4.日志中第10个字段表示连接时间，求平均连接时间
``cat access_log |grep “connect cbp” |awk` ‘BEGIN{sum=0;count=0;}{sum+=$10;count++;}END{printf(“sum=%d,count=%d,avg=%f/n”,sum,count,sum/count)}’``
 
5.lsof命令
```
lsof abc.txt 显示开启文件abc.txt的进程
lsof -i :22 知道22端口现在运行什么程序
lsof -c abc 显示abc进程现在打开的文件
lsof -p 12  看进程号为12的进程打开了哪些文件
```
6.杀掉一个程序的所有进程
```
pkill -9 httpd
killall -9 httpd
```
注意尽量不用-9，数据库服务器上更不能轻易用kill，否则造成重要数据丢失后果将不堪设想。
 
7.rsync命令（要求只同步某天的压缩文件，而且远程目录保持与本地目录一致）
```
/usr/bin/rsync -azvR –password-file=/etc/rsync.secrets `find . -name “*$yesterday.gz”  -type f ` storage@192.168.2.23::logbackup/13.21/
```
8.把目录下*.sh文件改名为*.SH
```
find .  -name "*.sh" | sed  's//(.*/)/.sh/mv /0 /1.SH/' |sh
find .  -name "*.sh" | sed  's//(.*/)/.sh/mv & /1.SH/'|sh  （跟上面那个效果一样）
``` 
9.ssh执行远程的程序，并在本地显示
`ssh -n -l zouyunhao 192.168.2.14 "ls -al /home/zouyunhao"`
 
10. 直接用命令行修改密码
`echo "zouyunhaoPassword" |passwd –stdin zouyunhao`
 
```
ssh-keygen
ssh-copy-id -i ~/.ssh/id_rsa.pub user@remoteServer
``` 
11.以http方式共享当前文件夹的文件
`$ python -m SimpleHTTPServer`
在浏览器访问http://IP:8000/即可下载当前目录的文件。


12.shell段注释
`:<<'echo hello,world!'`


13.查看服务器序列号
`dmidecode |grep "Serial Number"`
（查看机器其他硬件信息也可用这个命令）


14.查看网卡是否有网线物理连接
**监控网卡变化** `/sbin/mii-tool -m`
**重启单独网卡** `ifup eth* && ifdown eth*`


15、扫描开放端口
`nmap 127.0.0.1 |grep -w 443  #-w精确匹配`


下面我会陆续添加一些在实际工作中用到的其他不错的shell命令~~~
1、将大写文件名改为小写文件名
``for i in `find ./ -type f -name "[A-Z]*"| xargs -0`; do mv -f $i `echo $i | tr A-Z a-z`; done``
 
2、zat查看压缩文件
`zcat install.log.tgz |grep -a ssh #过滤必须加上-a参数`
 
3、杀掉程序所有进程，跟上面pkill功能一样
`pgrep httpd|xargs kill -9 #pgrep过滤出进程号再kill`
 
4、快速备份一个文件
`cp filename{,.bak}  #filename{,.bak}` **这一段会被展开成filename filename.bak再传给cp**


5、重命名文件：
`ls|xargs -t -i mv {} {}.bak` **所有文件添加.bak后缀**
`ls |sed 's/.bak$//'|xargs -I {} mv {}.bak {}` **去除所有文件.bak后缀，上一个命令的反向恢复**
`for i in `ls`;do mv $i ${i%.*};done` **同上,去除后缀名,利用变量模式匹配运算符**
`find ./ -name "*.py" -exec mv {} {}.bak \;` **将所有.py后缀文件，加上.bak后缀**
`rename .sh .py *.sh` **将所有.sh后缀重命名为.py**
``for i in `ls *.sh`;do mv $i `echo $i|awk -F \. '{print $1}'`.py;done`` **同上**


6、tar包不解压统计大小：
```
tar tvf nrpe-2.8.1.tar.gz | awk '{total+=$3}END{print total}'
tar tvf nrpe-2.8.1.tar.gz | awk '{total+=$3}END{print total/1024}'  #kb大小
```

7、将两个文件交叉显示
```
paste a.txt b.txt|tr "\t" "\n"
paste a.txt b.txt|xargs -n1
```


8、使用xargs分析单词出现的频率:
`cat file.txt |xargs -n1|tr A-Z a-z|sed -e 's/\.//g' -e 's/\,//g' -e 's/ /\n/g'|sort|uniq -c|sort -nr`
解释：
```
xargs -n1:列出文件, 每行一个单词
tr A-Z a-z:将字符转换为小写
sed -e 's/\.//g' -e 's/\,//g' -e 's/ /\n/g': 过滤掉句号和逗号,单词间的空格修改为换行
另一种空格转换行方法：直接使用换行符
 's/ /\      
/g'
sort|uniq -c|sort -nr:排序，计数，再排序
```
不使用xargs统计：
`sed -e 's/\.//g' -e 's/\,//g' -e 's/ /\n/g' README |tr 'A-Z' 'a-z'|sort|uniq -c|sort -nr`


9、xargs拷贝文件到另一个目录中：


`ls . | xargs -i -t cp ./{} /data/backup`

```
t 是 "verbose" (输出命令行到stderr) 选项.
-i 是"替换字符串"选项.
{} 是输出文本的替换点.
这与在"find"命令中使用{}的情况很相像.
列出当前目录下的所有文件(ls .),将 "ls" 的输出作为参数传递到 "xargs"(-i -t 选项)中,然后拷贝(cp)这些参数({})到一个新目录中(/data/backup).等价cp * /data/backup,除非有文件名中嵌入了"空白"字符.
```

10、利用xargs kill进程
`ps ax | grep "httpd" | awk '{print $1}' | xargs -i kill {} 2&>/dev/null`


11、重复字符序列中除去除第一个字符以外的所有字符，例如aaa,多个空格
```
tr -s 'a' <test.txt #多个a连续，只保留第一个
tr -s ' ' <test.txt #字符间多个空格，只保留一个
```

12、去除windows下换行符^M
```
tr -d "\r" <file.txt >out.txt
tr -d "\015" <file.txt >out.txt #^M八进制形式是 015
strings file.txt >out.txt
sed -i 's/^M//g' file.txt  #^M不是shift+^再加上M,应该是ctrl+v加上ctrl+m
vi文件下执行:%s/^M//g替换为空
dos2unix file.txt
```
13、过滤文件内电子邮箱和网站地址
过滤邮箱(只显示过滤文本)：
```
#egrep -o '[A-Za-z0-9.]+@[A-Za-z0-9.]+\.[a-zA-Z]{2,3}' file.txt
zhangsan@sina.com
lishi.123@sohu.com
```
过滤网站地址：
```
#egrep -o 'http://[A-Za-z0-9\-\.]+\.[a-zA-Z]{2,3}' file.txt
http://code.nagios.google.com
http://www.baidu.com
```
过滤IP地址：
`egrep -o "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" file.txt`

