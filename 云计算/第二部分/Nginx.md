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
```bash title:全局配置
全局配置
事件驱动模型epoll的配置
events {
}
http服务相关配置
http {
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


