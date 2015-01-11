title: Nginx生产环境调优
date: 2015-01-10 12:19
categories: Nginx
comments: true
tags: 
        - WEB服务器
---

<!--more-->
#### 一、内存调优

```ruby
内核关于内存的选项都在/proc/sys/vm目录下.
1.pdflush,用于回写内存中的脏数据到硬盘。可以通过 /proc/sys/vm/vm.dirty_background_ratio调整。
首先查看这个值默认应该是10。
[root@esf ~]# cat /proc/sys/vm/dirty_background_ratio
10
这个值是一个阀值,说明如果内存中的脏数据达到系统总内存的10%时,那么pdflush进程就会启动,将内存中的脏数据写回硬盘.这个值可适当调高.可获得更快的写入速度.
2.swappiness选项
[root@esf ~]# cat /proc/sys/vm/swappiness
60
swappiness表示使用swap分区的使用程度,可以适当调整swappiness=0的时候表示尽可能使用物理内存swap空间.swappiness=100积极使用swap.
3.dirty_ratio
[root@esf ~]# cat /proc/sys/vm/dirty_ratio
40
dirty_ratio的值是数据写进内存的阀值,40%是指当系统内存已经缓存了40%的数据以后,就不再往内存中缓存数据了.
```
#### 二、磁盘I/O调优

- Linux磁盘I/O调优
	- 概述

```python
1.   磁盘调优目录：/sys/block
2.   磁盘调优均是调内核参数，要求安装kernel-doc包，
3.   调预先读请求量（默认为128kb）
# blockdev –getra /dev/sda
# blockdev –setra 512 /dev/sda
实际修改/sys/block/sda/queue/read_ahead_kb=256kb；如果读情况多则将此参数调大一点，如写请求读，则将此参数调小一点
4.   调磁盘队列：队列长则处理快，提升硬盘的吞吐量，但会消耗更大的内存
Queue length：/sys/block/sda/queue/nr_requests
# cat /sys/block/sda/queue/nr_requests
512
#默认128，没有单位
二）、 磁盘I/O的4种调度算法

1.   CFQ(完全公平排队I/O调度程序)(elevator=cfq)
特点:
  这是默认算法，对于通用服务器来说通常是最好的选择。它试图均匀地分布对I/O带宽的访问。是deadline和anticipatory调度器的折中方案.
  CFQ对于多媒体应用(video,audio)和桌面系统是最好的选择.
  CFQ赋予I/O请求一个优先级,而I/O优先级请求独立于进程优先级,高优先级的进程的读写不能自动地继承高的I/O优先级.
  CFQ基于64位的队列请求,使用的轮询的方法处理队列.
调优参数：
#  /sys/block/sda/queue/iosched/queued：轮询时每次处理的最大请求数
#  /sys/block/sda/queue/iosched/quantum：每隔多少个请求数做一次轮询
cfq调优工具ionice
ionice可以更改任务的类型和优先级,不过只有cfq调度程序可以用ionice.
有三个例子说明ionice的功能:
 采用cfq的实时调度（实时调度：c1）,优先级为7（数字越低优先级越高）
# ionice -c1 -n7 -ptime dd if=/dev/sda1 f=/tmp/test bs=2M count=300&
 采用缺省的磁盘I/O调度（轮询调度：c2）,优先级为3
# ionice -c2 -n3 -ptime dd if=/dev/sda1 f=/tmp/test bs=2M count=300&
 采用空闲的磁盘调度（idle调度：c3）,优先级为0
# ionice -c3 -n0 -ptime dd if=/dev/sda1 f=/tmp/test bs=2M count=300&
#ionice的三种调度方法,实时调度最高,其次是缺省的I/O调度,最后是空闲的磁盘调度.
ionice的磁盘调度优先级有8种,最高是0,最低是7.
注意,磁盘调度的优先级与进程nice的优先级没有关系.一个是针对进程I/O的优先级,一个是针对进程CPU的优先级.
2.   Deadline（截止时间调度程序）(elevator=deadline)：
特点:
  Deadline确保了在一个截止时间内服务请求,这个截止时间是可调整的,而默认读期限短于写期限.这样就防止了写操作因为不能被读取而饿死的现象.
  这个算法试图把每次请求的延迟降至最低。该算法重排了请求的顺序来提高性能。可以调队列的过期的读写过程，如 read_expire 和 write_expire 二个参数来控制多久内一定要读到数据，超时就放弃排序。
  比较合适小文件。还可以使用打开 front_merges 来进行合并邻近文件。Deadline对数据库环境(ORACLE RAC,MYSQL等)是最好的选择.
调优：
# echo deadline >/sys/block/sda/queue/scheduler
# more /sys/block/sda/queue/scheduler
 noop anticipatory [deadline] cfq
# ls /sys/block/sda/queue/iosched/
fifo_batch  front_merges  read_expire  write_expire  writes_starved
# cat iosched/read_expire
500    #默认500ms
# cat iosched/write_expire
5000   #默认5000ms
# cat iosched/front_merges
1
#默认为1，请求整合，新的请求和之前请求有关联，则优先处理该请求,对IO性能没有影响，但优化了磁盘处理。
3.   Anticipatory（预料I/O调度程序）(elevator=as):
特点：
  预想调度算法。优化完成率，改善读请求。
  本质上与Deadline一样,但在最后一次读操作后，要等待6ms才能继续进行对其它I/O请求进行调度。可以从应用程序中预订一个新的读请求,改进读 操作的执行，但以一些写操作为代价，它会在每个6ms中插入新的I/O操作,而会将一些小写入流合并成一个大写入流，用写入延时换取最大的写入吞吐量。
  AS适合于写入较多的环境,比如文件服务器；适合大文件处理，适合web server等。AS对数据库环境表现很差。
调优：
# echo “anticipatory”>/sys/block/sda/queue/scheduler
# more iosched/antic_expire
6       #默认为6秒
# more /sys/block/sda/queue/iosched/read_expire
125      #读的最大队列时间
# more /sys/block/sda/queue/iosched/write_expire
250      #写的最大队列时间
注：测试时一定要清空缓存
# free –m    #查看缓存
             total    used   free   shared    buffers     cached
Mem:        4054    506   3548   0        140        256
-/+ buffers/cache:      108   3945
Swap:         8189   0    8189
测试：
# sysctl -w vm.drop_caches=3   #清空缓存
# rpm –ivh elevator-test-0.1-3.i386.rpm  #安装测试软件
# dd if=/dev/urandom of=/root/bigfile bs=1M count=100
# watch –n 1 ls –lh /root/bigfile   #每隔一秒查写入bigfile的数据量
 默认调度类型为cfq下，测试读数据速度（测试时要求清空缓存）
# cat /sys/block/sda/queue/scheduler
noop anticipatory deadline [cfq]
et_start reader /root/bigfile
Launching
Reading 25600 pages
Read 20000 pages
real    0m1.552s
user    0m0.011s
sys     0m0.147s
 将调度类型改为anticipatory，测试读数据速度（测试时要求清空缓存）
# echo “anticipatory”>/sys/block/sda/queue/scheduler
noop anticipatory deadline [anticipatory]
# cat /sys/block/sda/queue/iosched/antic_expire
12    #该值默认为6，为提高速度，改成12
et_start reader /root/bigfile
Launching
Reading 25600 pages
Read 20000 pages
real    0m1.456s
user    0m0.007s
sys     0m0.144s
 结论：很明显读的数度提高了。
4.   NOOP（电梯式调度程序）(elevator=noop):
特点：
  不做任何调优，主要用于节省CPU资源。
  Noop调度算法指的是当请求被存储到队列并交由I/O子系统处理时由磁盘硬件对其进行优化。该算法一般只对一些特定的硬件（例如RAM disk和TCQ disk等）。
  Noop对于I/O不那么操心，对所有的I/O请求都用FIFO队列形式处理，默认认为 I/O不会存在性能问题。这也使得CPU也不用那么操心
三）、 I/O调度算法使用建议

1.   Deadline I/O scheduler
   在这个中 deadline 调度算法通过降低性能而获得更短的等待时间，它使用轮询的调度器,简洁小巧,提供了最小的读取延迟
   和尚佳的吞吐量,特别适合于读取较多的环境(比如数据库,Oracle 10G 之类).
2.   Anticipatory I/O scheduler
   anticipatory 算法通过增加等待时间来获得更高的性能，假设一个块设备只有一个物理查找磁头(例如一个单独的SATA硬盘),将多个随机的小写入流合并成一个大写入流 (相当于给随机读写变顺序读写), 使用这个原理来使用读取写入的延时换取最大的读取写入吞吐量.适用于大多数环境,特别是读取写入较多的环境,比如文件服务器,Web 应用,App等应用我们可以采纳as调度.后面我会教大家怎么调这个的合并的等待时间。
3.   CFQ I/O scheduler
这个是 对所有因素也都做了折中而尽量获得公平性,使用QoS策略为所有任务分配等量的带宽,避免进程被饿死并实现了较低的延迟,可以认为是上述两种调度器的折中.适用于有大量进程的多用户系统。

```

#### 三、 sysctl.conf参数的调整

- 优化TIME_WAIT

```ruby
tcp连接断开后，会以TIME_WAIT状态保留一定的时候，然后才会释放端口。当并发很多的时候，就会产生大量的TIME_WAIT，无法及时断开，占用大量的端口和服务器资源。优化TCP的内核参数，及时处理TIME_WAIT
查看TIME_WAIT的数量
# netstat -n | awk ‘/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}’
优化
# vim /etc/sysctl.conf
net.ipv4.tcp_syncookies = 1     //开户SYN Cookies，当出现等待队列溢出时，启用cookies来处理，防范少量SYN攻击
net.ipv4.tcp_tw_reuse = 1       //开户重用，允许TIME_WAIT sockets重新用于新的TCP连接
net.ipv4.tcp_tw_recycle = 1     //开户TCP连接中TIME_WAIT sockets的快速回收
net.ipv4.tcp_fin_timeout = 30       //修改TIMEOUT的时间
net.ipv4.tcp_keepalive_time = 1200      //当keepalive起作用的时候，TCP发送keepalive消息的频度，缺少是2小时，改为20分钟
net.ipv4.ip_local_port_range = 10000 65000      //修改端口范围，允许更多的连接
net.ipv4.tcp_max_syn_backlog = 8192     //SYN队列的长度，默认为1024，加大队列长度为8192，以容纳更多等待连接的网络连接
net.ipv4.tcp_max_tw_buckets = 5000      //系统同时保持TIME_WAIT的最大数量，如果超过这个数字，TIME_WAIT将立即被清除
net.core.netdev_max_backlog = 32768     //每个网络接口接收数据包的速率比内核处理这些包的速率快时，允许送到队列的数据包的最大数目。
net.core.somaxconn = 32768      //web应用中listen函数的backlog默认会给我们内核参数的net.core.somaxconn限制到128，而nginx定义的NGX_LISTEN_BACKLOG默认为511，所以有必要调整这个值。
net.ipv4.tcp_wmem = 8192 436600 873200  //TCP写buffer
net.ipv4.tcp_rmem  = 32768 436600 873200    //TCP读buffer
net.inet.tcp.sendspace=65536  //最大的待发送TCP数据缓冲区空间
net.inet.tcp.recvspace=65536  //最大的接受TCP缓冲区空间
net.inet.udp.sendspace=65535  //最大的接受UDP缓冲区大小
net.inet.udp.maxdgram=65535  //最大的发送UDP数据缓冲区大小
net.local.stream.sendspace=65535  //本地套接字连接的数据发送空间
最后使用命令sysctl让其生效
# sysctl -p
```

#### 四、 网络调优

- 双网卡绑定以提高带宽

```python
1.安装软件
apt-get install ifenslave
2.修改配置文件
/etc/network/interfaces
auto lo
iface lo inet loopback
iface eth0 inet dhcp
iface eth1 inet dhcp
auto bond0
iface bond0 inet static
address 64.0.177.20
netmask 255.255.255.0
gateway 64.0.177.254
up ifenslave bond0 eth0 eth1
down ifenslave -d bond0 eth0 eth1
3.加载模块
vi /etc/modules
bonding
```

#### 五、 nginx调优
- 配置文件调优

```python
worker_processes 8;  //nginx进程数，建议按照cpu数目来指定，一般为它的倍数。
worker_cpu_affinity 00000001 00000010 00000100 00001000 00010000 00100000 01000000 10000000;  //为每个进程分配cpu，上例中将8个进程分配到8个cpu，当然可以写多个，或者将一个进程分配到多个cpu。
worker_rlimit_nofile 102400;  //这个指令是指当一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均匀，所以最好与ulimit -n的值保持一致。
use epoll;   //使用epoll的I/O模型，这个不用说了吧.
worker_connections 102400;   //每个进程允许的最多连接数，理论上每台nginx服务器的最大连接数为worker_processes*worker_connections。
keepalive_timeout 60;  //keepalive超时时间。
limit_rate_after 2m;
                                                //限制速度，当用户请求的文件超过2M的时候，开始限速，限制为128k
limit_rate  128k;
二）、安装插件调优

安装Google插件，以实现加速内存的分配和回收
TCMallocde的全称为Thread-Caching Malloc，是谷歌开发的开源工具google-perftools中得一个成员。与标准的glibc库的Malloc相比，TCMalloc库在内存 分配效率和速度上要高很多，这在很大程度上提高了服务器在高并发情况下的性能，从而降低了系统的负载。
要安装TCMalloc库，需要安装libunwind（32位操作系统不需要安装）和google-perftools两个软件包，libunwind库位基于64位cpu和操作系统的程序提供了基本函数调用链和函数调用寄存器功能。
1、安装libunwind库
可以从http://mirror.yongbok.net/nongnu/libunwind/ 下载相应的版本
下载地址：http://mirror.yongbok.net/nongnu/libunwind/libunwind-0.99.tar.gz
# tar xnf libunwind-0.99.tar.gz
# cd libunwind-0.99/
# CFLAGS=-fPIC ./configure
# make CFLAGS=-fPIC
# make CFLAGS=-fPIC install
2、安装google-perftools
可从http://code.google.com/p/gperftools/ 下载相应的版本。
下载地址：http://gperftools.googlecode.com/files/google-perftools- 1.9.tar.gz  最新版：http://gperftools.googlecode.com/files/gperftools-2.0.tar.gz 这里用的google-perftools-1.9.tar.gz
# tar xvf google-perftools-1.9.tar.gz
# cd google-perftools-1.9/
# ./configure
# make && make install
# echo “/usr/local/lib“ >> /etc/ld.so.conf.d/usr_local_lib.conf
# ldconfig
至此google-perftools安装完成
3、重新编译nginx
为了使nginx支持google-perftools，需要在安装过程中添加”–with-google_perftools_module”选项重新编译nginx。安装如下：
# ./configure –with-google_perftools_module –with-http_stub_status_module –with-http_ssl_module –prefix=/usr/local/webserver/nginx
# make
# make install
到这里nginx安装完成
4、为google-perftools添加线程目录
创建一个线程目录，这里将文件放在/tmp/tcmalloc下。
# mkdir /tmp/tcmalloc
# chmod 0777 /tmp/tcmalloc
5、修改nginx主配置文件
修改nginx.conf文件，在pid这行的下面添加如下代码：
google_perftools_profiles    /tmp/tcmalloc;
接着，重启nginx即可完成google-perftools的加载。
6、验证运行状态
为了验证google-perftools已经正常加载，可通过如下命令查看:
lsof -n | grep tcmalloc
```
