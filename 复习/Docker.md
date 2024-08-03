# Docker
<font color="#ff0000">容器和传统虚拟机的区别？？</font>
## 一.容器介绍(<font color="#d99694">完整</font>)
容器就是轻量级虚拟化技术
<font color="#ff0000">轻量级体现在什么地方？</font>
创建速度特别快；秒级
创建了 只保证某业务正常运行的必备的应用程序/指令
![[Pasted image 20240715093641.png]]
<font color="#ff0000">为什么创建速度快？</font>
<font color="#245bdb">1.容器是没有自己的虚拟硬件的，没有独立的内核</font>
<font color="#245bdb">2.共享物理机内核，所以容器的IO速度要比传统的虚拟机快</font>
按虚拟化的架构来说，我们创建了一个容器就相当于虚拟化出一堆指令，或者说虚拟化出一堆应用程序，<span style="background:#affad1">容器里面只有能维持那个软件正常运行必要的一些指令</span>。我们在建容器的时候，也是要基于特定的镜像来创建的，一个镜像可以理解为一个模板(这个说话不准确)
假设有一个mysql镜像，创建出了mysql容器，从容器创建好的那一刻开始，mysql软件都是装好的，mysql相关的指令也都存在

![[Pasted image 20240715190311.png]]
虽然我们拿centos7的镜像创建出一个centos7的容器出来，但是从技术的角度讲，他这里边并不是一个完整的系统，应为这个容器里面没有自己独立的内核
无非也就是提供了centos上的一些基础的指令
没有自己的内核，所以是直接共享物理机的内核，<span style="background:#affad1">所以从数据IO的教的来说，无论是磁盘IO还是网卡IO 速度都是远超传统的虚拟机的</span>

<span style="background:#affad1">传统的虚拟机</span>要上网，从app产生一份数据，交给虚拟机的虚拟内核，如果磁盘或者网卡装了半虚拟化驱动的话，这个数据会直接绕过虚拟硬件到hypervisor虚拟机管理软件，再到真实物理机的内核，然后再通过真实物理机的网卡出去
<span style="background:#affad1">容器</span>他并不是一个完整的系统，它上面只是有一堆应用程序，所以他们是共享物理机内核，这就意味着容器将来想借助你物理网卡上网的话，容器产生的数据直接就能到物理机内核然后通过物理网卡转出去，流程要比传统虚拟机少的多

<font color="#ff0000">有容器之前和没容器的时候有什么区别？</font>
(1)快速的构建业务，根据公司业务自定义镜像
(2)从业务迁移的角度讲，提供了很大的便利

### 1.容器的优势
快速部署业务环境；平台
便于业务迁移，避免兼容性问题

### 2.容器的三要素
容器，镜像，仓库(存储镜像)
![[Pasted image 20240715102954.png]]
三者之间的关联
左侧客户端(操作者)，右侧registry存镜像的仓库，中间真正跑容器的机器
客户端那边发起一个操作叫做 docker run  创建容器的流程就是 跑在这个服务器上的docker服务(docker daemon) 会去远端的仓库，根据客户端的需求，联系远端的镜像仓库去下载这个镜像，把镜像下载到本地，然后就真正能拿这个镜像，创建一个容器出来了

### 3.容器相关的核心技术
和传统的虚拟机做对比
传统的虚拟机上面装的都是完整的系统，每个虚拟机上边都是有自己独立的内核的，由内核就能帮我们实现，不同虚拟机之间的资源隔离(传统的虚拟机通过虚拟内核就可以实现资源隔离)
容器之间没有自己的内核，都是共享内核的
<font color="#ff0000"> 那么容器之间是怎么实现资源隔离的？</font>
linux物理机内核中的两个技术
(1)<span style="background:#affad1">namespace 命名空间</span>
实现资源隔离(文件目录，用户，端口，进程)
创建容器的时候会自动生成一个命名空间，然后把容器放到这个空间里面实现资源隔离，创建第二个容器的时候又会自动形成另外一个命名空间
(2)<span style="background:#affad1">cgroup 控制组</span>
实现容器的资源限制(cpu,内存)
限制每个容器所能使用的cpu，所能使用的最大内存
实际的生产环境里面，每个机器必须做资源限制

容器的管理工具/软件
docker
docker-ce(社区);docker-ee(企业)
podman 跟docker一模一样
containerd

## 二.安装docker(<font color="#d99694">完整</font>)
### 1.配置docker软件仓库
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
cat /etc/yum.repos.d/docker-ce.repo
```bash
[docker-ce]
name=docker-ce
baseurl=https://mirrors.aliyun.com/docker- ce/linux/centos/7.9/x86_64/stable/
enabled=1
gpgcheck=0
```
### 2.安装docker, 启动docker服务
yum install -y docker-ce
rpm -q docker-ce
![[Pasted image 20240715111547.png]]
systemctl enable --now docker
### 3.配置国内docker镜像仓库
cat /etc/docker/daemon.json
![[Pasted image 20240715111655.png|500]]
![[Pasted image 20240715111723.png]]
### 4.主机网络变化
ifconfig
![[Pasted image 20240715111904.png]]
cat /proc/sys/net/ipv4/ip_forward
![[Pasted image 20240715111938.png]]
iptables -t nat -nL
![[Pasted image 20240715112538.png]]


## 三.容器管理的常用操作(<font color="#d99694">完整</font>)
创建容器
我是用什么镜像创建容器，设置容器执行什么命令
docker run [选项] 镜像名称 [命令]
-t -i 提供一个终端命令提示符
-d 后台运行
<span style="background:#affad1">任何一个容器的运行必须依赖于一个持续运行的进程存在</span>
docker run -tid centos:7 /bin/bash
### 1.查看容器
docker ps -a
### 2.查看容器的详情
docker inspect id
### 3.查看容器的日志
docker logs  id
### 4.连接登录容器
docker exec -ti id bash
### 5.删除容器
docker rm id 
docker rm -f id 强制删除
### 6.启动/停止/重启容器
docker [start|stop|restart] 容器ID/名称
### 7.杀死容器
docker kill id
### 8.导出容器
docker export -o suiyi.tar id 
### 9.导入容器
docker import suiyi.tar
![[Pasted image 20240715202714.png]]
导入之后还是一个镜像，然后还是要根据这个镜像重新创建容器
![[Pasted image 20240715202731.png]]
修改名字跟tag
docker tag 555308ffc1d7 suiyi:1003
docker run -tid suiyi:1003 /bin/bash
<span style="background:#affad1">docker run -tid --name xxx  image_id /bin/bash</span>
![[Pasted image 20240715202855.png]]
## 四.容器常用选项(<font color="#d99694">完整</font>)
### 1.后台运行
-d 
不加-d的话
创建完容器之后，就自动登录到容器里面了
退出之后，容器就暂停运行了
### 2.指定容器名字 主机名字
--name=test1 --hostname=test1 
```bash
docker run -tid --name=test1 --hostname=test1 centos:7 
docker exec -ti test1 bash
```
### 3.设置开启自启
--restart=always 
```bash
docker run -tid --name=test2 --hostname=test2 --restart=always centos:7 
```
### 4.发布容器内容
<span style="background:#affad1">-p 物理机端口:容器端口</span>
```bash
docker run -tid --name=test4 --hostname=test4  nginx:1.18 
```
<font color="#ff0000">现在我们机器里的这个80端口谁可以访问？</font>
跑docker的这个物理机可以访问，其他docke容器也可以访问
如果想外界可以访问，第一种方式就是通过nat转换(dnat)
docker思想上是这样的，但是操作不是这样
容器一点创建好以后，里面的东西就已经固定了没法改变
```bash
docker run -tid --name=test4 --hostname=test4 -p 80:80 nginx:1.18 
```
![[Pasted image 20240715203734.png]]
![[Pasted image 20240715210340.png]]
![[Pasted image 20240715210553.png]]
-P 随机发布端口
docker run -tid --name=test6 --hostname=test6 -P nginx:1.18 
从三万多开始
无论是P还是p 其实本质上都属于dnat
iptables -t nat -nL
![[Pasted image 20240730113300.png]]
<font color="#ff0000">既然他本质就是一条dnat规则，那我们可以可以自己手写一条dnat规则往外发布呢？</font>
理论可行，实际上不能！容器内有内核，就没有办法驱动硬件，容器不支持固定ip，只能通过dhcp自动获取，为什么说理论上可行呢？就在于你确实可以手写一条dnat规则把服务发不出来，但是如果容器ip变了呢？这个dnat规则就失效了，而现在上面两条dnat是容器服务自动生成的，他的好处就是，他会将来检测到容器的ip变了这个dnat规则里面的ip会跟着自动更新。
### 5.传递环境变量
https://hub.docker.com/
-e 变量名称=值
docker run -tid --name=test7 --hostname=test7 
-e NAME=martin centos:7 
![[Pasted image 20240715210950.png]]
 docker run -tid mysql:5.7 
![[Pasted image 20240715211226.png]]
![[Pasted image 20240715211247.png]]
容器建出来就是挂的
当我们拿镜像创建容器的时候，尤其是一些docker官方的镜像，有的会要求创建容器的时候必须有变量给我传值，不然容器就创建出来就是挂的
查看日志
docker logs  4a3a
![[Pasted image 20240715211408.png]]
后面必须添加参数传递环境变量

```bash
docker run -tid --name=test8 --hostname=test8 
-e MYSQL_ROOT_PASSWORD=redhat mysql:5.7 

docker run -tid --name=test9 --hostname=test9 \
> -e MYSQL_ROOT_PASSWORD=redhat \
> -e MYSQL_DATABASE=it \
> -e MYSQL_USER=admin \
> -e MYSQL_PASSWORD=redhat \
> mysql:5.7
```
通过-e 创建出来的用户，会自动给it库授权
![[Pasted image 20240730114519.png]]
### 6.容器持久化保存
<span style="background:#affad1">-v 物理机目录:容器目录</span>
创建容器的同时，在你的物理机上面选定一个目录，然后把这个目录挂载到容器里面去，然后你的那个容器产生数据的时候，这些数据会被自动映射到物理机上一部分，就算你的容器被删了，但是你的数据在物理机上还有
物理机上的目录要是不存在的话会自动创建，容器机上的目录要是没有也会自动创建

docker run -tid --name=test1 --hostname=test1 -v /opt/test1:/test1 centos:7 
docker exec -ti test1 bash
![[Pasted image 20240715211858.png]]
查看镜像对应的持久化目录， 使用docker image inspect查看详细信息找Volumes关键字
docker image inspect 8cf625070931
![[Pasted image 20240715212202.png]]\
```bash
docker run -tid --name=test2 --hostname=test2 \
> -e MYSQL_ROOT_PASSWORD=redhat \
> -v /opt/test2/:/var/lib/mysql \
> mysql:5.7 
```
多个容器挂载同一个目录
docker run -tid --name=test3 --hostname=test3 -v /opt/test1/:/test3 centos:7 
docker run -tid --name=test4 --hostname=test4 -v /opt/test1/:/test4 centos:7 
docker exec -ti test30 bash
![[Pasted image 20240715212630.png]]
![[Pasted image 20240715212639.png]]
![[Pasted image 20240715212649.png]]
### 7.容器中应用的配置文件
-v
把配置文件在物理机上准备好，然后通过-v的方式挂载配置文件
![[Pasted image 20240716091034.png]]
![[Pasted image 20240716091115.png]]
### 8.定义容器的<span style="background:#affad1">通信别名</span>
--link=容器名:别名
容器不允许使用IP通信
容器是不支持配置固定ip所有的ip都是由dhcp自动分配出来的，这个ip不一定什么时候就会变，所以容器和容器之间通信不能走ip的，所以要定义一个别名
![[Pasted image 20240730115852.png|500]]
### 9.容器的资源限制
--cpus
--memory
![[Pasted image 20240730115757.png|500]]
![[Pasted image 20240730115824.png|500]]

### 10.小练习
前期规划：
#### (1)mysql 主从
主 配置文件
/opt/mysql_master
[mysqld]
server_id=1
log_bin=master
gtid_mode=ON 
enforce_gtid_consistency=true 
从配置文件
/opt/mysql_slave
[mysqld]
server_id=2
log_bin=master
gtid_mode=ON 
enforce_gtid_consistency=true 

创建主虚拟机
```bash
docker run -tid --name=mysql_master --hostname=mysql_master -v /opt/mysql_master:/etc/my.cnf \
--cpus=1 --memory=400M \
-e MYSQL_ROOT_PASSWORD=redhat  mysql:5.7
```
创建从虚拟机
```bash
docker run -tid --name=mysql_slave --hostname=mysql_slave \
--link=mysql_master:mysql_master \
-v /opt/mysql_slave:/etc/my.cnf \
--cpus=1 --memory=400M \
-e MYSQL_ROOT_PASSWORD=redhat mysql:5.7
```

用户的创建再mysql内部创建
docker exec -ti mysql_master bash
mysql> create user 'repluser'@'%' identified by '[WWW.1.com](http://WWW.1.com)';
mysql> grant replication slave ON _._ TO 'repluser'@'%';
mysql> FLUSH PRIVILEGES;

docker exec -ti mysql_slave bash
mysql> CHANGE MASTER TO 
-> MASTER_HOST="mysql_master",
-> MASTER_USER="repluser", 
-> MASTER_PASSWORD="[WWW.1.com](http://WWW.1.com)", 
-> MASTER_AUTO_POSITION=1; 
mysql> START SLAVE;
mysql> SHOW SLAVE STATUS\G;
![[Pasted image 20240716105941.png]]
主从复制完毕


#### (2)创建业务容器
wordpress

首先主库创建wordpress连接用户
create database wordpress charset utf8;
create user 'wpuser'@'%' identified by 'WWW.1.com';
grant all on wordpress.* to 'wpuser'@'%';
flush privileges;

```bash
docker run -tid --name=wordpress1 --hostname=wordpress1 \
--link=mysql_master:mysql_master \
--cpus=1 --memory=1024M \
-e  WORDPRESS_DB_HOST=mysql_master \
-e  WORDPRESS_DB_USER=wpuser \
-e WORDPRESS_DB_PASSWORD=WWW.1.com \
-e WORDPRESS_DB_NAME=wordpress  \
-p 80:80 wordpress

```

```bash
docker run -tid --name=wordpress2 --hostname=wordpress2 \
--link=mysql_master:mysql_master \
--cpus=1 --memory=1024M \
-e  WORDPRESS_DB_HOST=mysql_master \
-e  WORDPRESS_DB_USER=wpuser \
-e WORDPRESS_DB_PASSWORD=WWW.1.com \
-e WORDPRESS_DB_NAME=wordpress  \
-p 81:80 wordpress
```

![[Pasted image 20240716112929.png]]
测试成功

#### (3)负载均衡
haproxy镜像制定的用户是普通用户，普通用户不能使用1024 一下的端口
```bash
docker run -tid --name=haproxy --hostname=haproxy \
--link=wordpress1:wordpress1 \
--link=wordpress2:wordpress2 \
--cpus=1 --memory=1024M \
-v /opt/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg  \
-p 8000:8000 haproxy
```
准备haproxy配置文件
vim /etc/haproxy/haproxy.cfg 
![[Pasted image 20240716113954.png]]


```bash
frontend web_service
   bind 0.0.0.0:8000
   mode http
   option forwardfor	
   use_backend blog			
backend blog
   balance roundrobin
   mode http
   server wordpress1 wordpress1:80 check
   server wordpress2 wordpress2:80 check
```

容器和物理器之间相互cp数据
docker cp wordpres1:/wordpress.sql   ./


## 五.堡垒机/跳板机(<font color="#d99694">完整</font>)

堡垒机/跳板机，加强内网服务器的安全管理
在所有的服务器前端添加一个一跳板机，工作人员只能连接到跳板机，然后跳板机帮你连接到后端的服务器，就是为了加强后后端服务器的安全性
![[Pasted image 20240730140350.png]]
堡垒机设置好权限，规定好那个人可以通过什么用户去管理那些后端的服务器，后端的服务器叫做资产
<span style="background:#affad1">4A：认证，授权，账户，审计</span>
审计就算很详细的日志记录
![[Pasted image 20240716160037.png]]
### 1.jumpserver安装使用
#### (1)生成jumpserver需要的密钥
/dev/urandom这个文件里面存放着大量的随机数
```bash
cat /dev/urandom | tr -dc a-zA-Z0-9 | head -c 50
B5Df26hgzQffkqPYCodUmhDhrcIl7OlJuYxBBnzpbnKQnjoc5u
cat /dev/urandom | tr -dc a-zA-Z0-9 | head -c 20
yTn6VcSyW7VBs2kGvoOR
```
#### (2)创建容器运行jumpserver
```bash
docker run -tid --name=jumpserver --hostname=jumpserver \
 --restart=always \
 -p 83:80 -p 2222:2222 \
 -e SECRET_KEY= B5Df26hgzQffkqPYCodUmhDhrcIl7OlJuYxBBnzpbnKQnjoc5u \
 -e BOOTSTRAP_TOKEN=yTn6VcSyW7VBs2kGvoOR \
 -v /opt/jumpserver/data:/opt/jumpserver/data \
 -v /opt/jumpserver/mysql:/var/lib/mysql \
 jumpserver/jms_all:v2.8.4 
```
80端口，提供webUI，jumpserver后台管理界面  
2222端口
有了堡垒机之后对于这些运维人员他要登录服务器也好，登录数据库也好，做的操作都是要通过ssh连接这个堡垒机，然后jumpserver里面的ssl服务和大家熟知的ssh不太一样，他的内部增加了很多增强性的功能能，比如说它可以借助ssh去获取后端的资产信息，类似于ansible的机制，jumperver默认的ssh端口就算2222，<span style="background:#affad1">未来方便其他运维人员连接</span>
默认用户名admin，密码admin

![[Pasted image 20240716192221.png]]
#### (3)客户端登录
ssh admin@192.168.140.21:2222 
![[Pasted image 20240716192152.png]]
### 2.jumpserver的使用
创建用户组
创建用户
![[Pasted image 20240717091808.png]]
添加资产
创建系统用户----创建管理用户-----添加资产
创建系统用户
系统用户就是后端资产服务器上面真实存在的一个账号，作为跳板机登录后端资产使用，需要事先设置后sudo授权，然后对这个用户的操作权限做控制
![[Pasted image 20240801192728.png]]
ssh: linux设备
telnet：适用于路由，交换机，防火墙等网络设备
RDP: 远程桌面协议针对windows，3389端口
vnc: 后端是虚拟化设备
mysql:数据库
k8s:k8s集群

![[Pasted image 20240717092658.png]]

创建管理用户
获取资产的配置信息，将来你在跳板机上面把后端的设备添加玩资产之后，这个跳板机后端会有ansible相关的东西，通过ansible去获取你后端资产的配置信息，cpu网卡之类
后端服务器身上的root用户
![[Pasted image 20240717092940.png]]
添加资产
![[Pasted image 20240717093511.png]]
用户授权/资产授权
suiyi,huqian这两个账  号授权
![[Pasted image 20240717094026.png]]
测试连接
![[Pasted image 20240717094212.png]]

## 六.docker容器网络管理(<font color="#d99694">完整</font>)
不同网络命名空间之间也是相互隔离的如果想相互通信需要路由
### 1.docker网络的工作模式
docker network ls 
![[Pasted image 20240717112357.png]]
四种bridge host  container none
#### (1)bridge模式(nat)
实际就是NAT模式  
SNAT：网关、路由转发、SNAT规则  
snat依赖于物理机路由转发，snat规则和容器的网关这三方面
DNAT：创建容器的时候通过 -p, -P；把服务发布出来，但是注意端口冲突
#### (2)host模式
容器会和物理机共享一个网络命名空间，也就是说这个模式创出来的容器网络相关的参数会和物理机的一摸一样，<span style="background:#affad1">容器会和物理机共享一个IP</span>
docker run -tid --net=host centos:7
登录上之后跟物理机一个主机名，主机名和物理机是一样的(主机名就是一个网络参数)<span style="background:#affad1">但是实际上并不是同一个机器</span>，<span style="background:rgba(240, 200, 0, 0.2)">容易出现端口冲突</span>
通过docker inspect 查看时，发现network这一个部分只显示一个网络模式是host，其他的都是空
<font color="#ff0000">但是这种模式有什么用呢？</font>
我创建的容器和物理机共享一个ip,就相当于如果我这个容器里面真实跑一个服务的话，这个服务就相当于直接监听在物理机上了，就意味着外面的客户端直接通过物理机的ip就可以访问到容器里面的服务，就是为了服务被外界访问的

还方便跨物理机通信，方便不同物理机内的容器通信
<font color="#ff0000">和桥接网络相同么？</font>
自然是不相同的，桥接是你和物理机在同一个网段，然后会被分配一个独立的ip,而host是指，容器和物理机共享

#### (3)container模式
新建的容器会和已有的容器(bridge)共享一个ip,或者说共享同一个网络命名空间，<span style="background:#affad1">这个已有的容器只能是bridge默认模式的网络</span>
docker run -tid --name=test5 --net=container:test4 centos:7
<font color="#ff0000">这个模式有什么好处呢？</font>
有些公司在构建自己的内部业务的时候，尤其是构建内部局域网业务的时候，就会把这个容器的模式制成这个container模式，让着多个容器共享ip ,最主要的好处就是，<font color="#ff0000">减少容器之间的网络消耗</font>
两个不同的容器，不管你是通过ip通信还是通过别名通信，他们俩之间必然要走tcp网络，会有网络的消耗，通过container模式创建的容器，虽然新建的容器和已有的容器是两个不同的同期。但是由于ip是一致的，他们俩在相互通信的时候呢，就会认为是在和自己本地上的一个进程来通信，IP地址一样，不同的进程之间通信的时候走本地的套接字，就没有网络的消耗
#### (4)none模式
容器没有自己的网络命名空间

## 七.flannel+etcd网络(<font color="#d99694">完整</font>)
跨物理机之间的容器相互通信
第一种方法:
host网络模式 
第二种方法：
假设在物理机之间，在物理机的容器环境之间假设存在着一条特殊的路线或者说存在一条特殊的网线
![[Pasted image 20240717213818.png]]
把这两个物理机上的容器环境给串起来，这样子确实能够实现通信，但是在实现通信的基础上，<span style="background:#affad1">你需要去改容器网络的分配方式</span>，原因就是，假设真的有这么一条线路的话，你需要把两台机器默认使用的172.17的这和网段换了，不然必然会出现一连串ip冲突的问题
### 1.flannel工作原理介绍
解决跨物理机容器间通信的问题：
1.改变容器的ip分配方式
2.特殊的线路来连接容器网络
flannel就可以帮我们解容器通信问题的同时，也帮我们改变了那个docker默认使用的网络分配方式，要通过flannel这个软件解决这两个物理机上面的容器通信问题，这两个物理机上面我们都需要安装配置flannel这个软件，这个软件一旦配置好之后
借助这个flannel软件就相当于是在所有的物理机的基础上，形成了一个大的虚拟网络
这个虚拟网络在形成的时候，需要我们通过配置，人为的指定一下，你这个虚拟网络想用那个网段的地址，这个网段随便指，但是不要和现实网络里面 真实存在的网络冲突
<span style="background:#affad1">规划16位掩码的虚拟网络</span> 

在物理机上面安装启动这个flannel软件之后，他会在你的物理机上面，有会形成一块虚拟网卡，默认叫做flannel0
这就相当于我左侧的这个物理机通过这个flannel0的这个虚拟网卡，就自动接入到了上层的虚拟网络上面
一旦接入成功之后，上面的这个10.88.0.0/16网段会自动给flannel自动分配一个子网，分什么网段不一定，但是肯定可以保证给每个物理机分配的子网是不冲突的
![[Pasted image 20240717214947.png]]
假设分了一个10.88.2.0/24的这么一个子网
接入到虚拟网络上，这个虚拟网络给物理机分配了一个子网这是第一
第二，我们在物理机上面安装好flannel之后，我们还需要让flannel接管物理机上面的docker0网卡
一旦接管成功之后，就相当于我们装的flannel把我们机器上docker默认使用的网络给改了，改成了他拿到的10.88.2.0/24这个网段，然后容器获得的ip就是10.88.2.0/24这个网段的IP
![[Pasted image 20240717215525.png]]
flannel用的时候，他还必须配合这另外一个软件来使用

<span style="background:#affad1">etcd</span>
记录网段的分配信息的
和redis差不多，是一个键值对的数据库，但是性能比redis强。
flannel这个虚拟网络本身信息跟子网分配给了谁等各种网络信息，都会在这个etcd这个数据库里面记录

从网络的角度讲
左侧的某个容器要和右侧有个容器通信
最基本的他应该知道目的ip是99.0，那对于我左侧的物理机来说，他是怎么通的呢？他肯定要联系etcd查99.0这个网段被分配给了那个物理机，然后在通过这个数据库查询的结果把这个数据转发到右侧的这个物理机上
<span style="background:#affad1">这个etcd相当于一个路由的角色</span>

flannel网络的核心就是这个etcd数据库
K8S的核心也是etcd数据库
![[Pasted image 20240717220300.png]]

### 2.flannel网络部署
#### 2.1环境描述
192.168.140.22 docker/flannel/etcd  
192.168.140.23 docker/flannel
#### 2.2 两台物理机安装docker
#### 2.3 安装配置etcd数据库
yum install -y etcd
vim /etc/etcd/etcd.conf
	ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379" ETCD_ADVERTISE_CLIENT_URLS="http://0.0.0.0:2379"
systemctl enable --now etcd
netstat -tunlp | grep etcd
![[Pasted image 20240717220939.png]]
测试键值对数据库
基于文件的键值对
![[Pasted image 20240717221134.png]]
#### 2.4 安装配置flannel
安装flannel
yum install -y flannel
配置flannel
vim /etc/sysconfig/flanneld
FLANNEL_ETCD_ENDPOINTS="http://192.168.140.22:2379"
FLANNEL_ETCD_PREFIX="/atomic.io/network" # 网段的信息存放在network这个目录下

在etcd数据库中写入flannel网络信息
etcdctl mk  /atomic.io/network/config '{"Network":"10.88.0.0/16"}'

启动flannel
systemctl enable --now flanneld.service
ifconfig flannel0
![[Pasted image 20240717221840.png]]


配置flannel接管docker0
vim /usr/lib/systemd/system/docker.service
	ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock $DOCKER_NETWORK_OPTIONS
为什么写这个变量docker网络就变了呢？
就是配置好flannel这个软件后启动后生成的一个变量
这个变量存的就是flannel的子网信息
重启后docker的ip就变了
systemctl daemon-reload
systemctl restart docker
ifconfig docker0
![[Pasted image 20240717222838.png]]
查看一下那个变量在什么位置
cat /run/flannel/docker
![[Pasted image 20240717222911.png]]
另外一个服务器参考上述配置

### 测试容器通信
rpm包装出来的flannel 他会把你iptables里的防火墙的数据转发链默认规则改为drop,需要改一下
iptables -P FORWARD ACCEPT
![[Pasted image 20240717231324.png]]

## 八.docker镜像(<font color="#d99694">完整</font>)

### 1.镜像介绍
所有的容器都是要依赖于特定的镜像创建的，镜像如同模板一样(说法不够准确)
一个镜像就是一个<span style="background:#affad1">分层的文件系统</span>，并且<span style="background:#affad1">只读</span>
容器的镜像他都是由 多层文件系统叠加而成的，下载镜像的时候会一条一条的下载
![[Pasted image 20240718195530.png]]
这个就是分层的文件系统
![[Pasted image 20240718092507.png|350]]
从这个图上看 这个apache的镜像就是由三层文件系统构成的
最低层：表示容器和物理机共享一个内核(不包含在内)
三层文件系统：
最底层:debian系统，提供一些系统的基础指令(echo,ls.cd)等
上一层:提供emacs，这个就相当于debian系统里面的vim
最上层:提供apache相关的命令
这三层文件系统累加在一起，就算是构成一个<span style="background:#affad1">完整的apache镜像</span>
#### (1)<span style="background:#affad1">分层文件系统的</span><span style="background:#affad1">优势：</span>
节省空间，速度快，重用
比如：我们已经下载过apache的镜像，他的最低层是debian的文件系统，提ls，提供cd指令，当我们之后如果要下载tomcat的时候，有可能这个tomcat镜像底层也需要ls，cd指令，当我们下载tomcat镜像的时候，docker就能检测到我们这个机器里面已经有了一层文件系统，提供ls，提供cd了，那我们就可以直接复用了。
<span style="background:#affad1">只读</span>：任何一个容器镜像都是只读的

<font color="#ff0000">镜像是只读的？那为什么我们那这个镜像出来创建容器我们可以更改容器里面的额文件呢？镜像和容器有什么样子的关系？</font>
#### (2)镜像和容器的关系
有点类似于软件和进程之间的关系
在Docker中，镜像（Image）和容器（Container）之间的关系类似于面向对象编程中的类和实例。Docker <span style="background:#affad1">镜像是一个只读的模板</span>，<font color="#ff0000">它包含了运行一个容器所需的所有文件和配置</font>。镜像是不可变的，也就是说，一旦创建，其内容就不能再被修改。<span style="background:#affad1">容器是实例</span>：容器是从镜像创建的运行实例。容器可以运行、停止、移动和删除，它是镜像的实际运行状态。镜像是静态的，它是一个包含了所有运行应用程序所需文件、依赖库、环境变量和其他配置的只读模板。容器则是基于这个镜像运行时的动态实例，它提供了一个执行环境，允许应用程序在其中运行。
#### (3)容器如何修改文件系统
<span style="background:#affad1">通过镜像创建容器，从镜像的角度来说就是相当于，在容器的最上方添加一个writable(可写成)，就是应为这个可写层的存在，我们在可以在容器里面对数据，对文件进行写操作</span>，这个层也被称之为<span style="background:#affad1">容器层(Container Layer)</span>，而这些修改不会影响到下面的镜像层。因此，即使容器内有文件被修改或新增，原始的镜像仍然保持不变。
敲docker run 这个命令的这一刻，就先当与在镜像上面加了一层可写层，把容器删了，这个可写层也就没了，所以跟大家说，跑关键性业务的时候，关键数据必须做持久化存储
总结：镜像就是一个死的文件系统，通过镜像添加容器，就是在这个镜像上面增加可写层，然后便可以称之为一个容器
#### (4)镜像是只读的原因
镜像是只读的，这样做的主要目的是为了确保其一致性、完整性和可重用性。只读特性使得镜像可以被多个容器安全地共享，而不会因为其中一个容器的修改而影响到其他容器。此外，只读属性还简化了镜像的分发和版本控制，因为它们不会被意外更改。

### 2.镜像核心技术
#### (1)cow 
copy on write 写时复制
只有发生写操作的收才会出现复制行为，逻辑卷也是支持写时复制功能的
从容器镜像的角度来讲
我们刚才说，镜像是一个死的文件系统，那这个镜像创建容器有个可写层，可以在可写层对文件数据进行修改
~~容器建出来之后，我会把镜像里面涉及到的所有文件全都给你放到可写层里面去，随时方便你修改~~（肯定不会是这样的！！！）效率太低
<span style="background:#affad1">正确的是：当你要改某一个文件的时候，我们才会把这个文件加载到内存里面或者加载到可写层里面来做修改！</span>
#### (2)Unionfs 
<span style="background:#affad1">联合文件系统</span>：支持多重文件系统的挂载
	overlay2：直通硬件
	device mapper：靠软件模拟实现
<font color="#ff0000">linux中常见的文件系统分三类</font>
	单机文件系统 ext4、Btrfs、XFS
	网络文件系统 nfs cifs
	集群文件系统 gfs  共享文件锁
第四类联合文件系统
从文件系统的角度讲，拿镜像创建容器，就是相当于这么一回事，首先挂载第一层文件系统，第一层文件系统挂载出来之后，我们的容器里面相当于有了某些指令，挂完之后卸载，腾出空间再挂载第二层文件系统，以此类推
<span style="background:#affad1">镜像创建容器的过程，就是这些文件系统不停的挂载卸载的过程</span>
联合文件系统的挂载，不同于传统的挂载，文件系统挂载好之后，即使卸载了，相应的指令也还是会存在，并且可以叠加
<font color="#ff0000">linux的启动过程跟这个类似，先挂载内核，然后卸载，然后在挂载系统里面的各种初始化程序，卸载后再挂载上层应用</font>


### 3.镜像的核心操作
镜像文件存储路径
ls /var/lib/docker/image/overlay2/layerdb/mounts
#### (1)查看镜像列表
docker image ls 
![[Pasted image 20240715113429.png]]
TAG：标记镜像的版本
#### (2)搜索镜像
docker search nginx
#### (3)下载镜像
docker pull nginx:1.18
#### (4)导入镜像
docker load -i centos7.tar
#### (5)导出镜像
docker save -o tomcat.tar tomcat:latest
#### (6)删除镜像
docker rmi id 
#### (7)查看镜像详情
docker image inspect centos:7
查看镜像的详细信息，找Cmd的关键字
#### (8)更改镜像名称
docker tag 555308ffc1d7 suiyi:1003

## 九.Dockerfile定制镜像(<font color="#d99694">完整</font>)
dockefile就是给我们提供了一些指令，将来你要干什么活用什么指令，然后按照自己的需求，去做定向就行了
### 1.Dockerfile使用教程

#### (1)编写dockerfile
vim dockerfile.txt
FROM centos:7
COPY CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo
RUN yum -y install net-tools
#### (2)构建镜像
docker build -t centos:v1  ./ -f dockerfile.txt
#### (3)创建容器测试镜像定制操作
docker image ls
![[Pasted image 20240718141029.png]]
docker run -tid --name=test1 centos:v1
docker exec -ti test1 bash
ifconfig

### 2.dockerfile 常用指令
#### -FORM
指定基础镜像
镜像不存在，构建镜像时自动下载镜像
建议尽量选择小容量的镜像 /debian/ubuntu
FROM 镜像名称
#### -RUN
指定定制命令
RUN 命令   &&  命令   &&  命令
注意：首先你要确保你的基础镜像里面有这个命令，他才能执行成功
尽量少些run，如果将来你定制镜像的时候，如果一些操作是有关联的，尽量写在一行
镜像是个分层的文件系统，你在多些一行run，镜像生成的时候就会多加一层文件系统
#### -CMD
定义容器创建时自动执行的命令，一般系统级别的镜像里面默认的都是bash，应用级别的镜像里面的指令都是启动服务的
注意事项
前台启动服务的指令
创建容器时，不要自己指定命令，会覆盖CMD
一个dockerfile只能有一条CMD指令
CMD 命令
CMD http -D FOREGROUNG
CMD ['HTTPD','-D','FOREGROUNG']推荐这种写法
```
vim dockerfile.txt
FROM centos:7
COPY CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo
RUN yum -y install httpd
CMD systemctl start httpd # 这样子写是不对的，
所以服务启动要写成前台启动
CMD http -D FOREGROUNG
```
用CMD systemctl start httpd这样子生成的镜像容器启动的时候就是挂的。这是应为你的容器想一直处于up状态，你的容器里面必须要有一个持续的进程存在，但是这个启动服务的命令，启动完了就结束了
#### -ENTRYPOINT
作用和cmd一样的
定义容器创建时，自动执行的命令
ENTRYPOINT ['HTTPD','-D','FOREGROUNG']
CMD写的指令会被启动容器的时候的指定命令给覆盖；ENTRYPOINT不会被覆盖
ENTRYPOINT 在复杂的场景会有特殊的应用，定制相对复杂的镜像的时候，写脚本通过copy复制到容器里面去，通过ENTRYPOINT执行脚本
#### -COPY 
复制文件
COPY 源文件 目的文件
只能复制本地文件
#### -ADD
复制文件
ADD 源文件目的文件
支持本地文件，还支持远程的url 相当于wget
拷贝压缩包的化，压缩包会自动解压，只支持本地的压缩文件
```bash
vim dockerfile.txt
FROM centos:7
COPY CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo
RUN yum -y install httpd
CMD systemctl start httpd                                         # 这样子写是不对的，
CMD ping baidu.com
ADD file01 /tmp/file01
ADD http://nginx.org/download/nginx-1.27.0.tar.gz
ADD jdk-xzxxx.tar.gz  /tmp                                         # 相当于 tar xf xxx -C /tmp
```
#### -EXPOSE
说明容器服务端口,并不会自动发不出来，如果想自动发不出来，还需要-p -P 
EXPOSE 端口 端口  
注意：
-P随机发布端口时，dockerfile中必须有EXPOSE指令，如果没有的话就算创建容器的时候用了-P，也不会有任何效果
EXPOSE 80/TCP
#### -VOLUME
定义持久化存储的目录
创建容器的时候不使用-v明确指定目录，会自动生成匿名卷
VOLUME 目录
RUN mkdir /data
VOLUME /data
```bash
vim dockerfile.txt
FROM centos:7
COPY CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo
RUN yum -y install httpd                                  # 这样子写是不对的，
CMD httpd -D FOREGROUND
RUN mkdir /data
VOLUME /data
```
你这个镜像里面定义了一个volume，就像查看镜像详情的时候，可以看到volume的关键字
![[Pasted image 20240803104858.png]]
这个字段显示跟下的data目录应该做持久化，但是后面还带了个括号，正常情况下我们docker run -v 物理机目录:容器目录 选定了物理机目录之后就会挂载在物理机的这个目录下，如果创建容器的时候你不写-v 就是应为有这个字段，他也会自动的做持久化，会自动生成一个匿名卷，那这个匿名卷来做持久化 “{ }” 这个就算匿名卷的意思
自动生成的匿名卷在
![[Pasted image 20240803105611.png]]
#### -ENV
定义环境变量
ENV 环境变量名称 值
#### -WORKDIR
定义当前目录
WORKDIR /data
#### -USER
指定容器运行的用户
USER 用户名
用户改了影响权限，haproxy镜像就算用了普通用户启动的
```bash
vim dockerfile.txt
FROM centos:7
COPY CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo
RUN yum -y install httpd
CMD httpd -D FOREGROUND
RUN useradd admin
USER admin 
```
这样子写，构建成镜像做成容器后，容器启动不起来，原因就是权限不够
应为这个dockerfile里面写的是，下载一个apache 启动apache apache默认端口80，普通用户是没有权限用1024以下的端口的

### 3.练习
编写tomcat镜像
## 十.微服务项目的部署
192.168.183.10
微服务项目部署要按顺序
第一个容器
![[Pasted image 20240719101542.png]]
![[Pasted image 20240719101602.png]]

数据库容器
![[Pasted image 20240719101708.png]]
![[Pasted image 20240719101801.png]]

登录数据库导入三个库
![[Pasted image 20240719101840.png]]
登录数据库确认一下
![[Pasted image 20240719101903.png]]
创建远程连接的账号
![[Pasted image 20240719102033.png]]

创建消息队列容器
![[Pasted image 20240719102145.png]]
![[Pasted image 20240719102153.png]]

查看端口5672，前端业务连接 15672web管理界面
![[Pasted image 20240719102209.png]]
![[Pasted image 20240719102250.png]]
能成功访问说明rabbitmq 是部署成功的
guest guest 登录
需要在消息队列里面创建业务连接的账号
![[Pasted image 20240719102406.png]]
![[Pasted image 20240719102416.png]]
![[Pasted image 20240719102438.png]]
给用户授权
![[Pasted image 20240719102455.png]]
![[Pasted image 20240719102715.png]]
项目部署底层的三个组件ok

部署微服务框架
1.注册中心
2.配置中心
3.网关

部署erueka
注册中心，负责注册所有服务的通信地址
自己定义eureka的镜像
vim eurekadockerfile
![[Pasted image 20240719103918.png]]
**
![[Pasted image 20240719104009.png]]
![[Pasted image 20240719104045.png]]
查看是否正确启动
![[Pasted image 20240719104203.png]]
![[Pasted image 20240719104226.png]]

部署config-server
配置中心
负责集中管理所有业务组件的配置文件
把所有的配置集中放到git上面
从远程的git服务器上拉去配置
vim configdockerfile
![[Pasted image 20240719104835.png]]
![[Pasted image 20240719104901.png]]
![[Pasted image 20240719104928.png]]

查看日志看是否正常启动
![[Pasted image 20240719105023.png]]
别有error的报错

部署zuul-server
网关
从开发的角度来说
这个网关是用来集中处理请求的，类似于反向代理
![[Pasted image 20240719105304.png|325]]
vim zuuldockerfile
![[Pasted image 20240719105506.png]]
![[Pasted image 20240719105523.png]]

![[Pasted image 20240719105644.png]]
检查是否可以正常运行

部署业务
部署会员服务
vim memberdockerfile
![[Pasted image 20240719112308.png]]
![[Pasted image 20240719112318.png]]
![[Pasted image 20240719112634.png]]
查看日志
![[Pasted image 20240719112703.png]]

部署商品展示的业务
vim gooddockerfile
![[Pasted image 20240719112829.png]]
![[Pasted image 20240719112849.png]]
所有业务容器的主机名都是localhost
![[Pasted image 20240719112942.png]]
docker logs good-server

部署秒杀业务
参考上述流程
vim seckilldockerfile
![[Pasted image 20240719113133.png]]

![[Pasted image 20240719113158.png]]
![[Pasted image 20240719113242.png]]
docker logs seckill-server
![[Pasted image 20240719113312.png]]

部署web前端
提供前端的web页面
参考上述流程
vim frontenddockerfile
构建镜像
创建容器
查日志保证正常运行

部署websocket
实现http的长链接
vim websockerdockerfile
构建镜像
创建容器
查日志保证正常运行

![[Pasted image 20240719113914.png]]
docker logs websocket
8088端口

流程不能错！！！！！

访问前端测试
192.168.183.10:8080
![[Pasted image 20240719114232.png]]




## 十一.基于harbor镜像仓库(<font color="#d99694">完整</font>)
存放镜像的位置
### 1.仓库的类型
公有仓库           私有仓库:企业级的应用
[Dockerhub](https://hub.docker.com/)    registry;harbor(公司内部局域网)
其他的服务器再创建容器的时候需要镜像可以直接从公司内部的仓库下载
(1)走局域网速度快
(2)公司有新项目上线的话，新的定制的镜像也可以集中上传到这个仓库服务器
### 2.构建私有仓库的方案
registry(早期;容器镜像):就是一个容器;功能太少,缺少日志,权限分配,高可用功能;仅支持上传下载;5000端口
<span style="background:#affad1">harbor</span>(软件):VMware开源的;在早期的registry的基础之上进行改进;增加了web ui;支持日志审计;权限分配;统一认证等功能，更贴合公司实际的使用需求
两种安装包:离线安装包(包含镜像)；在线安装包(内存小;不包含镜像;需要自己下载)
### 3.部署harbor仓库(单机版)
#### 3.1安装docker
#### 3.2安装docker-compose工具
支持批量创建容器，和批量删除容器
mv docker-compose /usr/local/bin/ 
就是个命令所以把包放到系统的命令目录里就可以了
docker-compose version
![[Pasted image 20240722190736.png]]
#### 3.3安装harbor
解压完就算安装好了
```bash
mkdir /work
tar xf harbor-offline-installer-v2.2.2.tgz -C /work/
cp /work/harbor/harbor.yml.tmpl /work/harbor/harbor.yml # 复制配置文件
```
**修改配置文件**
![[Pasted image 20240722191106.png]]
harbor可以支持通过http协议工作，默认端口80;也支持https默认用的端口是443，<span style="background:#affad1">但是我们现在用harbor都是用https协议</span>；这个docker有关:仓库搭建好以后，想要上传或者下载镜像，还是需要用docker命令，而docker内部默认的就是基于https的，如果你的仓库设置成http的docker的配置还要改，所以我们仓库一般都用https
#### 3.4生成harbor需要的证书;密钥(v3版本)
harbor的版本从2.2开始就是一个分割线；2.1版本之前harbor需要证书密钥我们可以那我们之前学的https拿opens命令生成证书和密钥就可以了；但是2.2版本之后，harbor需要的证书必须是v3版本(第三个版本)的证书
##### (1)创建CA
生成个CA需要的密钥
```bash
mkdir /opt/ssl
cd /opt/ssl
openssl genrsa -out ca.key 4096 
```
![[Pasted image 20240722192216.png]]
生成个CA需要的证书
```bash
openssl req -x509 -new -nodes -sha512 -days 3650  -subj "/CN=harbor.linux.com"  -key ca.key  -out ca.crt  # 生成个CA需要的证书
ls
```
![[Pasted image 20240722192242.png|675]]
##### (2)创建harbor仓库需要的证书
创建harbor仓库需要的密钥
```bash
openssl genrsa -out server.key 4096
```
![[Pasted image 20240722192444.png|700]]
申请一个harbor仓库需要的证书
```bash
openssl req  -new -sha512  -subj "/CN=harbor.linux.com"  -key server.key  -out server.csr
vim v3.ext   # 真正生成证书的时候需要这么一个文件
	authorityKeyIdentifier=keyid,issuer
	basicConstraints=CA:FALSE
	keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
	extendedKeyUsage = serverAuth 
	subjectAltName = @alt_names
	[alt_names]
	DNS.1=harbor.linux.com
```
生成证书
```
openssl x509 -req -sha512 -days 3650 -extfile v3.ext -CA ca.crt -CAkey ca.key -CAcreateserial -in server.csr -out  server.crt
```
![[Pasted image 20240722192348.png]]
ca.key      密钥
server.csr 证书申请
server.crt  证书】
#### 3.5更改harbor配置文件
vim  harbor.yml
![[Pasted image 20240722193922.png|550]]
默认自带admin管理员用户
![[Pasted image 20240722193803.png|550]]
所有的仓库必须做持久化,默认目录就是/data
![[Pasted image 20240722194050.png|550]]
#### 3.6启动harbor
启动的时候会下载prepare镜像,我们可以提前导入
docker load -i  prepare.tar 
在harbor目录下
./prepare
运行prepare脚本主要是要干啥呢？
这个harbor仓库在运行的时候需要用nginx做反向代理，redis做会话，需要后台数据量来存储数据，然后需要很多的组件，所以这个脚本就是为了生成个个组件的配置文件
<span style="background:#affad1">然后会自动生成docker-compose.yml文件</span>
ls /harbor/common/config/  # 这个目录下
启动harbor
./install.sh 
![[Pasted image 20240722195016.png|400]]
docker image ls 
![[Pasted image 20240722195046.png|700]]
要确保这九个容器都启动;并且都是健康的状态
访问web
![[Pasted image 20240722200222.png]]
默认账号admin 密码Harbor12345

### 4.harbor仓库的使用
#### 4.1创建项目
![[Pasted image 20240722201635.png|325]]
![[Pasted image 20240722201707.png|475]]
#### 4.2创建用户授权
![[Pasted image 20240722202320.png|400]]
![[Pasted image 20240722202351.png|525]]
把用户添加到项目里面
![[Pasted image 20240722202700.png|500]]
#### 4.3上传镜像
##### (1)登录仓库
docker login # 回车默认登录dockerhub
docker login harbor.linux.com  # 需要添加解析
docker服务器默认走443端口，需要证书认证，需要再docker服务器上面添加证书
```bash
mkdir /etc/docker/certs.d/harbor.linux.com -p
scp root@192.168.140.11:/opt/ssl/server.crt /etc/docker/certs.d/harbor.linux.com/
docker login harbor.linux.com
```

##### (2)为镜像打标记
docker tag  （类似于给镜像改名字）
镜像的命名格式:
仓库名称/镜像名称:标记
docker tag websocket-server:1.0 harbor.linux.com/miaosha/websocket-server:1.0
##### (3)上传镜像
docker push harbor.linux.com/miaosha/websocket-server:1.0 
#### 4.4退出仓库
docker logout harbor.linux.com
### 5.harbor核心组件
单个harbor很容易成为单点故障
![[Pasted image 20240722150812.png|650]]

、

Database
	redis：存储前端用户产生的令牌
	harbor-db：关系型数据库，存放harbor仓库上数据；默认是postgreSQL/pgSQL


<span style="background:#affad1">registry</span>,任何一个镜像仓库最核心的功能就俩，一个上传一个下载，我们构建仓库早期的方案用的就是registry,我们的harbor部署好之后也是可以上传下载镜像的，这个功能就是由registry完成的，
<span style="background:#affad1">core service ,</span>提供三个作用，第一个作用就是提供一个webui 第二个作用就是给用生成token令牌的，通过用户名密码等webui后，能一直持续保证这个登录状态，镜像上传完成后，<font color="#ff0000">我们一样可以在这个webui上查看镜像的信息，这些信息是怎么显示到这个webui上的呢？</font>
第三个作用，就是由coreservice这个核心组件里面的webhook(钩子函数) 和 registry做交互来获取镜像的元数据信息，并且在webui上展示的
<span style="background:#affad1">logcollector</span>,采集日志
<span style="background:#affad1">job service</span>,harbor仓库你要做高可用，它自带了一种方案，就跟我们之前讲的数据库的主从复制一样，我们是一个harbor是单点故障，那我们可以弄两套harbor,弄两套harbor容易，但是要想真正起到高可用的作用，这两个harbor之间的数据需要是同步一致的(在web界面上支持一个复制管理的功能，这个就相当于是主从复制的功能)，将来可以在多个harbor之间做主从复制来同步数据，他们在做数据同步的时候，主要用的核心组件就是jobservice
<span style="background:#affad1">proxy</span>,做反向代理的，为后的webui，跟镜像的上传下载做反向代理的，会有一个nginx的容器与其对应
<span style="background:#affad1">database</span>，高可用的核心，主要更改的就是这个数据库
harbor主要用的数据库有俩，一个是redis,主要是用来存放前端用户产生的令牌，第二个叫harbor-db，一个关系型数据库，存放harbor仓库上数据，比如我们创建的用户密码，还有我们在harbor里创建的项目，给项目分配的权限等，这些数据都是要存到后端的关系型数据库里面的；harbor底层是不支持MySQL的，默认是postgreSQL/pgSQL
### 6.harbor高可用设计方案
核心思想
三个步骤
禁用其自带的数据库，配置连接第三方的库， 保证多个harbor间的数据同步
![[Pasted image 20240722205030.png|475]]
192.168.183.10 harbor仓库  
192.168.183.20 harbor仓库  
192.168.183.30 后端数据库、存储
从高可用的角度来说，多个harbor服务器个人用个人的数据库，没法同步数据，但是harbor也支持把自带的数据库给禁掉，连接外部数据库，因此从数据库的角度来讲，在harbor启动之前我们在外面单搞一个redis,一个pgsql数据库,所有harbor服务器的缓存数据都放到这个redis里，所有的数据都存放到同一个pgsql数据库里面，这样子从数据库的角度来讲，就可以实现harbor之间的数据共享了
第二方面，配置文件中定义了harbor默认用的持久化目录(/data,这里面存的就是我们上传的镜像)，所以从实际镜像的角度来讲，你想让他实现同步的话，我们可以在搞一套集中的存储(nas/san)，我们的默认的持久化目录还是用data，但是我们把这个目录挂载到后端的集中存储上，这样子就可以实现实际的镜像的持久化了
#### 6.1配置nfs作为harbor的数据目录
yum -y install nfs-utils
vim /etc/exports
/harbor_data	192.168.183.10(rw,no_root_squash) 192.168.183.20(rw,no_root_squash)
systemctl enable --now nfs-server
#### 6.2安装redis
作为harbor共享缓存
docker run -tid --name=harbor_redis --net=host --restart=always redis:latest 
#### 6.3安装postgreSQL
作为harbor的共享数据库
mkdir -p /pgsql/data
docker run -tid --name=harbor_pgsql -e POSTGRES_PASSWORD=redhat -e PGDATA=/var/lib/postgresql/data/pgdata -v /pgsql/data:/var/lib/postgresql/data/pgdata --net=host --restart=always postgres:12.2 

配置数据库
docker exec -ti harbor_pgsql bash
psql -h 127.0.0.1 -p 5432 -U postgres
create user harbor with password 'redhat';  # 给harbor仓库使用
create database harbor;
create database harbor_clair;
create database harbor_notary_server;
create database harbor_notary_signer;
grant all on database harbor to harbor;
grant all on database harbor_clair to harbor;
grant all on database harbor_notary_server to harbor;
grant all on database harbor_notary_signer to harbor;
pgsql默认只支持本地连接，所以要修改pgSQL的配置文件，允许远程主机(harbor仓库)连接
cd /var/lib/postgresql/data/pgdata
echo "host all all all trust" >> pg_hba.conf 

#### 6.4两台harbor仓库挂载nfs存储作持久卷
tail -n 1 /etc/fstab
192.168.140.13:/harbor_data	/data	nfs	defaults	0 0
mount -a 
#### 6.5编辑harbor配置文件
 禁用自带数据库，连接外部数据库
 vim harbor.yml
 ```bash title:harbor.yml  
 # Harbor DB configuration
# database:
  # The password for the root user of Harbor DB. Change this before any production use.
  # password: root123
  # The maximum number of connections in the idle connection pool. If it <=0, no idle connections are retained.
  # max_idle_conns: 50
  # The maximum number of open connections to the database. If it <= 0, then there is no limit on the number of open connections.
  # Note: the default number of connections is 1024 for postgres of harbor.
  # max_open_conns: 1000

// 配置harbor连接外部的pgsql
external_database:
  harbor:
    host: 192.168.140.10
    port: 5432
    db_name: harbor
    username: harbor
    password: redhat
    ssl_mode: disable
    max_idle_conns: 2
    max_open_conns: 0
  notary_signer:
    host: 192.168.140.10
    port: 5432
    db_name: harbor_notary_signer
    username: harbor
    password: redhat
    ssl_mode: disable
  notary_server:
    host: 192.168.140.10
    port: 5432
    db_name: harbor_notary_server
    username: harbor
    password: redhat
    ssl_mode: disable

//配置harbor连接外部redis
external_redis:
#   # support redis, redis+sentinel
#   # host for redis: <host_redis>:<port_redis>
#   # host for redis+sentinel:
#   #  <host_sentinel1>:<port_sentinel1>,<host_sentinel2>:<port_sentinel2>,<host_sentinel3>:<port_sentinel3>
  host: 192.168.140.10:6379
#   password:
#   # sentinel_master_set must be set to support redis+sentinel
#   #sentinel_master_set:
#   # db_index 0 is for core, it's unchangeable
  registry_db_index: 1
  jobservice_db_index: 2
  chartmuseum_db_index: 3
  trivy_db_index: 5
  idle_timeout_seconds: 30

```
#### 6.6启动harbor
```bash
./prepare
./install.sh 
另外一台harbor仓库配置参考上述
scp /usr/local/bin/docker-compose root@192.168.183.20:/usr/local/bin/docker-compose
docker-compose version
scp -r /opt/ssl* root@192.168.183.10:/opt/ssl
scp ~/prepare.tar root@192.168.183.10:~/
docker load -i prepare.tar
scp -r /work/harbor/ root@192.168.183.10:/work/harbor/
```
#### 6.7配置haproxy做harbor仓库的负载均衡
vim /opt/work/haproxy.cfg
我们在做调度的时候按理说是七层调度，<font color="#ff0000">mode类型因该写http,但我们建议写tcp,为什么呢？</font>
要写http也可以，但是麻烦在后端的两个harbor走的都是https协议，他走https协议就需要证书密钥，前端使用hproxy做负载均衡的时候，还要把那个证书密钥往haproxy服务器里面copy一份，要不然调度不过去，所以为了省事我们直接写tcp,但是我们端口可以写443，但是！！！普通用户不能用小于1024的端口所以要改一下，可以改成9443
```bash
frontend harbor
   bind 0.0.0.0:9443
   mode tcp
   use_backend harbor_server
backend harbor_server
   mode tcp 
   balance roundrobin
   server harbor01 192.168.183.10:443
   server harbor02 192.168.183.20:443
```

docker run -tid --name=harbor_haproxy -p 443:9443 -v /opt/work/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg --restart=always haproxy:latest 
客户端测试通过haproxy访问仓库 

## 十二.docker-compose
### 1.docker-compose安装
单机版的容器编排工具
```bash
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
mv docker-compose /usr/local/bin/ 
chmod a+x /usr/local/bin/docker-compose
docker-compose version                                 # 查看docker-compose版本
```
docker compose和docker的一个区别
用docker compose一样可以创建容器查看容器删除容器，一样可以干这些容器的常规操作
，但是我们是docker-compose是一个容器编排工具，所谓的编排就是指，他需要一个文件
叫做docker-compose.yaml,我们可以按照特定的语法，在这儿文件中规定好我们要创建的容器的名字，用的什么进项，需要传递什么参数，环境变量等。
然后我们可以运行一个叫做docker-compose up -d 的命令，这个命令会在你的当前目录下，去找这个叫docker-compose.yaml的文件，找不到会报错，找到之后，就会按照这个文件里面的描述，把我们需要的容器批量的全部创建出来
他对应的还有一个指令叫做docker-compose down,这个命令的作用也是会在你当前的目录下找一个叫做docker-compose.yaml的文件，把这个文件里面所描述的所有容器，先全部stop停掉，然后再全部删掉
<font color="#ff0000">为什么说docker-compose是单机版的容器编排工具呢？</font>
这是应为，docker-compose不能跨机器编排，
k8s也是一个容器编排工具
多个不同机器上的docker统一编排，就需要集群版的容器编排工具<span style="background:#affad1">k8s</span>
### 2.docker-compose使用流程
![[Pasted image 20240723092736.png]]
没有yml文件情况的报错
version: '3'     docker-compose和docker-api对接的版本，真正的删除创建操作本质上还是docker做的docker-compose只是调用docker
services:        下面写你要创建的容器信息
![[Pasted image 20240723093827.png|625]]
docker-compose up -d
![[Pasted image 20240723093858.png]]
会默认创建一个网络(nat类型)
docker ps -a
![[Pasted image 20240723094003.png]]
注意命名规则：当前目录_services名称_数字

### 3.docker-compose常用的指令
#### 3.1整体结构
```yaml
version: '3'
services:
    服务名称:
         容器选项:
    服务名称:
         容器选项:   
networks: 定义容器网络信息
volumes: 定义数据卷
```
#### 3.2常用选项
```bash title:常用选项
image: 镜像名称
volumes: 
	- 物理目录:容器目录 
	- 物理目录:容器目录 
command: 
	-"shell命令"
	-"shell命令"
```
![[Pasted image 20240723101800.png]]
docker run 创建能够默认运行是因为，-t -i 提供了一个终端
没有终端怎么执行/bin/bash
这就是docker-compose编排创建的时候，centos:7的容器虽默认执行bash但是无法启动的原因，所以要明确指定一个命令，比如sleep 3600 ,这样子才会正常运行
```bash title:常用选项
links: - 容器名称:别名              # 容器名称写的是服务名称
ports:                                        # 发布容器服务
	- 物理机端口:容器端口 
	- 物理机端口:容器端口 
expose: - 端口                          # 指定容器的服务端口
environment:  
	key: value 
	key: value
depends_on:                             # 定义容器依赖关系,启动顺序
	- 容器名称 
	- 容器名称
depoly:                                      # 定义容器的副本数
	replicas: 3 
    resources:                         # 做资源限制
		limits:                        # 硬限制 最高资源
			cpus: "0.50" 
			memory: 50M 
		reservations:            # 软限制 最低资源
			cpus: "0.25" 
			memory: 20M
healcheck:                                # 容器进行健康状态检测
	test: ["CMD", "curl", "-f", "http://localhost"] 
	interval: 10s 
	timeout: 10s 
	retries: 3 
	start_period: 40s              # 定义健康状态检查开始的时间
networks_mode: 'host'             # 定义容器网络模式
```
### 4.配置nginx负载均衡案例
nginx tomcat tomcat


