---
layout: post
title: Git-原理和常用命令
category: Linux
---
## Git原理
存在三个区域：working tree(工作区), index file(暂存区), commit(版本库)

## 命令
### workspace
撤销修改工作区的某个文件
`git checkout --filename`
- 如果工作区的文件没有提交到暂存区,就撤销修改就回到版本库的状态(也可以用于还原工作区中删除的文件)
- 如过工作区的文件提交到了暂存区，又做了修改，就回到添加到暂存区的状态

### 暂存
添加
`git add filename`或者`git add -A`添加全部
将某个文件从暂存区撤销
`git reset HEAD filename`

### 本地仓库
提交
`git commit -m "comment goes here"`
如果加入-a则会先添加全部再提交，省去git add步骤

版本回退
`git reset --hard versionnum`

### 远程仓库
设置ssh key
用户目录下.ssh目录下的github_rsa.pub文件内容添加到github的ssh keys设置中，授权这台电脑可以推送到github。如果没设置的话推送时会报fatal:unable to access的错误

本地仓库和远程仓库关联
`git remote add origin yourcloneurl `
添加后远程库的名字就是origin

推送
`git push -u origin dev`
把当前分支(不一定叫dev，但最好名称一致)推送到origin，远程仓库创建dev分支,加入-u参数是说将本地dev推送到远程dev分支，并将本地dev和远程dev分支关联，下次推送可以用命令：
`git push origin dev`

### 分支
创建
`git branch dev`
切换
`git checkout dev`
创建并切换
`git checkout -b dev`
合并指定分支到当前分支
`git merge dev`
删除
`git branch -d dev`

特定分支关联
`git branch --set-upstream dev origin/dev`

### tag
创建标签
`git tag v1.0`
推送标签
`git push origin v1.0`

### diff
`git diff` ：，比较working tree和index file(如没有则是和commit)的区别
`git diff --cached`: 比较index file和commit的区别
`git diff HEAD`:比较working tree和commit的区别
上述命令都可以加入filename用来单独对比某个文件。

## github
如果对一个开源项目感兴趣可以fork到自己的github仓库，clone到本地做修改后push。
如果要将fork出来的项目与原项目merge,需要pull request，提交申请等原作者同意后才会将修改最终提交。
