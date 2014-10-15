---
layout: post
title: "git基础操作"
date: 2014-10-14 10:34:40 +0800
comments: true
categories: git
---

> 以下是阅读《Pro Git》时的记录  
> 《Pro Git》Scott Chacon 2010, licensed under the Creative Commons Attribution-Non Commercial-Share Alike 3.0 license.

### 1. 取得项目的 Git 仓库

有两种取得Git仓库的方法：1.对已有项目创建新的Git仓库；2.克隆已有的Git仓库。

##### 1.1 从项目目录初始化

- 开始用Git管理当前项目，在项目目录下执行：
```
$ git init
```
`初始化后会在当前目录下增加 .git 目录，但还没有跟踪项目任何文件。`

- 将几个文件纳入版本控制
```
$ git add *.c
$ git add README
$ git commit -m 'initial project version'
```

##### 1.2 从已有仓库克隆

```
$ git clone git://github.com/user/repo.git
```
`在当前目录下创建repo目录，里面装着克隆过来的项目仓库。`

```
$ git clone git://github.com/user/repo.git myrepo
```
`和上一条命令一样，但明确指定创建的目录名为myrepo。`

另外 Git 还支持其他传输协议，如http,https,SSH：
```
$ git clone http://github.com/user/repo.git
$ git clone user@server:/path/repo.git
```

### 2. 更新记录到仓库

##### 2.1 检查当前文件状态

```
$ git status
```
`它会告诉你哪些文件当前处于什么状态。`

##### 2.2 跟踪新文件

```
$ git add file
```
`使file文件或目录开始被跟踪；它可以接受通配符，如 git add *.c 。`

##### 2.3 暂存已修改文件

```
$ git add file
```
`被跟踪文件发生变化后，把它放到暂存区还是使用 git add，它是个多用途的命令。`  
`注意：若文件已暂存后又修改了文件，则提交时不会纳入修改，你需要重新执行 git add 将文件放到暂存区！`

##### 2.4 忽略某些无需跟踪的文件

在项目目录下建立 .gitignore 文件，添加过滤规则：

```
# 此为注释 
*.a 			# 忽略所有 .a 结尾的文件
!lib.a 		# 但 lib.a 除外
/TODO 	# 仅仅忽略项目根目录下的 TODO 文件,不包括 subdir/TODO
build/ 		# 忽略 build/ 目录下的所有文件
doc/*.txt 	# 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

##### 2.5 查看未暂存和已暂存的更新

git status 仅仅列出了修改过的文件，若要查看工作目录中当前文件和暂存区快照之间的差异：

```
$ git diff
```
`git diff 会使用文件补丁的格式显示具体添加和删除的行。`

若要看已经暂存起来的文件和上次提交时的快照之间的差异：

```
$ git diff --cached
```
`Git 1.6.1及以上版本还可使用 git diff --staged，效果相同但更好记。`

##### 2.6 提交更新

***在提交前请一定 git status，确认还有什么新建或修改过的文件没有 git add 进来！***  

Git 会调用文本编辑器让你填写“提交说明”（编辑器中显示的注释中会显示更新内容，可用 -v 选项显示修改差异）：

```
$ git commit
```

也直接在命令行带上“提交说明”：

```
$ git commit -m “提交说明”
```

若想自动把所有已经跟踪过的文件暂存起来一并提交,从而跳过 git add 步骤，可以加上 -a 选项：

```
$ git commit -am “提交说明”
```

##### 2.7 移除文件

***在提交前请一定 git status，确认还有什么新建或修改过的文件没有 git add 进来！***    

Git 会调用文本编辑器让你填写“提交说明”（编辑器中显示的注释中会显示更新内容，可用 -v 选项显示修改差异）：

```
$ git commit
```











