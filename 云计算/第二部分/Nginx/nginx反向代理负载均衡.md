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

