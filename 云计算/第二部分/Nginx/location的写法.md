# 一.location的写法

作用：匹配客户端响应

location [ = | ~ | ~* | ^~ ] uri { … }
优先级: =, ^~, ~, ~\*, location /


1、= 
精确匹配
location = /upload {    }
http://x.x.x.x/upload

2、~ 
以正则表达式匹配请求，区分大小写
location ~ /test {    }
location ~ \\.php$ {    }
location ~ \\.(jpg|jpeg|gif|png)$ {    }
http://x.x.x.x/upload/test
http://x.x.x.x/test/a/b/c

3、~* 
以正则匹配请求，不区分大小写
location ~* /download {    }
http://x.x.x.x/test/download/
http://x.x.x.x/DownLoad/test

4、^~ 
不以正则的方式匹配请求
location ^~ /test {    }
http://x.x.x.x/test

5、/
所有请求
location  /   {    }





# 二.stub_status模块

显示虚拟主机的工作状态
vim /usr/local/nginx/conf.d/music.conf

```bash
location /status {
        stub_status;
        access_log off;
        allow 192.168.1.101;
        deny all;
    }
```

![[Pasted image 20240529205316.png]]

/usr/local/nginx/sbin/nginx -t
/usr/local/nginx/sbin/nginx -s reload

![[Pasted image 20240529205448.png]]

| 名称                 | 含义       |
| ------------------ | -------- |
| Active connections | 当前的活跃连接数 |
| accepts            | 接收的连接数   |
| handled            | 处理的连接    |
| requests           | 请求数      |

作业：写个脚本把这个状态数字 单独提取出来，差值十以内负载正在，插值十意以外，显示警报


curl music.linux.com/status > status.txt 2> /dev/null
awk 'NR\==3{print "accepts:"$1,"handled:"$2,"requests"$3}' ./status.txt

awk 'NR\==3{if($1-$2 gt 10){print "警告"} else{ print "正常"} }' ./status.txt 
警告
