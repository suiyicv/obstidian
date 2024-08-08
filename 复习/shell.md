删除文件，并保存删除文件的信息
wenjian="/wwh"
delete_time=$(date  '+%Y-%m-%d %H:%M:%S')
rm -rf "$wenjian"/*
echo "file_delete_time: $delete_time " >> /root/wwh

for file in "wem"
批量创建用户，并且创建用户名对应的文本文件，并设置权限
