---
title: "在DOS下的浏览器"
date: 2023-03-27
categories: 
  - "dos"
  - "教程"
---

#### ps：由于是网站之前的文章，所以在最新版本的Wordpress可能会有bug，图片原文件已经全部扑街了

## **让****DOS****连接网络**

注：如果你没有安装鼠标驱动，你需要先安装鼠标驱动

想让DOS连接网络，就要先安装网卡驱动

安装网卡驱动需要Microsoft Network Client for DOS软盘

至于安装过程我就不多说了，[点击这里查看Microsoft Network Client for DOS安装教程和下载链接](http://www.kompx.com/en/network-setup-in-dos-microsoft-network-client.htm)

如果你觉得麻烦可以直接使用AMD的Packet Driver驱动

这是这个驱动的下载链接:[https://www.123pan.com/s/9piDVv-6OkVv.html](https://www.123pan.com/s/9piDVv-6OkVv.html)提取码:0M3f

这个驱动的运行代码是:

```
<驱动路径>pcntpk int=0x60
```

(可以直接添加到autoexec.bat）

那么驱动就安装完成了，网络就可以连接了，下面我们就要安装浏览器了

### 若是dosbox-x

你需在dosbox-x安装目录中的dosbox-x.conf配置文件中启用ne2000并使用pcap，并在realnic = 后面写上"自己的网卡名称”，就像这样

```
[ne2000]
ne2000 = true
nicbase = 300
nicirq = 10
macaddr = random
backend = pcap
[ethernet, pcap]
realnic = "Intel(R) 82579V Gigabit Network Connection"
timeout = default
```

[这是Github上的NE2000工具集](https://github.com/gammy/dos_net_ne2000)，其中PKTDRV目录里面就包含NE2000的驱动，把它下载下来并用命令把它挂载，进到目录里面运行以下命令

```
SET MTCPCFG=在dosbox-x下的工具集目录\TCP\samples\sample.cfg
PKTDRV/NE2000 0x60 10 0x300
TCP/dhcp
```

这样网络就配置好了

你可以吧这些命令修改后添加进dosbox-x.conf文件中\[autoexec\]的下面

这样就可以联网了

## **Arachne****浏览器**

Arachne浏览器是DOS浏览器的首选，因为这个浏览器是本文章中安装第二简单浏览器还是图形浏览器

Arachne浏览器官网下载:[glennmcc.org](http://glennmcc.org/arachne/)

下面我们开始安装arachne，我们已经将安装文件拷贝到了C盘的根目录下，运行一下就可以了。

我们回答两个"Y"，然后我们静静等待

安装很快就结束了，然后进入设置界面，两个问题是memory type和video card，都回车就好了

此时，返回到了DOS提示符下，你的arachne已经被成功第安装到了c:\\arachne目录下，现在只要输入arachne就可以启动浏览器了。

在第一次使用时，arachne会要求先进行设置，前两个问题和刚才的一样，都回车就好了

这个时候，我们用鼠标就比较方便了，你可以用鼠标在里面随便点一下

如果用不了鼠标，请按“捕获”，这样你的鼠标就可以使用了，记得，当希望退出鼠标在guest中的使用时，请按右边的“ALT”键

如果你的屏幕支持更高的分辨率，可以点1024 X 768，然后点击下面的“Try selected graphics mode”按钮

我们点击“Packet Wizard”，然后点击“Detect packet driver”再然后点击“Continue”

如果你使用DHCP，直接点击“BOOTP or DHCP”，你的设置就完成了，如果你不使用DHCP，请点击“Manual Setup”按钮，下面的界面

这里是设置的主要地方，按照我们在安装前收集的信息进行填写后，按“Continue”按钮

如果你当初选择使用DHCP的话，也会看到这个界面，直接按“Finish”浏览器就安装好了

再次说一下，arachne不支持中文，浏览中文网页会看到乱码，不过arachne是个开源项目，有兴趣有能力的读者不妨试试让它支持汉字。

## **Dillo for DOS****浏览器**

这个浏览器是DOS浏览器的第二选择，因为它的更能很多，是跟现代浏览器最像的

这个是他的原链接:https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/nanox-microwindows-nxlib-fltk-for-dos/DILLODOS-302b.zip

Dillo for DOS下载链接：https://www.123pan.com/s/9piDVv-yOkVv.html提取码:7i5a

这个浏览器可能需要多一点的内存，可以结合上一期的DOS扩展器文章来使用

将您下载的存档解压缩到目录中 ，例如 C:\\dillodos\\

这个浏览器需要配置一下

DILLODOS解压后的文件夹中有一个 DILLO.BAT 文件，它将设置环境变量并启动 Dillo。

须自行检查此批处理文件中的路径名以匹配您的环境。

默认批处理文件当前如下所示：

```
@echo off
REM Customize these lines for your system:
REM --------------------------------------
set DILLO=C:\DILLODOS
set NANOSCR=1024 768 8888
REM -------------------------------------------
REM You shouldn't need to edit below this line.
REM -------------------------------------------
set WATTCP.CFG=%DILLO%\ETC
%DILLO%\bin\dillo.exe %1 %2 %3 %4 %5 %6 %7 %8 %9
```

第一个环境变量 DILLO 必须指向 DILLODOS 目录或你命名的任何内容。检查驱动器号以匹配您的环境。

NANOSCR 变量定义为：

```
NANOSCR=[SCREEN_WIDTH] [SCREEN_HEIGHT] [SCREEN_PIXTYPE]
```

对于SCREEN\_PIXTYPE以下值有效：8888 表示 32 位，888 表示 24 位，565 表示 16 位。24 位值当前未 测试。在 16 位模式下，某些颜色可能无法正确呈现。

要允许使用 SNARF 进行屏幕截图，请将 DILLO.BAT 文件中的像素类型条目设置为 16 位。

WATTCP.CFG 环境规则变量由 Watt32 TCP/IP 驱动程序读取，并指向 WATTCP.CFG 文件，位于 BIN 子目录中。

如果将Dillo的STDOUT消息重定向到文件，您将无法运行从Dillo菜单中选择的DOS shell

如果配置完成，直接运行DILLO.BAT就可以运行浏览器了

注意如果没添加显卡驱动，软件可能会出现黑屏无法启动现象，dosbox和dosbox-x不会出现此现象

具体详情请看解压文件后目录下的USRGuide.pdf文件

### **添加中文？**

这是这个浏览器拥有添加字体的功能

在软件的根目录里有个Fonts文件夹，我们可以把中文字符集TTF文件添加进里面

然后重启软件就可以浏览中文了吗？实际上浏览网页会死机，估计是硬件配置太低了，有成功的可以发在评论区。

## **Links****浏览器**

这是一个纯文本浏览器，它不支持中文

官方下载链接：[Twibright Labs：链接 - 下载](http://links.twibright.com/download.php)

直接运行目录里的links-2，等一下

如果出现错误：Load error: no DPMI - Get csdpmib.zip ，可以把上一期文章的DPMI接口文件复制到这个软件的安装路径根目录，然后再运行

运行之后如果是黑屏的，把鼠标移动到最上方，然后左键召唤出菜单，点击菜单里的file，然后再点击go to url，输入网页地址就可以浏览网页了

## **DosLynx**

这也是个纯文本浏览器，添加中文的方法是在LYNX.CFG文件中设置“CHARACTER\_SET:euc-cn”

下载链接：[http://www.fdisk.com/doslynx/EXE\_16A.ZIP](http://www.fdisk.com/doslynx/EXE_16A.ZIP)

我们先运行根目录里的TCPINFO文件

然后再打开doslynx.exe

## **结尾**

那么本文章中所有浏览器教程到此结束

如果你嫌安装麻烦，可以直接使用[freedos](https://www.freedos.org/download/)
