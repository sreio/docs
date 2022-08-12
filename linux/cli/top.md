# top

> Linux top命令用于实时显示 process 的动态。 使用权限：所有使用者。


## 语法

> top [-] [d delay] [q] [c] [S] [s] [i] [n] [b]

* d : 改变显示的更新速度，或是在交谈式指令列( interactive command)按 s
* q : 没有任何延迟的显示速度，如果使用者是有 superuser 的权限，则 top 将会以最高的优先序执行
* c : 切换显示模式，共有两种模式，一是只显示执行档的名称，另一种是显示完整的路径与名称
* S : 累积模式，会将己完成或消失的子进程 ( dead child process ) 的 CPU time 累积起来
* s : 安全模式，将交谈式指令取消, 避免潜在的危机
* i : 不显示任何闲置 (idle) 或无用 (zombie) 的进程
* n : 更新的次数，完成后将会退出 top
* b : 批次档模式，搭配 "n" 参数一起使用，可以用来将 top 的结果输出到档案内

> top -p 139 #显示进程号为139的进程信息，CPU、内存占用率等

---

```shell
# top
top - 01:06:48 up  1:22,  1 user,  load average: 0.06, 0.60, 0.48
Tasks:  29 total,   1 running,  28 sleeping,   0 stopped,   0 zombie
Cpu(s):  0.3% us,  1.0% sy,  0.0% ni, 98.7% id,  0.0% wa,  0.0% hi,  0.0% si 0.0 st
Mem:    191272k total,   173656k used,    17616k free,    22052k buffers
Swap:   192772k total,        0k used,   192772k free,   123988k cached

   PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
  1379 root      16   0  7976 2456 1980 S  0.7  1.3   0:11.03 sshd
14704 root      16   0  2128  980  796 R  0.7  0.5   0:02.72 top
     1 root      16   0  1992  632  544 S  0.0  0.3   0:00.90 init
     2 root      34  19     0    0    0 S  0.0  0.0   0:00.00 ksoftirqd/0
     3 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 watchdog/0
```

## 参数解释

### 统计信息区

前五行是系统整体的统计信息。第一行是任务队列信息，同 uptime 命令的执行结果。其内容如下：

|实例值|解释|
|:---:|:---:|
01 :06 :48 |	当前时间
up 1:22	| 系统运行时间，格式为时:分
1 user	| 当前登录用户数
load average: 0.06, 0.60, 0.48 |	系统负载，即任务队列的平均长度。三个数值分别为 1分钟、5分钟、15分钟前到现在的平均值。

第二、三行为进程和CPU的信息。当有多个CPU时，这些内容可能会超过两行。内容如下：

|实例值|解释|
|:---:|:---:|
|Tasks: 29 total |	进程总数|
1 running |	正在运行的进程数
28 sleeping |	睡眠的进程数
0 stopped |	停止的进程数
0 zombie |	僵尸进程数
Cpu(s): 0.3% us |	用户空间占用CPU百分比
1.0% sy	 | 内核空间占用CPU百分比
0.0% ni	 | 用户进程空间内改变过优先级的进程占用CPU百分比
98.7% id |	空闲CPU百分比
0.0% wa	 |等待输入输出的CPU时间百分比
0.0% hi	 | 硬件中断
0.0% si	 | 软件中断
0.0% st | 被虚拟机偷走的cpu

最后两行为内存信息。内容如下：

|实例值|解释|
|:---:|:---:|
Mem: 191272k total |	物理内存总量
173656k used |	使用的物理内存总量
17616k free	| 空闲内存总量
22052k buffers |	用作内核缓存的内存量
Swap: 192772k total |	交换区总量
0k used	| 使用的交换区总量
192772k free |	空闲交换区总量
123988k cached |	缓冲的交换区总量。 <br> 内存中的内容被换出到交换区，而后又被换入到内存，<br>但使用过的交换区尚未被覆盖，<br>该数值即为这些内容已存在于内存中的交换区的大小。 <br> 相应的内存再次被换出时可不必再对交换区写入。


### 进程信息区

统计信息区域的下方显示了各个进程的详细信息。首先来认识一下各列的含义。

|序号 |	列名 | 含义|
|:---:|:---:|:---:|
a |	PID | 进程id
b |	PPID |	父进程id
c |	RUSER |	Real user name
d |	UID	 |进程所有者的用户id
e |	USER |	进程所有者的用户名
f |	GROUP |	进程所有者的组名
g |	TTY	|启动进程的终端名。不是从终端启动的进程则显示为 ?
h |	PR	|优先级
i |	NI	|nice值。负值表示高优先级，正值表示低优先级
j |	P	|最后使用的CPU，仅在多CPU环境下有意义
k |	%CPU|	上次更新到现在的CPU时间占用百分比
l |	TIME|	进程使用的CPU时间总计，单位秒
m |	TIME+|	进程使用的CPU时间总计，单位1/100秒
n |	%MEM|	进程使用的物理内存百分比
o |	VIRT|	进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES
p |	SWAP|	进程使用的虚拟内存中，被换出的大小，单位kb。
q |	RES	|进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA
r |	CODE|	可执行代码占用的物理内存大小，单位kb
s |	DATA|	可执行代码以外的部分(数据段+栈)占用的物理内存大小，单位kb
t |	SHR	|共享内存大小，单位kb
u |	nFLT|	页面错误次数
v |	nDRT|	最后一次写入到现在，被修改过的页面数。
w |	S	|进程状态。<br> D=不可中断的睡眠状态 <br> R=运行 <br> S=睡眠 <br> T=跟踪/停止 <br> Z=僵尸进程
x |	COMMAND	|命令名/命令行
y |	WCHAN	|若该进程在睡眠，则显示睡眠中的系统函数名
z |	Flags	|任务标志，参考 sched.h
