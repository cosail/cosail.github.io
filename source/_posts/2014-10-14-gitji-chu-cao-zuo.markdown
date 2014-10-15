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

若想从 Git 仓库中移除文件(即从暂存区中移除)，但仍然在工作目录中保留该文件（也许想稍后将文件加入 .gitignore 文件中）：

```
$ git rm --cached log/\*.log		#移除 log/ 目录下所有 .log 文件
```
`**注意：** Git 有自己的文件模式扩展方式，当我们使用其自己的扩展方式（而不是让Shell来扩展）时，应在特殊字符前加 ’\‘ 。`

若想从 Git 仓库中移除文件，并且从工作目录中移除该文件：

```
$ rm file					#从工作目录中移除，但仍然被git跟踪着
$ git rm file				#从 Git 仓库中移除(不再跟踪)
或 $ git rm -f file		#若file已被修改且放到暂存区，则需强制选项（防止因误删除文件而丢失修改内容）
```
`**注意：** Git 有自己的文件模式扩展方式，当我们使用其自己的扩展方式（而不是让Shell来扩展）时，应在特殊字符前加 ’\‘ 。`

##### 2.8 移动(重命名)文件

若要对 Git 中的文件改名：

```
$ git mv file_a file_b
```
实际上，上面的命令相当于：

```
$ mv file_a file_b
$ git rm file_a
$ git add file_b
```

### 3. 查看提交历史

最常用的提交历史输出格式：

```
$ git log				#按提交时间列出所有的更新
$ git log -p			#-p：展开显示每次提交的内容差异（看看别人改了啥）
$ git log -4			#-4：仅显示最近的4次更新
$ git log --stat		#--stat：仅显示简要的增改行数统计
```

你可能想要更特殊的输出格式：

```
$ git log --pretty=oneline（或short,full,fuller） #oneline表示每个提交显示为一行
$ git log --pretty=format:"%h - %an, %ar : %s"  #定制显示格式(输出便于后期编程提取分析)
常用的格式占位符及其意义:
		%H 提交对象(commit)的完整哈希字串
		%h 提交对象的简短哈希字串
		%T 树对象(tree)的完整哈希字串
		%t 树对象的简短哈希字串
		%P 父对象(parent)的完整哈希字串
		%p 父对象的简短哈希字串
		%an 作者(author)的名字
		%ae 作者的电子邮件地址
		%ad 作者修订日期(可以用 -date= 选项定制格式)
		%ar 作者修订日期,按多久以前的方式显示
		%cn 提交者(committer)的名字
		%ce 提交者的电子邮件地址
		%cd 提交日期
		%cr 提交日期,按多久以前的方式显示
		%s 提交说明
```

也可以用简单的字符图形, 形象地展示了每个提交所在的分支及其分化衍合情况：

```
$ git log --pretty=format:"%h %s" --graph
```

一些其他常用的选项及其释义:

```
	--stat 显示每次更新的文件修改统计信息。
	--shortstat 只显示 --stat 中最后的行数修改添加移除统计。
	--name-only 仅在提交信息后显示已修改的文件清单。
	--name-status 显示新增、修改、删除的文件清单。
	--abbrev-commit 仅显示 SHA-1 的前几个字符,而非所有的 40 个字符。
	--relative-date 使用较短的相对时间显示(比如,“2 weeks ago”)。
```

***还可以限制输出的历史记录所在时间段：***

```
$ git log --since=2.weeks
$ git log --since=“2008-01-15”
```
其他常用的类似选项及说明：

```
-(n) 仅显示最近的 n 条提交
--since, --after 仅显示指定时间之后的提交。
--until, --before 仅显示指定时间之前的提交。
--author 仅显示指定作者相关的提交。
--committer 仅显示指定提交者相关的提交
```

`综合示例：`查看 Git 仓库中,2008年10月期间, Junio提交的但未合并的测试脚本(位于项目的 t/ 目录下的文件)

```
$ git log --pretty="%h:%s" --author=“Junio” --since="2008-10-01" \
--before="2008-11-01" --no-merges  --  t/
```

### 4. 撤销操作

`注意：有些操作并不总是能撤销的！`

##### 4.1 修改最后一次提交

如果刚才提交时忘了暂存某些修改,可以先补上暂存操作,然后再运行 --amend 提交:

```
$ git commit -m 'initial commit'		#修改提交说明
$ git add forgotten_file					#补上暂存操作
$ git commit --amend
```
`--amend提交命令修正了前一个的提交内容。`

##### 4.2 取消已暂存的文件

使已暂存的文件(当然是已修改的文件)回到已修改但未暂存状态:

```
$ git reset HEAD <file>
```

##### 4.3 取消对文件的修改

使已修改的文件回到修改前(上一次提交后)状态:

```
$ git checkout --  <file>
```
`其实是把之前版本文件拷贝过来覆盖了当前文件，请慎重！`

### 5. 远程仓库的使用

##### 5.1 查看当前远程库

克隆某个项目后，会有名为origin的远程库，用来表示所克隆的远程仓库。列出远程仓库：

```
$ git remote
$ git remote -v 		#-v：显示对应的仓库地址
```

##### 5.2 添加远程仓库

添加一个新的远程仓库,指定一个简单的名字xiaoming,以便将来引用：

```
$ git remote
$ git remote add xiaoming git://github.com/xiaoming/repo.git
```

##### 5.3 从远程仓库抓取数据

从远程仓库抓取数据到本地:

```
$ git fetch origin master
```
`fetch只是将远端的数据拉到本地仓库,并不自动合并到当前工作分支,当你需要是可手工合并。`

如果想从远程仓库抓取数据，并自动合并到本地仓库当前分支:

```
$ git pull origin master
```

##### 5.4 推送数据到远程仓库

`如果有其他人在你之前推送了若干更新，那你得先抓取，合并，然后再推送。`  
和别人分享劳动成果啦:

```
$ git push origin master
```
`成功条件：在服务器上有写权限；同一时刻没有别人在推送数据。`

##### 5.5 查看远程仓库信息

```
$ git remote show origin
```
`它会告诉我们，运行 git pull/push 时缺省拉取/推送的分支是什么，以及分支的同步情况。`

##### 5.6 远程仓库的删除和重命名

修改某个远程仓库的简短名称，如把xiaoming改为xm:

```
$ git remote rename xiaoming xm
```

若要移除远程仓库xiaoming(仅仅是添加远程仓库的逆操作):

```
$ git remote rm xiaoming xm
```

### 6. 打标签

Git 可以对某一时间点的版本打上标签，人们在发布某个软件版本(比如 v1.0)的时候，经常这么做。

##### 6.1 列出和查看已有的标签

```
$ git tag
```
`显示的标签按字母顺序排列。`

也可以用特定的搜索模式列出符合条件的标签：
```
$ git tag -l 'v1.4.2.*'
```
`列出 v1.4.2 系列的版本。`

可以查看某个标签的信息：

```
$ git show  v1.4.2.0
```

##### 6.2 新建标签

`一般建议使用含附注型的标签，以便保留相关信息；但若只是临时性加注标签或不需要旁注信息，用轻量级标签也行。`

- `轻量级标签`就像是个不会变化的分支，其实只是个指向特定提交对象的引用。新建轻量级标签：

```
$ git tag v1.0-LW
```

- `含附注标签`实际上是存储在仓库中的一个独立对象，有自身的校验和，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。

```
$ git tag -a v1.4 -m 'my version 1.4'    #-m 指定了对应的标签说明
```

- 签署一个标签：如果你有自己的私钥，还可以用 GPG 来签署标签，只需要把之前的 -a 改为 -s 。

```
$ git tag -s v1.5 -m 'my signed 1.5 tag'
```
`这可能会提示你输入密码啥的。`

- 验证一个标签：以下命令会调用 GPG 来验证签名，所以你需要有签署者的公钥，存放在 keyring 中，才能验证。

```
$ git tag  -v  v1.5
```
 
 - 后期加注标签

你甚至可以在后期对早先的某次提交加注标签，跟上对应提交对象的校验和(或前几位字符)：

```
$ git tag -a v1.2 9fceb02
```

 - 分享标签

默认情况下，git push 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库：

```
$ git push origin v1.2   	#仅推送标签 v1.2
$ git push origin --tags   #一次推送所有标签上去
```
`这样别人也能看到这些标签了。`

### 7. Git 命令别名

可以为命令设置别名：

```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
$ git config --global alias.unstage 'reset HEAD --'
$ git config --global alias.last 'log -1 HEAD'
```
`现在若想输入 git commit，只需键入 git ci 即可。`

若想运行某个外部命令，而非 Git 的附属工具，只需在命令前加上`!` 就行：

```
$ git config --global alias.visual "!gitk"
```

### 8 命令自动补全

如果你用的是 Bash shell，可以试试看 Git 提供的`自动完成脚本`：  

下载 Git 的源代码，将contrib/completion/git-completion.bash 文件复制到你自己的用户主目录中：  

```
cp git-completion.bash ∼/.git-completion.bash  
```
	
并把下行添加到你的 .bashrc 文件中:  
```
source ~/.git-completion.bash
```
`要为系统上所有用户都设置默认使用此脚本，Linux 上复制到 /etc/bash_completion.d/ 目录中即可。`







