title: "Git教程"
date: 2015-04-29 14:40:00
updated:
layout: post
comments:   true
categories:
- git
- 工具
tags:
---

## 常用命令

- add        Add file contents to the index
- bisect     Find by binary search the change that introduced a bug
- branch     List, create, or delete branches
- checkout   Checkout a branch or paths to the working tree
- clone      Clone a repository into a new directory
- commit     Record changes to the repository
- diff       Show changes between commits, commit and working tree, etc
- fetch      Download objects and refs from another repository
- grep       Print lines matching a pattern
- init       Create an empty Git repository or reinitialize an existing one
- log        Show commit logs
- merge      Join two or more development histories together
- mv         Move or rename a file, a directory, or a symlink
- pull       Fetch from and integrate with another repository or a local branch
- push       Update remote refs along with associated objects
- rebase     Forward-port local commits to the updated upstream head
- reset      Reset current HEAD to the specified state
- rm         Remove files from the working tree and from the index
- show       Show various types of objects
- status     Show the working tree status
- tag        Create, list, delete or verify a tag object signed with GPG


## 创建仓库

- 创建仓库目录

    创建一个目录来作为git的仓库目录
    *mkdir gitdemo*

- 仓库初始化

    进入仓库目录，使用git命令对仓库进行初始化
    *cd gitdemo*
    *git init*

- 添加文件

    添加文件到仓库目录，如:添加文件readme

- 添加文件到版本控制

    添加到仓库的文件还没有在git的版本控制之下，要想对readme文件的改动
    能被git记录，需要把文件添加到版本控制。
    *git add readme*

- 提交记录到版本库

    把文件的修改信息添加到版本库
    *git commit -m "commit description*

- 修改文件

    对文件readme进行修改

- 添加改动记录

    再次执行*git add readme*, 把对文件的改动添加到版本控制

- 提交改动

    *git commit -m "second commit"*

- 还原到上一版本

    *git reset --hard HEAD^*
    如果知道上一个版本的commmit id 可以指定id进行还原:
    *git rest --hard ee3b344*
    在这不用输入完整的commit id,只需前几位即可，git会自己查找对应的记录

- 后悔了

    如果再想回到最后一个版本，而且当前窗口还没有关闭，只要有最后一次提交的
    commit id, 使用命令*git reset --hard xxxxxx*,还是可以回去最后一
    个版本的。

    但如果终端窗口已关闭，找不到了最后一次的commit id怎么办？运行命令
    *git log*, 发现也没了最后一次的记录,这可如果是好?
    其实所有的commit id 也是有log的。
    *git reflog*

