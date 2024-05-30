# 一.反向代理

隐藏后端服务器地址信息

![[Excalidraw/Drawing 2024-05-30 10.10.01.excalidraw.md#^group=HbLCPhEYBELhowfQv3wJ0]]

大多针对的后端都是和<span style="background:#affad1">http相关</span>的东西

1.语法
location uri { proxy_pass 后端服务器地址; }

需求: 将/mp3的访问请求转交到后端的/music地址

测试
server：
yum -y install httpd
systemctl enable --now httpd
mkdir /var/www/html/music
echo "1.21 httpd music" > /var/www/html/music/index.html

nginx:
先把前两天做的实验配置备个份
cp nginx.conf nginx.conf.backup
把 nginx.conf 还原
cp nginx.conf.default nginx.conf
![[Pasted image 20240530102811.png]]
vim /usr/local/nginx/conf/nginx.conf
![[Pasted image 20240530103104.png]]
重启nginx
/usr/local/nginx/sbin/nginx -s stop
/usr/local/nginx/sbin/nginx

客户端测试
![[Pasted image 20240530103410.png]]

2.注意：
反向代理时，nginx会将location中的uri地址自动拼接后端服务器地址，前提是那个后端你自己没写地址
![[Pasted image 20240530104837.png]]

http://192.168.1.10/fiist --->  http://192.168.140.11/
如果 写成 http://192.168.140.11 他真是访问的后端服务器的地址为
http://192.168.1.10/first --->  http://192.168.140.11/first 

location中要涉及到正则匹配，后端服务器<span style="background:#affad1">不支持</span>写具体的uri地址
![[Pasted image 20240530105436.png]]



3.后端服务器记录客户端真是IP
现在我们来查看一下httpd服务的日志文件
tail -f /var/log/httpd/access_log
![[Pasted image 20240530110029.png]]
可见他记录的都是nginx反向代理服务器的ip地址，<span style="background:#affad1">并不是客户端的</span>,为了后端服务器能显示客户端访问的真是IP，需要做以下操作

(1)在nginx反向代理时添加x-real-ip字段

vim /usr/local/nginx/conf/nginx.conf
![[Pasted image 20240530110321.png]]
(2)后端httpd修改combined日志格式
vim /etc/httpd/conf/httpd.conf
![[Pasted image 20240530111038.png]]

此时日志文件可以记录真是IP了
![[Pasted image 20240530111145.png]]

(3)后端是nginx的情况
proxy_set_header X-Forwarded-For  $proxy_add_x_forwarded_for;

修改main日志格式
$http_x_forwarded_for
![[Pasted image 20240530111554.png]]
把红框里面的进行更换



# 二.负载均衡

upstream模块

![[Excalidraw/Drawing 2024-05-30 10.10.01.excalidraw.md#^group=nhvcZTntFkPlPZWDjcM99]]



1、负载均衡作用
流量分发，提升连接

2、调度算法
- rr 轮询 默认算法  
    支持权重 weight， 高配置主机处理更多请求  
    会话持久问题，利用NoSQL做会话共享
    ![[Excalidraw/Drawing 2024-05-30 10.10.01.excalidraw.md#^group=IrtAaFz3aQ51-yIzB_LPV]]
    
- sh 源hash  
    一段时间内，同一个客户端的请求到达同一个后端服务器  
    解决会话持久问题
- lc 最少连接

3.配置应用

定义后端服务器组， 支持健康状态检查
nginx服务器会实时检查后端服务器80端口的状态，如果出错则不会往这个服务器发送请求
```bash
upstream 组名 {
	[调度算法];
	server IP:port weight=权重 fail_timeout=时间 max_fails=次数;
	server IP:port;
}
location uri {
	proxy_pass http:   //组名;
}
```

实验：
负载均衡，后端部署的业务必须是一样的
但是为了练习看出差别，暂时使后端业务不同


