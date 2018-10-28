[20个命令行工具监控 Linux 系统性能](https://linuxstory.org/command-line-tools-to-monitor-linux-performance/)

[linux常用的监控命令](https://www.cnblogs.com/huangxm/p/6278615.html)

[25个Linux性能监控工具](https://www.cnblogs.com/peter316/p/6287212.html)

[Linux free -m 详解命令](https://blog.csdn.net/luo_xia530/article/details/54341389)
## top
### 选项

-b：以批处理模式操作；<br>
-c：显示完整的治命令；<br>
-d：屏幕刷新间隔时间；<br>
-I：忽略失效过程；<br>
-s：保密模式；<br>
-S：累积模式；<br>
-i<时间>：设置间隔时间；<br>
-u<用户名>：指定用户名；<br>
-p<进程号>：指定进程；<br>
-n<次数>：循环显示的次数。<br>
### 交互命令
h：显示帮助画面，给出一些简短的命令总结说明；<br>
k：终止一个进程；<br>
i：忽略闲置和僵死进程，这是一个开关式命令；<br>
q：退出程序；<br>
r：重新安排一个进程的优先级别；<br>
S：切换到累计模式；<br>
s：改变两次刷新之间的延迟时间（单位为s），如果有小数，就换算成ms。输入0值则系统将不断刷新，默认值是5s；<br>
f或者F：从当前显示中添加或者删除项目；<br>
o或者O：改变显示项目的顺序；<br>
l：切换显示平均负载和启动时间信息；<br>
m：切换显示内存信息；<br>
t：切换显示进程和CPU状态信息；<br>
c：切换显示命令名称和完整命令行；<br>
M：根据驻留内存大小进行排序；<br>
P：根据CPU使用百分比大小进行排序；<br>
T：根据时间/累计时间进行排序；<br>
w：将当前设置写入~/.toprc文件中。<br>
### 实例
top - 09:44:56 up 16 days, 21:23,  1 user,  load average: 9.59, 4.75, 1.92<br>
Tasks: 145 total,   2 running, 143 sleeping,   0 stopped,   0 zombie<br>
Cpu(s): 99.8%us,  0.1%sy,  0.0%ni,  0.2%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st<br>
Mem:   4147888k total,  2493092k used,  1654796k free,   158188k buffers<br>
Swap:  5144568k total,       56k used,  5144512k free,  2013180k cached<br>

#### 解释
top - 09:44:56[当前系统时间],<br>
16 days[系统已经运行了16天],<br>
1 user[个用户当前登录],<br>
load average: 9.59, 4.75, 1.92[系统负载，即任务队列的平均长度]<br>
Tasks: 145 total[总进程数],<br>
2 running[正在运行的进程数],<br>
143 sleeping[睡眠的进程数],<br>
0 stopped[停止的进程数],<br>
0 zombie[冻结进程数],<br>
Cpu(s): 99.8%us[用户空间占用CPU百分比],<br>
0.1%sy[内核空间占用CPU百分比],<br>
0.0%ni[用户进程空间内改变过优先级的进程占用CPU百分比],<br>
0.2%id[空闲CPU百分比], 0.0%wa[等待输入输出的CPU时间百分比],<br>
0.0%hi[],<br>
0.0%st[],<br>
Mem: 4147888k total[物理内存总量],<br>
2493092k used[使用的物理内存总量],<br>
1654796k free[空闲内存总量],<br>
158188k buffers[用作内核缓存的内存量]<br>
Swap:  5144568k total[交换区总量],<br>
56k used[使用的交换区总量],<br>
5144512k free[空闲交换区总量],<br>
2013180k cached[缓冲的交换区总量],<br>

## free
### 选项
-b：以Byte为单位显示内存使用情况；<br>
-k：以KB为单位显示内存使用情况；<br>
-m：以MB为单位显示内存使用情况；<br>
-o：不显示缓冲区调节列；<br>
-s<间隔秒数>：持续观察内存使用状况；<br>
-t：显示内存总和列；<br>
-V：显示版本信息。<br>
### 实例
free -m<br>
             total       used       free     shared    buffers     cached<br>
Mem:          2016       1973         42          0        163       1497<br>
-/+ buffers/cache:        312       1703<br>
Swap:         4094          0       4094<br>
###第一部分mem行解释:
total：内存总数；<br>
used：已经使用的内存数；<br>
free：空闲的内存数；<br>
shared：当前已经废弃不用；<br>
buffers Buffer：缓存内存数；<br>
cached Page：缓存内存数。<br>
关系：total = used + free<br>

### 第二部分(-/+ buffers/cache)解释:


(-buffers/cache) used内存数：第一部分Mem行中的 used – buffers – cached<br>
(+buffers/cache) free内存数: 第一部分Mem行中的 free + buffers + cached<br>
可见-buffers/cache反映的是被程序实实在在吃掉的内存，而+buffers/cache反映的是可以挪用的内存总数。<br>

### 第三部分是指交换分区。
我想大家看了上面,还是很晕.第一部分(Mem)与第二部分(-/+ buffers/cache)的结果中有关used和free为什么这么奇怪.<br>
其实我们可以从二个方面来解释.<br>
**对操作系统来讲是Mem的参数.buffers/cached 都是属于被使用,所以它认为free只有232.**<br>
对应用程序来讲是(-/+ buffers/cach).buffers/cached 是等同可用的,因为buffer/cached是为了提高程序执行的性能,当程序使用内存时,buffer/cached会很快地被使用.<br>
所以,以应用来看看,以(-/+ buffers/cache)的free和used为主.所以我们看这个就好了.另外告诉大家一些常识.Linux为了提高磁盘和内存存取效率, Linux做了很多精心的设计, 除了对dentry进行缓存(用于VFS,加速文件路径名到inode的转换), 还采取了两种主要Cache方式：Buffer Cache和Page Cache.前者针对磁盘块的读写,后者针对文件inode的读写.这些Cache能有效缩短了 I/O系统调用(比如read,write,getdents)的时间.<br>
记住内存是拿来用的,不是拿来看的.不象windows,无论你的真实物理内存有多少,他都要拿硬盘交换文件来读.这也就是windows为什么常常提示虚拟空间不足的原因.你们想想,多无聊,在内存还有大部分的时候,拿出一部分硬盘空间来充当内存.硬盘怎么会快过内存.所以我们看linux,只要不用swap的交换空间,就不用担心自己的内存太少.如果常常swap用很多,可能你就要考虑加物理内存了.这也是linux看内存是否够用的标准哦.<br>





## vmstat
### 选项<br>
-a：显示活动内页；<br>
-f：显示启动后创建的进程总数；<br>
-m：显示slab信息；<br>
-n：头信息仅显示一次；<br>
-s：以表格方式显示事件计数器和内存状态；<br>
-d：报告磁盘状态；<br>
-p：显示指定的硬盘分区状态；<br>
-S：输出信息的单位。<br>
### 参数<br>
事件间隔：状态信息刷新的时间间隔；<br>
次数：显示报告的次数。<br>
实例<br>
vmstat 3<br>
procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu------<br>
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st<br>
 0  0    320  42188 167332 1534368    0    0     4     7    1    0  0  0 99  0  0<br>
 0  0    320  42188 167332 1534392    0    0     0     0 1002   39  0  0 100  0  0<br>
 0  0    320  42188 167336 1534392    0    0     0    19 1002   44  0  0 100  0  0<br>
 0  0    320  42188 167336 1534392    0    0     0     0 1002   41  0  0 100  0  0<br>
 0  0    320  42188 167336 1534392    0    0     0     0 1002   41  0  0 100  0  0<br>
#### 字段说明：<br>
Procs（进程）<br>
r: 运行队列中进程数量，这个值也可以判断是否需要增加CPU。（长期大于1）<br>
b: 等待IO的进程数量。<br>
Memory（内存）<br>
swpd: 使用虚拟内存大小，如果swpd的值不为0，但是SI，SO的值长期为0，这种情况不会影响系统性能。<br>
free: 空闲物理内存大小。<br>
buff: 用作缓冲的内存大小。<br>
cache: 用作缓存的内存大小，如果cache的值大的时候，说明cache处的文件数多，如果频繁访问到的文件都能被cache处，那么磁盘的读IO bi会非常小。<br>
Swap<br>
si: 每秒从交换区写到内存的大小，由磁盘调入内存。<br>
so: 每秒写入交换区的内存大小，由内存调入磁盘。<br>
注意：内存够用的时候，这2个值都是0，如果这2个值长期大于0时，系统性能会受到影响，磁盘IO和CPU资源都会被消耗。有些朋友看到空闲内存（free）很少的或接近于0时，就认为内存不够用了，不能光看这一点，还要结合si和so，如果free很少，但是si和so也很少（大多时候是0），那么不用担心，系统性能这时不会受到影响的。<br>
IO（现在的Linux版本块的大小为1kb）<br>
bi: 每秒读取的块数<br>
bo: 每秒写入的块数<br>
注意：随机磁盘读写的时候，这2个值越大（如超出1024k)，能看到CPU在IO等待的值也会越大。<br>
system（系统）<br>
in: 每秒中断数，包括时钟中断。<br>
cs: 每秒上下文切换数。<br>
注意：上面2个值越大，会看到由内核消耗的CPU时间会越大。<br>
CPU（以百分比表示）<br>
us: 用户进程执行时间百分比(user time)<br>
us的值比较高时，说明用户进程消耗的CPU时间多，但是如果长期超50%的使用，那么我们就该考虑优化程序算法或者进行加速。<br>
sy: 内核系统进程执行时间百分比(system time)<br>
sy的值高时，说明系统内核消耗的CPU资源多，这并不是良性表现，我们应该检查原因。<br>
wa: IO等待时间百分比<br>
wa的值高时，说明IO等待比较严重，这可能由于磁盘大量作随机访问造成，也有可能磁盘出现瓶颈（块操作）。<br>
id: 空闲时间百分比<br>

### lsof
### tcpdump — 网络数据包分析器
### netstat — 网络统计
### 语法<br>
netstat(选项)<br>
### 选项<br>
-a或--all：显示所有连线中的Socket；<br>
-A<网络类型>或--<网络类型>：列出该网络类型连线中的相关地址；<br>
-c或--continuous：持续列出网络状态；<br>
-C或--cache：显示路由器配置的快取信息；<br>
-e或--extend：显示网络其他相关信息；<br>
-F或--fib：显示FIB；<br>
-g或--groups：显示多重广播功能群组组员名单；<br>
-h或--help：在线帮助；<br>
-i或--interfaces：显示网络界面信息表单；<br>
-l或--listening：显示监控中的服务器的Socket；<br>
-M或--masquerade：显示伪装的网络连线；<br>
-n或--numeric：直接使用ip地址，而不通过域名服务器；<br>
-N或--netlink或--symbolic：显示网络硬件外围设备的符号连接名称；<br>
-o或--timers：显示计时器；<br>
-p或--programs：显示正在使用Socket的程序识别码和程序名称；<br>
-r或--route：显示Routing Table；<br>
-s或--statistice：显示网络工作信息统计表；<br>
-t或--tcp：显示TCP传输协议的连线状况；<br>
-u或--udp：显示UDP传输协议的连线状况；<br>
-v或--verbose：显示指令执行过程；<br>
-V或--version：显示版本信息；<br>
-w或--raw：显示RAW传输协议的连线状况；<br>
-x或--unix：此参数的效果和指定"-A unix"参数相同；<br>
--ip或--inet：此参数的效果和指定"-A inet"参数相同。<br>
实例<br>
列出所有端口 (包括监听和未监听的)<br>
<br>
netstat -a     #列出所有端口<br>
netstat -at    #列出所有tcp端口<br>
netstat -au    #列出所有udp端口                             <br>
列出所有处于监听状态的 Sockets<br>
<br>
netstat -l        #只显示监听端口<br>
netstat -lt       #只列出所有监听 tcp 端口<br>
netstat -lu       #只列出所有监听 udp 端口<br>
netstat -lx       #只列出所有监听 UNIX 端口<br>
显示每个协议的统计信息<br>
<br>
netstat -s   显示所有端口的统计信息<br>
netstat -st   显示TCP端口的统计信息<br>
netstat -su   显示UDP端口的统计信息<br>
在netstat输出中显示 PID 和进程名称<br>
<br>
netstat -pt<br>
netstat -p可以与其它开关一起使用，就可以添加“PID/进程名称”到netstat输出中，这样debugging的时候可以很方便的发现特定端口运行的程序。<br>
<br>
在netstat输出中不显示主机，端口和用户名(host, port or user)<br>
<br>
当你不想让主机，端口和用户名显示，使用netstat -n。将会使用数字代替那些名称。同样可以加速输出，因为不用进行比对查询。<br>
<br>
netstat -an<br>
如果只是不想让这三个名称中的一个被显示，使用以下命令:<br>
<br>
netsat -a --numeric-ports<br>
netsat -a --numeric-hosts<br>
netsat -a --numeric-users<br>
持续输出netstat信息<br>
<br>
netstat -c   #每隔一秒输出网络信息<br>
显示系统不支持的地址族(Address Families)<br>
<br>
netstat --verbose<br>
在输出的末尾，会有如下的信息：<br>
<br>
netstat: no support for `AF IPX' on this system.<br>
netstat: no support for `AF AX25' on this system.<br>
netstat: no support for `AF X25' on this system.<br>
netstat: no support for `AF NETROM' on this system.<br>
显示核心路由信息<br>
<br>
netstat -r<br>
使用netstat -rn显示数字格式，不查询主机名称。<br>
<br>
找出程序运行的端口<br>
<br>
并不是所有的进程都能找到，没有权限的会不显示，使用 root 权限查看所有的信息。<br>
<br>
netstat -ap | grep ssh<br>
找出运行在指定端口的进程：<br>
<br>
netstat -an | grep ':80'<br>
显示网络接口列表<br>
<br>
netstat -i<br>
显示详细信息，像是ifconfig使用netstat -ie。<br>
<br>
IP和TCP分析<br>
<br>
查看连接某服务端口最多的的IP地址：<br>
<br>
netstat -ntu | grep :80 | awk '{print $5}' | cut -d: -f1 | awk '{++ip[$1]} END {for(i in ip) print ip[i],"\t",i}' | sort -nr<br>
TCP各种状态列表：<br>
<br>
netstat -nt | grep -e 127.0.0.1 -e 0.0.0.0 -e ::: -v | awk '/^tcp/ {++state[$NF]} END {for(i in state) print i,"\t",state[i]}'<br>
查看phpcgi进程数，如果接近预设值，说明不够用，需要增加：<br>
<br>
netstat -anpo | grep "php-cgi" | wc -l<br>

<br>
sh-4.2# netstat -a<br>
Active Internet connections (servers and established)<br>
Proto Recv-Q Send-Q Local Address           Foreign Address         State      <br>
tcp        0      1 55a14df955d5:54350      ti-in-f139.1e100.:https SYN_SENT    <br>
tcp        0      0 55a14df955d5:55322      123.125.114.32:http     ESTABLISHED<br>
tcp6       0      0 [::]:squid              [::]:*                  LISTEN     <br>
tcp6       1      0 55a14df955d5:squid      172.17.0.1:41604        CLOSE_WAIT <br>
tcp6       0      0 55a14df955d5:squid      172.17.0.1:41596        ESTABLISHED<br>
tcp6       1      0 55a14df955d5:squid      172.17.0.1:41626        CLOSE_WAIT <br>
udp        0      0 0.0.0.0:55157           0.0.0.0:*                          <br>
udp6       0      0 [::]:43257              [::]:*                             <br>
Active UNIX domain sockets (servers and established)<br>
Proto RefCnt Flags       Type       State         I-Node   Path<br>
unix  3      [ ]         STREAM     CONNECTED     29565    <br>
unix  3      [ ]         STREAM     CONNECTED     29564    <br>
<br>
<br>
sh-4.2# netstat -n<br>
Active Internet connections (w/o servers)<br>
Proto Recv-Q Send-Q Local Address           Foreign Address         State      <br>
tcp        0      1 172.17.0.3:46868        74.125.204.138:443      SYN_SENT   <br>
tcp        0      1 172.17.0.3:46864        74.125.204.138:443      SYN_SENT   <br>
tcp        0      1 172.17.0.3:52454        74.125.204.100:443      SYN_SENT   <br>
tcp6       1      0 172.17.0.3:3128         172.17.0.1:41660        CLOSE_WAIT <br>
tcp6       0      0 172.17.0.3:3128         172.17.0.1:41596        ESTABLISHED<br>
tcp6       1      0 172.17.0.3:3128         172.17.0.1:41626        CLOSE_WAIT <br>
tcp6       1      0 172.17.0.3:3128         172.17.0.1:41642        CLOSE_WAIT <br>
Active UNIX domain sockets (w/o servers)<br>
Proto RefCnt Flags       Type       State         I-Node   Path<br>
unix  3      [ ]         STREAM     CONNECTED     29565    <br>
unix  3      [ ]         STREAM     CONNECTED     29564    <br>
<br>
<br>
<br>
<br>
sh-4.2# netstat -g<br>
IPv6/IPv4 Group Memberships<br>
Interface       RefCnt Group<br>
--------------- ------ ---------------------<br>
lo              1      all-systems.mcast.net<br>
eth0            1      all-systems.mcast.net<br>
lo              1      ip6-allnodes<br>
lo              1      ff01::1<br>
ip6tnl0         1      ip6-allnodes<br>
ip6tnl0         1      ff01::1<br>
eth0            1      ip6-allnodes<br>
eth0            1      ff01::1<br>
<br>
<br>
sh-4.2# netstat -i<br>
Kernel Interface table<br>
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg<br>
eth0      1500    78029      0      0 0         67480      0      0      0 BMRU<br>
lo       65536        0      0      0 0             0      0      0      0 LRU<br>
<br>
<br>
<br>
sh-4.2# netstat -l<br>
Active Internet connections (only servers)<br>
Proto Recv-Q Send-Q Local Address           Foreign Address         State      <br>
tcp6       0      0 [::]:squid              [::]:*                  LISTEN     <br>
udp        0      0 0.0.0.0:55157           0.0.0.0:*                          <br>
udp6       0      0 [::]:43257              [::]:*                             <br>
Active UNIX domain sockets (only servers)<br>
Proto RefCnt Flags       Type       State         I-Node   Path<br>
<br>
<br>
sh-4.2# netstat -p<br>
Active Internet connections (w/o servers)<br>
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    <br>
tcp        0      1 55a14df955d5:41400      ti-in-f101.1e100.:https SYN_SENT    -                   <br>
tcp6       0      0 55a14df955d5:squid      172.17.0.1:41596        ESTABLISHED -                   <br>
tcp6       1      0 55a14df955d5:squid      172.17.0.1:41700        CLOSE_WAIT  -                   <br>
Active UNIX domain sockets (w/o servers)<br>
Proto RefCnt Flags       Type       State         I-Node   PID/Program name     Path<br>
unix  3      [ ]         STREAM     CONNECTED     29565    -                    <br>
unix  3      [ ]         STREAM     CONNECTED     29564    -    <br>
<br>
sh-4.2# netstat -r<br>
Kernel IP routing table<br>
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface<br>
default         gateway         0.0.0.0         UG        0 0          0 eth0<br>
172.17.0.0      0.0.0.0         255.255.0.0     U         0 0          0 eth0<br>
<br>
sh-4.2# netstat -s<br>
Ip:<br>
    78155 total packets received<br>
    0 forwarded<br>
    0 incoming packets discarded<br>
    78155 incoming packets delivered<br>
    67684 requests sent out<br>
Icmp:<br>
    0 ICMP messages received<br>
    0 input ICMP message failed.<br>
    ICMP input histogram:<br>
    3 ICMP messages sent<br>
    0 ICMP messages failed<br>
    ICMP output histogram:<br>
        destination unreachable: 3<br>
<br>
<br>
### iostat — 输入/输出统计





## ps
选项<br>
-a：显示所有终端机下执行的程序，除了阶段作业领导者之外。<br>
a：显示现行终端机下的所有程序，包括其他用户的程序。<br>
-A：显示所有程序。<br>
-c：显示CLS和PRI栏位。<br>
c：列出程序时，显示每个程序真正的指令名称，而不包含路径，选项或常驻服务的标示。<br>
-C<指令名称>：指定执行指令的名称，并列出该指令的程序的状况。<br>
-d：显示所有程序，但不包括阶段作业领导者的程序。<br>
-e：此选项的效果和指定"A"选项相同。<br>
e：列出程序时，显示每个程序所使用的环境变量。<br>
-f：显示UID,PPIP,C与STIME栏位。<br>
f：用ASCII字符显示树状结构，表达程序间的相互关系。<br>
-g<群组名称>：此选项的效果和指定"-G"选项相同，当亦能使用阶段作业领导者的名称来指定。<br>
g：显示现行终端机下的所有程序，包括群组领导者的程序。<br>
-G<群组识别码>：列出属于该群组的程序的状况，也可使用群组名称来指定。<br>
h：不显示标题列。<br>
-H：显示树状结构，表示程序间的相互关系。<br>
-j或j：采用工作控制的格式显示程序状况。<br>
-l或l：采用详细的格式来显示程序状况。<br>
L：列出栏位的相关信息。<br>
-m或m：显示所有的执行绪。<br>
n：以数字来表示USER和WCHAN栏位。<br>
-N：显示所有的程序，除了执行ps指令终端机下的程序之外。<br>
-p<程序识别码>：指定程序识别码，并列出该程序的状况。<br>
p<程序识别码>：此选项的效果和指定"-p"选项相同，只在列表格式方面稍有差异。<br>
r：只列出现行终端机正在执行中的程序。<br>
-s<阶段作业>：指定阶段作业的程序识别码，并列出隶属该阶段作业的程序的状况。<br>
s：采用程序信号的格式显示程序状况。<br>
S：列出程序时，包括已中断的子程序资料。<br>
-t<终端机编号>：指定终端机编号，并列出属于该终端机的程序的状况。<br>
t<终端机编号>：此选项的效果和指定"-t"选项相同，只在列表格式方面稍有差异。<br>
-T：显示现行终端机下的所有程序。<br>
-u<用户识别码>：此选项的效果和指定"-U"选项相同。<br>
u：以用户为主的格式来显示程序状况。<br>
-U<用户识别码>：列出属于该用户的程序的状况，也可使用用户名称来指定。<br>
U<用户名称>：列出属于该用户的程序的状况。<br>
v：采用虚拟内存的格式显示程序状况。<br>
-V或V：显示版本信息。<br>
-w或w：采用宽阔的格式来显示程序状况。　<br>
x：显示所有程序，不以终端机来区分。<br>
X：采用旧式的Linux i386登陆格式显示程序状况。<br>
-y：配合选项"-l"使用时，不显示F(flag)栏位，并以RSS栏位取代ADDR栏位　。<br>
-<程序识别码>：此选项的效果和指定"p"选项相同。<br>
--cols<每列字符数>：设置每列的最大字符数。<br>
--columns<每列字符数>：此选项的效果和指定"--cols"选项相同。<br>
--cumulative：此选项的效果和指定"S"选项相同。<br>
--deselect：此选项的效果和指定"-N"选项相同。<br>
--forest：此选项的效果和指定"f"选项相同。<br>
--headers：重复显示标题列。<br>
--help：在线帮助。<br>
--info：显示排错信息。<br>
--lines<显示列数>：设置显示画面的列数。<br>
--no-headers：此选项的效果和指定"h"选项相同，只在列表格式方面稍有差异。<br>
--group<群组名称>：此选项的效果和指定"-G"选项相同。<br>
--Group<群组识别码>：此选项的效果和指定"-G"选项相同。<br>
--pid<程序识别码>：此选项的效果和指定"-p"选项相同。<br>
--rows<显示列数>：此选项的效果和指定"--lines"选项相同。<br>
--sid<阶段作业>：此选项的效果和指定"-s"选项相同。<br>
--tty<终端机编号>：此选项的效果和指定"-t"选项相同。<br>
--user<用户名称>：此选项的效果和指定"-U"选项相同。<br>
--User<用户识别码>：此选项的效果和指定"-U"选项相同。<br>
--version：此选项的效果和指定"-V"选项相同。<br>
--widty<每列字符数>：此选项的效果和指定"-cols"选项相同。<br>
由于ps命令能够支持的系统类型相当的多，所以选项多的离谱！<br>













