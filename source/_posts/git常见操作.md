---
title: git常见操作
date: 2025-03-03 10:39:23
categories:
- Git
tags:
- Git
---



# <center><font color='blue'>Git操作场景

## <font color='black'> 1、新建分支做开发

首先确保本地没有被修改的文件
	
```
	git checkout master
	git checkout -b fa_{branch_name}
	edit
	git add -A && git commit -m "comment ..."
	git push -u presandbox fa{branch_name}
```

## 2、别人已经新建分支（我本地没有该分支，需要做后续开发）

1. 首先确保本地没有被修改的文件
```	
git checkout master && git pull presandbox
```

2. 查看所有的远端开发分支

	```
	git branch -r  
	git checkout -b fa{branch_name} --trace presandbox/fa{branch_name}
	```

3. 根据业务需求，修改相应的文件和代码 。。。
	
4. 对修改完毕的代码做本地测试，如果没有问题的话，使用下面的代码做本地提交

	```
	git add -A && git commit-m "commit code for ..."
	```

5. 为了可以和其他人共享你的代码修改，请执行以下命令
	```
	git push presandbox fa{branch_name}
	```
   > 注意服务器可能会reject你的代码，如果是被reject的话，参考Merge

## 3、别人已经新建分支，我在本地有该分支，我需要做后续开发

```
git checkout fa{branch_name}
git pull presandbox fa{branch_name}
```

根据业务需求组，修改相应的代码和文件 。。。

```
修改完毕的代码做本地测试，如果没有问题的话，使用下面的代码做本地提交
git add -A && git commit-m "commit code for ..."
```

## 4、 为了可以和其他人共享你的代码修改，请执行以下命令

```
git push presandbox fa{branch_name}
注意服务器可能会reject你的代码，如果是被reject的话，参考Merge
```

## 5、Merge代码

1. <font color='black'> 如果在提交代码是被reject的话，执行下面命令做merge(合并)

```
git pull presandbox fa{branc_name}
请仔细阅读改命令的输出：
输出会有类似conflict的字样，对每一个标注conflict的文件，仔细阅读其内容，然后把不需要的代码删除，保留最终的代码
```

2. 修改了文件，但是分支不对

3.  例如：在master上修改了文件，在某个分支上修改了文件

```
* 新建临时分支
* git checkout -b temp_new_branch
* 将文件改动保存到新的临时分支下
* git add -A && git commit -m "add all change to new branch"
* 没有新的正确的分支，新建正确的分支
* git checkout master && git checkout -b fa{branch_name}
* 否则 git checkout fa{right_branch}
* 从临时分支获取最新的改动
* git merge temp_new_branch  ## 如果有冲突参考上边的做法
* 保存最新的改动到正确的分支
* git add -A && git commit -m "append all changes back to right branch"
* 删除临时分支
* git branch -D temp_new_branch
```

## 6、删除无用的文件夹

```git
git rm -r --cached xxx
```

## 7、合并某分支上的某次提交

```git
git cherry-pick commitid
```

## 8、获取远程最新更新

```git
git fetch
本地已有修改的话： TODO待验证
1. git stash
2. git fetch
3. git stash pop
```

## 9、版本回退

```git
git revert commitid
```

## 10、如果在提交（commit）之后发现需要进行修改或追加工作

```git
git commit -amend
1. 已push的
2. 未push的都可以修改
3. 历史提交信息请Google
	
如果只追加之前的修改，而不修改提交信息
git commit --amend --no-edit
```


## 11、修改远程仓库，连接到新地址

```shell
git remote set-url origin url
```


## 12、删除远程分支
```shell
git push origin -d branch_name
```


## 13、本地修改后，提交之前，pull远程代码，保持树干净
必须要在 git add . && git commit -m ""之前执行以下操作。
```shell
# 储藏
git stash save "msg"
git pull origin branch
git stash pop/git stash apply stash@{index}
```
删除指定index的储藏
```shell
git stash drop stash@{index}
```
## 14、 其他分支的单个文件覆盖当前分支的对应文件
切换到要被覆盖的分支
```shell
git checkout dst_branch
```
执行覆盖命令
```shell
git checkout file_from_branch  file
```


## 15、 列出项目所有标签
```shell
git tag -n
```

## 16、 本地项目初次连接远程仓库
```shell
git init
git add README.md
git commit -m "first commit"
git remote add origin url
git push -u origin master
```