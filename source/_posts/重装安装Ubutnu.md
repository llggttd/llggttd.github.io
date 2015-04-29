title: "Ubuntu下重新安装Ubuntu"
date: 2015-04-29 15:30:00
updated: 2015-04-30 07:30:00
layout: post
comments:   true
categories:
- Ubuntu
---

## 通过grub引导ubuntu镜像文件来安装系统

下载了ubuntu的镜像文件，如何安装? 如果你还有光盘刻录设备，可以把ubuntu系统镜像刻录到光盘来进行安装，但现在很少人还在用有光驱的机器了吧。你也可以使用工具（windows下有ultraiso, ubuntu下有startup disk creater)把光盘镜像写入到u盘，通过u盘来安装。其实不用这么麻烦，镜像文件不是已经下载到你的硬盘了吗，我们可以直接从硬盘来安装系统。

*本安装方法，仅适用于已经有grub2引导环境的情况*

- 准备iso镜像文件

    ubuntu最新发行版下载地址：[ubuntu](http://releases.ubuntu.com/)

- 拷贝iso文件到指定目录

    把iso文件拷贝到一个路径相对简洁一些的目录。因为在grub下你要用到这个目录， 太长了输起来麻烦。比如放在：/boot/下。
    *注意不要放在中文目录中，在grub下，你是无法输入中文的*

- 引导iso

    + 如果你现在的系统是ubuntu, 可以通过修改文件/etc/grub.d/40-custom来添加启动项。
    打开40-custom, 输入以下内容：

    ``` sh
    menuentry "ISO" {
        set isofile="/boot/iso/ubuntu-15.04-desktop-amd64.iso"
        loopback loop (hd0, 1)$isofile
        linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile
        initrd (loop)/casper/initrd
    }
    ```
    上面isofile是光盘镜像的位置，根据自己的情况进行设置。

    对上面几个命令的说明：

        - menuentry

        设定启动菜单的标题，也就是在grub启动选择菜单中看到的内容

        - set isofile

        设置变量isofile, 指向iso镜像文件的路径， 因为iso的路径比较长，设置变量方便其它地方的输入

        - loopback

        挂载镜像文件，这里要指明镜像文件的绝对路径。
        (hd0, 4)表示硬盘0的4号分区， 可以通过命令df查看自己电脑上的具体情况

        - linux

        指定要搜索镜像的路径和要加载的内核路径

        - initrd

        初始化临时的根文件系统
        
    然后运行sudo update-grub来重新生成启动菜单。重启电脑，进入grub的启动菜单，就可以看到ISO的启动项了。

    + 如果不想添加启动项， 也可以直接在grub的命令环境下手动引导

    开机重启后，按住shift键直到进入grub菜单界面，再按C进入命令行模式。

    把上面启动菜单项的命令，在这里执行一遍。
