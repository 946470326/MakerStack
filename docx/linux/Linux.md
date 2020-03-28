### 1. Linux启动过程

#### 1.1 内核引导

- 进行BOIS自检，按照内部指定的方式启动设备，操作系统接管硬件设备
- 读取`/boot`目录

#### 1.2 运行init

- 执行`init`进程。
  - `init`进程是所有的进程的起点，没有这个进程任何的进程都不可能启动。
- `init`进程会读取`/etc/inittab`配置文件，加载一个运行级别

```tex
Linux系统有7个运行级别(runlevel)：
运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动
运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆
运行级别2：多用户状态(没有NFS)
运行级别3：完全的多用户状态(有NFS)，登陆后进入控制台命令行模式
运行级别4：系统未使用，保留
运行级别5：X11控制台，登陆后进入图形GUI模式
运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动
```

常用的运行模式有3和5。

- 在`centOS7`下可以通过`CTRL+ALT+F1`切换到图形化窗口，也可以在命令行窗口中通过`init 5`切换
- 通过`CTRL+ALT+F6`切换到命令行窗口

#### 1.3 系统初始化

>在`./etc/init.d`的配置文件中存在这一行：`si::sysinit:/etc/rc.d/rc.sysinit`。这一行调用执行了`/etc/rc.d/rc.sysinit`。而`rc.sysinit`是一个`shell`脚本，他主要完成一些系统初始化的工作，这个脚本每一个运行级别运行的时候都要首先运行。他主要完成了激活交换分区，检查磁盘等

#### 1.4. 建立终端

- 打开6个终端，以便让用户登录系统

#### 1.5.登录系统

### 2. VIM编辑模式

```shell
一般模式：
    vim 文件名
    dd 删除光标所在行 d数字d删除光标所在以下的多少行
    yy 复制当前行 y数字y复制光标所在以下的多少行
    u 撤销上一步操作
    shift^ 回到当前行的行首
    shift$ 回到当前行的行尾
    1 shift g 回到文件首部
    n shift g 跳转到第n行
    shift g 跳转到文件末尾
    set nu 开启行号
    set nonu 关闭行号显示
编辑模式：
	i , 插入模式，插入的数据光标的前面、
	a , 插入模式，插入的数据在光标的后面、
	o , 插入模式，插入的数据在光标的下一行
指令模式：
	:
		q ， 退出文件
		w , 写入文件，相当于保存
		! , 强制执行指令
	/target 查找文本中和target相等的值，并且标识出来
		n 查询下一个
		shift n 查询上一个
	? 和 /相同 ，唯一的不同就是n 和shift n 的作用相反
```

注意： `Linux`操作系统所有的东西都是文件，没有文件后缀的区分。我们看到的文件后缀名字只是为了迎合`window`的习惯，增加个人的可读性

### 3. Linux用户和用户组

```shell
用户操作：
    useradd 用户名 # 增加一个用户
    useradd -g 用户组名字 用户名 # 将这个用户添加到这个用户组中
    passwd 用户名 # 设置该用户的密码
    userdel 用户名 # 删除一个用户
    	userdel -r 用户名 # 删除用户并且删除用户主目录
    usermod -g 用户组 用户名 # 修改这个用户名到哪个组
    su - 用户名 # 切换用户
    whoami / who am i #查看当前用户信息
    id 用户名 #查看当前用户是否存在
用户组操作：
	groupadd 用户组名 # 增加一个用户组
	groupdel 用户组名 # 删除一个用户组
	groupmod -n 新的名字 旧名字
```

- `Linux`中的用户信息存放于`/etc/passwd`文件下，每一行就是一个用户信息

```tex
    44	wangwu:x:1001:1001::/home/wangwu:/bin/bash
    45	lisi:x:1002:1001::/home/lisi:/bin/bash
    	用户名:密码:用户唯一标识:用户组唯一标识:注释性描述:用户文件目录:登录shell
```

**用户唯一标识：**0是`root`的标识；1~99系统保留，作为管理账户；普通用户从100开始

**用户组唯一标识：**它对应了`/etc/group`文件中的一条记录

**注释性描述：**记录用户的个人信息

**用户文件目录：**用户登录系统以后所处的目录

**登录Shell：**用户登录以后，要启动一个进程，负责将用户的操作传给内核，这个进程是用户登录到系统后运行的命令解释器或者某个特定的程序，即`Shell`。

- `Linux`中的密码信息存放于`/etc/shadow`文件下

```tex
wangwu:$6$WXky4K1b$rbdtIdxtESyR3DYl8zZnB4lv6KArsIu.aGx1FuZLKneM3o1nZMtWPM.WVYWcsTMAT8G7PAyHjB4.TBUGv6YoG.:18322:0:99999:7:::

lisi:$6$XEOvXDHs$4aXlsEh5eTaIukGa9QSSWaC5A5jPFZ2QqyPo.KtccD2CXvcpi6a0NUggmurqCK9b0nCdCLh3/9AaVuXROQL071:18322:0:99999:7:::
登录名:密码:最后一次的修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间
```

**最后一次修改时间：**表示的是某一个时刻起，到用户最后一次修改口令时的天数

**最小时间间隔：**两次修改口令之间所需的最小天数

**最大时间间隔：**口令保持有效的最大天数

**警告时间：**系统从刚开始警告用户到用户密码正式失效之间的天数

**不活动时间：**表示用户没有登录活动，单账号仍能保存的有效天数

**失效天数：**当过了这么多天以后，该账号就是一个不合法的账号了

- `Linux`中的用户组信息保存在`/etc/group`文件下

```shell
dev:x:1001:
组名:密码:组标识号:组内用户列表
```

### 4. 时间日期类

- `date` 通过`man`命令来具体使用
- `cal`

### 5. 文件目录类

```shell
pwd # 显示当前目录
ls # 显示当前目录中的所有文件和目录内容
	-l 将文件目录以列表显示
	-a 将隐藏文件也显示出来，包括.和..
	-d 仅仅列出目录本身
mkdir # 创建一个文件夹
	-p # 创建多层目录
rmdir # 删除一个空的文件夹
touch # 创建一个文件
vim # 编辑或者创建文件，如果文件不存在直接编辑以后写入即可
cd # 切换文件夹
	. #当前文件夹
	.. # 上一级文件夹
	~ # 回到用户目录
	- # 回到上一次所在的目录
cp # 复制文件 
	cp 需要进行复制的文件 进行复制的文件路径(可以指定文件的名字，如果不指定使用初始名字)
rm # 删除文件或者文件夹
	-r # 直接删除文件，会出现提示
	-f # 递归删除文件，不会提示
	-rf 
mv #移动文件也可以修改文件的名称
cat #由第一行开始显示文件
	-A 显示所有的隐藏内容，包括换行符
	-b 显示行号，空白行没有号
	-v 显示所有的隐藏内容
tac
more #一页一页的显示文件内容
	显示以后可以执行的指令
		space 下一页
		enter 下一行
		b 上一页
		q 离开
less # 和more一样，功能更多
	先是以后可以执行的指令
		pagedown 下一页
		pageup 上一页
		/ 查找字符串，和vim中的用法相同
		?
		n 下一个需要查找的字符串
		N 上一个需要查询的字符串
		q 离开这个less程序
head # 显示前几行文件内容
tail # 显示文件最后几行的内容
history # 显示所有显示过的指令内容
	将使用过的指令的内容写入到一个文件中
		覆盖写：
			history>文件名
		追加写：
			history>>文件名
```

### 6. 网络配置类

- `ifconfig`

### 7. 磁盘分区类

```shell
fdisk -l # 查看磁盘分区信息
设备 Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048      616447      307200   83  Linux
/dev/sda2          616448    36274175    17828864   8e  Linux LVM

sda1
s----> 表示scsi硬盘
d----> 表示disk
a----> 表示第一块盘。如果有第二块磁盘就会显示b
数字---> 前四个分区使用1~4显示，他们是主分区或者扩展分区，从第5个开始才是逻辑分区
```

- 主分区

相当于`window`的`C盘`存放了系统启动需要的文件

- 扩展分区

除去主分区以外的所有分区都是扩展分区

- 逻辑分区

在扩展分区中分配多个逻辑分区

```shell
df / # 查看当前文件夹或者文件挂载到哪一个分区
文件系统                   1K-块    已用     可用 已用% 挂载点
/dev/mapper/centos-root 15718400 4542208 11176192   29% /

```

- 挂载

`Linux`通过将固定的目录或者文件通过挂载的方式挂载到一个磁盘分区中，以此将目录与磁盘相联系

```shell
目录-->挂载-->分区

mount 参数名称 设备名称 目录 # 将目录挂载到指定的分区上
umount 目录 # 将目录与分区接触挂载
```

### 8. 搜索查找类

```shell
find 搜索路径 参数 # 查找指定的文件或者目录
find /home -name 'lisi' 

grep 参数 查找内容 源文件在文件中搜索字符串-匹配的行输出
grep -n 'this' a.txt 

参数：
    －c：只输出匹配行的计数。
    －I：不区分大小写(只适用于单字符)。
    －h：查询多文件时不显示文件名。
    －l：查询多文件时只输出包含匹配字符的文件名。
    －n：显示匹配行及 行号。
    －s：不显示不存在或无匹配文本的错误信息。
    －v：显示不包含匹配文本的所有行。
```

### 9.进程线程类

```shell
 ps # 查看正在运行的进程信息
 PID TTY          TIME CMD
  6816 pts/0    00:00:00 bash
  9471 pts/0    00:00:00 ps
PID 进程识别号
TTY 终端机号
TIME 此进程所消CPU时间
CMD 正在执行的命令或者进程名字

ps -a 显示当前终端所有进程的信息
ps -u 以用户的格式显示进程信息
ps -x 显示后台进程运行的参数

通常使用方式：
	ps -aux
	ps -ef 
```

```shell
netstat
 显示整个系统的网络状态，例如目前的链接，数据包传输等
 使用方式：
 	netstat -anp 
 	结合grep使用
```

```shell
top
显示CPU的使用率 
kill 进程ID 9 直接杀进程
```

### 10. 压缩和解压缩

- `.gz`。压缩以后源文件不在了

```shell
gzip 文件名 #压缩文件
gunzip 文件名 # 解压文件
```

- `.tar.gz`。压缩以后源文件存在。

```shell
tar -zcvf 文件名 # 压缩文件 
tar -zxvf # 解压文件
```

- `zip`。压缩后源文件存在

```shell
zip 文件名
unzip 文件名
```

### 11. 文件权限类

```shell
什么是文件权限
	执行ls -l 命令
	-rw-r--r--. 1 root root     0 3月   2 15:21 1.txt #代表一个文件
	drwxr-xr-x. 2 root root    21 3月   1 18:29 my #代表一个文件夹
	文件类型及权限信息 链接数   文件所属 文件所属组 文件大小 最后一次修改时间 文件名称
```

```shell
文件类型及权限信息
drwxr-xr-x和-rw-r--r--
第一位代表文件类型，之后的每三位代表文件权限
d代表文件夹 - 代表文件
rwx  
	文件：r 代表可读  w 代表可写 x 代表可以执行
	文件夹：r 代表可读 w 代表可写 x 代表可以进入这个文件夹
第一个rwx代表的是用户自己的权限
第二个rwx代表的时候用户所在组的权限
第三个rwx代表的是所有其他用户的权限
```

```shell
chmod 更改当前文件或者目录的权限信息
使用方式	
	rwx 三种权限要么有要么没有。所以可以理解为二进制的0和1,所以只需要指定每一种用户权限二进制所代表的十进制为多少即可
	例如：
		rwx 代表的二进制是111 即十进制为7 
		r_x 代表的二进制是101 即十进制为5
		r__ 代表的二进制位100 即十进制为4
	现在需要：修改a.txt文件的用户权限为rwx 用户组权限为rx_ ， 其他用户权限为r__
	对应的指令为：
	chmod 764 a.txt
	这样其权限信息就修改成功了

#只能root修改
    修改文件所在的用户组
    chgrp 组名 文件名
    修改文件所有者
    chown 用户名 文件名

# 文件和文件夹存在一种默认权限
    通过命令 umask -S 查看文件和文件夹的默认权限
	u=rwx,g=rx,o=rx # 默认权限
```

>默认规则：
>
>是666 减去 022等于644，
>   十进制的6等于二进制的110，所以第一组就是rw-
>   十进制的4等于二进制的100,  所以第二组就是r--
>   十进制的4等于二进制的100,  所以第三组就是r--
> 目录是777 减去 022等于755，

### 12. Linux文件目录树

![](image/Linux目录结构.png)

- `/`是`Linux`目录的起始
- `/bin`存放了各种`Linux的运行命令`
- `/etc`存放了系统的配置文件
- `/opt`安装非系统应用软件提供的目录
- `/home`存放用户的个人信息
- `/usr`存放用户的应用程序和文件，类似于window的program
- `/mnt`用来让系统挂载其他的文件系统，可以挂载光驱
- `/media` Linux识别各种设备，会将这些设备挂载到这个目录上
- `/sbin`系统管理员使用的系统管理程序
- `/boot`系统加载时候读取的文件
- `/sys`安装了`Linux`出现的新的文件系统
- `/dev`存放了`Linux`的外部设备
- `/var`存放经常需要改动的文件，比如日志文件
- `/root`root用户的主目录
- `/temp`临时文件
- `/lib`存放各种动态链接库

### 13. rpm

>类似于node提供的npm 是一种软件包管理工具

```shell
rpm -qa 查询安装的所有的rpm软件包
	结合more 和 grep使用
rpm -q 查询软件包是否安装
rpm -qi 查询软件包信息

#卸载软件
rpm -e rpm软件包名称

#安装软件
rpm -ivh rpm软件包名
```

### 14. Shell

>一种C语言编写的程序，用户和Linux沟通的桥梁。就是我们在命令行中输入的命令就是Shell语言

### 15.内存类

- `cat /proc/meminfo` 查看内存信息

```shell
MemTotal:        1863104 kB 总内存
MemFree:           81320 kB 空闲内存
MemAvailable:     631160 kB 可用内存
Buffers:             220 kB 缓冲区内存
Cached:           760552 kB 缓存内存
SwapCached:            0 kB
Active:           971044 kB
Inactive:         476132 kB
Active(anon):     638052 kB
Inactive(anon):   165052 kB
Active(file):     332992 kB
Inactive(file):   311080 kB

```

- `free`显示系统内存的使用情况，结合-h使用

```shell
total        used        free      shared  buff/cache   available
Mem:           1.8G        917M         91M        109M        810M        628M
Swap:          2.0G        264K        2.0G
```

- `top`实时显示各个进程资源占用情况

![](image/top.png)

```shell
PR 进程优先级
NI nice值。负值代表高优先级，正值代表低优先级
VIRT 虚拟内存
RES 实际使用的内存
SHR 共享内存大小 单位kb
S 进程状态
%cpu 上次更新到现在的CPU时间占用比
%MEM 进程使用的物理内存百分比
TIME 使用CPU的时间

```

- `ps -aux|sort-k4nr-head-10`查看内存中使用最多的前10个进程

### 16.系统安全设定

>前言：早晨起来，看到腾讯云亲切的提醒，您的云服务器疑似被黑客入侵。请到控制台查看具体的信息。上服务器一看，XX，从昨天下午15:00到今天早晨8:00一直暴力访问，最终还登录成功了……没办法，笔者设置的密码过于简单。不得不提一句，笔者遇见的这个比价傻，登录上去以后，记录都不清除，估计是挂在那破解，第二天成功以后，此人还没醒，居然把IP地址留下了。哈哈哈

#### 16.1 开启用户登录次数限制

```shell
vim /etc/pam.d/system-auth
vim /etc/pam.d/ login
vim /etc/pam.d/sshd 
```

上面三个文件中的第二行添加这行配置

```shell
auth required pam_tally2.so onerr=fail deny=3 unlock_time=30 even_deny_root root_unlock_time=100
```

- pam_tally2.so 一个安全模块
- deny 输入错误的次数
- unlock_time 到达次数上限以后锁定账户30秒
- even_deny_root    root账户也是该次数，可以自己指定
- root_unlock_time  次数上限以后，锁定100秒

此时这个`pam_tally2`安全模块就设置成功了

当输入超过设置的次数以后就会提示该账户已经被锁定，输入正确的密码也不能登录

查看被锁定的账户

```shell
pam_tally2                             查看被锁定的用户

pam_tally2  --reset  -u  username      将被锁定的用户解锁 
```

#### 16.2 用户权限管理

最好可以有一个启动服务的账户，仅仅给予其执行的权限

密码！密码！密码

一定要复杂，不然暴力破解很简单。想想自己的密码，心中麻麻批。最后设置了一个18位的密码。再给我暴力破解。靠！

#### 16.3服务器被暴力破解怎么办

>最坏的情况：和笔者一样，被暴力破解了。

```shell
top # 查询CPU的执行情况，看看有没有什么不熟悉的应用执行，防止木马，有木马查看其名字，先杀在删

grep -o "Failed password" /var/log/secure | uniq -c  #查看被暴力攻击的次数

grep "Failed password" /var/log/secure |head -1 #获取第一条暴力攻击的时间
grep "Failed password" /var/log/secure |tail -1 #获取最后一条暴力攻击的时间
#到这里你就知道此人的攻击时间和IP了

# 这个是查询所有进行暴力的IP。一串正则……脑壳痛
grep "Failed password" /var/log/secure|grep -E -o "(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)"|uniq -c | sort -nr 

# 查看所有被暴力的用户名
grep "Failed password" /var/log/secure|perl -e 'while($_=<>){ /for(.*?) from/; print "$1\n";}'|uniq -c|sort -nr
```

- 如果你发现他登录成功了

```shell
# 查询登录成功的用户，然后根据时间，看看使用哪一个账户登录的，这个也可以作为你看他是不是有木马的依据，以这个被入侵的用户为条件搜索
grep "Accepted " /var/log/secure | awk '{print $1,$2,$3,$9,$11}'

# 登录成功的IP
grep "Accepted " /var/log/secure | awk '{print $11}' | sort | uniq -c | sort -nr | more
```
