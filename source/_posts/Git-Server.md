title: "Windows平台下创建Git服务器"
date: 2015-05-14 18:17:54
updated: 2015-05-14 18:17:54
categories:
- 工具
- Git
---

如果想在Windows平台上实现一个Git服务器，可以通过apache+git组合来实现。

步骤如下：

- 下载Git for Windows

    首先，在Windows平台上，要先安装Git的Windows版本

    [git for windows](http://msysgit.github.io)

    注意： 安装时，要把git和其相关工具添加到系统环境变量

- 设置Git仓库的根目录

    在系统上创建一个目录，如：D:\GITROOT, 这个目录将要做为所有git项目的存放仓库。

- 创建测试git项目

    进入到Git仓库的根目录（D:\GITROOT),执行命令：

    git init --bare io.git

    这样就会在D:\GITROOT下创建一个名为io.git的目录，并进行初始化。选项“--bare”用来指明
    这个项目为一个公共项目

- 调试git-http-backend

    在git的安装目录libexec/git-core下，有一个可执行文件git-http-backend.exe, 这个程序
    是专门处理http请求的。
    
    要想这个程序正常工作，拷贝git安装目录下bin/libiconv-2.dll到libexec/git-core/下，然后在命令行下运行git-http-backend， 如果能正常返回"Status: 500 Internal Server Error"，说明git-http-backend.exe执行环境正常

- 配置apache的httpd.conf

    为了能通过http来访问Git Server, 需要通过apache把对git的请求，转发给git-http-backend, 处理完成会再通过apache返回。

``` sh
# 设置Git仓库的根目录, 这就是之前创建的那个目录
SetEnv GIT_PROJECT_ROOT D:/GITROOT

# 暴露所有仓库，如果不设置它的话，我们必须在每一个子仓库中放置一个名为
# git-daemon-export-ok的文件才行
SetEnv GIT_HTTP_EXPORT_ALL

# 匹配请求，转发给git-http-backend.exe
# 我这里是把git的根仓库放在了服务器的子目录git下，http链接形式为：
# http://crazyman.top/git/io.git, 如果放在其它的目录下，修改正则表式
# git-http-backend.exe的路径要根据自己的安装位置设置，不要漏掉末尾的"$1"
ScriptAliasMatch "(?x)^/git/(.*/(HEAD | info/refs | objects/(info/[^/]+ | [0-9a-f]{2}/[0-9a-f]{38} | pack/pack-[0-9a-f]{40}.(pack|idx)) | git-(upload|receive)-pack))$" "C:/Program Files/Git/libexec/git-core/git-http-backend.exe/$1"

# 添加认证
# 其中认证文件为纯文本，形如：
# root:123456
# admin:123456
# 认证文件这里存放在了GIT仓库的根目录下
<Location /git/>
    AuthType Basic
    AuthName "Auth For Git"
    AuthUserFile "D:/GITROOT/passwd"
    Require valid-user
</Location>
```

修改完httpd.conf后，重启apache让改动生效

- 测试git服务器

    以下工作都完成后，可以其它机器上进行测试，

    git clone http://crazyman.top/io.git

    命令会要求帐号和密码，如果能正常把io.git仓库克隆到本地，说明Git Server搭建成功。

- ALL IS DONE
