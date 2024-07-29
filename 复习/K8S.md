# Kubernetes
## 一.Kubernetes概述(<font color="#d99694">完整</font>)
### 1.kubernetes是什么
有谷歌公司基于go语言开发的集群版的容器编排工具
简称K8s，K8s就是一个集群
管理容器的工具 docker docker-compose kubernetes 
三五个机器部署一套k8s集群出来，所有的容器都是创建在集群里面的
K8s官网：kubernetes.io
<span style="background:#affad1">用K8s管理容器就是为了让容器的管理变得更简单更加高效</span>

### 2.Kubernetes作用/优势
#### (1)自我修复
一旦检测到我的容器挂了以后，他会自动给我新建出一个一摸一样的容器出来(一摸一样！)
如果坏的是数据库呢?
数据库最重要的是数据，K8s针对数据库挂了，会自动创建一个新的数据库容器，然后自动连接数据存储设备
#### (2)滚动更新
分批次更新
假设业务服务器有四个，分批次更新就是可能先把新版本更新到两个服务器上，让新版本先运行一段时间，没错后，在接着更新其余的业务服务器，这样子就算新版本有bug也不会影响所有的终端用户
#### (3)支持服务发现和负载均衡
自动做负载均衡
四个容器部署好了以后，跑一样的业务，传统我们肯定要通过haproxy或者ngxin做负载均衡，但是有了K8s以后， 只要你创建了多个一莫一样的容器，k8s就会在这个多个容器上自动做负载均衡，k8s集群接收到用户的访问量之后，他会把这个访问量自动分担到相同的容器上去。
他在做负载均衡的时候，本质上k8s调用的就是<span style="background:#affad1">linux内核里面的lvs</span>
#### (4)支持存储编排
也就是说K8s支持多种类型的存储
#### (5)支持水平扩展
做一些阈值的控制，提升资源的利用率
例如：只要cpu的是使用率超过了百分之70%,会自动的去帮我扩展这个容器的数量，等着cpu的使用率降下去了，就会自动的删除多余的容器

总而言之，就是让容器化的应用部署变得更简单，高效
现在很多大厂都对K8s做了二次封装
<font color="#ff0000">kubernetes和docker有什么区别？</font>
## 二.kubernetes核心组件(<font color="#d99694">完整</font>)
![[Pasted image 20240723152208.png]]
### 1.核心节点
<font color="#f79646">master节点</font>(主节点)
负责正整个集群的管理操作(集群状态的监控，维护，扩容，缩容)
<font color="#f79646">node节点</font>(工作节点)
至少一个node节点，多了不限，运行容器的节点
<font color="#f79646">etcd数据库节点</font>  
高性能的键值对数据库，作为K8s集群的后台数据库使用，存储集群的所有数据
至少是一个三节点的高可用集群，etcd节点是k8s的核心，要按时做备份
<font color="#4bacc6">etcd数据库怎么做备份(以快照的方式 )？</font>
<font color="#f79646">kubectl</font>
客户端命令行工具和docker类似
### 2.核心组件 | <span style="background:#affad1">面试必聊</span>
Kubernetes 1.25 版本开始，不再依赖 Docker 作为容器运行时。使用containerd
#### 2.1 Master节点的组件 
说是组件，启动的时候就是一个一个的进程
<font color="#f79646">kube-apiserver</font>
(1)负责接收客户端操作请求、认证授权(安全角度)
(2)负责与etcd数据库交互，~~K8s所有的数据都是在etcd里面~~，K8s里面所有的数据都是要存在etc里面，谁存进去的？<font color="#f79646">apiserver这个组件</font>
我们想查看所有容器的状态，但是我们能看到的额所有数据都是从etcd的数据库里面来的，那谁帮我们去etcd数据库里面取数据呢？<font color="#f79646">apiserver这个组件</font>
(3)负责接收工作节点的注册请求
apiserver接收到一个创建容器的请求，这个请求肯定是要创建在某一个工作节点上，假设你是master节点，你是怎么知道你的集群里面有哪些工作节点，这些工作节点的状态是不是都是好的呢？然后你还需要知道工作节点的ip,知道这些信息以后最终才能找到工作节点，才能把真正的容器创建出来。
我们在搭建集群的时候，首先我们要先把这个主节点搭好，然后再去部署工作节点，工作节点上的服务(kublet)起来以后，他会主动联系apiserver注册自己，把自己的各种信息告诉apiserver，然后apiserver把这些信息存入到etcd数据库
<font color="#f79646">kube-scheduler 调度器</font>
选择一个合适的工作节点来运行容器
<font color="#f79646">kube-controller-manager 控制器管理器</font>
是一个编译好的二进制程序，他把所有的控制器全都编译好编辑进去了；负责管理k8s集群内部中所有的控制器的(无状态、有状态的容器等)
#### 2.2 Node节点的组件
<font color="#f79646">容器管理引擎</font>
可以是docker, containerd
以前k8s和docker对接的时候k8s产生的数据格式底层docker不支持
所以以前k8s和底层docker进行配合的时候中间需要有个转换，必须有个翻译所以影响效率，所以k8s从1.25版后弃用docker，改用containerd，性能效率考虑
<font color="#f79646">kubelet</font>
负责调用工作节点的容器引擎进行容器的整个生命周期管理(增删改查)；向api server发送注册请求
与openstack里面的nova-computer是一样的
<font color="#f79646">kube-proxy</font>
负责服务发布、负载均衡(调用Linux内核的lvs)
一些复杂的业务，可能还会涉及到物理机上面写一些防火墙规则，做一些数据过滤

## 三.k8s创建容器的流程(<font color="#d99694">完整</font>)
<font color="#f79646">1.创建容器的请求首先发给apiserver</font>
apiserver接收到我们创建容器的请求，对我们进行身份验证，鉴权通过之后，他来接收我们创建容器的请求
apiserver拿到我们创建容器的请求之后，他肯定能够知道我们需要创建的容器的各种信息，
<font color="#f79646">2.然后apiserver会把容器的创建信息写道etcd数据库里面去</font>
比如存着：<span style="background:#affad1">容器名xxxx 镜像名xxx (创建请求)</span>
然后这个时候schedule这个调度会干什么呢？
<font color="#f79646">3.schedule会周期性的询问apiserver你有没有请求需要我调度</font>，apiserver接收到这个询问之后，他就回去etcd数据库里面去查，肯定能够查到数据库里面存在着一个创建请求，然后他会拿着这个请求返回告诉给这个调度
调度现在就知道了有一个创建的请求需要调度
<font color="#f79646">4.然后schedule就会按着一定的算法选择一个工作节点</font>，选择到合适的工作节点之后
<font color="#f79646">5.然后scheduler会把这个工作节点的ip和端口告诉apiserver</font>
说我帮你选择一个容器，apiserver拿到这个工作节点的ip等信息后，<font color="#f79646">6.apiserver又会把这些信息存放到etcd数据库里面，</font>会跟之前的创建请求形成一个对应关系
<span style="background:#affad1">容器名xxxx 镜像名xxx (创建请求)</span><span style="background:rgba(136, 49, 204, 0.2)">--->node02</span>

<font color="#f79646">7.工作节点的kubelet会不停的找apiserver去问，你这边有没有需要我真正帮你创建的容器，</font>
apiserver接收到询问之后，又回去etcd库里面去查有没有 <span style="background:#affad1">容器名xxxx 镜像名xxx (创建请求)</span><span style="background:rgba(136, 49, 204, 0.2)">--->node02</span>这样子的创建关系存在，
<font color="#f79646">8.apiserver查到相应的信息之后，会把这个信息返回给kubelet</font>
kubelet拿到apiserver返回的信息之后
<font color="#f79646">9.这个时候kubelet才会调用底层的Docker Engine 最终把这个容器创建出来</font>
创建出来之后kubelet再把建好的容器信息返回给apiserver，
<font color="#f79646">10.然后apiserver再把信息存到etcd数据库里</font>
k8s底层设计的时候，为什么不用关系性数据库呢？
应为Nosql类型的数据库速度要比关系型数据库快的多得多


## 四.部署K8s1.29集群(<font color="#d99694">完整</font>)
两种部署方式
<span style="background:#affad1">二进制包的方式</span>
(非常提升个人能力)，整个集群里面的所有组件都需要手动安装，所有的组件都有相应的配置文件，所有的配置文件都需要手工编写，而且配置文件根本就没有，需要vim新建，一行一行手写，每个组件还需要手写启动脚本，k8s里面所有的组件之间通信，为了保证数据安全，都是需要证书的，生成证书用的还不是openssl 而是cfsl，第一你需要生成组件之间通信所需要的所有证书，真正生成证书的时候不报错，所有组件之间对接的时候检测到你的证书不对，才开始报错

<span style="background:#affad1">自动化的部署工具</span>
kubeadm,minikube
优势：简洁方便，原始版本的自动化部署工具不支持master节点的高可用，master节点容易成为单点故障，所以很久以前都用二进制包的方式部署K8s
但是现在对这个自动化部署工具做了优化升级，解决了不支持master节点高可用的缺陷

环境规划
192.168.140.21 K8s-master.linux.com     2u4G
192.168.140.22 K8s-node01.linux.com    2u4G
192.168.140.23 K8s-node02.linux.com    2u4G
### 1.主机环境配置
（所有的主机都要做）
#### 1.1 关闭防火墙selinxu时间同步 
```bash
ntpdate ntp.aliyun.com
crontab -e
*/30 * * * * /usr/sbin/ntpdate ntp.aliyun.com &> /dev/null
crontab -l
```
#### 1.2 所有主机配置ssh免密
```bash
ssh-keygen -t rsa
mv /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys
scp -r /root/.ssh/ root@192.168.140.22:/root/
scp -r /root/.ssh/ root@192.168.140.23:/root/
```
#### 1.3主机名解析
192.168.140.21 k8s-master.linux.com
192.168.140.22 k8s-node01.linux.com
192.168.140.23 k8s-node02.linux.com
```bash
for i in 22 23  
> do 
> scp /etc/hosts root@192.168.140.$i:/etc/hosts 
> done
```
#### 1.4 所有主机关闭<span style="background:#affad1">swap</span>
<span style="background:#affad1">一旦用swap虚拟机的性能就会急速下降</span>
free -m    # 查看swap
![[Pasted image 20240724200253.png]]
```bash
关闭swap
swapoff -a                           临时关闭
sed -ri '/swap/d' /etc/fstab  永久关闭
free -m
```
![[Pasted image 20240724200333.png]]

#### 1.5 <span style="background:#affad1">所有主机修改内核参数</span>
```bash
vim /etc/sysctl.d/k8s.conf
	net.bridge.bridge-nf-call-iptables=1       
	net.bridge.bridge-nf-call-ip6tables=1
	net.ipv4.ip_forward=1
	vm.swappiness=0                                       # 从内核层面关闭swap
	vm.overcommit_memory=1
	vm.panic_on_oom=0
	fs.inotify.max_user_instances=8192
	fs.inotify.max_user_watches=1048576
	fs.file-max=52706963
	fs.nr_open=52706963
	net.ipv6.conf.all.disable_ipv6=1
	net.netfilter.nf_conntrack_max=2130720
	25:24 老师讲解了部分参数的意思
```
net.bridge.bridge-nf-call-iptables=1   
net.bridge.bridge-nf-call-ip6tables=1
为了把容器上的数据流量接收道德请求啥的，各种数据流量全都导入到物理机上的iptables上好借助物理机的iptables做服务发布，包括做一些数据的过滤
net.ipv4.ip_forward=1
开启路由转发
sysctl -p /etc/sysctl.d/k8s.conf
加载内核模块
modprobe br_netfilter
modprobe ip_conntrack
为了容器往iptables上面倒流能够倒流成功的
#### 1.6 安装基础软件依赖
yum install -y wget jq psmisc net-tools nfs-utils socat telnet device-mapper-persistent-data lvm2 git tar zip curl conntrack ipvsadm ipset iptables sysstat libseccomp

#### 1.7 加载lvs负载均衡策略
vim  /etc/sysconfig/modules/ipvs.modules
```bash
#!/bin/bash
#
modprobe -- ip_vs         #一些轮询算法
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack
```
bash /etc/sysconfig/modules/ipvs.modules
lsmod | grep -e ip_vs -e nf_conntrack
![[Pasted image 20240724201453.png]]

### 2.安装容器虚拟引擎软件
所有主机
<font color="#ff0000">为什么所有主机都要装呢？</font>
我们之前将这个架构的时候，我们说工作节点是将来真正跑容器的，他身上需要容器管理软件需要有容器引擎，但是实际部署的时候master主节点上也需要有容器引擎，所有机器上面都需要有容器引擎。
我们这个集群的部署方式用的是kubeadm,我们之前讲的框架中所说的那些apiserver,scheduler调度器，etcd数据库所有的节点组件本身就是以容器的方式运行的，所以我们所有的机器上面底层都需要有容器管理软件

k8s 1.25版本后弃用docker， 改用containerd
#### 2.1安装docker-ce
vim /etc/yum.repos.d/docker-ce.repo
```bash title:docker-ce.repo
[docker-ce-stable]
name=Docker CE Stable - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-stable-debuginfo]
name=Docker CE Stable - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/stable
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-stable-source]
name=Docker CE Stable - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/stable
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test]
name=Docker CE Test - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test-debuginfo]
name=Docker CE Test - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/test
enabled=0
gpgcheck=1
yum install -y docker-cegpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test-source]
name=Docker CE Test - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly]
name=Docker CE Nightly - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly-debuginfo]
name=Docker CE Nightly - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly-source]
name=Docker CE Nightly - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

```

```bash
yum install -y docker-ce
vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://rywdmoco.mirror.aliyuncs.com"]
}
systemctl enable --now docker
```
#### 2.2 安装containerd
yum install -y containerd.io 
生成一份默认的配置文件
```bash
containerd config default > /etc/containerd/config.toml
vim /etc/containerd/config.toml 
SystemdCgroup = true
sandbox_image = "registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.6"
systemctl enable --now containerd.service 
```
https://www.jianshu.com/p/948fe79f3619
![[Pasted image 20240727145115.png|625]]

#### 2.3 安装kubeadm/kubectl/kubelet软件
```bash
vim  /etc/yum.repos.d/k8s.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes-new/core/stable/v1.29/rpm/
enabled=1
gpgcheck=0
```
yum install -y kubeadm-1.29.1 kubectl-1.29.1 kubelet-1.29.1 

#### 2.4 启动kubelet
```bash
vim  /etc/sysconfig/kubelet
KUBELET_EXTRA_ARGS="--cgroup-driver=systemd"
systemctl enable --now kubelet
```
kubelet是调用底层的容器软件去真正创建销毁容器的，工作节点是真正运行容器的他身上肯定需要kubelet
<font color="#ff0000">为什么主节点上也需要kubelet呢？</font>
用kubeadm这个自动化工具部署集群的话，所有的组件本身就是以容器的方式运行的，master节点上的容器能建出来肯定也要有kubelet去调用容器引擎去帮我创建出这个几个容器
#### 2.5 配置<span style="background:#affad1">crictl客户端工具</span>
```bash
vim  /etc/crictl.yaml
	runtime-endpoint: unix:///run/containerd/containerd.sock
	image-endpoint: unix:///run/containerd/containerd.sock
	timeout: 10
	debug: false
systemctl restart containerd.service 
```
让crictl这个命令去对接一下containerd服务的套接字文件(containerd.sock)
crictl是个客户端，containerd是个服务的
C/S架构
endpoint端点 跟url一个意思，指定containerd这个服务的套接字文件在那里存在，联系本地套接字文件用的协议就叫做<span style="background:#affad1">unix</span>
主机上任何两个进程通信的时候，一个客户端一个服务的这叫C/S架构；有两种通信方式
(1)走tcp网络
(2)走本地的套接字文件
### 3.部署k8s集群
#### 3.1 在Master节点创建集群初始化文件
```bash
kubeadm config print init-defaults > init.yaml
advertiseAddress: 192.168.140.10 # kube-apiserver监听地址
name: k8s-master.linux.com           # master节点的名称， 控制面板
imageRepository: registry.cn-hangzhou.aliyuncs.com/google_containers # 镜像仓库地址
kind: ClusterConfiguration
kubernetesVersion: 1.29.1		         # k8s集群版本，和kubeadm版本一致
networking:
  dnsDomain: cluster.local
  podSubnet: 10.88.0.0/16	         # pod网段，给容器分配IP的网段
  serviceSubnet: 10.96.0.0/16
```
#### 3.2 初始化集群
<span style="background:#affad1">这里所说初始化就集群就是把master节点上的所有组件都给你部署好</span>

建议提前导入相应的镜像, docker导入的镜像是在文件系统中(磁盘)  
containerd导入的镜像在k8s.io命名空间
ctr -n k8s.io image import xxxxx.tar 
master节点
![[Pasted image 20240725091816.png]]
![[Pasted image 20240725091850.png]]
node节点
![[Pasted image 20240725091914.png]]
kubeadm init --config=init.yaml  --ignore-preflight-errors=SystemVerification
![[Pasted image 20240725092007.png]]
#### 3.3 定义KUBECONFIG环境变量
<span style="background:#affad1">目的：为了让kubectl客户端工具可正常使用</span>
vim /etc/profile
export KUBECONFIG=/etc/kubernetes/admin.conf
source /etc/profile
#### 3.4 添加工作节点
在工作节点执行
![[Pasted image 20240725092316.png]]
![[Pasted image 20240725092346.png]]
#### 3.5 查看初始化后的状态
![[Pasted image 20240725092417.png]]
![[Pasted image 20240725092438.png]]
但是现在还存在网络通信的问题
主节点是一个单独的机器，工作节点也是一个单独的机器，这些容器是夸物理机分散存着的，我们可以通过flannel将网络打通，但是我们不用flannel，性能太垃圾，他所使用的网络规模是相对来说比较小的
第一为了考虑网络通信的性能，第二为了考虑将来集群规模的扩展，一般我们都用<span style="background:#affad1">calico</span>这个组件，功能和flannel类似，但是使用的规模要比flannel大的多
### 4.部署calico打通容器网络通信
calico基于BGP协议设计，这个协议一般用于跨省的这种大型的网络才会用这个协议
```bash
vim calico.yaml 
 - name: CALICO_IPV4POOL_CIDR
    value: "10.88.0.0/16"
```
这个calico.yaml文件从老师ftp下载的
事先导入镜像
```bash
ctr -n k8s.io image import calico_node_v3.27.0.tar 
ctr -n k8s.io image import calico_kube-controllers_v3.27.0.tar 
ctr -n k8s.io image import calico_cni_v3.27.0.tar 
```
kubectl create -f calico.yaml 

### 5.k8s集群部署完成
#### 5.1查看核心组件运行状态
![[Pasted image 20240725092848.png]]
#### 5.2 查看节点的运行状态
![[Pasted image 20240725092924.png]]


## 五. Kubernetes资源-pod(<font color="#d99694">完整</font>)
工作负载
### 1.namespace 命名空间
#### 1.1 命名空间介绍
docker使用命令空间主要是来实现资源隔离的
在K8s中也是用来实现各种不同类型资源之间的隔离的，通过这个命名空间可以对资源进行分组，不同业务的容器可以放置在不同的命名空间里面，方便管理

k8s搭建之后会默认提供四个命名空间
kubectl get ns  # 查看命名空间
![[Pasted image 20240725184034.png]]
如果在创建资源的时候我们不明确指定命名空间的话，默认使用的就是default这个命名空间
kubectl get pod
![[Pasted image 20240725184308.png]]
kubectl get pod -n kube-system  # -n 指定命名空间
kubectl get pod -A # 查看所有命名空间里的所有pod
![[Pasted image 20240725184525.png]]
#### 1.2 创建命名空间
##### (1)通过命令行创建
kubectl create ns game  
##### (2)通过yaml文件的方式(编排)
vim web.yaml
```bash
apiVersion: v1             # api版本
kind: Namespace       # 资源类型
metadata:                   # 元数据
	name: web
```
kubectl create -f web.yaml
k8s支持的资源类型很多，支持namespace，有状态的容器，无状态的容器，支持服务，支持存储，支持配置映射，每种资源对应一个api接口的版本
kubectl delete -f web.yaml  # 删除这个yaml文件里描述的资源
### 2.pod 
#### 2.1.pod介绍
非常重要的一类资源
后面我们在实际使用K8s的时候，很少直接操作pod本身，但是后续k8s后续的资源都和pod有关，一个pod可以当作一个容器(但是一个pod可以放多个容器)
<font color="#f79646">pod是k8s集群所能够管理的最小单位</font>
<font color="#f79646">相当于装载容器的箱子</font>
<font color="#f79646">实际情况：一个容器一个pod</font>
K8s搭好之后就是为了借助这个集群管理容器，但是K8s这个容器它设计的时候不会直接操作容器
<font color="#ff0000">那他在是怎么操作容器的呢？</font>
我们在创建容器的时候会自动创建一个pod，然后k8s会把我们真正要创建的这个容器自动给放到这个pod里面去，然后K8s在帮我们管理这个容器的时候，他并不会直接操作这个容器本身，他唯一能操作的就是这个pod 对于K8s来说他管理这个pod就相当于管理了里面的容器
<font color="#ff0000">K8s为什么要设计pod这个东西呢？</font><font color="#ff0000">k8s集群搭建出来就是为了管理容器，为什么他不直接管理容器非要设计出一个pod出来间接的管理容器呢？</font>
从K8s软件设计的角度讲，市面上常用的容器引擎软件有很多(docker,container,portman等)，要是让k8s直接去管理底层的容器，这就意味着它需要去对接多种容器引擎软件，出一款新的软件，k8s就需要对接代码就要修改，从软件更新的角度来说就太繁琐了。所以K8S设计了一个自己能直接管理容器的东西，这个东西就是pod，容器创建出来之后，我全都给你放到pod里面，我只要能够操作这个pod就行，我不需要关心底层中，这个容器是拿那个软件建出来的

刚才说一个pod中放一个容器，但是实际k8s通过pod帮我们管理容器的时候，一个pod中会有两个容器
从思想上说这个容器的ip,容器的挂载的持久化呀都是挂载到这个pod上的，但是实际上pod就是一个破空壳子，<font color="#ff0000">他凭什么能够接收ip,能够挂载目录,能够发布服务呀？</font>
其实我们在k8s里面创建一个业务容器，然后这个容器会被K8s扔到一个pod里面，这个时候呢K8s实际上会在这个pod里面自动的再创建一个容器！！！这个容器就是为了，能够通过dhcp获取ip挂载目录发布项目的，他自动创建这个容器用的镜像就是<span style="background:#affad1">pause</span>
![[Pasted image 20240725192828.png]]

#### 2.2 创建管理pod
创建pod就相当于创建容器
##### (1)创建pod
```bash title:创建pod
apiVersion: v1
kind: Pod
metadata:                                                         # 元数据
    name: test1-pod
    namespace: web                                      # 命名空间
spec:                                                                  # 容器信息
    containers:
        - name: test1-pod                            # 容器名
           image: centos:7
           imagePullPolicy: IfNotPresent      # 指定镜像的下载策略
           command:                                      #容器自动执行的命令
          - sleep
          - "3600"
```
kubectl create -f test1-pod.yaml
真正创建之前，在两个工作节点上导入镜像
##### (2)查看这个命令空间里面的pod
kubectl get pod -n web  
kubectl get pod -n web -o wide  # 查看更加详细的信息
##### (3)查看pod创建过程(可以用来排查错误)
kubectl describe pod test1-pod -n web
##### (4)查看日志
kubectl logs test1-pod -n web
##### (5)连接登录
kubectl exec -ti test1-pod -n web bash
##### (6)物理机pod间拷贝文件
kubectl cp file01 test1-pod:/file01 -n web
kubectl exec -ti test1-pod -n web ls /
![[Pasted image 20240725200107.png]]

#### 2.3 pod常用选项
![[Pasted image 20240725200737.png]]
##### (1)指定容器名称
name: xxxx
##### (2)指定镜像名称
image: xxxxx
##### (3)指定镜像下载策略
imagePullPolicy: [Always|IfNotPresent|Never]
	always:总是联网下载
	never:从不联网下再
	IfNotPresent:有就不下载没有就下载
##### (4)指定容器自动执行的命令
```bash
command:
- sleep
- "3600"
```
##### (5)指定命令的参数
```bash
command:
- sleep
args:
- "3600"
```
##### (6)容器中的服务端口
```bash
ports:
- containerPort: 80
```
##### (7)传递环境变量
```bash
env:
- name: 变量名称
   value: 值 
```
##### (8)资源限制
```bash
resources:
	requests:                         # 软限制
		cpu: "2000m"
		memory: "2G"
	limits:                               # 硬限制
		cpu: "4000m"          # m:毫核
		memory: "4G"
```
1000毫核 = 1核
##### (9)创建mysql容器
ctr -n k8s.io image import mysql57.tar
导入镜像
![[Pasted image 20240725203734.png]]
```bash
kubectl create -f pod-mysql.yaml
kubectl get pod -n web 
kubectl exec -ti mysql -n web bash
mysql -uroot -prehat
```
### 3.pod健康状态检测机制
#### 3.1 健康状态检测探针
做健康状态检查的两种方法
livenessprobe  
检测pod是否正常启动
readnessprobe  
检测pod是否能正常接收请求
#### 3.2 健康状态检测的方式
httpGet  
发送http请求，检测状态码200-400间，说明服务健康，否则不健康
![[Pasted image 20240725204813.png]]
tcpSocket  
针对所有tcp服务
6379redis端口
![[Pasted image 20240725205122.png]]
exec  
执行shell命令，判断命令的状态码
![[Pasted image 20240725205159.png]]
<font color="#ff0000">健康状态检查加不加有影响吗？</font>
其实没啥影响，但是必须要加！！
在一些集群环境下，尤其是负载均衡状态下
 ![[Pasted image 20240725210546.png]]
 检测这些业务好不好，是通过负载均衡器检测业务是否正常运行
 如果没有健康状态检查，负载均衡器在接受请求的时候有可能把这个请求分发到故障的业务服务器上面，导致客户访问不正常，如果有健康状态检查，负载均衡器就不再往故障机器上分发请求
## 六.Kubernetes资源-deployment无状态负载
#### 4.1 无状态负载介绍
一种特殊的pod
在k8s里面创建业务的时候，我们强烈不建议直接去构建pod
大多都是：要么创建无状态负载，要么创建有状态负载
直接去创建pod的话，K8s里面很多的高级功能pod不支持
比如：自我修复，负载均衡，滚动更新
所以我们需要通过无状态负载来创建pod
优势：<span style="background:#affad1">支持pod副本自动维护</span>，指定一模一样的容器有几个，有机器故障，副本数不够五个会自动创建直至副本数为5
<span style="background:#affad1">支持滚动更新</span>
<font color="#ff0000">这些功能是怎么实现的呢？无状态负载往外建pod他的流程是怎样的？</font>
将来我们从K8s里面创建出一个无状态负载，它会自动创建出来三样东西
这个无状态负载本身会被创建
还会创建一个<span style="background:#ff4d4f">副本集</span>
最后是多个pod
<span style="background:#affad1">副本集的作用：通过副本集帮我们真正的创建容器，通过副本集帮我们维护这些容器</span>
最终副本集会创建出几个pod
![[Pasted image 20240725212127.png|286]]
k8s内部有很多控制器，然后经常被人提到的最著名的控制器是<span style="background:#affad1">副本集</span>
图上的三样东西加在一起称之为无状态负载
假设集群里面有两个无状态负载(两个副本集)
![[Pasted image 20240725212507.png|475]]
<font color="#ff0000">从K8s设计的角度来说，他怎么知道那个RS是用来管理那批容器的？</font>
<font color="#ff0000">他是怎么在这个rs和实际的pod之间建立这个对应关系的？</font>
K8s非常重要的一个概念：<span style="background:#ff4d4f">lable：标签选择器</span> ；一个标签就是一个键值对的数据
所以创建pod的时候要给pod分配标签 
例如：<span style="background:#affad1">app：web</span>
然后再在rs上面指定一个标签 <span style="background:#affad1">app：web</span>
这样子这个rs就回去维护标签为<span style="background:#affad1">app：web</span>的所有pod
包括滚动更新和副本的自动维护
<font color="#245bdb">k8s所有的资源他们之间建立关系的时候所有的资源都是靠标签来绑定的</font>
<span style="background:#affad1">使用场景：频繁更新的业务</span>
#### 4.2 创建deployment
vim deployment.yaml
![[Pasted image 20240725214032.png]]
ctr -n k8s.io image import nginx16.tar
kubectl create -f deployment.yaml
![[Pasted image 20240725214808.png]]
##### (1)查看无状态负载
kubectl get deploy
![[Pasted image 20240725214856.png]]
##### (2)查看rs副本集
kubectl get rs
![[Pasted image 20240725215025.png]]
kubectl get pod
![[Pasted image 20240725215043.png]]
##### (3)查看pod标签
kubectl get pod --show-labels
![[Pasted image 20240725215058.png]]
##### (4)验证副本维护
kubectl delete pod  test1-nginx-5d858b7fc5-88j4t
![[Pasted image 20240725215256.png]]出现了个新的

#### 4.3 deployment滚动更新
还需要多加一些参数才能实现滚动更新
![[Pasted image 20240725215909.png]]
maxUnavailable: 3 超过三个更新失败，那么就认为整个更新都是失败的
kubectl create -f deployment.yaml
![[Pasted image 20240725220154.png]]
kubectl get pod
![[Pasted image 20240725220221.png]]
##### (1)测试滚动更新流程
每次更新的时候都会新建一个rs，他会连以前的rs都更新掉
只要有改动就叫更新！！！！
![[Pasted image 20240725220802.png]]
换成1.18做更新
##### (2)执行更新
kubectl apply -f  deployment.yaml
![[Pasted image 20240725220921.png]]
##### (3)查看更新过程
kubectl describe deploy test2-nginx
![[Pasted image 20240725221059.png]]
##### (4)查看更新历史
kubectl rollout history deploy test2-nginx
![[Pasted image 20240725221308.png]]
##### (5)版本回退
kubectl rollout undo deployment test2-nginx --to-revision=1
![[Pasted image 20240725221437.png]]
回退的过程也是一个滚动更新
##### (6)暂停更新
kubectl rollout pause deployment test2-nginx

## 七.kubernetes资源-其他特殊pod(<font color="#d99694">完整</font>)
### 1.daemonset服务集
简称:DS
也是一类特殊的pod
特征：
通过daemonset创建出来的pod数量是和工作节点一致的，如果五个工作节点就会创建五个pod，每个工作节点肯定会运行一个pod
应用场景：<span style="background:#affad1">便于部署客户端/agent工具。</span>例如zabbix-agent，filebeat
创建pod
kubectl delete -f web.yaml
vim daemonset
![[Pasted image 20240726092843.png]]
kubectl create -f daemonset 
kubectl get pod
![[Pasted image 20240726092926.png]]
一共两个节点一共创建出来两个pod
### 2.job
特殊的pod，类似于Linux系统中的一次性任务
典型的针对公司里面测试的工作(临时性的任务)
特征：pod在执行任务时，状态为running，任务执行完毕后，他的状态就会变为completed
应用场景：运行临时性任务(测试)
kubectl delete -f daemonset 
vim job.yaml
![[Pasted image 20240726094349.png]]
不设置pod重启策略，这个pod就成死循环了
<span style="background:#affad1">restartPolicy有三种</span>
Always: 总是(默认) 无论是正常退出还是异常退出
Never：重启不启动
OnFailure:  只有pod异常退出时，k8s才会重启他

 kubectl create -f job.yaml 
 ![[Pasted image 20240726095127.png]]
 但是状态是error
 kubectl logs 
![[Pasted image 20240726095648.png]]
日志这个提示(暂未解决)
最后发现是yaml文件写错了
![[Pasted image 20240726100035.png]]
$i; 应该这么写
![[Pasted image 20240726100110.png|575]]
成功解决！！！！
![[Pasted image 20240726100302.png]]
### 3.cronjob
笔记需要补充
特殊pod，类似于周期性计划任务
应用场景：执行重复行的任务(备份、清理日志、巡检脚本)
每执行一次就会创建一个新的pod(一定要设置限制)
**创建cronjob资源**
vim cronjob.yaml
![[Pasted image 20240726102417.png|650]]

![[Pasted image 20240726100645.png]]
过一会观察变化
![[Pasted image 20240726100745.png]]
旧的pod删除，有出现一个新的pod
```bash
successfulJobsHistoryLimit: 1 
failedJobsHistoryLimit: 1
```
这两个参数必须要加，不然过不了多久集群的资源就会被慢慢的耗光
它们定义了系统保留成功和失败作业历史记录的数量限制
因为cronjob每执行一次就会创建一个新的pod(一定要设置限制)
不限制的话存储空间会越来越少

有状态负载讲存储的时候在做补充

## 八.kubernetes资源-service服务(<font color="#d99694">完整</font>)
service相当于一个业务的访问入口
类似于负载均衡器的作用，不是单独用的，要配合着pod
### 1.service服务介绍
假设我们有一个集群要跑一个网站，根据我们之前讲的就是创建一个无状态负载pod，然后把这个网站真正的部署起来，对于客户端而言，他最终带要能访问我们的网站但是你<font color="#ff0000">不能让客户端直接 访问这个pod，为什么呢？</font>

应为这个pod的ip也是k8s通过dhcp自动分配的，自动分配的说不准什么时候就会变<span style="background:#affad1">，这是第一个原因；第二个原因，</span>k8s针对无状态负载有自我修复的功能，pod异常退出了，k8s会帮我们重新创建这个pod,假设一共有两个工作节点，重新创建的pod不能确定会固定在那个节点上，所以对于客户端也没有办法直接通过pod去访问；<span style="background:#affad1">第三个原因</span>假设我们创建了多个一样的pod搭建业务，多个一样的pod就是为了负载均衡，如果客户端能够直接访问pod的话，负载均衡就无法实现的，<span style="background:#affad1">pod本身是没有负载均衡功能的</span>
<span style="background:#9254de">一是从负载均衡的角度来讲，二是从ip的角度来讲，客户端是无法直接访问pod的</span>

<font color="#ff0000">客户端是无法直接访问pod那k8s是怎么 设计的呢？</font>
将来我们在集群里面创建了一些pod，这些pod里面把业务真正部署起来之后，客户端怎么访问的呢？
所以k8s设计了<span style="background:#ff4d4f">services</span>（服务）
我们要想pod里面真正跑的网站的(80端口)让客户端能够访问到，我们要配合着pod在其前端建一个服务，这个service就相当于业务的入口，客户端访问service，再由service接收请求，然后把请求真正转到pod上去(有点类似于反向代理)
<span style="background:#b1ffff">任何一个实际跑在pod里面的业务都要对应这一个service</span>，pod和service从实际业务的角度来讲是配合着使用的

![[Pasted image 20240726190026.png]]
<span style="background:#40a9ff">总结：services是业务的访问入口，类似于反向代理的作用；同时带有负载均衡的作用，但是负载均衡能不能实现，你需要看后端 pod的数量</span>

某一个service接收到请求之后(他只负责接收请求)，他接收到请求之后，要把这个请求真正的转到后端的pod去处理，<font color="#ff0000">那他是怎么知道改吧请求转到那组pod上的呢？</font>
通过<span style="background:#affad1">标签选择器</span>在service和pod建立联系
![[Pasted image 20240726190351.png]]
### 2.kubernetes和kube-dns服务
<span style="background:#b1ffff">service简称svc</span>
创建K8s时候的初始化文件
![[Pasted image 20240726190651.png]]
其实k8s集群创建好之后就自带两个service
kubectl get svc -A
![[Pasted image 20240726190908.png]]
#### (1)kubernetes
第一个服务kubernetes，就是整个集群创建的时候自动建的一个服务
作用：K8s搭好之后，我们管理员通过kubectl向K8s发送操作请求的时候是由这个kube-apiserver接收的。
但是从服务的角度来说，我们通过kubectl这个客户端命令干的所有的事情都是由kubernetes这个服务接收处理的。
#### (2)kube-dns服务发现机制
第二个服务kube-dns，
kubectl get pod -A 
![[Pasted image 20240726191503.png]]
集群搭建好之后会有两个叫做coredns的pod,kube-dns服务对应的就是这样两个pod
作用：
k8s检测到我们在集群里建了一个服务，这个服务会有个ip,第二会有个名字，然后这个服务从建出来的那一刻开始，它就会自动的去联系kube-dns
就会在dns内部形成一条记录，记录着这个服务器名字是什么，ip是什么
刚才说服务的ip会变，但是服务的名字和IP的对应关系是K8s自动的注册到dns服务器里面的，这就意味着整个都是由k8s集群管理的，哪怕服务的ip变了dns里面的记录也会随之自动更新
<span style="background:#affad1">你在集群里面创建的任何一个pod，一但建出来之后pod网卡上的dns会自动就是这个dns服务</span>，这就是为了pod借助dns去解析其他的服务，从而进行访问的
![[Pasted image 20240726193822.png|450]]
按照官方来说这就叫，<span style="background:#affad1">服务发现机制</span>
### 3.服务名
我们在k8s里面创建的服务会有ip,但是这个ip也是dhcp分配的，既然是dhcp分配的就意味着这个ip也会变，k8s有自我修复苟能，如果k8s检测到这个服务如果异常挂了，k8s也会帮我们重新创建这个服务，所以这个ip也有可能变。
pod前端的service身上虽然有ip但是我们作为客户端真正访问的时候，也是不用这个ip的，我们只能<span style="background:#b1ffff">靠服务的名字访问！！</span>(服务名)
<span style="background:#40a9ff">docker里面好像也涉及到，翻翻回顾一下</span>

服务的命名格式
服务名称.命名空间.svc.k8s集群的域名
示例：在web的命名空间，服务名为：test-service
test-service.web.svc.<span style="background:#affad1">cluster.local</span>
<span style="background:#affad1">cluster.local</span>:如果这个K8s集群的域名没有改过的话，这个域名就是默认域名
![[Pasted image 20240726192519.png]]

服务名支持简写，但是要分情况
如果你的网站的pod和你要联系的service在同一个命名空间，我就只要写前面的服务名就可以了 <span style="background:#affad1">服务名称</span>
如果你的网站的pod和你要联系的service不在同一个命名空间，也支持简写，但是要写到<span style="background:#affad1">服务名称.命名空间</span>
从网络的角度来讲，我们能通过一个名字就能访问到服务，或者访问到其他什么东西，就需要dns解析，就是kube-dns服务的作用！！！
### 4.服务的类型
关于服务的概念清楚之后，然后关于k8s的服务我们还需要知道的就是<span style="background:#affad1">服务的类型</span>
默认有三种类型之分

| 服务类型        | 作用                                    |
| ----------- | ------------------------------------- |
| ClusterIP   | 默认的服务类型  <br>该服务只能在k8s集群内部被访问         |
| NodePort    | 用于发布服务，暴露端口                           |
| LoadBalance | (性能最好的)用于发布服务  ，只能在云平台使用，配合云上的负载均衡器使用 |
#### (1)ClusterIP  
分为两种
有ClusterIP的服务
集群内部服务A访问服务B时，DNS组件会将其解析到服务器B对应的IP上
无ClusterIP的服务(从来不用)
集群内部服务A访问服务器B时，由于服务B没有IP，dns会直接解析到对应的pod的ip地址上，这样子就是去了负载均衡的作用，如果这个pod挂了，业务就访问不到了
以下演示有ClusterIP的服务
![[Pasted image 20240726195637.png|450]]
要给yaml文件里面可以写pod的创建，也可以写svc，数据卷的创建
selector 标签选择器 指定后端pod的标签
kubectl create -f yaml文件名
![[Pasted image 20240726200123.png|500]]
![[Pasted image 20240726200147.png|500]]
测试
创建客户端
![[Pasted image 20240726200259.png|500]]
![[Pasted image 20240726200547.png]]
访问验证
![[Pasted image 20240726200626.png|600]]
两台nginx pod改一下网页内容测试负载均衡
![[Pasted image 20240726200749.png|600]]
查看client客户端的dns解析
![[Pasted image 20240726200934.png|600]]
无法ping
![[Pasted image 20240726201109.png|600]]
但是由此也能看出dns解析成功了

#### (2)NodePort类型
(类似于四层调度)
不同的端口调度到不同的服务上，对外暴露的是端口号
K8s原生本身就支持的一种服务
缺点：如果公司的业务涉及到几十个甚至上百个业务，对外发布一个就会有一个端口，端口多了不便于记忆跟管理
服务发布
![[Pasted image 20240726202651.png|450]]
通过发布端口可以正常访问
	![[Pasted image 20240726202820.png]]
<font color="#ffc000">但是需要注意的是：</font>
kubeadm部署的集群  
可以通过集群中任意节点的IP访问服务
二进制方式部署的集群  
通过查看pod所在的物理机访问服务
## 九.kubernetes资源-ingress(<font color="#d99694">完整</font>)
### 1.ingress介绍
ingress(相当普及)，类似于(七层调度)
对k8s集群来说，就是一个插件，需要单独安装
借助这个插件给我们提供的功能<span style="background:#affad1">一样可以是实现服务的对外发布</span>

假设有个服务1，他对外发布的时候，最终实际的体现就是主机名
我们可以把k8s里面的某一个服务通过主机名的方式给他发布出来
服务1——>主机名 AA.linxu.com
服务2——>主机名 BB.linxu.com
### 2.ingress的实现方式
3种

| 名称              | 作用                                                       |
| --------------- | -------------------------------------------------------- |
| haproxy-ingress | 通过haproxy实现                                              |
| nginx-ingress   | 核心的功能通过nginx这个软件实现                                       |
| traefik-ingress | 也是通过七层调度的思想 通过traffic这个软件实现，工作机制跟nginx几乎一样，但是性能是nginx的十倍 |
#### (1)haproxy
haproxy也好nginx也好它本身既是四层调度，也可以做七层调度
haproxy里面功能的核心思想:
haproxy里面有acl功能，可以匹配客户端请求,如果按服务发布的方式来说，haproxy内部的实现就是通过所写的acl检测,如果发来的请求主机名是AA就把请求调到集群的服务一上
#### (2)naginx
nginx拿什么匹配请求？
通过nginx配置文件中
location{}所写的东西，可能里面借助的就是proxy_pass，nginx的反向代理功能，如果你的主机名是a就代理到服务1上如果是b就代理到服务2上
### 3.通过nginx的方式练习ingress的使用
用nginx-ingress实现内部服务的发布需要一个核心组件
#### (1)核心组件ingress-controller
ingress-controller ingress控制器
部署完之后，这个叫ingress-controller的东西表现在K8s里面就是一个pod,这个pod里面跑的服务叫做ingress-controller

假设我们的集群里面存在这三个服务
服务123，一个服务对应一个主机名
这个叫nginx-ingress的东西，内部核心形成的可能就是这样的配置
他里面的核心就是nginx的反向代理
你发布完一个服务，他可能就会形成这个一段配置
![[Pasted image 20240726212421.png|525]]、
<font color="#ff0000">这个nginx-ingress真正的想要形成这个配置，最起码他带有一种机制能够检测到我的k8s里面有那些服务?</font> 
就这些服务本身的ip是什么，本身的端口是什么，这是第一。第二k8s里面创建个服务，这个服务本身有一个名有个ip，有个端口，一旦这个服务挂了，K8s会重建这个服务，这样子服务的ip就有可能发生变化。nginx里面形成的那个反代理需要写的那个ip 也要自动跟着变
<span style="background:#affad1">所以ingress需要设计这种机制就是</span>
第一，需要能够检测到K8s里面有那些服务
第二，如果我的集群里面的服务本身发生变化的话，他也带需要能够及时的知道，并且自动去更新他生成的这种location的配置，检测到某个服务删除了，自动生成的配置也应该自动删除
这样子才能够一直确保我们的服务发布一直是正常的
所以要设计一种核心，这个核心就是<span style="background:#affad1">ingress-controller</span>
<span style="background:#40a9ff">这个控制器主要就是和k8s内部的apiserver交互来识别集群内部的服务状态信息来自动生成发布规则</span>
将来客户端通过BB的主机名访问我们集群里面的服务的时候，也得通过这个ingress-contorller控制器来，应为这个bb主机名和服务二的对应关系本身就是这个控制器生成的
### 4.ingress的部署
事先导入镜像
ctr -n k8s.io image import k8s.dockerproxy.com_ingress_nginx_controller_v1.9.6.tar 
ctr -n k8s.io image import k8s.dockerproxy.com_ingress_nginx_kube_webhook_certgen_v20231226_1a7112e06.tar 

#### (1)修改yaml文件
ingress_1.9.6.yaml 
做一下修改
删除所有镜像后的sha值
![[Pasted image 20240726215424.png]]
<font color="#ff0000">这个就是把这个pod的网络模式指成host，为什么要这样呢？</font>
我们通过ingress发布服务，最终客户端想用过主机名访问服务，他需要通过ingress-controller这个控制器来访问，最起码的你需要让客户端能够访问这个控制器本身，所以把他的网络模式指成host，让他和物理机公用一个ip，这样客户端通过对应的物理机ip就可以访问到它了
#### (2)部署ingress
kubectl create -f ingress_1.9.6.yaml 
![[Pasted image 20240726220652.png]]
自动创建了一个命令空间，所有的pod都在这个命名空间里面
![[Pasted image 20240726220832.png]]
### 5.通过ingress发布k8s中的服务
#### (1)nginx
先创建个服务
![[Pasted image 20240726221453.png|352]]
![[Pasted image 20240726221603.png]]
这个服务暂时只能在集群内部被访问
通过ingress进行发布
![[Pasted image 20240726223855.png]]
![[Pasted image 20240726223932.png]]
物理机访问测试
添加解析
![[Pasted image 20240726223708.png]]
需要解析到ingress-controller的ip
![[Pasted image 20240726223606.png|875]]

#### (2)tomcat
创建服务
![[Pasted image 20240726225654.png|475]]
![[Pasted image 20240726225705.png]]
创建ingress规则发布服务
![[Pasted image 20240726225727.png]]
![[Pasted image 20240726225732.png]]
测试访问
![[Pasted image 20240726225838.png]]
上传项目
kubectl cp project.war  test3-tomcat-78cb46587d-5srn4:/usr/local/tomcat/webapps/project.war
![[Pasted image 20240726230338.png]]



-
# 十.kubernetes资源-pv/pvc持久化卷

尝试挂载不同的远程存储测试一下
## 1.volume数据卷
作用：
就是借助数据卷对pod里面的数据做持久化，当然也可以挂载配置文件，部署业务项目
Kubernetes支持多种类型的卷，例如EmptyDir、HostPath、nfs、glusterfs、ceph等
### 1.1 EmptyDir
(<span style="background:#fff88f">根本不用</span>)
创建pod时，pod运行的node节点会创建临时卷，并将卷挂到pod指定的项目中
临时卷存放目录
/var/lib/kubelet/pods/<\pod id >/volumes/kubernetes.io-empty-dir/ # 卷名字
Pod宕机销毁后，该临时卷中的数据会随之被删除
![[Pasted image 20240729102429.png|330]]
### 1.2 hostPath
创建Pod时，Pod运行的node节点会在本地创建目录，并该目录挂载到Pod中
Pod宕机后，宿主机对应目录中的文件不会消失
以前讲docker -v挂载目录，前提是你的物理机上面应该有这个目录
K8S里面这么多工作节点，我这个pod到那个工作节点上还不一定，那我们在物理机上面准备这个目录的话，<font color="#ff0000">我们应该怎么准备？</font>
k8s挂目录不需要我们事先准备，将来这个pod在那个被创建，都会自动的在改节点上创建相应的目录
![[Pasted image 20240729124733.png]]
查看在那个节点上
![[Pasted image 20240729125001.png]]
在物理机上面可以看到相应的文件
![[Pasted image 20240729125031.png]]

### 1.3 基于nfs的网络卷
![[Pasted image 20240729125755.png|379]]
都要装nfs-utils不然会报错，如果没有的话，报错了查看日志，会发现是挂载失败
## 2.pv/pvc 持久卷/持久卷声明(<font color="#ff0000">最多的方式</font>)

### 2.1 pv和pvc的介绍
从软件设计的角度来讲(跟pod类似)，k8s在做持久化存储的时候，支持很多种格式的存储，各种品牌的存储
从软件设计的角度来说，市面上出现一款新的存储k8s就要添加一个新的的接口，去对接该存储，这样子就会很繁琐，所以k8s为了方便自己的软件维护，所以在k8s内部设计了一个通用的接口 <span style="background:#affad1">storage class </span><span style="background:#affad1">（存储类）,通过存储类去对接各厂商的存储设备</span>
如果说，出现了一家新的存储厂商，我只需要对存储类进行升级更新就行了，不需要动K8s集群的其他东西，可以基于这一个存储类，去对接所有的存储。
这个<span style="background:#affad1">存储类</span>，给我们提供了一种使用方式
一个叫pv
一个叫pvc
![[Pasted image 20240729184312.png|500]]

pv       ：持久卷，后端真实存储空间的映射  
pvc     ：持久卷声明，使用存储的申请

假设公司有一套华为ocean store的存储，当我们创建某一个pod的时候，假设我们规划想用后端的华为存储给我们提供100g空间，安装传统的方式，我们可以直接让k8s去直接挂载这个华为的存储器，但是这种方式，k8s直接和存储设备对接，将来难免就会出现兼容性等乱七八糟的问题，所以k8s提供了存储类去对接后端的存储，后端的这个存储不管是以块设备的方式，还是文件系统的方式，给我提供了100g的真实的存储空间，将来我们要把它挂载到pod上面去用，<font color="#ff0000">按照官方的方式我们应该怎么做呢？</font>
首先我们应该在这个集群里面去创建一个叫做<span style="background:#affad1">pv</span>的东西，然后用这个pv去代表后端真实的100g的存储，同理我们也可以创建一个500g的pv
<font color="#ff0000">那如何让pod和pv之间形成挂载关系呢？</font>
他们俩之间需要一个叫<span style="background:#affad1">pvc</span>的东西，假设将来我们要在集群里面创建一个跑数据库的pod，对数据做持久化，<span style="background:#affad1">我们需要一个40g的存储空间，这个需求就叫一个pvc</span>，一旦这个pvc创建好之后，K8s集群会自动在一个合适的pv和pvc之间建立关联关系，优先选容量最小最合适的，但是实际上，k8s在和pv和pvc之间形成绑定关系的时候，还有两外一种方式，<font color="#f79646">就是还要考虑pv的权限，需要读写权限的pvc肯定不会和只读的pv绑定</font>。
accessModes：用于指定PV的访问模式，共有四种
ReadWriteOnce          被单个节点以读写模式挂载
ReadWriteMany          被多个节点以读写模式挂载
ReadOnlyMany           被多个节点以只读模式挂载
ReadOnlyOnce            被单节点以只读模式挂载 (k8s 1.29出现的新模式)  

最后创建pod的时候在拿pod绑定pvc

总结使用流程:  
创建pv，描述后端真实的存储空间  
创建pvc，描述使用存储的申请  
创建pod，挂载pvc使用存储
![[Pasted image 20240729190748.png|500]]

### 2.2 pv/pvc的使用流程
(1)创建pv
![[Pasted image 20240729191746.png]]











# 十一.kubernetes资源-statefulset
有状态负载

<font color="#ff0000">有状态负载和无状态负载的区别？</font>
无状态负载
支持副本，滚动更新，适用于频繁更新的业务
有状态负载
适用于部署数据库
从业务的角度来讲，什么叫做无状态，什么叫做有状态？
创建的时候yaml文件的结构大致是一样的

从业务的角度来说
无状态的业务跑起来之后，客户端访问的过程中不需要记录任何客户端信息
有状态负载 ，服务端在运行期间，需要记录客户端状态信息(令牌，会话)

2.创建有状态负载



