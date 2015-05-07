title: "使用Rsync"
date: 2015-05-07 10:55:10
tags:
categories:
- 工具
- rsync
---

## rsync 命令

### rsync的命令格式可以为以下六种：

- rsync [OPTION]... SRC [SRC]... DEST

本地文件同步，即SRC和DES在同一台机器上。SRC可以指定多个目录或文件。如:

``` sh
rsync -arv ~/www /var/www
```

- rsync [OPTION]... SRC [SRC]... [USER@]HOST:DEST

使用一个远程shell程序(如rsh、ssh)来实现将本地机器的内容拷贝到远程机器。
DEST有两种写法：相对当前用户家目录的路径和绝对路径。如:

``` sh
rsync -arv test ozone@crazyman.top:temp
rsync -arv test ozone@crazyman.top:/home/ozone/temp
```

- rsync [OPTION]... SRC [SRC]... [USER@]HOST::DEST

从本地机器拷贝文件到远程rsync服务器中。
DEST此时不再是路径，而是再服务器上rsync配置的模块名称。rsync的具体配置以后我会详细讲解。
如：

``` sh
rsync -arv test ozone@crazyman.top::www
```

- rsync [OPTION]... SRC [SRC]... rsync://[USER@]HOST[:PORT]/DEST

从本地机器拷贝文件到远程rsync服务器中。和使用"rsync [OPTION]... SRC [SRC]... [USER@]HOST::DEST"相同， 此时的DEST也是模块名称，不是路径。如：

``` sh
rsync -arv test rsync://ozone@crazyman.top/www
```

- rsync [OPTION]... [USER@]HOST:SRC [DEST]

使用一个远程shell程序(如rsh、ssh)来实现将远程机器的内容拷贝到本地机器。SRC有两种写法：相对当前用户家目录的路径和绝对路径。当DEST省略时，只显示远程文件列表，不进行实际的拷贝工作。如:

``` sh
rsync -arv ozone@crazyman.top:temp test 
rsync -arv ozone@crazyman.top:/home/ozone/temp test 
```

- rsync [OPTION]... [USER@]HOST::SRC [DEST]

从远程rsync服务器中拷贝文件到本地机。SRC此时不再是路径，而是再服务器上rsync配置的模块名称。如：

``` sh
rsync -arv ozone@crazyman.top::www test
```

- rsync [OPTION]... rsync://[USER@]HOST[:PORT]/SRC [DEST]

从远程rsync服务器中拷贝文件到本地机。和使用"rsync [OPTION]... [USER@]HOST::SRC [DEST]"相同， 此时的DEST也是模块名称，不是路径。如：

``` sh
rsync -arv rsync://ozone@crazyman.top/www test
```

几个需要注意的地方：

- 从本地拷贝文件到远程计算机时，SRC可以指定多个
- 当DEST为本地路径时，DEST参数可以省略，省略时只会显示文件列表，不执行实际的同步工作，这在确认远程路径内容时非常有用
- 使用":"时，实际是通过remote shell连接，远程服务器不需要启动rsync服务。 使用"::"或"rsync:"时，连接到的是rysnc daemon, 所以这时要指定模块的名称

### rsync 命令参数

rsync参数很多，常用的参数如下：

- -v, --verbose                 详细模式输出
- -q, --quiet                   精简输出模式
- -q, --no-motd                 不显示欢迎信息
- -a, --archive                 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD
- -a, --no-OPTION               关闭-a中打开的某个选项(e.g. --no-D)
- -r, --recursive               对子目录以递归模式处理
- -d, --dirs                    只拷贝文件夹
- -R, --relative                使用相对路径信息
- 更新
    + -u, --update  跳过所有已经存在于DST，并且文件时间晚于要备份的文件
    + -u, --inplace
    + -u, --append
    + -u, --append-verify

- 文件备份
    + -b, --backup                  创建备份，也就是对于目的已经存在有同样的文件名时，将老的文件重新命名为~filename。可以使用--suffix选项来指定不同的备份文件前缀。
    + --backup-dir=DIR              将备份文件(如~filename)存放在在目录下。
    + --suffix=SUFFIX               定义备份文件前缀

- 链接处理
    + -l, --links                   保留软链结
    + -L, --copy-links              想对待常规文件一样处理软链结
    + --copy-unsafe-links           仅仅拷贝指向SRC路径目录树以外的链结
    + --safe-links                  忽略指向SRC路径目录树以外的链结
    + -H, --hard-links              保留硬链结
    + -k, --copy-dirlinks           把文件夹链接拷贝成一个链接文件
    + -K, --keep-dirlinks           把文件夹链接拷贝成一个文件夹

- 文件属性
    + -A, --acls                    保持ACLs
    + -X, --xattrs                  保持扩展属性
    + -p, --perms                   保持文件权限
    + -o, --owner                   保持文件属主信息
    + -g, --group                   保持文件属组信息
    + -D, --devices                 保持设备文件信息
    + -t, --times                   保持文件时间信息

- -S, --sparse                      对稀疏文件进行特殊处理以节省DST的空间
- -n, --dry-run                     现实哪些文件将被传输
- -W, --whole-file                  拷贝文件，不进行增量检测
- -x, --one-file-system             不要跨越文件系统边界
- -c, --checksum                    打开校验开关，强制对文件传输进行校验
- -B, --block-size=SIZE             检验算法使用的块尺寸，默认是700字节

- 文件排除
    + -C, --cvs-exclude                 使用和CVS一样的方法自动忽略文件
    + --exclude=PATTERN                 指定排除不需要传输的文件模式
    + --include=PATTERN                 指定不排除而需要传输的文件模式
    + --exclude-from=FILE               排除FILE中指定模式的文件
    + --include-from=FILE               不排除FILE指定模式匹配的文件

- --config=FILE                         指定其他的配置文件，不使用默认的rsyncd.conf文件
- --password-file=FILE                  从FILE中得到密码
- -z, --compress                        对备份的文件在传输时进行压缩处理
- - --progress                            在传输时现实传输过程
- --existing                            仅仅更新那些已经存在于DST的文件，而不备份那些新创建的文件
- --delete                              删除那些DST中有SRC没有的文件
--delete-excluded                       同样删除接收端那些被该选项指定排除的文件
--delete-after                          传输结束以后再删除
--ignore-errors                         及时出现IO错误也进行删除
--partial                               保留那些因故没有完全传输的文件，以是加快随后的再次传输
--force                                 强制删除目录，即使不为空