title: "Ubuntu下重新安装Ubuntu"
date: 2015-04-29 15:30:00
updated:
layout: post
comments:   true
categories:
- Ubuntu
- 系统
---

## 准备iso镜像文件

## 拷贝iso文件到指定目录

## 修改系统现有引导菜单

    /etc/grub.d/40-custom

    ``` sh
    menuentry "ISO" {
        set isofile="/boot/iso/ubuntu-15.04-desktop-amd64.iso"
        loopback loop (hd0, 1)$isofile
        linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=$isofile
        initrd (loop)/casper/initrd
    }

## step by step

- menuentry

    The menuentry title (between the quotations) is determined by the user. Any title can be used. The line must end with { 

- set isofile

    Designating an 'isofile' variable is not required but simplifies the creation of multiple Ubuntu ISO menuentries. By using this convention, the user can copy an existing ISO menuentry and, if the ISO is located in the same folder, simply change the menuentry title and the name of the ISO file on this line. No other changes are necessary.

    The set isofile line should reflect the exact name of the ISO file 

- loopback

    The loopback line must reflect the actual location of the ISO file. In the example, the ISO file is stored in the user's Downloads folder.
        X is the drive number, starting with 0; Y is the partition number, starting with 1
            sda5 would be designated as (hd0,5); sdb1 would be (hd1,1) 
    Other Location Examples:

        (hd0,5)/boot/$isofile
            
        Located in the system's normal /boot partition on sda5

        (hd0,6)/$isofile
            

        Located in a separate boot partition on sda6

        (hd0,7)/username/Downloads/$isofile
            

        Located in a separate home partition on sda7

        (hd1,2)/iso/$isofile
            
        Located in the /iso folder of the sdb2 data partition

- linux

    This line is specific to the Ubuntu family of ISOs. Other Linux distributions and system ISOs may use different filenames, folders and switches to boot their ISOs. Check the Grub2/ISOBoot/Examples page to see if an example exists. The user can also mount the ISO and explore its contents to ensure the menuentry contains the correct filenames and paths. 

- initrd

    See the linux line notes. Additionally, some other systems use initrd.gz rather than initrd.lz on their ISOs. 