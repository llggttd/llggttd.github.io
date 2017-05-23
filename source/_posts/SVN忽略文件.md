---
title: "SVN忽略文件"
date: 2015-05-21 18:29:48
updated: 2015-05-21 18:29:48
categories:
- 工具
- SVN
tags:
- ignore
---

如何忽略subversion中的文件或目录

## 文件或目录未添加到版本控制

文件或目录还未添加到版本控制，那我们忽略它们的目的，就是在提交时不想在看到他们。无论是要忽略文件不是文件夹，我们都要对它们的父目录进行操作。有如下文件结构：

- project
    + src
        - main.c
        - lib.c
        - lib.h
    + demo
        - 1.c
        - 2.c
    + log
    + .svn

- 忽略文件夹以文件下的所有文件
demo, demo/1.c, demo/2.c都还未添加到版本控制，现在我们想要忽略文件夹demo,以及demo下的现有文件或将来会添加的文件，我们可以执行操作：

``` sh
cd project
svn propedit svn:ignore .
```

在打开的文件中添加一行，写入：demo, 每一行一个匹配模式

- 忽略文件
log文件还未添加到版本库，现在想要忽略它

``` sh
cd project
svn propedit svn:ignore .
```

在打开的文件中添加一行，写入：log





