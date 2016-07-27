---
layout: post
title: Linux 常用命令
date: 2015-05-21 01:41
author: admin
comments: true
categories: [Linux]
tags: [Linux]
---
 
## 性能分析

### vmstat

虚拟内存统计

用法 

    Usage:
     vmstat [options] [delay [count]]
    
    Options:
     -a, --active           active/inactive memory
     -f, --forks            number of forks since boot
     -m, --slabs            slabinfo
     -n, --one-header       do not redisplay header
     -s, --stats            event counter statistics
     -d, --disk             disk statistics
     -D, --disk-sum         summarize disk statistics
     -p, --partition <dev>  partition specific statistics
     -S, --unit <char>      define display unit
     -w, --wide             wide output
     -t, --timestamp        show timestamp
    
     -h, --help     display this help and exit
     -V, --version  output version information and exit


<!-- more -->


示例

    [root@localhost ~]# vmstat
    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
     r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
     0  0  71256  92720      0 565120    0    0     1   115   22   36  1  0 99  0  0
 
其中 

* r : 运行和等待 CPU 时间片的进程数。若该值大于系统 CPU 个数，则说明 CPU 不足
* b : 表示等待资源的进程数
* swpd : 切换到内存交换区的内存大小（单位 KB）
* free： 空闲的物理内存的大小，我的机器内存总共2G，剩余92M。
* buff： Linux/Unix系统是用来存储，目录里面有什么内容，权限等的缓存
* cache：直接用来记忆我们打开的文件,给文件做缓冲，把空闲的物理内存的一部分拿来做文件和目录的缓存，是为了提高 程序执行的性能，当程序使用内存时，buffer/cached会很快地被使用
* si：每秒从磁盘读入虚拟内存的大小，如果这个值大于0，表示物理内存不够用或者内存泄露了，要查找耗内存进程解决掉。
* so：每秒虚拟内存写入磁盘的大小，如果这个值大于0，同上。
* bi：块设备每秒接收的块数量（读磁盘）（kb/s）
* bo：块设备每秒发送的块数量（写磁盘）（kb/s）
* in：每秒CPU的中断次数，包括时间中断
* cs：每秒上下文切换次数。
* us：用户进程消耗 CPU 时间百分比
* sy：内核进程消耗 CPU 时间百分比，sy 的值较高时，则说明内核消耗的 CPU 资源很多
* id：空闲 CPU 时间百分比
* wt：IO 等待 CPU 时间百分比


### free

内存使用状况


    [root@localhost ~]# free
                  total        used        free      shared  buff/cache   available
    Mem:        1849336     1193336       90344        1752      565656      460000
    Swap:       2097148       71248     2025900
    [root@localhost ~]# free -h
                  total        used        free      shared  buff/cache   available
    Mem:           1.8G        1.1G         88M        1.7M        552M        449M
    Swap:          2.0G         69M        1.9G
    [root@localhost ~]# free -m
                  total        used        free      shared  buff/cache   available
    Mem:           1805        1165          88           1         552         449
    Swap:          2047          69        1978
    [root@localhost ~]# free -h 2
                  total        used        free      shared  buff/cache   available
    Mem:           1.8G        1.1G         88M        1.7M        552M        449M
    Swap:          2.0G         69M        1.9G
    [root@localhost ~]# free -h -s 2
                  total        used        free      shared  buff/cache   available
    Mem:           1.8G        1.1G         88M        1.7M        552M        449M
    Swap:          2.0G         69M        1.9G
    
                  total        used        free      shared  buff/cache   available
    Mem:           1.8G        1.1G         88M        1.7M        552M        449M
    Swap:          2.0G         69M        1.9G
    
                  total        used        free      shared  buff/cache   available
    Mem:           1.8G        1.1G         88M        1.7M        552M        449M
    Swap:          2.0G         69M        1.9G

### uptime

统计当前系统的运行情况

    [root@localhost ~]# uptime
     09:59:55 up 2 days, 7 min,  1 user,  load average: 0.00, 0.01, 0.05
     
load average: 系统 1分钟，5分钟，15分钟的平均负荷

### netstat

当前网络信息

用法 

    usage: netstat [-vWeenNcCF] [<Af>] -r         netstat {-V|--version|-h|--help}
           netstat [-vWnNcaeol] [<Socket> ...]
           netstat { [-vWeenNac] -I[<Iface>] | [-veenNac] -i | [-cnNe] -M | -s [-6tuw] } [delay]
    
            -r, --route              display routing table
            -I, --interfaces=<Iface> display interface table for <Iface>
            -i, --interfaces         display interface table
            -g, --groups             display multicast group memberships
            -s, --statistics         display networking statistics (like SNMP)
            -M, --masquerade         display masqueraded connections
    
            -v, --verbose            be verbose
            -W, --wide               don't truncate IP addresses
            -n, --numeric            don't resolve names
            --numeric-hosts          don't resolve host names
            --numeric-ports          don't resolve port names
            --numeric-users          don't resolve user names
            -N, --symbolic           resolve hardware names
            -e, --extend             display other/more information
            -p, --programs           display PID/Program name for sockets
            -o, --timers             display timers
            -c, --continuous         continuous listing
    
            -l, --listening          display listening server sockets
            -a, --all                display all sockets (default: connected)
            -F, --fib                display Forwarding Information Base (default)
            -C, --cache              display routing cache instead of FIB
            -Z, --context            display SELinux security context for sockets
    
      <Socket>={-t|--tcp} {-u|--udp} {-U|--udplite} {-w|--raw} {-x|--unix}
               --ax25 --ipx --netrom
      <AF>=Use '-6|-4' or '-A <af>' or '--<af>'; default: inet
      List of possible address families (which support routing):
        inet (DARPA Internet) inet6 (IPv6) ax25 (AMPR AX.25)
        netrom (AMPR NET/ROM) ipx (Novell IPX) ddp (Appletalk DDP)
        x25 (CCITT X.25)

其中 

* -a (all)显示所有选项，默认不显示LISTEN相关
* -t (tcp)仅显示tcp相关选项
* -u (udp)仅显示udp相关选项
* -n 拒绝显示别名，能显示数字的全部转化成数字
* -l 仅列出有在 Listen (监听) 的服務状态
* -p 显示建立相关链接的程序名
* -r 显示路由信息，路由表
* -e 显示扩展信息，例如uid等
* -s 按各个协议进行统计
* -c 每隔一个固定时间，执行该netstat命令。

提示：LISTEN和LISTENING的状态只有用-a或者-l才能看到

### top
    
    [root@localhost java_demos]# top
    top - 14:36:14 up 2 days,  4:43,  1 user,  load average: 0.00, 0.01, 0.05
    Tasks: 110 total,   2 running, 108 sleeping,   0 stopped,   0 zombie
    %Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.3 id,  0.2 wa,  0.0 hi,  0.0 si,  0.0 st
    KiB Mem :  1849336 total,   963348 free,   542372 used,   343616 buff/cache
    KiB Swap:  2097148 total,  2011496 free,    85652 used.  1126908 avail Mem
    
      PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
     1858 mysql     20   0 1465504 200800   6212 S   1.0 10.9  34:56.46 mysqld
        1 root      20   0   56636   4308   2808 S   0.0  0.2   0:02.05 systemd
        2 root      20   0       0      0      0 S   0.0  0.0   0:00.04 kthreadd
        3 root      20   0       0      0      0 S   0.0  0.0   0:01.53 ksoftirqd/0
        5 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H
        7 root      rt   0       0      0      0 S   0.0  0.0   0:00.07 migration/0
        8 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcu_bh
        9 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcuob/0
       10 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcuob/1
       11 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcuob/2
       12 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcuob/3
       13 root      20   0       0      0      0 S   0.0  0.0   0:14.63 rcu_sched
       14 root      20   0       0      0      0 S   0.0  0.0   0:10.43 rcuos/0
       15 root      20   0       0      0      0 S   0.0  0.0   0:04.67 rcuos/1
       
其中

* 第一行：
  * 14:36:14 当前系统时间
  *  days,  4:43 系统已经运行了2天4小时43分钟（在这期间没有重启过）
  * 1 users 当前有1个用户登录系统
  * load average: 0.00, 0.01, 0.05 ，后面的三个数分别是1分钟、5分钟、15分钟的负载情况。load average数据是每隔5秒钟检查一次活跃的进程数，然后按特定算法计算出的数值。如果这个数除以逻辑CPU的数量，结果高于5的时候就表明系统在超负荷运转了。
* 第二行：Tasks 任务（进程），
  * 系统现在共有10个进程，其中处于运行中的有2个，108个在休眠（sleep），stoped状态的有0个，zombie状态（僵尸）的有0个。
* 第三行：cpu状态
  * us 用户空间占用CPU的百分比
  * sy 内核空间占用CPU的百分比
  * ni 改变过优先级的进程占用CPU的百分比
  * id 空闲CPU百分比
  * wa IO等待占用CPU的百分比
  * hi 硬中断（Hardware IRQ）占用CPU的百分比
  * si 软中断（Software Interrupts）占用CPU的百分比
* 第四行：内存状态
  * total 物理内存总量
  * free 空闲内存总量
  * used 使用中的内存总量
  * buff/cache 缓存的内存量
* 第五行：swap交换分区
  * total 交换区总量
  * free 空闲交换区总量
  * used 使用的交换区总量
  * avail Mem 缓冲的交换区总量 
* 第六行：各进程（任务）的状态监控
  * PID 进程id
  * USER 进程所有者
  * PR 进程优先级
  * NI nice值。负值表示高优先级，正值表示低优先级
  * VIRT 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES
  * RES 进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA
  * SHR 共享内存大小，单位kb
  * S 进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程
  * %CPU 上次更新到现在的CPU时间占用百分比
  * %MEM 进程使用的物理内存百分比
  * TIME+ 进程使用的CPU时间总计，单位1/100秒
  * COMMAND 进程名称（命令名/命令行）
       
### 监控java线程数：

ps -eLf | grep java | wc -l

### 监控网络客户连接数：

netstat -n | grep tcp | grep 侦听端口 | wc -l
 

