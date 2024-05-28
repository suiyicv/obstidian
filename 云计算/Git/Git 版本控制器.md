[git版本控制工具](https://blog.csdn.net/u010198709/article/details/139135704?spm=1001.2014.3001.5501)

---

作用：git 版本控制器，记录文本文件的版本变化，便于回退
典型的工具：`Git,Svn`

---

# 一.安装Git

```bash fold title:Git安装
yum -y install  git        #安装git
rpm -qa git                 #查询git软件包
```
![[Pasted image 20240526232600.png|700]]
# 二.初始化设置

**1.设置用户名，邮箱**
```bash fold title:1.设置用户名|邮箱
git config --global user.name "Suiyi"
git config --global user.email "2028393350@qq.com"
```

**2.设置git里面的颜色高亮**
```bash  fold title:设置git里面的颜色高亮
git config --global color.ui true
```
# 三.Git的使用

## 1.创建git仓库

```bash fold title:创建git仓库：一个仓库代表一个项目
mkdir /opt/gitlearn
cd /opt/gitlearn/
git init                        
```
![[Pasted image 20240526220105.png|700]]

## 2.提交修改

```bash title:提交修改流程
cd /opt/gitlearn
vim file01
git add file01
git commit -m "文件中添加aaaaa"
```
![[Pasted image 20240526220141.png|700]]
```bash title:查看git状态
git status
```
![[Pasted image 20240526220213.png|700]]
<span style="background:#affad1">所谓的干净就是指当前目录下所有的文件都已经被记录到仓库里面去了，无论你做了什么操作，最后你要确保你的仓库是干净的</span>
### (1)若未提交修改

```bash fold title:若修改未提交
vim file01  #添加信息1111111111
git status
```
![[Pasted image 20240526220413.png|700]]
```bash title:此时需要重新提交修改
git add file01
git commit -m "文件中添加11111111"
```
![[Pasted image 20240526220438.png|700]]
```bash title:查看状态
git status
```
![[Pasted image 20240526220456.png|700]]

```bash
cat file01
```
![[Pasted image 20240526220633.png|700]]

## 3.版本回退

```bash title:查看历史版本
git reflog
```
![[Pasted image 20240526220814.png|700]]
<span style="background:#affad1">每一次提交都会生成一个唯一的id,之后回推就是靠这个唯一的id</span>

```bash title:版本回退
git reset --hard fc456a7
```
![[Pasted image 20240526221124.png|700]]
```bash 
cat file01
```
![[Pasted image 20240526221153.png|700]]
<span style="background:#b1ffff">这个回退的意思，不是智能推到前一个，是再任意版本之间都可以跳转</span>

```bash title:再次查看历史版本
git reflog
```
![[Pasted image 20240526221610.png|700]]
```bash title:根据id切换版本
git reset --hard 98234ac
```
![[Pasted image 20240526221636.png|700]]
```bash title:重新恢复
cat file01
```
![[Pasted image 20240526221712.png|700]]



## 4.撤销修改

- 暂存区
	- 暂存区相当于内存
	- `{bash}git add` 将修改记录到 暂存区
- 工作区
	- 工作区相当于硬盘
	- `{bash} git commit` 将修改记录到工作区

<span style="background:#b1ffff">一旦这个记录被记录到工作区之后，你的这个仓库就是个干净的状态了</span>

### (1)完整提交修改流程

```bash title:添加数据模拟修改
vim file01
cat file01
```
![[Pasted image 20240526222952.png|700]]
这就相当于改过一次代码，但是这次代码我改错了，我想把刚才的所有修改全部抹去

```bash title:查看状态
git status
```
![[Pasted image 20240526224913.png|700]]
```bash title:添加到暂存区
git add file01
git status
```
![[Pasted image 20240526225020.png|700]]
<span style="background:#b1ffff">修改这个文件一旦进入到暂存区之后，它是可能就检测到说有一个修改可能要被提交</span>
```bash title:添加到工作区
git commit -m "文件中添加wwwwww"
```
![[Pasted image 20240526225339.png|700]]
```bash title:查看状态
git status
```
![[Pasted image 20240526225408.png|700]]
<span style="background:#b1ffff">真正添加之后，在查这个状态，工作区就是干净的了</span>

### (2)撤销修改

<font color="#ff0000">撤销文件的修改，要分情况</font>，你要看你这个修改是<span style="background:#b1ffff">被添加到暂存区，还是工作区</span>它的撤销的方法是不一样的
#### 1.撤销未提交到暂存区的修改

``` bash
vim file01
cat file01
```
![[Pasted image 20240526230456.png|700]]
```bash
git status
```
![[Pasted image 20240526230541.png|700]]
```bash
git checkout -- file01
git status
```
![[Pasted image 20240526230636.png|700]]


#### 2.撤销提交到暂存区的修改
```bash
vim file01
```
![[Pasted image 20240526231109.png|700]]
```bash
git add file01
git status
```
![[Pasted image 20240526230842.png|700]]
<span style="background:#b1ffff">这个信息就是已经把修改提交到暂存区了</span>
```bash
git reset HEAD file01
```
![[Pasted image 20240526231235.png|725]]
<span style="background:#b1ffff">这个命令只是暂时把修改从暂存区里面踢出来，这个修改本身并没有撤掉</span>
```bash
cat file01
```
![[Pasted image 20240526231115.png|700]]
```bash
git status
```
![[Pasted image 20240526231337.png|700]]
```bash
git checkout -- file01
git status
```
![[Pasted image 20240526231503.png|700]]

#### 3.撤销提交到工作区的修改

<span style="background:#b1ffff">这句话说的不准，这个修改一旦被提交到工作区，就没法撤销回来了，<font color="#ff0000">只能版本回推</font></span>
```bash
vim file01
```
![[Pasted image 20240526231115.png|700]]
```bash
git add file01
git commit -m "文件中添加444444"
git status
git reflog
```
![[Pasted image 20240526232036.png|700]]
```bash
git reset --hard 73a73e6
cat file01
```
![[Pasted image 20240526232237.png|700]]

---
