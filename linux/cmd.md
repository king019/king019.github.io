[20个命令行工具监控 Linux 系统性能](https://linuxstory.org/command-line-tools-to-monitor-linux-performance/)
[linux常用的监控命令](https://www.cnblogs.com/huangxm/p/6278615.html)
[25个Linux性能监控工具](https://www.cnblogs.com/peter316/p/6287212.html)
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