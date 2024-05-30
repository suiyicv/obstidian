<span style="background:#affad1">证书/密钥</span>

1.获取证书的方式
- 业务发布到互联网，合法的CA机构申请证书(云平台)
- 私有CA

一.部署私有CA
1.创建CA的数据库文件
touch /etc/pki/CA/index.txt
echo 01 > /etc/pki/CA/serial
2.创建密钥对
openssl genrsa -out /etc/pki/CA/private/cakey.pem 1024
![[Pasted image 20240529190752.png]]
3.创建自签证书
openssl req -new -x509 -key /etc/pki/CA/private/cakey.pem -out /etc/pki/CA/cacert.pem -days 3650
![[Pasted image 20240529191001.png]]
![[Pasted image 20240529191145.png]]

二.部署https网站
1.申请证书
(1)生成密钥
mkdir /usr/local/nginx/ssl
cd /usr/local/nginx/ssl
openssl genrsa -out secure.linux.com.key 1024
![[Pasted image 20240529191721.png]]


(2)创建证书申请
openssl req -new -key secure.linux.com.key -out secure.linux.com.csr
![[Pasted image 20240529192344.png]]

(3)将证书申请发送到CA
 ls
![[Pasted image 20240529192556.png]]

scp secure.linux.com.csr root@192.168.1.21:/opt/
![[Pasted image 20240529192614.png]]

(4)CA签发证书
openssl ca -in /opt/secure.linux.com.csr -out /etc/pki/CA/certs/secure.linux.com.crt -days 3650
![[Pasted image 20240529192939.png]]

(5)CA签订好的证书发送到网站服务器
scp /etc/pki/CA/certs/secure.linux.com.crt root@192.168.1.10:/usr/local/nginx/ssl
![[Pasted image 20240529193347.png]]

2.部署虚拟主机
mkdir /secure
vim /secure/index.html
vim /usr/local/nginx/conf.d/secure.conf
![[Pasted image 20240529193726.png]]
vim /usr/local/nginx/conf/nginx.conf
![[Pasted image 20240529194039.png]]
/usr/local/nginx/sbin/nginx -t
![[Pasted image 20240529194428.png]]
```ad-error
报错！
nginx 是一个模块化的软件，装软件的时候没装ssl模块，所以不支持https的配置的，需要重新安装一下
```

重装nginx

进入到软件包目录
cd nginx-1.20.2
ls
![[Pasted image 20240529195147.png]]

查找需要安装的模块
./configure --help | grep ssl
![[Pasted image 20240529195235.png]]

查看以前的安装的模块
/usr/local/nginx/sbin/nginx -V
![[Pasted image 20240529195314.png]]

重新编译安装
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
make
make install
```ad-success
重装完成
```

再次检查配置语法
/usr/local/nginx/sbin/nginx -t
![[Pasted image 20240529195921.png]]
/usr/local/nginx/sbin/nginx -s reload

查看端口
netstat -tunlp |grep nginx
![[Pasted image 20240529200144.png]]
发现并没有443端口，发生错误

关闭重启端口启动成功
/usr/local/nginx/sbin/nginx -s stop
/usr/local/nginx/sbin/nginx
netstat -tunlp |grep nginx
![[Pasted image 20240529200425.png]]

客户端添加解析
192.168.1.10 secure.linux.com
客户端访问
https://secure.linux.com
![[Pasted image 20240529201041.png]]

