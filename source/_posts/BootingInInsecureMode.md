title: "booting in insecure mode"
date: 2015-05-06 21:03:52
categories:
- 系统
- Ubuntu
---

最新安装了Ubuntu 15.04, 发现启动时屏幕显示"Booting in insecure mode", 网上一番搜索，发现这是Ubuntu 14.10引入的一个Bug, 在15.04还没有修复。这行文字会显示3秒，然后自动消失，对系统的使用没有什么影响。但如果你看着不爽，要把它去掉，可以缷载掉shim。

``` sh
sudo apt-get purge shim shim-signed
sudo grub-install
```

重启电脑，整个世界都清净了。（强迫症就是这样养成的）
