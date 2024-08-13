# 一.linux基础
Linux操作系统的内核，叫做<span style="background:#affad1">kernel</span>，如果希望找到它的最新源代码，我们介绍过了网站：kernel.org。kernel提供操作系统的最基本的功能，驱动硬件、初始化内存、CPU等。
什么是<span style="background:#affad1">shell</span>
Shell是系统的用户界面，提供了用户与内核进行交互操作的一种接口(命令解释器)
Shell接收用户输入的命令并把它送入内核去执行
Shell起着协调用户与系统的一致性和在用户与系统之间进行交互的作用
![[Excalidraw/Drawing 2024-07-10 19.49.48.excalidraw.md#^group=J6ZFZ8fHNER5oMS4pwtCd]]
## 0. 快捷键 
ctrl+alt + F1 图形 F2-F6文本
![[Pasted image 20240709200450.png|500]]
`CTRL+C`：发送 `SIGINT` 信号，通常用于中断并终止一个进程。
`CTRL+Z`：发送 `SIGTSTP` 信号，用于暂停（挂起）一个进程，但不终止它。

**查看别名**
```bash

alias                      ：查看别名
alias la = 'ls -al'    ：设置临时别名
unalias la              :   取消别名
永久设置别名个人： vim ~/.bashrc
永久设置别名全局： vim /etc/bashrc
```
**info在线帮助**
```bash
info ls 
pinfo ls
```

**man手册**
```bash
man 1 用户命令 *  man 2 系统调用 
man 3 库调用        man 4 特殊文件 
man 5 配置文件 *  man 6 游戏 
man 7 杂项            man 8 系统命令 *
man -k pass     :   模糊查询
```
<span style="background:#affad1">man -f passwd</span>： 显示passwd在那些章节有描述
![[Pasted image 20240710190538.png]]
```bash
mandb             :   刷新man手册数据库
```
**[date](https://wangchujiang.com/linux-command/c/date.html)**
date +'%Y/%m/%d   %T  %p'
![[Pasted image 20240710191244.png]]
**[type](https://wangchujiang.com/linux-command/c/type.html)**
![[Pasted image 20240710192920.png]]
别名>外部>内部
按照优先级和执行顺序，当输入一个命令时，Shell首先检查它是否是一个别名，如果是，则展开别名；然后检查是否是一个内置命令，如果是，则直接执行；最后，如果既不是别名也不是内置命令，Shell将在`PATH`环境变量指定的目录中查找该命令对应的可执行文件，即外部命令。
**which**
查找并显示给定命令的绝对路径
![[Pasted image 20240710193849.png]]
**history**
查看历史命令，默认可以存储1000条历史命令
调用之前的命令
！+历史命令首字母/命令 
！+历史命令编号

## 1. 文件管理
ls
```bash
-a -A -l -d -h -t -r -S -R
```
![[Pasted image 20240710091046.png]]
第一段: 文件类型 
```bash title:文件类型
文件类型:(7种) 
- 普通文件 file 
d 目录文件 directory 
c 字符设备文件 character 
b 块设备文件 block 
s 套接字文件 socket 
p 管道文件 pipe 
l 符号链接文件(软链接) symbolic
```
 块设备和字符设备的区别
 ![[Pasted image 20240813093728.png|450]]
 第二段: 基本权限 
 第三段: 是否设置了ACL权限 
 第四段: 硬链接数 
 第五段: 拥有者 
 第六段: 所属组 
 第七段: 大小(字节),b4it8=1Byte 1024 1K M G
 第八段: 最后一次修改时间 
 第九段: 文件名

cd
```bash
.        当前目录
..       上级目录
cd .   刷新目录
cd -  上次工作目录
```
linux的HFS标准
Filesystem Hierarchy Standard，简称<span style="background:#affad1">FHS</span> -文件系统层次结构标准
"/" 跟目录下每个目录的作用 
<span style="background:#affad1">bin</span>        用户可执行目录(命令 root 和 普通) 
<span style="background:#affad1">sbin </span>     系统可执行目录(命令 root) 
lib         库文件目录(32位) 
lib64     库文件目录(64位)
<span style="background:#affad1">dev</span>       设备文件目录
usr        应用程序目录
var        服务器数据目录(数据 日志) 
srv        服务器数据目录 
<span style="background:#affad1">etc</span>        配置文件目录 
tmp      临时文件目录 
boot     服务器启动目录(内核和启动文件) 
media   媒介目录(u盘,cdrom) 
mnt      其他挂载点 opt 第三方应用程序目录 
proc     伪文件系统(内核参数,进程信息,硬件信息) 
sys       伪文件系统(c 配置文件目录 内核参数,进程信息,硬件信息) 
run       进程锁目录 
<span style="background:#affad1">root</span>      root管理员家目录
<span style="background:#affad1">home</span>   普通用户家目录 

pwd
绝对路径: 从/开始的路径 
相对路径: 从当前目录开始路径或非根开头的路径
cat 
```bash
-n 显示所有行的行号(包括空行)
-b 显示有效行(不包括空行）
```
head
tail
```bash
默认前/后十行
-n number  
实时监控并自定义显示行数
tail -n 20 -f filename
```
less
more
touch
```bash
touch {a,b,c}{1..3}.txt
touch /opt/file7 /tmp/file8
```
mkdir
```bash
mkdir /opt/cc.txt /etc/tt.txt
mkdir {x,y,z}{1..5}
mkdir -pv /opt/x/y/
-v:显示提示信息
```
cp
```bash
-r:递归复制
-p:保留权限
-a:=rp
```
mv
```bash
移动/改名/重命名
```
rm
```bash
-f 强制删除 
-r 递归删除目录时使用
```

file
用来探测给定文件的类型。file命令对文件的检查分为文件系统、魔法幻数检查和语言检查3个过程。

## 2. vim文本编辑器

不仅可以编辑已存在的文件，还可以创建新文件
三种模式：命令模式，输入模式，末行模式
命令模式：用于光标移动复制删除
输入模式：编辑文本
末行模式：保存退出，设置环境显示行号制表符，设置缩进等
### 2.1 命令模式：
#### (1)进入输入模式
<span style="background:#affad1">a </span>    当前字符后输入
A     当前行行尾输入
<span style="background:#affad1">i</span>      当前字符前输入
I      当前行行首输入
<span style="background:#affad1">o</span>     当前行下一行输入
O    当前行上一行输入 
s     删除当前字符后输入 
S    删除当前行后输入
#### (2)光标移动
键盘上的↑↓←→ 上下左右箭头 移动光标，<span style="background:#affad1">hjkl 左下上右</span> nh:向左移动n个字符
0               将光标定位到行首
$               将光标定位到行尾
gg             将光标定位到文章首部
shift+g/G  将光标定位到文章尾部
nG /10G    将光标定位到第10行
#### (3)复制粘贴
y                复制
y^              复制光标所在位置到行首
y$              复制光标所在位置到行尾
yw             复制一个单词
yy              复制一行
<span style="background:#affad1">nyy </span>           复制光标一下的多行
p/P            黏贴(<span style="background:#affad1">p</span>当前行下一行) (<span style="background:#affad1">P</span>当前行的上一行)
#### (4)删除撤销
d               删除
dd            剪切/删除(p P)
ndd          剪切/删除n行
d^             删除当前字符到行首(不包含当前字符) 
d$/D        删除当前字符到行尾
<span style="background:#affad1">dgg </span>         从当前行删除到首行(包含当前行) 
<span style="background:#affad1">dG   </span>           删除当前行到尾行(不包含当前行)

<span style="background:#affad1">u    </span>             撤销上一步的操作
.               重复之前的操作
ZZ            保存并退出
注意：大写字母都可以用shift+小写字母代替
### 2.2 末行模式
#### (1)常用指令
shfit + ；                   进入末行模式
:w /tmp/cc.txt           另存为
:1,3w /tmp/new.txt   将1到3行另存为
:x                                如果未修改直接退出;如已修改保存并退出
:r /etc/passwd           将/etc/passwd文件内容读取到当权文件光标处
:! command               临时输入linux命令
:e!                               重新打开当前文件
:e /root/aa.txt            打开另外一个文件
:X                               加密文件 
:X                               将密码设置为空，即可取消密码
#### (2)环境设置
:set nu/nonu                                显示行号
:set list/nolist                               显示空格或者制表符
:set tabstop=16                           文件中所有制表符都设置为16
:set softtabstop=16                     只更改设置后的制表符长度
:set autoindent/noautoindent    自动缩进
set ignorecase smartcase           搜索忽略大小写
:noh                                              取消高亮

#### (3)vim配置文件
vim ~/.vimrc                                 个人永久开启行号
set nu
vim /etc/vimrc                             所有人永久添加行号
set nu 

### 2.3 可视化模式
按 v 进入可视化模式
按 V 进入行可视化模式，选择整行
按 Ctrl+V 进入可视块模式
y：复制选中的文本到 Vim 的寄存器
d：删除选中的文本
p：粘贴之前复制的文本
u：将选中的文本转换为小写
U：将选中的文本转换为大写

### 2.4 查找替换
/关键字    从上往下搜索     n下一个 N上一个
?关键字   从下往上搜索

| 名称  | 作用    |
| --- | ----- |
| s   | 行     |
| g   | 到该行结尾 |
| %   | 全文    |
| c   | 交互式   |
末行模式做替换  /old/new/
:%s/ab/xx/gc      交互式的把文本中所有行中的ab换成xx 
3,5s/ab/xx/gc     交互式的把文本中3到5行中的ab换成xx

## 3. 用户管理
Linux系统中，用户被创建时，系统都会分配一个用户ID号，简称UID。同时也会给该用户创建一个用户组，简称GID。系统通过ID号来识别用户。

| 分类    | UID     | 作用          |
| ----- | ------- | ----------- |
| 超级管理员 | 0       | 系统管理/基本所有权限 |
| 普通账号  | 1000以上  | 系统管理/使用权限   |
| 系统账号  | 201-999 | 不能登录/用于启动应用 |

| 分类     | UID     | 作用             |
| ------ | ------- | -------------- |
| 超级管理员组 | 0       | 让其他用户加入组，使用组权限 |
| 普通账号组  | 1000以上  | 让其他用户加入组，使用组权限 |
| 系统账号组  | 201-999 | 让其他用户加入组，使用组权限 |
用户组的作用，将相同兴趣用户加入组内按统一权限管理。linux系统中，用户可以加入组，组不能再加入组。
### 3.1 用户管理
增删改查
useradd，usermod，userdel，id，passwd
#### (1)useradd 添加用户
useradd 选项 选项参数 用户名

| 选项  | 作用      |                                                                                                                                                                                                                        |
| --- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -u  | uid     | id号会按当前系统最大用户id号顺延+1                                                                                                                                                                                                   |
| -g  | gid     |                                                                                                                                                                                                                        |
| -c  | comment | 用户描述信息（非必要）                                                                                                                                                                                                            |
| -d  | home    | 指定家目录位置如果，需要使用不存在的目录                                                                                                                                                                                                   |
| -s  | shell环境 | 系统默认值/bin/bash，<span style="background:#affad1">/bin/bash</span>可登录的用户，可以和系统交互；<span style="background:#affad1">/sbin/nologin</span>非交互式shell，不能登录系统，只能加载应用；<span style="background:#affad1">/bin/false</span>,不能和系统交互 |
| -G  | 附加组     | 附加组，也称为从组，将用户加入其他组中                                                                                                                                                                                                    |
| -o  | 重复uid   | 重复使用UID时使用（非常不推荐）                                                                                                                                                                                                      |
```bash
useradd -g 4000 group2  
```
![[Pasted image 20240713102246.png]]
创建一个名为 group2 的新用户，并将这个用户的<span style="background:#affad1">主要组</span>设置为GID为4000的组。但是这个组不存在不能创建。
```bash
useradd -G root tom  
```
将tom加入到root组内
![[Pasted image 20240713102943.png]]
主要组g和附加组G区别
-g gid 主要群组，修改用户gid 
-G groups 附加组，将用户加入到哪个附加组内
```bash
useradd -g root tom 
```
![[Pasted image 20240713103546.png]]
意义为指定tom的主要群组为root，该操作影响了/etc/password中的gid部分，之后使用tom创建文件时所有者为tom，所属组为root 
```bash
useradd -G root tom 
```
![[Pasted image 20240713103645.png]]
意义为给tom指定附加组为root，也就是将tom加入了root群组中，当tom访问所属组为root的文件时可以使用root组权限
```bash
useradd -u 2005 -g 2000 -G 0 -c 'test user' -d /mnt/abc6 -s /bin/bash wwh
useradd -o -u 0 test1    # 重复使用UID时使用
```

#### (2)usermod 修改用户信息
usermod 选项 选项参数 用户名
-a 保留原始附加组，加入额外的组
usermod -G wheel newuser
![[Pasted image 20240713104600.png|550]]
usermod -aG root newuser
![[Pasted image 20240713104620.png|550]]
-u  -g  -c  -d  -s  同用户添加
修改家目录的方法
方法1：
```bash
usermod -d /tmp/jack jack 
mv /home/jack/ /tmp/
```
方法2：
```bash
usermod -m -d /home/jack jack
```
-m：这个选项表示移动用户的主目录到新的路径。如果新家目录不存在，usermod 会尝试创建它。

#### (3)userdel 删除用户
创建用户时(useradd user)会在系统中产生相关文件：
/home/user 用户家目录
/var/spool/mail/user 邮箱文件
/etc/passwd中写入用户信息
/etc/group中创建对应名称的用户组

userdel user1 只删除/etc/passwd用户信息和/etc/group中组信息，而不删除家目录及邮箱
userdel -r       删除用户同时删除家目录及邮箱信息

#### (4)用户配置文件
vim /etc/passwd
<span style="background:rgba(163, 67, 31, 0.2)">wwh</span>:<span style="background:rgba(240, 200, 0, 0.2)">x</span>:<span style="background:#affad1">1001</span>:<span style="background:#40a9ff">0</span>:<span style="background:#ff4d4f"> </span>:<span style="background:rgba(3, 135, 102, 0.2)">/home/wwh</span>:<span style="background:#9254de">/bin/bash</span>
第一段:用户名
第二段:密码占位符,现已转存/etc/shadow如果删除x，用户则可以直接免密登录
第三段:uid
第四段:gid
第五段:描述
第六段:家目录
第七段:shell 
#### (5)passwd设置用户密码
passwd 选项 用户名
passwd  直接回车  修改当前用户的密码
passwd  user          给user创建密码，只有root可以给别人设置密码，普通用                                 户只能改自己的密码
方法一:交互式
![[Pasted image 20240713154914.png]]
方式二:通过标准输入的方式
```bash
echo 123456 |  passwd --stdin root
```

#### (6)切换用户
su    用户名
su - 用户名  <span style="background:#affad1">- 会切换环境变量，推荐这个方法</span>
#### (7)验证用户是否存在
id username

### 3.2 组管理
#### (1)添加组
groupadd  test
groupadd -g 3000 test
#### (2)修改组
-g 修改组ID 
-n 修改组名
```bash
groupmod -g 3000 test
groupmod -n newtest test   # 修改test组名为newtest
```
#### (3)删除组
groupdel uplooking
#### (4)gpasswd
gpasswd命令可以将用户添加到组或将用户从组中删除(<span style="background:#affad1">附加组</span>)
gpasswd 选项 组名
-a 将用户添加到组 
-d 将用户从组中删除
gpasswd -a cl mysql  将cl加入mysql组
![[Pasted image 20240713161105.png|500]]
 gpasswd -d cl root   将cl从root组删除
 ![[Pasted image 20240713161235.png|500]]
#### (5)组配置文件
 vim /etc/group
 <span style="background:#affad1">cl</span>:<span style="background:rgba(240, 200, 0, 0.2)">x</span>:<span style="background:#d3f8b6">1002</span>:<span style="background:#40a9ff">  </span>
 第一段: 组名 
 第二段: 组密码占位符号 
 第三段: gid 
 第四段: 用户列表

### 3.3 密码管理
#### (1)密码配置文件
vim /etc/shadow
<span style="background:#affad1">user1</span>:<span style="background:rgba(240, 200, 0, 0.2)">!!</span>:<span style="background:rgba(136, 49, 204, 0.2)">19917</span>:<span style="background:#ff4d4f">0</span>:<span style="background:#40a9ff">99999</span>:<span style="background:rgba(240, 107, 5, 0.2)">7</span>:<span style="background:#affad1">  </span>:<span style="background:#fff88f">  </span>:<span style="background:rgba(173, 239, 239, 0.55)">  </span>
第一列：用户名
第二列：密码      密码字段为空没有密码；密码字段不为空 加密后的字符串，表示密码已经设置  ； !! 锁定密码状态   ； * 永久不能登录系统
第三列: 密码的最后一次修改时间；从1970年1月1日至今的天数
第四列: 密码的最小时间；密码最后一次修改后多少天内不能再重复修改
第五列: 密码的最大时间(密码有效期)，最后一次修改多久后必须变更密码
第六列: 密码过期前警告时间
第七列: 密码过期后帐号宽限时间
第八列: 帐号有效期（账号失效后，无论密码是否过期都不能使用）
第九列: 保留列
#### (2)修改密码
方法一:交互式
![[Pasted image 20240713154914.png]]
方式二:通过标准输入的方式
```bash
echo 123456 |  passwd --stdin root
```

#### (3)锁定用户
usermod -L robin：这个命令会<span style="background:#affad1">锁定</span>用户 `robin` 的账户，使其无法登录系统。
usermod -U robin：这个命令用于<span style="background:#affad1">解锁</span>用户 `robin` 的账户，如果之前该账户被锁定的话
#### (4)锁定密码
passwd -l robin：锁定用户 `robin` 的密码。这将禁用该用户的密码认证，使其无法通过密码登录系统
passwd -S robin：显示用户 `robin` 的密码状态。这个命令会输出关于用户密码的详细信息，包括密码是否已锁定、最后一次更改密码的日期、密码到期日等
passwd -u robin：解锁用户 `robin` 的密码

#### (5)配置文件
/etc/login.defs (uid,gid范围)
![[Pasted image 20240713183319.png]]
/etc/default/useradd
![[Pasted image 20240713183407.png]]
### 3.4 手动管理账号
#### (1)添加用户信息
```bash
vim /etc/passwd 
rose:x:3007:3007::/home/rose:/bin/bash
```
#### (2)添加用户组
```bash
vim /etc/group 
rose:x:3007
```
#### (3)创建用户家目录
```bash
cp -r /etc/skel/ /home/rose 
chmod 700 /home/rose                 修改文件权限 
chown -R rose:rose /home/rose   修改文件所有者和所属组
```
#### (4)创建用户邮箱
```bash
cd /var/spool/mail/ 
cp -p class1 rose class1
chown rose:mail rose                      修改用户所有者为rose/所属组为mail
```
#### (5)添加用户密码登录测试
```bash
passwd rose
su - rose
```
### 3.5 重置root密码
![[Pasted image 20240720102425.png|425]]
<span style="background:#affad1">启动界面按E</span>
找到linux16这一行，光标跳到行尾，添加<span style="background:#affad1">rd.break</span>，然后ctrl+x 启动系统
![[Pasted image 20240720102908.png]]
```bash
mount -o remount.rw /sysroot/    重新挂载根目录并添加写入权限
chroot /sysroot                               切换到根目录
touch /.autorelabel                         创建/.autorelabel文件
echo 123 | passwd --stdin root    修改密码
exit
exit                                                  重启系统,用新密码进入系统
```
## 4. 权限管理
linux系统中涉及到的安全技术方面
```bash
防火墙 (限制数据包传出和经过) 
selinux 
软件权限 
文件系统权限
```
### 4.1 查看权限
Linux中普通权限也称为文件系统权限，作用是保护文件，让有权限的用户可以访问系统文件等资源，否则不能访问，linux文件系统权限，主要设置在文件上，限制对象是<span style="background:#affad1">用户</span>。
<font color="#ff0000">注意：root用户不受文件系统权限控制</font>
<span style="background:#affad1">rwx     </span>|<span style="background:rgba(240, 200, 0, 0.2)">    rwx     </span>|<span style="background:rgba(136, 49, 204, 0.2)">    rwx </span>
<span style="background:#affad1">拥有者</span><span style="background:rgba(240, 200, 0, 0.2)">  所属组     </span><span style="background:rgba(136, 49, 204, 0.2)">其他人</span>(ugo)
权限的优先级顺序
uid=0(完全控制)-->拥有者(完全控制)-->所属组(附加组)-->其他人
<span style="background:#affad1">rwx</span> 对文件及目录的含义
file：
```bash
r  ------->read    cat head tail more
w ------->write   vim
x  ------->exec    ./后者角度路径执行
```
directory
```bash
r  ------->read     ls r-x详细信息
w ------->write    touch mkdir
x  ------->exec     cd 
```
### 4.2 修改权限
chmod  <span style="background:rgba(136, 49, 204, 0.2)">ugo</span><span style="background:#affad1">+-=</span><span style="background:rgba(240, 200, 0, 0.2)">rwx</span>  filename
chmod  <span style="background:rgba(136, 49, 204, 0.2)">7</span><span style="background:#affad1">7</span><span style="background:rgba(240, 200, 0, 0.2)">7 </span> filename
### 4.3 umask权限反掩码
umask值:<span style="background:#affad1">它表示应该屏蔽掉那些权限</span>，即可以规定linux默认目录及文件权限
#### (1)umask计算规则
目录默认权限最大777
文件默认权限最大666
超级用户umask值预设0022 
普通用户umask值预设0002
0022(第一位0代表特殊权限位，后三位代表普通权限位)
**计算方式1:**
目录或文件最大权限和umask值取反
取反规则
```bash
1 1 0 0
0 1 0 1
1 0 0 0
777=111 111 111
022=000 010 010
755=111 101 101
所以目录默认权限为755
```
**计算方式2：**
直接将数字相减的话，是会出现问题的，为什么呢？
linux系统中创建文件或目录时，默认权限会被`umask`修改。`umask`不是一个简单的减法操作，而是一种<span style="background:#affad1">权限屏蔽机制</span>。
文件默认最大权限666，假设umask=022
```bash
666=rw-rw-rw-
022=----w--w-
644=rw-r--r--
-------------------------------------------------------------
666=rw-rw-rw-
033=----wx-wx
644=rw-r--r--   # 本来就没有权限再减去一个x权限自然也还是没有
```
#### (2)管理umask值
查看umask值
[root@root /]# umask
设置umask值
[root@root /]# umask 0002
查看修改后对目录的权限
[root@root /]#umask -S
给某个用户永久设置umask
```bash
echo 'umask 0033' >> ~/.bashrc
```
给所有用户永久设置umask
```bash
echo 'umask 0033' >> /etc/bashrc
```
### 4.4 修改文件所有者所属组
chown 命令可以修改所有者和所属组，作用于超级用户，普通用户不可用 
```bash
chown [options] user [:group] file
-R 递归修改
chown -R robin:upup /tmp/dir
```
chgrp  命令只可以修改所属组，作用于普通用户，修改所属组为自己的组
```bash
chgrp upup /tmp/test.txt
```
### 4.5 特殊权限
linux权限；特殊权限；acl权限；隐藏权限

| 权限   | 对应权限数字 |
| ---- | ------ |
| SUID | 4      |
| SGID | 2      |
| SBIT | 1      |
#### (1)SUID
任何用户在运行拥有suid权限的<span style="background:#affad1">命令</span>(二进制可执行文件)时,都以该命令拥有者的身份执行
两种更改方式
chmod u+s 命令 
chmod 4755 命令
注意权限位的变化
![[Pasted image 20240724103304.png]]
![[Pasted image 20240724103339.png]]
如果本身没有执行权限
![[Pasted image 20240724103533.png]]
小s会变成大S
#### (2)SGID
如果目录设置了sgid权限,在<span style="background:#affad1">目录</span>中创建的文件/目录时都要<span style="background:#affad1">继承父目录的所属组权限</span>
chmod g+s  father
chmod 2xxx directory
![[Pasted image 20240724134434.png]]
![[Pasted image 20240724134522.png]]

#### (3)SBIT
当目录设置了SBIT权限后，只能自己删除所有者为自己的文件,其他人无权删除
chmod o+t directory
chmod 1xxx directory
### 4.6 facl文件访问控制列表
文件系统权限，主要是对一堆用户做限制，但是并不能只针对一个用户或者一个组进行单独的权限控制。
facl可以帮助我们<span style="background:#affad1">实现针对单个用户或组进行权限控制</span>
#### (1)查看是否支持acl功能
首先要确认文件系统是否支持了ACL功能，可以通过<span style="background:#affad1">Default mount options的acl字样</span>来判定是否支持，RHEL8或Centos默认都支持acl。
![[Pasted image 20240725142651.png]]
权限后面的“.” 或者没有“.”，均表示未设置acl权限
setfacl -m u:lisa:r /opt/test.txt
![[Pasted image 20240725142832.png]]
后面是加号代表设置了acl权限
#### (2)acl管理
setfacl用于管理acl权限 
getfacl用于查看acl权限
<span style="background:#affad1">setfacl</span> <span style="background:rgba(240, 200, 0, 0.2)">-m</span> <span style="background:#affad1">u</span><span style="background:#fdbfff">:lisa</span><span style="background:#40a9ff">:r</span> <span style="background:#d4b106">file</span>
第一项：命令 
第二项：添加一个ACL 
第三项：针对对象是单一用户 
第四项：用户名称 
第五项：权限 
第六项：文件名
<span style="background:#ff4d4f">setfacl：</span> 
命令选项： 
-m: 设置acl 
-x：删除指定的acl 
-b：删除所有acl 
-R：递归 
```bash
setfacl -m u:lisa:r /opt/test.txt
setfacl -x u:tom file 
setfacl -b file
```
<span style="background:#ff4d4f">getfacl</span>：
-R：递归
getfacl  /opt/test.txt
![[Pasted image 20240725153443.png]]
<span style="background:#ff4d4f">ACL权限设置的对象 </span>:
u user 所有者 
g group 所属组 
o 其他人
m mask值
#### (3)mask值的特点
修改mask值
setfacl -m m:staff file
![[Pasted image 20240725154914.png|500]]
![[Pasted image 20240725155001.png|500]]
![[Pasted image 20240725155109.png|500]]
![[Pasted image 20240725155214.png|500]]

1.对文件设置了acl权限后,mask值产生,或刷新旧mask值。除非手动设置，否则mask值会首先满足最大facl中的权限。 
2.我们可以通过调整mask值来限制acl权限的大小范围，也会影响基本组权限(有效权限范围)。
3.mask值权限和facl权限重叠部分，为有效权限
4.mask值权限不仅会显示在getfacl查询表中，也会同步到文件<span style="background:#affad1">所属组权限位置</span>。 
5.文件一旦设置了acl权限，需要使用getfacl来查看文件<span style="background:#affad1">真实权限</span>及acl权限。 
6.当删除单个acl之后，使用ll查看，发现在改文件或者目录不存在任何acl的情况下，仍然存在一个加号，显示存在acl设置，<span style="background:#affad1">使用getfacl查看发现有mask值残留</span>，使用命令setfacl -x m file ，删除之后再ll查看发现没有加号。 
<span style="background:#b1ffff">7.文件设置acl权限后，chmod命令修改的是mask值，不是文件基本组权限。</span>
#### (4)修改文件原<span style="background:#affad1">用户组</span>权限
setfacl -m g::rwx test2.txt
#### (5)acl备份及还原
```bash
setfacl -Rm u:harry:rwx /opt/dir
getfacl -R /opt/dir > /acl.bak                 # 将权限备份到/acl.bak文件中
setfacl -Rb /opt/dir                                # 清除，模拟丢失 
getfacl -R /opt/dir                                  # 此时没有权限 
setfacl -R --set-file=/acl.bak /opt/dir   # 恢复 
getfacl -R /opt/dir                                  # 再查看权限已经恢复
```

### 4.7 隐藏权限
lsattr 文件名              # 查看隐藏权限
chattr [选项] 文件名  # 修改隐藏权限
选项： 
-i：不能写入和删除 
-a：可以追加数据，不能删文件
![[Pasted image 20240725162927.png]]
chattr +a 1.txt  
lsattr 1.txt
![[Pasted image 20240725163002.png]]


## 5.软件包管理

按安装方式划分
rpm包管理
源码包管理
linux本地的rmp包存放位置
![[Pasted image 20240725165118.png]]
软件包结构
<span style="background:#affad1">nmap-6.40-7</span>.<span style="background:rgba(240, 200, 0, 0.2)">el7</span>.<span style="background:#affad1">x86_64</span>.<span style="background:#fdbfff">rpm </span>
<span style="background:#affad1">包名-版本</span>.<span style="background:rgba(240, 200, 0, 0.2)">系统版本</span>.<span style="background:#affad1">平台</span>.rpm
安装软件
rpm 选项 软件包全名 
rpm -ivh /mnt/Packages/telnet-0.17-64.el7.x86_64.rpm 
-i  安装 install 
-e 卸载
-v 显示过程 view 
-h 显示%
RPM 包默认安装路径
/etc/                          配置文件安装目录 
/usr/bin/                   <span style="background:#affad1">可执行的命令安装目录 </span>
/usr/lib/                     程序所使用的函数库保存位置 
/usr/share/doc/        基本的软件使用手册保存位置 
/usr/share/man/       帮助文件保存位置

软件包的查询
查询是否安装
rpm -q nmap  # 不加.rpm后缀
查询包的信息
rpm -qi nmap
查询安装位置
rpm -ql nmap
查看配置文件 
rpm -qc nmap
查看帮助文档的位置 
rpm -qd nmap 
查询已安装软件包 
rpm -qa 
rpm -qa | grep nmap 
查询文件对应的软件包 
rpm -qf /etc/man_db.conf 
rpm -qf `which cat`
![[Pasted image 20240729153306.png]]
rpm -e qemu-kvm-1.5.3-167.el7.x86_64 --nodeps <span style="background:#fff88f"># --nodeps忽略依赖</span>
安装或卸载时忽略依赖关系（rpm软件包在安装或卸载时都可以忽略依赖，但是可能导致安装后的软件功能不完整或不能正常使用）
<span style="background:#affad1">所以用rpm安装软件的时候，要手动解决依赖关系</span>

yum工具
yum是linux操作系统中最常见的软件安装工具，名为（Yellowdog Updater Modified），主要用于批量管理rpm包，可以做到批量安装软件包，且自动解决软件间依赖关系。
/etc/yum.conf           yum的配置文件，一般保持默认
/etc/yum.repos.d/     yum源文件目录
![[Pasted image 20240729154827.png]]

yum clean all                    # 清空yum缓存
yum makecache fast       # 快速建立缓存
yum repolist all                # 执行此命令时yum makecache fast此步骤自动执行
yum install  软件名          # 安装
yum update 软件名          # 升级软件
yum remove 软件名         # 卸载
yum search  软件名         # 搜索所有和vim相关的软件包
yum reinstall  软件名       # 重新安装
yum info 软件名               # 查看软件信息
yum list   软件名              # 列出软件列表
yum provides  /etc/mime.types           # 查询提供 /etc/mime.types文件的软件包

yum update和yum upgrade是两个常用的Linux命令，它们用于更新系统中已安装的软件包。<font color="#ff0000">它们之间的区别在于：</font>
yum update：这个命令会更新系统中已安装的软件包到最新可用版本，但不会升级到新的发行版。它只会更新已安装软件包的版本，而不会安装新的软件包或删除已有的软件包。 
yum upgrade：与yum update不同，yum upgrade会执行系统的升级操作，包括安装新的软件包、删除旧的软件包以及升级已安装软件包的版本。它会将系统升级到新的发行版，而不仅仅是更新已有软件包的版本。 
总的来说，yum update和yum upgrade的功能都是一样的，都是将需要更新的package更新到源中的最新版。唯一不同的是，yum upgrade会删除旧版本的package，而yum update则会保留(obsoletes=0)。

### 手动建立yum仓库
createrepo是一个用于创建yum存储库的工具，它的原理如下：
1.遍历指定目录下的RPM包：createrepo会遍历指定的目录，找到其中的RPM包文件。
2.生成元数据信息：对于找到的RPM包，createrepo会提取它们的元数据信息，包括文件列表、依赖关系、版本号等，<span style="background:#affad1">并将这些信息存储在一个叫做repodata的目录中。</span>
3.创建索引文件：createrepo会生成一个名为<span style="background:#affad1">repomd.xml的索引文件</span>，其中包含了存储库的元数据信息，以便yum工具能够快速地定位和访问存储库中的软件包。
4.完成存储库创建：一旦元数据信息和索引文件生成完毕，createrepo就会将其存储在指定目录下，完成存储库的创建过程。
总的来说，createrepo的原理就是通过提取RPM包的元数据信息，并生成索引文件，从而创建一个符合yum存储库标准的目录结构，使得该目录可以被yum工具正确识别和访问。
```bash
mkdir /zijian                                                   创建存放软件的目录
cp /mnt/cdrom/Packages/wire* /zijian/       给目录添加软件包
createrepo /zijian                                         
```
将目录制作为yum软件仓库,根据当前软件包记录，形成元数据，让仓库可用。                   
![[Pasted image 20240802101034.png]]
vim /etc/yum.repos.d/zijian.repo
yum makecache fast                             建立元数据缓存
![[Pasted image 20240802102035.png|450]]
yum repolist                                           列出可用仓库信息
![[Pasted image 20240802102136.png|450]]
如果这个仓库需要又新添加了新的软件包
```bash
createrepo /zijian        # 重新建立元数据 
yum makecache fast  # 建立元数据缓存
yum repolist all           # 列出可用仓库信息
```
### 配置网络yum源
两种方式
1.直接下载yum配置文件
```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.cloud.tencent.com/repo/centos7_base.repo
```
2.配置yum文件
```bash
1.虚拟机联网 
2.找到网络上的开源镜像站地址 https://mirrors.163.com/centos/7.9.2009/os/x86_64/ 
3.配置yum源文件 
[163] 
name=163 
baseurl=https://mirrors.163.com/centos/7.9.2009/os/x86_64/ 
enabled=1 
gpgcheck=1 
gpgkey=https://mirrors.163.com/centos/7.9.2009/os/x86_64/RPM-GPG-KEY-CentOS-7
```
/var/run/yum.pid 已被锁定，删除文件就可以了


### 归档文件
1.打包和压缩
压缩：将大文件压缩至更小，方便存储                               <span style="background:#affad1">压缩工具：gzip,bzip2,xz</span>
打包：linux操作系统中，为了更好的保存文件，可以先将多文件，或目录进行打包，成为一个文件，在做后续管理（linux系统中不能对目录进行压缩） <span style="background:#affad1">tar</span>
文件归档就称为 <span style="background:#affad1">打包并压缩</span>

#### 压缩：
linux系统中文件可以压缩，目录不能被压缩
```bash
gzip 1     压缩
gzip -d   解压
gzip保留源文件的方法
gzip -c file1 > file1.gz
```
![[Pasted image 20240802111412.png|550]]
```bash
bzip2  -k 1  
bzip2 -d  1   
-k 会保留源文件
```
![[Pasted image 20240802111543.png|550]]

```bash
xz  1
xz -k 2
xz  -d  1 
-k 会保留源文件
```
![[Pasted image 20240802112055.png|550]]

#### 打包
可以是文件也可以是目录
语法： tar 选项 新建打包文件名 被打包的文件1 被打包的文件2
选项： 
c 打包 
v 显示过程（可选） 
f 指定文件名 
t 查看打包文件内容 
x 解包 （x和c不能共同使用） 
C 指定解包路径（-C /opt/ 指定解包到/opt/目录下） 
r 为打包文件追加内容
```bash
tar -xvf boot.tar -C /home/           # 解包到指定路径/home/下
tar -cvf /home/boot.tar /boot/     #指定包的存储路径
tar -rvf boot.tar      /tmp/data      # 为已经打包的文件夹里面添加新内容
```

#### 打包并压缩
tar 选项 新建打包文件名 被打包的文件1 被打包的文件2 选项： 
```bash
-z gzip 
-j bzip2 
-J xz
tar -zcvf /opt/boot.tar.gz /boot/              # 打包并压缩的新文件名称自定义
tar -jxvf /tmp/boot.tar.bz2 -C /home/     # 解包并解压缩
```

## 6.网络管理
常用命令ifconfig ip ping tcpdump traceroute arp
### 修改网络信息
route -n                          查询网关
cat /etc/reslov.conf        查询DNS地址,通过查看该文件的nameserver字段来查看dns地址

(1)图形化修改
(2)nmtui
(3)修改配置文件
(4)nmcli
重启网卡的方式
一
nmcli connection reload ens33
nmcli connection up ens33 
二
nmcli connection reload ens33 
systemctl restart NetworkManager
二者的区别
方式一 更加专注于 ens33 这个特定的接口，如果 ens33 当前是断开状态，这种方式可以确保它被重新激活。
方式二 则会影响到所有由 NetworkManager 管理的网络接口，可能会引起更广泛的网络重置。
systemctl restart network                  # 适用于Centos7版本 
systemctl restart NetworkManager  # 适用于Centos8以上版本
### nmcli命令管理网络
nmcli [ OPTIONS ] OBJECT COMMAND
![[Pasted image 20240802142622.png]]
device叫网络接口，是物理设备，网卡 connection是连接，偏重于逻辑设置，网卡配置文件
一个device(ens33)可以有多个connection(配置文件)，但同一时间一个device只能启用其中一个connection。这样的好处是针对一个网络接口，我们可以设置多个网络连接，比如静态IP和动态IP的获取方式，

查看网卡配置
nmcli connection show
删除网卡
nmcli con delete ens33
添加网卡
mcli connection add ifname ens33 con-name ens33 type ethernet
配置网络参数
![[Pasted image 20240808144105.png]]
重新加载配置
nmcli connection reload
nmcli connection up ens33

【有道云笔记】4.网络参数配置
https://note.youdao.com/s/5F3n5qEc
## 7.很不熟悉的一部分

```bash
转义符的使用  \
```
通配符 *   可以代表任意长度的任意字符
删除叫  “\*” 的这个软件改怎么删除
```bash
rm -rf /opt/\*
```

find 查找文件
```bash
find /data/ -name "*.txt"  # 区分大小写
find /data/ -iname "*.txt"  # 不区分大小写
按类型查找
find /dev/ -type b
按文件大小查
find /etc/ -size +1M  # + 代表大于的意思
按文件的修改时间
find /etc/ -mtime +7  # + 表示七天前
按创建时间
find /etc/ -ctime  -10  # - 十天之内
-a 和 -o 或
find /A -maxdepth 2 -name "*.txt"  最多两层目录

找出文件在对文件做后续的操作

find /A -name "*.txt" -exec rm -rf {} \;  # 大括号表示删除所有 

```
grep 过滤内容
```bash
不同字符，正则表达式

```

在正则表达式
```bash 
由一类特殊字符组成的表达式，匹配一类具有相同特征的文本、
匹配单个字符
grep "r..t" /etc/passwd  # . 就代表某一个字符
grep "\." /etc/passwd 
[rkt]     # 在我们写的范围内任写其一
[a-z]    # 连续的字母任选其一
[^a-z]    # 取反
[[:space:]]   #  任意单个空白
grep   "[rkt]"  /etc/passwd

前一个字符
匹配连续出现的次数
* 前一个字符出现0次或多次
ab* a,ab,abbb
[0-9]* 8 88  45345
.* 任意字符

？ 代表前一个字符出现一次或0次 可有可无有
ab? a ab 
+  出现一次或多次
\{3\}  前一个字符精确出现三次 ,要加转义
{2，5} 表示一个区间
{2,}  可以是两次多了不限

```
https://flowus.cn/share/3bc7f27e-686a-4b1f-9521-8c271ea99331?code=HBHJBX
【FlowUs 息流】SHELL脚本
https://flowus.cn/share/eec0938b-2644-4213-a3be-b2be5d62b345?code=HBHJBX
【FlowUs 息流】LINUX



## 8.存储

【有道云笔记】3.磁盘管理
https://note.youdao.com/s/N7MNbOR7

## 9.进程管理
【有道云笔记】6.进程管理
https://note.youdao.com/s/QIiXTf0K


## 10.linux内核 kernel

![[Pasted image 20240810160152.png|250]]
应用程序通过内核访问硬件资源，而内核则作为中间层来协调应用程序与硬件之间的通信。例如，当一个应用程序需要读取硬盘中的数据时，它会向内核发送请求，然后内核将该请求转发给相应的设备驱动程序，最终完成数据传输。
**Linux内核的任务：**
1.从技术层面讲，内核是硬件与软件之间的一个中间层。作用是将应用层序的请求传递给硬件，并充当底层驱动程序，对系统中的各种设备和组件进行寻址。
2.从应用程序的层面讲，应用程序与硬件没有联系，只与内核有联系，内核是应用程序知道的层次中的最底层。在实际工作中内核抽象了相关细节。
3.内核是一个资源管理程序。负责资源(CPU时间、磁盘空间、网络连接等)分配得到各个系统进程。
4.内核就像一个库，提供了一组面向系统的命令。系统调用对于应用程序来说，就像调用普通函数一样。
**内核实现策略：**
1.微内核。最基本的功能由中央内核（微内核）实现。所有其他的功能都委托给一些独立进程，这些进程通过明确定义的通信接口与中心内核通信。
2.宏内核。内核的所有代码，包括子系统（如内存管理、文件管理、设备驱动程序）都打包到一个文件中。内核中的每一个函数都可以访问到内核中所有其他部分。目前支持模块的动态装卸(裁剪)。Linux内核就是基于这个策略实现的。  
**哪些地方用到了内核机制？**
1.进程（在cpu的虚拟内存中分配地址空间，各个进程的地址空间完全独立;同时执行的进程数最多不超过cpu数目）之间进行通   信，需要使用特定的内核机制。
2.进程间切换(同时执行的进程数最多不超过cpu数目)，也需要用到内核机制。
进程切换也需要像FreeRTOS任务切换一样保存状态，并将进程置于闲置状态/恢复状态。
3.进程的调度。确认哪个进程运行多长的时间。

### **Linux内核体系结构简析**简析

Linux内核的主要组件有：系统调用接口、进程管理、内存管理、虚拟文件系统、网络堆栈、设备驱动程序、硬件架构的相关代码。

### **Linux体系结构和内核结构区别**
1．当被问到Linux体系结构（就是Linux系统是怎么构成的）时，我们可以参照下图这么回答：从大的方面讲，Linux体系结构可以分为两块：
（1）用户空间：用户空间中又包含了，用户的应用程序，C库
（2）内核空间：内核空间包括，系统调用，内核，以及与平台架构相关的代码
![[Pasted image 20240810160927.png|425]]




# 11.linux 常用服务
【有道云笔记】2.基础服务应用
https://note.youdao.com/s/TT3rczzP

# 12.strace
[【调试技巧】strace神器的使用方法详解与实践_strace参数详解-CSDN博客](https://blog.csdn.net/qq_16933601/article/details/117248806)

[较详细的 gdb 入门教程 - Zesty_Fox - 博客园 (cnblogs.com)](https://www.cnblogs.com/acceptedzhs/p/13161213.html)

[主流服务器品牌的基本硬件配置_查一下戴尔、ibm、惠普的服务器型号和常见的配置。-CSDN博客](https://blog.csdn.net/u013558123/article/details/136896294)
# 13.端口号
[【默认端口】市面上各种中间件、软件、服务的默认端口汇总_常见的中间件,服务,软件端口号-CSDN博客](https://blog.csdn.net/xzb5566/article/details/116938507#:~:text=%E6%9C%AC%E6%96%87%E5%88%97%E4%B8%BE%E4%BA%86%E5%A4%9A%E7%A7%8D%E5%B8%B8,%E7%BB%9F%E9%85%8D%E7%BD%AE%E8%87%B3%E5%85%B3%E9%87%8D%E8%A6%81%E3%80%82)

# 14.chrony时间同步

[Linux服务器时间同步chrony详解+案例-CSDN博客](https://blog.csdn.net/qq_43437874/article/details/110651605)
# 15.ftp

[ftp主动模式和被动模式的区别_ftp主动传输和被动传输的区别-CSDN博客](https://blog.csdn.net/ludan_xia/article/details/105705473)
