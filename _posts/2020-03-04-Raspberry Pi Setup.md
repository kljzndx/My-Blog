---
layout: post
title: 树莓派装机日记
categories: 硬件
tags: [树莓派, RaspberryPi]
---

两个星期前我买的 《树莓派4B 4G版》 经过千山万水终于到了我手上，为啥说的这么艰难呢    
因为我是1月20号下单的，直到2月19号才收到货🤣    
ok，闲话不多说，其实呢，装机这一块其实我到手后的第二天就基本搞定了    
为啥要现在写文章呢，因为装机步骤相对来说太TM多了，而且我也踩了一两个坑     
      
现在开始正题，我会尽量把步骤写详细点的     

前期准备
======================================================

| 设备 | 数量 |  
| :---- | :---: |  
| 电脑 | 一台 |  
| 树莓派主板 | 一块 |  
| TF卡 | 一张 |  
| 读卡器 | 一个 |  
  
系统下载和安装
==========================================

我用的是树莓派官方系统 -- Raspbian ，所以我也将以该系统来演示

首先到 树莓派官网 下载系统镜像

系统镜像下载地址： https://www.raspberrypi.org/downloads/raspbian/

下完之后把压缩包解压一下，你会得到一个扩展名为 img 的镜像文件，这就是下一步要用到的文件了

![Raspbian 系统下载]({{'/blog-imgs/Rpi/system-download.png' | prepend:site.baseurl}})

接着我们还要去下个烧录工具 balena Etcher 

下载地址： https://www.balena.io/etcher/

至于这工具怎么安装我就不说了，连这点动手能力都没有的话，我劝你还是别玩树莓派了吧

关于怎么烧录也基本不用说，把TF卡插进读卡器，再把读卡器插进电脑，然后打开工具跟着提示做就行了

![balena Etcher]({{'/blog-imgs/Rpi/balenaEtcher.png' | prepend:site.baseurl}})

这样就把系统烧录好了

注意：烧录完成后有可能会自动把你的卡弹出来，重新插一下TF卡或读卡器就行了

这时候你会发现你的卡里有两个分区其中一个还读不出来，另外一个能读出来的分区是boot分区，也就是启动分区

我们将在这个分区的根目录下新建两个文件，一个是名为ssh的空文件来启用ssh远程连接，还有一个是wifi配置文件

首先咱们来新建 ssh 文件

在空白处按住shift键再点击鼠标右键，选择“在此处打开 PowerShell 窗口”

![右键菜单]({{'/blog-imgs/Rpi/right-tap-menu.png' | prepend:site.baseurl}})

输入以下命令

```powershell
New-Item ssh
```

![新建空文件命令]({{'/blog-imgs/Rpi/new-ssh.png' | prepend:site.baseurl}})

这样就算把 ssh远程连接 开启了

以同样的方式创建 wifi配置文件

```powershell
New-Item wpa_supplicant.conf
```

用记事本打开这个文件并把以下内容复制进去

```
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="wifi名称"
    psk="密码"
    priority=1
}
```

接着再填写下wifi名称和密码就行了，**注意别把引号给删了**

ok，到此为止 ssh 和 wifi 配置工作就已经完成了，现在可以安全弹出读卡器并拔掉读卡器，再把TF卡拔出来并插在树莓派上了

接下来我将详细说下远程工具的安装和配置



远程工具安装
=========================================

首先你必须要有一个 ssh客户端，要不然你是不能远程控制这个树莓派的

如果你的系统是 win 10 1803 及以上的版本，那么你可以开启系统自带的ssh客户端  
如果你是1803以下版本甚至是win7，那么你就需要自己装一个ssh客户端了

本文只介绍系统自带的ssh客户端的安装方法

系统自带的ssh客户端安装方法如下

首先打开设置，点击“应用”选项卡，接着再点击“可选功能”按钮

![应用选项卡界面]({{'/blog-imgs/Rpi/setup-ssh-step1.png' | prepend:site.baseurl}})

然后点击“添加功能”，接着找到并选中 OpenSSH 服务器，再点击安装即可

![可选功能界面]({{'/blog-imgs/Rpi/setup-ssh-step2.png' | prepend:site.baseurl}})

至此 ssh客户端 就安装好了

接着咱们还需要装一个远程桌面工具 -- VNC Viewer  
下载地址： https://www.realvnc.com/en/connect/download/viewer/  
这工具也是傻瓜式安装，就不说安装步骤了

ok，远程工具已经全部安装好了，可以开始配置工作了

vnc 远程桌面工具配置
===========================================

首先你必须要先知道你的光猫/路由器给你的树莓派分配的ip地址是多少   
**提示：如果你开启了光猫/路由器的AP隔离功能，请把他关了，他会阻止设备间相互访问**

查看方式有很多，你可以直接去光猫/路由器的后台查看，也可以借助一个ip扫描工具查看   
Advanced IP Scanner: https://www.advanced-ip-scanner.com/cn/

我是直接在光猫后台看的   
![查看ip地址]({{'/blog-imgs/Rpi/look-ip-address.png' | prepend:site.baseurl}})

这里的 192.168.1.41 就是我的树莓派的ip地址  
知道ip地址后就可以用远程工具连接了

首先打开powershell，当然cmd也可以，看你习惯了   
输入 `ssh pi@192.168.1.41` 回车，接着再输入密码 raspberry 回车  
需要注意的是输入密码时是不会有任何显示的   
你可能会想问用户名在哪，其实用户名就是 "@" 符号前面的 "pi"，这是默认的用户名

这样就成功进入到树莓派里了

在命令行输入以下命令，注意美元符是不需要输入的

```bash
$ sudo raspi-config
```

通过上下方向键选择 Interfacing Options，回车

![启用vnc-步骤]({{'/blog-imgs/Rpi/enable-vnc-step1.png' | prepend:site.baseurl}})

再选择 vnc 

![启用vnc-步骤]({{'/blog-imgs/Rpi/enable-vnc-step2.png' | prepend:site.baseurl}})

然后选择 Yes

![启用vnc-步骤]({{'/blog-imgs/Rpi/enable-vnc-step3.png' | prepend:site.baseurl}})

完成vnc开启后，我们还需要设置下分辨率，要不然等会用工具连接的时候会报错   
选择 Advanced Options

![分辨率调整]({{'/blog-imgs/Rpi/fbl1.png' | prepend:site.baseurl}})

选择 Resolution

![分辨率调整]({{'/blog-imgs/Rpi/fbl2.png' | prepend:site.baseurl}})

选择 1280 x 720 60HZ

![分辨率调整]({{'/blog-imgs/Rpi/fbl3.png' | prepend:site.baseurl}})

回车选择之后他应该会提示需要重启，再按一下回车让他重启就行  
如果没提示，请通过左右方向键选择 Finish 来退出设置界面

![退出设置界面]({{'/blog-imgs/Rpi/enable-vnc-step4.png' | prepend:site.baseurl}})

然后输入 `sudo reboot` 再回车即可让树莓派重启

![重启命令]({{'/blog-imgs/Rpi/reboot.png' | prepend:site.baseurl}})

ok，vnc server 已经配置好了  
现在可以使用 vnc viewer 进行远程连接了

打开vnc viewer 工具，按下 ctrl + N 会打开新建连接对话框

![vnc 新建连接窗口]({{'/blog-imgs/Rpi/vnc-viewer1.png' | prepend:site.baseurl}})

填写下 vnc server 和 name 就行了   
vnc server 里面写树莓派的ip地址  
name 就随便写了  
都填好了之后就点下 ok 按钮

------

双击你刚才创建好的连接项目   
这时候他就会开始连接树莓派，并且会弹出对话框让你输入用户名和密码

![vnc 正在连接窗口]({{'/blog-imgs/Rpi/vnc-viewer2.png' | prepend:site.baseurl}})

输入 pi 和 raspberry ，然后点击ok  
这样就进入树莓派的桌面环境了

![树莓派桌面环境]({{'/blog-imgs/Rpi/vnc-viewer3.png' | prepend:site.baseurl}})

第一次进入桌面环境时他会弹出一个初始化向导，跟着提示走一遍就成了  
到此 vnc 和 树莓派的桌面环境 就都配置好了

OpenSSH免密配置
==================================

首先我们得先创建一对密钥文件，一个公钥和一个私钥   
公钥是放在树莓派上的，私钥是放在电脑上的

那怎么创建呢，很简单，还是一条命令，打开PowerShell，输入以下命令

```
ssh-keygen -t rsa
```

回车之后 他会先问你要保存在哪，然后会问你要不要设个密钥  
我是都保持默认的，也就是一路回车过去的

![密钥文件创建]({{'/blog-imgs/Rpi/rsa-key-create.png' | prepend:site.baseurl}})

现在咱们打开保存位置，至少会看到两个文件

![密钥文件保存位置]({{'/blog-imgs/Rpi/key-save-location.png' | prepend:site.baseurl}})

其中带pub后缀的就是公钥，另外的那个就是私钥

我们先来把公钥添加进树莓派  
因为这次涉及到粘贴内容的操作，纯靠命令行实在太麻烦了  
所以我们先打开 vnc viewer 进入桌面环境

其实我们要做的也就是新建一个文件再复制些内容而已

首先来打开左上角的LX 终端，并输入以下命令

```bash
$ mkdir -p ~/.ssh
```

接着用cd命令进去这个“.ssh”文件夹

```bash
$ cd ~/.ssh/
```

之后输入以下命令来打开文本编辑器并新建文件

```bash
$ mousepad authorized_keys
```

这时用记事本打开之前生成的 “id_rsa.pub” 文件，将里面的内容全部复制，再粘贴到树莓派里的文本编辑器中，然后保存文件，这样公钥就添加好了

------

然后我们再来配置电脑上的 ssh，其实我感觉这也算不上配置  
因为只需新建个文件，再写点东西就行了

先来打开之前密钥生成命令显示的**默认保存位置**，注意：要打开的是**默认保存位置**

打开之后先看看有没有 config 文件，有就直接用记事本打开，没有就用之前说过的 `New-Item` 命令新建一个，总之必须得有这个文件，然后他里面的内容得像这样

![ssh 密钥配置]({{'/blog-imgs/Rpi/setup-ssh-key.png' | prepend:site.baseurl}})

```
Host raspberryPi
  HostName 192.168.1.41
  User pi
  IdentityFile C:/Users/kljzn/.ssh/id_rsa
```

我先来说下这些属性是干嘛用的

| 属性名       |             值             |  
| ------------ | :------------------------: |  
| Host         | 除了空格和特殊字符外随便写 |  
| HostName     |       ip地址 / 域名        |  
| User         |         登录用户名         |  
| IdentityFile |       私钥文件的路径       |  

这里有个属性需要特别说明下，就是 `Host` 属性，这个属性后面跟的值是一个别名  
设置这个别名之后以后需要使用ssh远程时就能像下面这么写了  

```
ssh 别名
```

不需要用 `user@host` 的写法了

言归正传  
如果你是本来就有这个config文件，你很可能会发现你的文件内是没有 `IdentityFile` 属性的，这是正常的，因为这文件是我已经配置好的了，换句话说就是这属性是我加的

总之最关键的就是 `IdentityFile` 属性   
这个属性后面跟的文件路径最好是绝对路径，并且路径不能有空格，还有就是最好不要有中文字符，其他的就没什么要注意的了

关于路径的提取其实有个小技巧，按住 shift 键再用鼠标右键点击文件   
弹出的菜单中会有个“复制为路径”的选项的

![复制为路径]({{'/blog-imgs/Rpi/copy-path.png' | prepend:site.baseurl}})

需要注意的是用这个复制的路径，路径的两头是带引号的，粘贴的时候别忘了把引号给删了

```
"C:\Users\kljzn\.ssh\id_rsa"
```

到此，ssh的免密配置就搞好了，接下来是最后一部分，更换apt软件源

更换APT软件源
==============================

打开 vnc viewer 来进入树莓派的桌面环境

再打开 “LX终端”，输入以下命令，注意美元符是不需要输入的

```bash
$ sudo mousepad /etc/apt/sources.list
```

在第一行开头加个井号，并把以下代码粘贴到最后一行

```
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib
```

最终结果要像下面这样

![更换apt软件源]({{'/blog-imgs/Rpi/change-apt-source1.png' | prepend:site.baseurl}})

除了这个外还有一个系统软件源需要更换，再次输入以下命令

```bash
$ sudo mousepad /etc/apt/sources.list.d/raspi.list
```

也是在第一行开头加个井号，并把以下代码添加到最后

```
deb http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
```

最终结果要像下面这样

![更换apt软件源]({{'/blog-imgs/Rpi/change-apt-source2.png' | prepend:site.baseurl}})

最后还需要在终端中输入以下命令来更新软件索引

```bash
$ sudo apt-get update
```

![更换apt软件源]({{'/blog-imgs/Rpi/change-apt-source3.png' | prepend:site.baseurl}})

至此软件源的更换就完成了

现在你可以输入以下命令来更新所有软件 

```bash
$ sudo apt-get dist-upgrade
```

这个更换软件源的教程我是参考了下清华大学镜像站的文档的   
https://mirrors.tuna.tsinghua.edu.cn/help/raspbian/

