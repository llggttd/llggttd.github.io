---
title: sed命令介绍
date: 2017-09-27 10:00:15
updated: 2017-09-27 10:00:15
categories:
tags:
---

*sed是一个非交互式的流编辑器（stream editor）。所谓非交互式，是指使用sed只能在命令行下输入编辑命令来编辑文本，然后在屏幕上查看输出；而所谓流编辑器，是指sed每次只从文件（或输入）读入一行，然后对该行进行指定的处理，并将结果输出到屏幕（除非取消了屏幕输出又没有显式地使用打印命令），接着读入下一行。整个文件像流水一样被逐行处理然后逐行输出。*

*sed一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区(pattern space)中的内容，处理完成后，把缓冲区(pattern space)的内容送往屏幕。接着清空缓冲区(pattern space)，处理下一行，这样不断重复，直到文件末尾。*

*SED维护两个数据缓冲区：主动模式空间（pattern space）和辅助保留空间（hold space）。在操作过程中，SED从输入流读取一行存入pattern space，这里就是文本编辑操作发生的地方。hold space最初是空的，但也有在pattern space和hold space直接移动数据的命令。*

[官方文档地址](http://www.gnu.org/software/sed/manual/sed.html)

#### sed命令语法

使用sed命令的基本形式如下：

- ` sed OPTIONS... [SCRIPT] [INPUTFILE...] `
- ` sed OPTIONS... [addr]X[options] [INPUTFILE...] `

sed命令行选项如下：

 - --version 
    打印版本信息

 - --help
    打印帮助信息

 - -n
   --quiet
   --silent
    默认情况下，sed命令在每轮循环结束时打印出模式空间中的内容，这个选择可以关闭自动打印，除非使用命令p指定打印。

 - -e script
   --expression=script
    指定命令运行的脚本内容

 - -f script-file
   --file=script-file
    指定命令运行的脚本文件

 - -i[SUFFIX]
   --in-place[=SUFFIX]
    默认情况下sed命令把处理结果输出到标准输出上，通过这个命令可以指定处理结果输出到操作文件本身。如果指定了后缀SUFFIX, 再写入内容到文件前先对文件进行备份，并以SUFFIX为后缀

 - -l N
   --line-length=N
    指定换行长度，0表示不换行，若不指定默认为70

- -E
  -r
  --regexp-extended
    使用扩展的正则表达式

 - -s
   --separate
    默认情况下，sed会所传入的文件列表作为一个文件流来处理，使用此选择可以让每个文件单独处理

 - --sandbox
    在沙盒模式下，e/w/r命令不能执行，包含它们的命令会被取消。

 - -z
   --null-data
   --zero-terminated

    Treat the input as a set of lines, each terminated by a zero byte (the ASCII ‘NUL’ character) instead of a newline. This option can be used with commands like ‘sort -z’ and ‘find -print0’ to process arbitrary file names. 

#### sed的基本命令

|命令         |说明         |示例         |
|:------     |:------       |------     |
|a          |附加行     |sed -i 'a----' 1.txt, 在1.txt每行后添加一行"----"|
|c          |替换行     |sed -i '/----/c++++' 1.txt, 把1.txt每行"----"替换成"++++"|
|d          |删除行     |sed -i '/++++/d' 1.txt, 把1.txt每行"++++"删除|
|g          |把hold space中的内容写到pattern space|sed -i '/----/c++++' 1.txt, 把1.txt每行"----"替换成"++++"|
|i          |删除行     |sed -i '/----/c++++' 1.txt, 把1.txt每行"----"替换成"++++"|
|h          |把pattern space中的内容写到hold space|sed -i '/----/c++++' 1.txt, 把1.txt每行"----"替换成"++++"|
|p          |打印patten space     |sed -i '/----/c++++' 1.txt, 把1.txt每行"----"替换成"++++"|
|q          |退出命令     |sed -i '/----/c++++' 1.txt, 把1.txt每行"----"替换成"++++"|
|s          |替换pattern space中的内容     |    |
|x          |交换pattern space和hold space的内容     |    |
|z          |清空pattern space|    |


#### 匹配地址

匹配地址决定了sed命令会对哪些行进行处理，若不指定匹配地址，默认会在所有行执行。

- 指定行 - 具体行号
`sed '144s/hello/world/' input.txt > output.txt` 把144行的hello替换成world

- 指定行 - 行号范围
`sed '144,244s/hello/world/' input.txt > output.txt` 把144行到244行的hello替换成world
`sed '144,$s/hello/world/' input.txt > output.txt` 把144行到最后一行的hello替换成world

- 指定行 - 步进范围
`sed '0~2s/hello/world/' input.txt > output.txt` 把偶数行的hello替换成world
`sed '1~2s/hello/world/' input.txt > output.txt` 把奇数行的hello替换成world

- 指定行范围反选 - *!*
`sed '144,244!s/hello/world/' input.txt > output.txt` 把144行到244行之外的行的hello替换成world

- 使用正则表达式
`sed '/apple/s/hello/world/' input.txt > output.txt` 把包含apple行的hello替换成world



