git

 fetch 命令只是将远端的数据拉到本地仓库，并不自动合并到当前工作分支，只有当你确实准备好了，才能手工合并。 



**1.1 git commit -m “message”**

​      这种是比较常见的用法，-m 参数表示可以直接输入后面的“message”，如果不加 -m参数，那么是不能直接输入message的，而是会调用一个编辑器一般是vim来让你输入这个message，

　　　message即是我们用来简要说明这次提交的语句。还有另外一种方法，当我们想要提交的message很长或者我们想描述的更清楚更简洁明了一点，我们可以使用这样的格式，如下：

​    git commit -m ‘

​    message1

​    message2

​    message3

​    ’

  **1.2 git commit -a -m “massage”**

​      其他功能如-m参数，加的-a参数可以将所有已跟踪文件中的执行修改或删除操作的文件都提交到本地仓库，即使它们没有经过git add添加到暂存区，注意，

　　　新加的文件（即没有被git系统管理的文件）是不能被提交到本地仓库的。建议一般不要使用-a参数，正常的提交还是使用git add先将要改动的文件添加到暂存区，再用git commit 提交到本地版本库。

## 03 配置

### 配置user.name和user.email

```bash
git config --global user.name 'your_name'
git config --global user.email 'your_email'
```

### config的三个作用域

缺省为local

- local 只对某个仓库有效
- global对当前用户的所有仓库有效
- system对系统所有登陆 的用户有效

```bash

git config --local
git config --global
git config --system

```

### 显示config的配置，加`--list`

```bash
git config --list --local
git config --list --global
git config --list --system
```

在配置中可以通过alias配置命令的别名

## 04 创建git仓库

```bash
git init
git init dirname
git add filename
#将已跟踪的文件加入到暂存区
git add -u
```

## 06 重命名

```bash
git mv a.nd a.md
#equals to
mv a.nd a.md
git add a.md
git rm a.nd
```

- 重置暂存区

```bash
git reset --hard
```



## 07 git log

```bash
# 查看提交记录
git log 
# 单行查看
git log --oneline
# 查看最近的n=4次提交
git log -n4 --oneline
# 查看所有分枝的提交记录
git log --all 
# 以图的方式查看提交记录
git log --all --graph
```

切换分支的方法

```bash
#你可以运行一个带有 -b 参数的 git checkout 命令：
$ git checkout -b iss53
Switched to a new branch "iss53"

#它是下面两条命令的简写：
$ git branch iss53
$ git checkout iss53
```

- 查看所在分支

```bash
git branch -av
```



08 图形界面工具查看版本历史

```bash
gitk
```



## 10 commit 、tree、blob

**git cat-file** Provide content or type and size information for repository objects.

`git cat-file` 命令显示版本库对象的内容、类型及大小信息。

**-t**
Instead of the content, show the object type identified by **object**.
显示对象的类型。

**-s**
Instead of the content, show the object size identified by **object**.
显示对象的大小。

**-e**
Suppress all output; instead exit with zero status if **object** exists and is a valid object.
如果对象存在且有效，命令结束状态返回值为 0 。

**-p**

Pretty-print the contents of **object** based on its type.
根据对象的类型，以优雅的方式显式对象内容。

```bash
#查看指定对象
git cat-file -p hashxxx
```

## 11 对象个数

```bash
#查看对象详情
find .git/objects -type f
#
.git/objects/6f/e5d4a4e9024ae3195b5dce77ea966da487e4f2
.git/objects/7e/b965eb3e6035c0baec06033052aafb0e2b1d8d
.git/objects/43/069241053c7938ccf4952fca58b8b0dc462586
.git/objects/2b/05e1a27e2a7685bba262d6b6335f55d148fb29
#

# 查看对象类型
git cat-file -t 2b05e1a2
#
tree
# 查看对象详情
git cat-file -p 2b05e1a2
```

- 将内容写入到文件

```bash
echo 'sdfsdf' > readme
#将创建readme文件，内容为sdfsdf
```



## 12 分离头指针

若在切换分支时不将分离头指针对应的分支保存起来，很可能会被清理掉。

```bash
git branch new_branch_name xxxhashxxx
```



## 13 HEAD与branch

HEAD可以不跟任何分支挂钩（分离头）

head可以指向最新的分支

做分支切换时head会自动指向切换的分支

- head落脚于某个commit

```bash
# 查看当前head的指向
cat .git/HEAD
#: ref: refs/heads/fix_dddH

cat  .git/refs/heads/fix_dddH
#: 4831d906fd8d5300bd8fb04c635b18c025aac780

git cat-file -t 4831d906fd8d5300bd8fb04c635b18c025aac780
#: commit
#文件类型是commit，由此可见head落脚于commit
```



HEAD的表示法

- head的父亲：HEAD^  或 HEAD^1 或 HEAD~ 或 HEAD~1
- head的爷爷：HEAD^^   或 HEAD^1^1 或 HEAD~~ 或 HEAD~2 或 HEAD~1~1
- 总结 ^ 和~ 差不多一样，但~功能更强，支持~n 但不支持^n，只能是n个^



## 14 删除分支

```bash
git branch -d branch_name
# sure to delete use -D
git branch -D branch_name
```



## 15 修改最新commit的message

```bash
#使用
git commit --amend 
```



## 16 修改历史commit的message

```bash
# 先变基，
git rebase -i xxxhashxxx

#在弹出的界面修改指令
#在新弹出的界面修改message
```

