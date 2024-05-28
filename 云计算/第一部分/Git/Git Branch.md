
Git 分支是 Git 版本控制系统中的一个核心概念，它代表了项目开发过程中的不同发展路线。

---
# 一.概念

```ad-text
这个仓库建好之后，仓库上自动就会建出来一条分支，这个分支我们所有人都是一样的叫**master**，然后这个分支无法删除，无法更改

```
---
git 上面所有的提交修改操作，撤销修改操作，全部都是基于分支来做的
![[Excalidraw/Drawing 2024-05-27 15.08.40.excalidraw.md#^group=xrlM3S6HuCWF9bpIPQOJj|Git 分支 | 400]]
```ad-text
上图有两个分支，切换到v1.1分支上，在v1.1分支上对文件进行修改，对修改内容进行提交，应为分支之间是隔离的，所有我在mster这个分支上是看不见的，切换到master分支相当于这个文件并没有进行更改。除非把v1.1分支上的修改合并到了master分支上
```

---

**应用：软件的升级更新**
从<font color="#ff0000">公司实际项目开发的角度</font>来说，<span style="background:#affad1">master分支都保留最新的那个版本</span>，新分支上代码修改完毕，测试完成后，把新分支所有的修改合并到master分支上

---
**Github仓库**
下图就是一个github的一个仓库，可见该仓库一共有54个分支。
![[Pasted image 20240527163537.png|700]]

# 二.操作
## 1.查看分支

```bash title:查看分支
git branch
```
![[Pasted image 20240527164115.png|700]]
星号代表当前分支

## 2.创建分支

```bash title:创建分支
git branch release-v1.1
git branch
```
![[Pasted image 20240527164417.png|700]]

## 3.删除分支

```bash title:删除分支
git branch -D release-v1.1
```

## 4.切换分支

```bash 
git checkout release-v1.1
```
![[Pasted image 20240527164720.png|700]]
```bash
git branch
```
![[Pasted image 20240527164835.png|700]]

## 5.分支的合并

```bash title:把所输入的分支合并到当前分支上
git merge  release-v1.1
```
![[Pasted image 20240527165257.png|700]]
这个提示是因为，两个分支是相同的
![[Pasted image 20240527165728.png|700]]
两个分支不同，提示如上图








