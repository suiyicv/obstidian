1、核心区别
将[httpd](https://so.csdn.net/so/search?q=httpd&spm=1001.2101.3001.7020)换为nginx，高并发、高性能  
nginx通过fastCGI机制调用PHP，处理动态资源  
php以fpm的方式运行，有自己独立的进程、服务

配置安装php72的软件仓库
https://webtatic.com/
安装平台所需的软件
yum install -y mariadb-server php72w php72w-cli <span style="background:#affad1">php72w-fpm</span> php72w-common php72w-devel php72w-embedded php72w-gd php72w-mbstring php72w-mysqlnd php72w-opcache php72w-pdo php72w-xml 

启动php-fpm、数据库
systemctl enable --now mariadb
systemctl enable --now php-fpm
netstat -tunlp | grep php

将nginx和php融合
vim /usr/local/nginx/conf.d/vedio.conf
