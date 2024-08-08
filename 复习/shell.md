删除文件，并保存删除文件的信息
wenjian="/wwh"
delete_time=$(date  '+%Y-%m-%d %H:%M:%S')
rm -rf "$wenjian"/*
echo "file_delete_time: $delete_time " >> /root/wwh

```bash
wenjian="/wwh"
shanchujilu="/root/wwh/"
delete_time=$(date  '+%Y-%m-%d %H:%M:%S')
for file in "$wenjian"/*; do
	if [-f "$file"]; then
	 rm "$file"
	 echo "file '$file' deleted at : '$delete_time'   " >> "$wenjian"
	 fi
done
echo "所有的文件已经删除"
```
批量创建用户，并且创建用户名对应的文本文件，并设置权限
