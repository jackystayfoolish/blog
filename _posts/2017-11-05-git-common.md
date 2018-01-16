---
layout: post
title: Git-原理和常用命令
category: Linux
---
## Git原理
存在三个区域：working tree(工作区), index file(暂存区), commit(版本库)
![1](http://ecbadddb.wiz03.com/share/resources/965b7c43-96c7-4868-ac33-9e5e0da4b084/index_files/38818738.png)

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
版本回退
`git reset --hard versionnum`



### diff
`git diff` ：，比较working tree和index file(如没有则是和commit)的区别
`git diff --cached`: 比较index file和commit的区别
`git diff HEAD`:比较working tree和commit的区别
上述命令都可以加入filename用来单独对比某个文件。
