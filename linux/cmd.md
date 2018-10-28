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






















