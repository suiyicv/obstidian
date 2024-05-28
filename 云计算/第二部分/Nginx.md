# 一.nginx介绍
作用:
- 部署web服务
	- 虚拟主句
	- URL重写
	- LNMP平台
- 反向代理
	- 隐藏服务器地址
	- 安全

优势：
基于epoll的模型设计
高并发，高性能，资源消耗少

![[Excalidraw/Drawing 2024-05-28 10.16.23.excalidraw.md#^group=9BcQfup-EoMuXq7mAn-Nu]]
epoll模型（补充）


# 二.nginx安装

1.下载nginx软件包
[nginx: download](https://nginx.org/en/download.html)
选择一个较低版本的比较稳定
![[Pasted image 20240528104412.png|475]]
wget https://nginx.org/download/nginx-1.20.2.tar.gz
![[Pasted image 20240528104718.png|500]]

2.安装nginx依赖
yum install -y gcc openssl-devel pcre-devel zlib-devel

3.解压nginx软件包，编译安装

tar xf nginx-1.20.2.tar.gz
ls nginx-1.20.2/
![[Pasted image 20240528105159.png]]
./configure --prefix=/usr/local/nginx --with-http_stub_status_module
![[Pasted image 20240528105347.png]]

make && make install
![[Pasted image 20240528105609.png]]
安装完毕!


# 三.nginx相关文件目录

![[Pasted image 20240528105802.png]]

sbin: 可执行命令，二进制程序 
logs: 日志
html：默认的网页目录 
conf: 配置文件，nginx.conf 


# 四.启动nginx

## 1.启动

/usr/local/nginx/sbin/nginx
netstat -tunlp | grep nginx
![[Pasted image 20240528110402.png]]
客户端访问
![[Pasted image 20240528110500.png]]

## 2.启动之后查看进程
ps -elf | grep nginx
![[Pasted image 20240528110647.png]]
master process: 主进程，负责记录日志、读取配置文件，管理子进程

## 3.设置开机自启
sed -ri '$a \/usr/local/nginx/sbin/nginx' /etc/rc.d/rc.local
chmod a+x /etc/rc.d/rc.local

## 4.关闭nginx
/usr/local/nginx/sbin/nginx -s stop
![[Pasted image 20240528123726.png]]
## 5.检测配置文件语法
/usr/local/nginx/sbin/nginx -t
![[Pasted image 20240528123803.png]]
## 6.查看版本
/usr/local/nginx/sbin/nginx -v
![[Pasted image 20240528123845.png]]
/usr/local/nginx/sbin/nginx -V
![[Pasted image 20240528123920.png]]

## 7.加载配置文件
vim /usr/local/nginx/conf/nginx.conf
![[Pasted image 20240528124007.png]]
端口修改为8080
/usr/local/nginx/sbin/nginx -s reload
netstat -tunlp | grep nginx
![[Pasted image 20240528124725.png]]
ps -elf | grep nginx
![[Pasted image 20240528124621.png]]

# 五.Nginx配置web服务

## 1.语法结构

/usr/local/nginx/conf/nginx.conf
```bash title:语法结构
全局配置
事件驱动模型epoll的配置
events {
}
http服务相关配置
http {
		http公共配置
		
	一个server代表就是一个虚拟主机
	server {
		匹配客户端请求，根据不同的请求给不同的响应
		location {
			不同的响应方式
		}
		location {
		}
	}
	server {
	}
}
```
## 2.全局配置

![[Pasted image 20240528141829.png]]


### (1)user xxx
定义工作进程的用户
vim /usr/local/nginx/conf/nginx.conf
![[Pasted image 20240528130553.png]]
useradd -s /sbin/nologin suiyi
/usr/local/nginx/sbin/nginx -s reload
ps -elf | grep nginx
![[Pasted image 20240528130752.png|700]]
这个进程对应的用户改了影响什么？
影响权限！要确认你这个用户，最起码对这个网页有读的权限

### (2)work_processes
定义启动时，工作进程的数量
![[Pasted image 20240528131514.png]]
nginx的优化参数，指定nginx启动之后，默认启动几个工作进程，来接受处理客户端的请求
增大可以提高性能，建议和cpu数量一致或者为cpu数量的2倍，也不能过大，容易造成阻塞内存浪费

查看cpu参数
lscpu
![[Pasted image 20240528132355.png]]
显示cpu核数
nproc
![[Pasted image 20240528132731.png]]

### (3)error_log
定义错误日志
![[Pasted image 20240528132913.png]]
后面的 选项指定错误日志的级别的
[nginx documentation](https://nginx.org/en/docs/)
![[Pasted image 20240528133338.png|525]]
一下这几种级别

轻度
`debug`, `info`, `notice`, `warn`,
重度
`error`, `crit`, `alert`, or `emerg`.

### (4)pid
定义pid文件
cat /use/local/nginx/logs/nginx.pid
![[Pasted image 20240528134145.png]]




## 3.事件驱动模型
![[Pasted image 20240528134940.png]]
use epoll:可写可不写，nginx装载红帽的系统上，它默认的事件驱动模型就是epoll
worder_connections:指定nginx的每个工作进程指定的最大连接数

## 4.http公共配置

![[Pasted image 20240528141728.png]]

(1)include
![[Pasted image 20240528140232.png]]
mime: 定义能够支持传输那些非文本数据
cat mime.types
![[Pasted image 20240528140352.png]]

(2)log_format
定义访问日志/访问日志格式
![[Pasted image 20240528140621.png]]

变量说明：
$remote_addr：客户端地址
$time_local：时间
$request：请求方法 请求资源 http协议版本
$status：状态码
$body_bytes_sent：响应数据大小 
$http_referer：超链接地址
$http_user_agent：客户端系统、浏览器类型

(3)sendfile
启用sendfile机制
实现零拷贝，内核读取到数据后，直接放入web进程的内存空间
(4)keepalive_timeout
长连接keepalive,长连接,一条连接发送多次请求
keepalive_timeout 65;       //长连接的超时时间 
keepalive_requests 1000;  //最大请求数

(5)gzip
启用gzip压缩
## 5.虚拟主机配置

虚拟主机类型
基于名称的虚拟主机  
基于IP的虚拟主机

### (1)基于名称的虚拟主机配置

规划
music.linux.com
网页目录
/music

建子配置文件
mkdir /usr/local/nginx/conf.d
vim /usr/local/nginx/conf.d/music.conf
![[Pasted image 20240528150740.png]]

```bash title:虚拟主机的配置
server{
listen 80;
server_name music.linux.com;
error_log /usr/lcoal/nginx/logs/music_error.log error;
access_log /usr/local/nginx/logs/music_access.log main;

location / {
root /music;
index index.html;
}
}
```

mkdir /music
cd /music/
vim index.html

自己建的子配置文件，想让他生效，必须在nginx.conf中加载一下这个子配置文件

vim /usr/local/nginx/conf/nginx.conf
![[Pasted image 20240528145140.png]]
加在这个地方，但是加到这个地方检查语法会报错

检查语法有没有错误
/usr/local/nginx/sbin/nginx -t
![[Pasted image 20240528145912.png]]

修改配置文件
![[Pasted image 20240528150006.png]]
放到这个main配置下面，他是按着自上而下的方式读取配置文件，如果放在main格式前面，
就会读取到未知的main格式从而报错

/usr/local/nginx/sbin/nginx -t
![[Pasted image 20240528150820.png]]
/usr/local/nginx/sbin/nginx -s reload 
让配置生效
客户端访问，客户端在hosts文件，添加dns地址解析
但是访问失败！
![[Pasted image 20240528161432.png|275]]

经过排查是因为客户端开了vpn，把vpn关掉即可正常访问
![[Pasted image 20240528161755.png|325]]




(2)基于IP地址的虚拟机配置

添加一个网卡，配置好ip地址
![[Pasted image 20240528164947.png]]

添加一个测试界面
/vedio/index.html
vim   /usr/local/nginx/conf.d/vedio.cnf
![[Pasted image 20240528170905.png]]

加载到 
vim /usr/local/nginx/conf/nginx.conf
![[Pasted image 20240528171032.png]]
检查语法
/usr/local/nginx/sbin/nginx -t
/usr/local/nginx/sbin/nginx -s reload
客户端测试
![[Pasted image 20240528171144.png|340]]
