---
layout: post
title: "Git常用命令"
date: 2015-03-21 21:13:40 +0800
comments: true
categories: ['笔记']
---
提交代码
```javascript
git status //查看本地版本状态
git add . //添加本地修改文件
git add -A //会将未通过git rm删除的文件全部stage
git commit -m 'comment' //提交到本地仓库
git push origin master //提交到远程仓库
```

查看历史
```javascript
git log --pretty=oneline // 一行显示log(不能察看已删除的commit记录)
git reflog //可查看所有分支的所有操作记录(包括已经删除的commit)
git show commit-id // 查看某次修改
```

本地代码回滚
```javascript
git reset HEAD //回滚至最新一次的提交
git reset --hard commit-id //回滚到commit-id，将commit-id之后提交的commit都去除 
git reset --hard HEAD~3 //将最近3次的提交回滚
```

获取远程版本
```javascript
git pull //从远程获取最新版本并合并到本地仓库：等同于git fetch + git mearge
```

撤销修改
```javascript
git checkout -- <filename> //撤销某文件修改
git checkout . //撤销所有未提交修改
```

标签功能
```javascript
git tag // 显示所有标签
git tag v1.3 // 简单打标签 
```

删除文件
```javascript
git rm <filename> //直接删除文件
git rm --cached <filename> //删除文件暂存状态
```

文件比较
```javascript
git diff //查看未暂存的文件更新 
git diff --cached //查看已暂存文件的更新 
```

创建分支
```javascript
git branch develop //只创建分支
git checkout -b master develop //创建并切换到develop分支 
```

合并分支
```javascript
git checkout master //切换到主分支
git merge --no-ff develop //把develop合并到master分支，no-ff选项的作用是保留原分支记录
git rebase develop //合并分支
git branch -d develop //删除develop分支
```

Git设置
```javascript
git config --list //列出global配置
git config user.name "xx" //设置user.name
git config user.email "xx@xx.com" ////设置user.email
```

初始化项目
```javascript
cd [package folder]
git init
git add
git commit -m "Initial"
git remote add origin [your git repo]
git pull origin master //同步远程库最新代码至本地
git push origin master
```