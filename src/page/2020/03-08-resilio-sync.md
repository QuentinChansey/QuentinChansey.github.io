---
title: 初次使用Resilio Sync的权限问题
category: IT
---
初次使用Resilio Sync可能会遇到的，权限不足(没有写权限)，而无法进行后续的操作使用。记录一下解决方法
<!--more-->
如题，第一次使用Resilio Sync会默认以rslsync用户以及rslsync用户组来运行程序，命令行敲上rslsync运行后，可以ps -aux | grep rslsync看到的确是以rslsync身份来运行，因此没有权限读写原本用户上的文件夹。解决的办法有2个，第1个是，可以对你打算拿来作分享同步用的文件夹，chown更改此文件夹的user和group，这样虽然可行，但是并不完美，以后每用一个文件夹就要改一次，很麻烦，倒不如用第2个办法，直接以原用户身份运行rslsync。
- - -
### 配置 ###
资料基本上参照archwiki和一些博客的教程，最后会贴出来。其实要说的，<font color="#FF0000">基本上wiki已经说得很清楚了</font>，我只是稍微翻译一下要点，推荐打开Resilio Sync的wiki配合理解。

首先pacman把rslsync包安装完之后，会有一个config文件和一个storage_path的路径，rslsync.conf存放一些参数的配置，storage_path所指向的路径文件夹存放的是一些metadata。这两项如果不特别指定，则默认是在/etc/rslsync.conf和/var/lib/rslsync/这2个地方。
接下来改办正事了，像wiki说的，首先复制一份模板到自己目录下：
``` bash
# rslsync --dump-sample-config > ~/.config/rslsync/rslsync.conf
```
然后就编辑这份东西，里面看到可以设置storage_path路径，以及login和password等等的参数，如果对隐私比较注重，把device_name改成一些不相关的东西。

### 开机自启 ###
输入 rslsync --help 可以看到有两个参数,--config <path>和--storage <path>，按实际情况设置好。
所有的用户服务都位于 ~/.config/systemd/user路径下。如果你想在首次用户登陆时运行服务，对想要自动启动的服务执行 systemctl --user enable service 即可。如无意外，重启一下电脑，然后 ps -aux | grep rslsync 就会看到是以你的用户运行了程序，而不是“rslsync”这个默认用户。
<font color="#663300">(如果选择采用service自启，当然正如一开始说，不指定私人的storage_path和config文件，使用默认给的/etc/rslsync.conf，配置好后，一样可以不指定--user来enable service)</font>

### 引用资料 ###
https://wiki.archlinux.org/index.php/Resilio_Sync
https://wiki.archlinux.org/index.php/Systemd/User_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87) <font color="#FF0000">//'工作原理'有服务在不同目录下的详细解释，以及'基础设置'有举例操作</font>

一位朋友在Ubuntu环境下的解决
https://medium.com/@justlaputa/run-resiliosync-btsync-as-normal-user-under-ubuntu-1498f7701a28

`rslsync --help`命令下的参数
https://www.getnas.com/resilio-sync-linux-guide/
