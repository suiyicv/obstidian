删除文件，并保存删除文件的信息

```bash
#!/bin/bash
wenjian="/wwh"
shanchujilu="/root/wwh.log"
# 获取当前时间
delete_time=$(date '+%Y-%m-%d %H:%M:%S')
# 删除目录中的所有文件，并记录删除时间和文件名
for file in "$wenjian"/*; do
    if [ -f "$file" ]; then
        rm -f "$file"
        echo "File '$file' deleted at: '$delete_time'" >> "$shanchujilu"
    fi
done
echo "所有文件已经删除"
```
批量创建用户，并且创建用户名对应的文本文件，并设置权限
```bash
#!/bin/bash
# 用户列表
users=("user1" "user2" "user3" "user4")
# 创建用户并为每个用户创建一个同名的文本文件
for user in "${users[@]}"; do
# 创建用户
sudo useradd -m -s /bin/bash "$user"
# 创建同名的文本文件
touch "/home/$user/$user.txt"
 # 设置文件权限
sudo chown "$user:$user" "/home/$user/$user.txt"
 sudo chmod 600 "/home/$user/$user.txt"
done
echo "Users and files created successfully."

```