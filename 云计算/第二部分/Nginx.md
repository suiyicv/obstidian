一.nginx介绍
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


二.nginx安装

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


三.nginx相关文件目录
![[Pasted image 20240528105802.png]]

sbin: 可执行命令，二进制程序 
logs: 日志
html：默认的网页目录 
conf: 配置文件，nginx.conf 


四.启动nginx
/usr/local/nginx/sbin/nginx
netstat -tunlp | grep nginx
![[Pasted image 20240528110402.png]]
客户端访问
![[Pasted image 20240528110500.png]]
启动之后查看进程
ps -elf | grep nginx
![[Pasted image 20240528110647.png]]
