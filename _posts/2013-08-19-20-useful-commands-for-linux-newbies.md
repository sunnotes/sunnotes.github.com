---
layout: post
title: "对 Linux 新手非常有用的 20 个命令"
description:
category: "notes"
tags: [linux , shell , 转载]
---
{% include JB/setup %}

原文：[20 Useful Commands for Linux Newbies](http://www.tecmint.com/useful-linux-commands-for-newbies/)

转自：[oschina](http://www.oschina.net/translate/useful-linux-commands-for-newbies)

你打算从Windows换到Linux上来，还是你刚好换到Linux上来？哎哟！！！我说什么呢，是什么原因你就出现在我的世界里了。从我以往的经验来说，当我刚使用Linux，命令，终端啊什么的，吓了我一跳。我担心该记住多少命令，来帮助我完成所有任务。毫无疑问，在线文档，书籍，manpages以及社区帮了我一个大忙，但是我还是坚信有那么一篇文章记录了如何简单学习和理解命令的秘籍。这激发了我掌握Linux和使它容易使用的积极性。本文就是通往那里的阶梯。


##1. ls命令

ls命令是列出目录内容(List Directory Contents)的意思。运行它就是列出文件夹里的内容，可能是文件也可能是文件夹。

	root@tecmint:~# ls
	Android-Games                     Music
	Pictures                          Public
	Desktop                           Tecmint.com
	Documents                         TecMint-Sync
	Downloads                         Templates

“ls -l” 命令以详情模式(long listing fashion)列出文件夹的内容。

	root@tecmint:~# ls -l

	total 40588
	drwxrwxr-x 2 ravisaive ravisaive     4096 May  8 01:06 Android Games
	drwxr-xr-x 2 ravisaive ravisaive     4096 May 15 10:50 Desktop
	drwxr-xr-x 2 ravisaive ravisaive     4096 May 16 16:45 Documents
	drwxr-xr-x 6 ravisaive ravisaive     4096 May 16 14:34 Downloads
	drwxr-xr-x 2 ravisaive ravisaive     4096 Apr 30 20:50 Music
	drwxr-xr-x 2 ravisaive ravisaive     4096 May  9 17:54 Pictures
	drwxrwxr-x 5 ravisaive ravisaive     4096 May  3 18:44 Tecmint.com
	drwxr-xr-x 2 ravisaive ravisaive     4096 Apr 30 20:50 Templates

"ls -a" 命令会列出文件夹里的所有内容，包括以"."开头的隐藏文件。

	root@tecmint:~# ls -a
	
        .			.gnupg			.dbus			.goutputstream-PI5VVW		.mission-control
        .adobe                  deja-dup                .grsync                 .mozilla                 	.themes
        .gstreamer-0.10         .mtpaint                .thumbnails             .gtk-bookmarks          	.thunderbird
        .HotShots               .mysql_history          .htaccess		.apport-ignore.xml      	.ICEauthority           
        .profile                .bash_history           .icons                  .bash_logout                    .fbmessenger
        .jedit                  .pulse                  .bashrc                 .liferea_1.8             	.pulse-cookie            
        .Xauthority		.gconf                  .local                  .Xauthority.HGHVWW		.cache
        .gftp                   .macromedia             .remmina                .cinnamon                       .gimp-2.8
        .ssh                    .xsession-errors 	.compiz                 .gnome                          teamviewer_linux.deb          
        .xsession-errors.old	.config                 .gnome2                 .zoncolor


###注意：

在Linux中，文件以“.”开头的就是隐藏文件，并且每个文件，文件夹，设备或者命令都是以文件对待。ls -l 命令输出：

  1.d (代表了是目录).
  
  2.rwxr-xr-x 是文件或者目录对所属用户，同一组用户和其它用户的权限。
  
  3.上面例子中第一个ravisaive 代表了文件文件属于用户ravisaive
  
  4.上面例子中的第二个ravisaive代表了文件文件属于用户组ravisaive
  
  5.4096 代表了文件大小为4096字节.
  
  6.May 8 01:06 代表了文件最后一次修改的日期和时间.
  
  7.最后面的就是文件/文件夹的名字
  
  
##2. lsblk命令

"lsblk"就是列出块设备。除了RAM外，以标准的树状输出格式，整齐地显示块设备。

	root@tecmint:~# lsblk

	NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
	sda      8:0    0 232.9G  0 disk
	├─sda1   8:1    0  46.6G  0 part /
	├─sda2   8:2    0     1K  0 part
	├─sda5   8:5    0   190M  0 part /boot
	├─sda6   8:6    0   3.7G  0 part [SWAP]
	├─sda7   8:7    0  93.1G  0 part /data
	└─sda8   8:8    0  89.2G  0 part /personal
	sr0     11:0    1  1024M  0 rom

“lsblk -l”命令以列表格式显示块设备(而不是树状格式)。

	root@tecmint:~# lsblk -l

	NAME MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
	sda    8:0    0 232.9G  0 disk
	sda1   8:1    0  46.6G  0 part /
	sda2   8:2    0     1K  0 part
	sda5   8:5    0   190M  0 part /boot
	sda6   8:6    0   3.7G  0 part [SWAP]
	sda7   8:7    0  93.1G  0 part /data
	sda8   8:8    0  89.2G  0 part /personal
	sr0   11:0    1  1024M  0 rom

###注意：
lsblk是最有用和最简单的方式来了解新插入的USB设备的名字，特别是当你在终端上处理磁盘/块设备时。


##3. md5sum命令

“md5sum”就是计算和检验MD5信息签名。md5 checksum(通常叫做哈希)使用匹配或者验证文件的文件的完整性，因为文件可能因为传输错误，磁盘错误或者无恶意的干扰等原因而发生改变。

	root@tecmint:~# md5sum teamviewer_linux.deb
	
	47790ed345a7b7970fc1f2ac50c97002  teamviewer_linux.deb

###注意：
用户可以使用官方提供的和md5sum生成签名信息匹对以此检测文件是否改变。Md5sum没有sha1sum安全，这点我们稍后讨论。


##4. dd命令

“dd”命令代表了转换和复制文件。可以用来转换和复制文件，大多数时间是用来复制iso文件(或任何其它文件)到一个usb设备(或任何其它地方)中去，所以可以用来制作USB启动器。

	root@tecmint:~# dd if=/home/user/Downloads/debian.iso of=/dev/sdb1 bs=512M; sync
###注意：
在上面的例子中，usb设备就是sdb1（你应该使用lsblk命令验证它，否则你会重写你的磁盘或者系统），请慎重使用磁盘的名，切忌。
dd 命令在执行中会根据文件的大小和类型 以及 usb设备的读写速度，消耗几秒到几分钟不等。


##5. uname命令

"uname"命令就是Unix Name的简写。显示机器名，操作系统和内核的详细信息。

	root@tecmint:~# uname -a
	
	Linux tecmint 3.8.0-19-generic #30-Ubuntu SMP Wed May 1 16:36:13 UTC 2013 i686 i686 i686 GNU/Linux
	
###注意：
uname显示内核类别， uname -a显示详细信息。上面的输出详细说明了uname -a

“Linux“: 机器的内核名

“tecmint“: 机器的节点名

“3.8.0-19-generic“: 内核发布版本

“#30-Ubuntu SMP“: 内核版本

“i686“: 处理器架构

“GNU/Linux“: 操作系统名

##6. history命令

“history”命令就是历史记录。它显示了在终端中所执行过的所有命令的历史。

	root@tecmint:~# history

	sudo add-apt-repository ppa:tualatrix/ppa
	sudo apt-get update
	sudo apt-get install ubuntu-tweak
	sudo add-apt-repository ppa:diesch/testing
	sudo apt-get update
	sudo apt-get install indicator-privacy
	sudo add-apt-repository ppa:atareao/atareao
	sudo apt-get update
	sudo apt-get install my-weather-indicator
	pwd
	cd && sudo cp -r unity/6 /usr/share/unity/
	cd /usr/share/unity/icons/
	cd /usr/share/unity

###注意：
按住“CTRL + R”就可以搜索已经执行过的命令，它可以在你写命令时自动补全。

	(reverse-i-search)'if': ifconfig


##7. sudo命令

“sudo”(super user do)命令允许授权用户执行超级用户或者其它用户的命令。通过在sudoers列表的安全策略来指定。

	root@tecmint:~# sudo add-apt-repository ppa:tualatrix/ppa
##注意：
sudo 允许用户借用超级用户的权限，然而"su"命令实际上是允许用户以超级用户登录。所以sudo比su更安全。
并不建议使用sudo或者su来处理日常用途，因为它可能导致严重的错误如果你意外的做错了事，这就是为什么在linux社区流行一句话：

	“To err is human, but to really foul up everything, you need root password.”
	“人非圣贤孰能无过，但是拥有root密码就真的万劫不复了。” # 译

	
##8. mkdir命令

“mkdir”(Make directory)命令在命名路径下创建新的目录。然而如果目录已经存在了，那么它就会返回一个错误信息"不能创建文件夹，文件夹已经存在了"("cannot create folder, folder already exists")

	root@tecmint:~# mkdir tecmint
	
###注意：
目录只能在用户拥有写权限的目录下才能创建。mkdir：不能创建目录`tecmint`，因为文件已经存在了。（上面的输出中不要被文件迷惑了，你应该记住我开头所说的-在linux中，文件，文件夹，驱动，命令，脚本都视为文件）

##9. touch 命令

“touch”命令代表了将文件的访问和修改时间更新为当前时间。touch命令只会在文件不存在的时候才会创建它。如果文件已经存在了，它会更新时间戳，但是并不会改变文件的内容。
	
	root@tecmint:~# touch tecmintfile
	
###注意：
touch 可以用来在用户拥有写权限的目录下创建不存在的文件。

##10. chmod 命令

“chmod”命令就是改变文件的模式位。chmod会根据要求的模式来改变每个所给的文件，文件夹，脚本等等的文件模式（权限）。
在文件(文件夹或者其它，为了简单起见，我们就使用文件)中存在3中类型的权限

Read (r)=4

Write(w)=2

Execute(x)=1

所以如果你想给文件只读权限，就设置为'4';只写权限，设置权限为'2';只执行权限，设置为1; 读写权限，就是4+2 = 6, 以此类推。
现在需要设置3种用户和用户组权限。第一个是拥有者，然后是用户所在的组，最后是其它用户。

	rwxr-x--x   abc.sh
	
这里root的权限是 rwx（读写和执行权限），

所属用户组权限是 r-x (只有读和执行权限, 没有写权限)，

对于其它用户权限是 -x(只有只执行权限)

为了改变它的权限，为拥有者，用户所在组和其它用户提供读，写，执行权限。

	root@tecmint:~# chmod 777 abc.sh
	
三种都只有读写权限

	root@tecmint:~# chmod 666 abc.sh
	
拥有者用户有读写和执行权限，用户所在的组和其它用户只有可执行权限

	root@tecmint:~# chmod 711 abc.sh
	
###注意：
对于系统管理员和用户来说，这个命令是最有用的命令之一了。在多用户环境或者服务器上，对于某个用户，如果设置了文件不可访问，那么这个命令就可以解决，如果设置了错误的权限，那么也就提供了为授权的访问。


##11. chown命令

“chown”命令就是改变文件拥有者和所在用户组。每个文件都属于一个用户组和一个用户。在你的目录下，使用"ls -l",你就会看到像这样的东西。

	root@tecmint:~# ls -l

	drwxr-xr-x 3 server root 4096 May 10 11:14 Binary
	drwxr-xr-x 2 server server 4096 May 13 09:42 Desktop
	
在这里，目录Binary属于用户"server",和用户组"root",而目录"Desktop"属于用户“server”和用户组"server"
“chown”命令用来改变文件的所有权，所以仅仅用来管理和提供文件的用户和用户组授权。

	root@tecmint:~# chown server:server Binary

	drwxr-xr-x 3 server server 4096 May 10 11:14 Binary
	drwxr-xr-x 2 server server 4096 May 13 09:42 Desktop
###注意：
“chown”所给的文件改变用户和组的所有权到新的拥有者或者已经存在的用户或者用户组。


##12. apt命令

Debian系列以“apt”命令为基础，“apt”代表了Advanced Package Tool。APT是一个为Debian系列系统（Ubuntu，Kubuntu等等）开发的高级包管理器，在Gnu/Linux系统上，它会为包自动地，智能地搜索，安装，升级以及解决依赖。

root@tecmint:~# apt-get install mplayer

	Reading package lists... Done
	Building dependency tree      
	Reading state information... Done
	The following package was automatically installed and is no longer required:
		java-wrappers
	Use 'apt-get autoremove' to remove it.
	The following extra packages will be installed:
		esound-common libaudiofile1 libesd0 libopenal-data libopenal1 libsvga1 libvdpau1 libxvidcore4
	Suggested packages:
		pulseaudio-esound-compat libroar-compat2 nvidia-vdpau-driver vdpau-driver mplayer-doc netselect fping
	The following NEW packages will be installed:
		esound-common libaudiofile1 libesd0 libopenal-data libopenal1 libsvga1 libvdpau1 libxvidcore4 mplayer
	0 upgraded, 9 newly installed, 0 to remove and 8 not upgraded.
	Need to get 3,567 kB of archives.
	After this operation, 7,772 kB of additional disk space will be used.
	Do you want to continue [Y/n]? y


	root@tecmint:~# apt-get update

	Hit http://ppa.launchpad.net raring Release.gpg                                          
	Hit http://ppa.launchpad.net raring Release.gpg                                          
	Hit http://ppa.launchpad.net raring Release.gpg                     
	Hit http://ppa.launchpad.net raring Release.gpg                     
	Get:1 http://security.ubuntu.com raring-security Release.gpg [933 B]
	Hit http://in.archive.ubuntu.com raring Release.gpg                                                  
	Hit http://ppa.launchpad.net raring Release.gpg                     
	Get:2 http://security.ubuntu.com raring-security Release [40.8 kB]  
	Ign http://ppa.launchpad.net raring Release.gpg                                                 
	Get:3 http://in.archive.ubuntu.com raring-updates Release.gpg [933 B]                           
	Hit http://ppa.launchpad.net raring Release.gpg                                                               
	Hit http://in.archive.ubuntu.com raring-backports Release.gpg
	
###注意：
上面的命令会导致系统整体的改变，所以需要root密码（查看提示符为"#"，而不是“$”）.和yum命令相比，Apt更高级和智能。
见名知义，apt-cache用来搜索包中是否包含子包mplayer, apt-get用来安装，升级所有的已安装的包到最新版。
关于apt-get 和 apt-cache命令更多信息，请查看 25 APT-GET和APT-CACHE命令


##13. tar命令

“tar”命令是磁带归档(Tape Archive)，对创建一些文件的的归档和它们的解压很有用。

	root@tecmint:~# tar -zxvf abc.tar.gz (记住'z'代表了.tar.gz)
	root@tecmint:~# tar -jxvf abc.tar.bz2 (记住'j'代表了.tar.bz2)
	root@tecmint:~# tar -cvf archieve.tar.gz(.bz2) /path/to/folder/abc

###注意： 
"tar.gz"代表了使用gzip归档，“bar.bz2”使用bzip压缩的，它压缩的更好但是也更慢。
了解更多"tar 命令"的例子，请查看 18 Tar命名例子


##14. cal 命令

“cal”（Calender），它用来显示当前月份或者未来或者过去任何年份中的月份。

	root@tecmint:~# cal

		May 2013       
	Su Mo Tu We Th Fr Sa 
			1  2  3  4 
	 5  6  7  8  9 10 11 
	12 13 14 15 16 17 18 
	19 20 21 22 23 24 25 
	26 27 28 29 30 31

显示已经过去的月份，1835年2月

	root@tecmint:~# cal 02 1835
 
		February 1835     
	Su Mo Tu We Th Fr Sa 

	 1  2  3  4  5  6  7 
	 8  9 10 11 12 13 14 
	15 16 17 18 19 20 21 
	22 23 24 25 26 27 28

显示未来的月份，2145年7月。

	root@tecmint:~# cal 07 2145

		July 2145       
	Su Mo Tu We Th Fr Sa 
				1  2  3 
	4  5  6  7  8  9 10 
	11 12 13 14 15 16 17 
	18 19 20 21 22 23 24 
	25 26 27 28 29 30 31

###注意： 
你不需要往回调整日历50年，既不用复杂的数据计算你出生那天，也不用计算你的生日在哪天到来，**因为它的最小单位是月，而不是日**。


##15. date命令

“date”命令使用标准的输出打印当前的日期和时间，也可以深入设置。

	root@tecmint:~# date

	Fri May 17 14:13:29 IST 2013

	root@tecmint:~# date --set='14 may 2013 13:57'

	Mon May 13 13:57:00 IST 2013
	
###注意：
这个命令在脚本中十分有用，以及基于时间和日期的脚本更完美。而且在终端中改变日期和时间，让你更专业！！！（当然你需要root权限才能操作这个，因为它是系统整体改变）


##16. cat命令

“cat”代表了连结（Concatenation），连接两个或者更多文本文件或者以标准输出形式打印文件的内容。

	root@tecmint:~# cat a.txt b.txt c.txt d.txt abcd.txt
	root@tecmint:~# cat abcd.txt
	....
	contents of file abcd
	...
	
###注意：
“>>”和“>”调用了追加符号。它们用来追加到文件里，而不是显示在标准输出上。“>”符号会删除已存在的文件，然后创建一个新的文件。所以因为安全的原因，建议使用“>>”，它会写入到文件中，而不是覆盖或者删除。

在深入探究之前，我必须让你知道通配符(你应该知道通配符，它出现在大多数电视选秀中)。通配符是shell的特色，和任何GUI文件管理器相比，它使命令行更强大有力！如你所看到那样，在一个图形文件管理器中，你想选择一大组文件，你通常不得不使用你的鼠标来选择它们。这可能觉得很简单，但是事实上，这种情形很让人沮丧！
例如，假如你有一个有很多很多各种类型的文件和子目录的目录，然后你决定移动所有文件名中包含“Linux”字样的HTML文件到另外一个目录。如何简单的完成这个？如果目录中包含了大量的不同名的HTML文件，你的任务很巨大，而不是简单了。
在LInux CLI中，这个任务就很简单，就好像只移动一个HTML文件，因为有shell的通配符，才会如此简单。这些是特殊的字符，允许你选择匹配某种字符模式的文件名。它帮助你来选择，即使是大量文件名中只有几个字符，而且在大多数情形中，它比使用鼠标选择文件更简单。
这里就是常用通配符列表：

	Wildcard Matches
	*            零个或者更多字符
	?            恰好一个字符
	[abcde]             恰好列举中的一个字符
	[a-e]          恰好在所给范围中的一个字符
	[!abcde]        任何字符都不在列举中
	[!a-e]          任何字符都不在所给的范围中
	{debian,linux}      恰好在所给选项中的一整个单词

! 叫做非，带'！'的反向字符串为真
更多请阅读Linux cat 命令的实例 13 Linux中cat命令实例


##17. cp 命令

“copy”就是复制。它会从一个地方复制一个文件到另外一个地方。

	root@tecmint:~# cp /home/user/Downloads abc.tar.gz /home/user/Desktop (Return 0 when sucess)

###注意： 
cp，在shell脚本中是最常用的一个命令，而且它可以使用通配符（在前面一块中有所描述），来定制所需的文件的复制。

##18. mv 命令

“mv”命令将一个地方的文件移动到另外一个地方去。

	root@tecmint:~# mv /home/user/Downloads abc.tar.gz /home/user/Desktop (Return 0 when sucess)

###注意：
mv 命令可以使用通配符。mv需谨慎使用，因为移动系统的或者未授权的文件不但会导致安全性问题，而且可能系统崩溃。

##19. pwd 命令

“pwd”（print working directory），在终端中显示当前工作目录的全路径。
	
	root@tecmint:~# pwd
	
	/home/user/Desktop

###注意： 
这个命令并不会在脚本中经常使用，但是对于新手，当从连接到nux很久后在终端中迷失了路径，这绝对是救命稻草。


##20. cd 命令

最后，经常使用的“cd”命令代表了改变目录。它在终端中改变工作目录来执行，复制，移动，读，写等等操作。

	root@tecmint:~# cd /home/user/Desktop

	server@localhost:~$ pwd

	/home/user/Desktop
###注意： 
在终端中切换目录时，cd就大显身手了。“cd ～”会改变工作目录为用户的家目录，而且当用户发现自己在终端中迷失了路径时，非常有用。“cd ..”从当前工作目录切换到(当前工作目录的)父目录。
这些命令肯定会让你在Linux上很舒服。但是这并不是结束。不久，我就会写一些其它的针对于中级用户的有用命令。例如，如果你熟练使用这些命令，欢呼吧，少年，你会发现你已从小白级别提升为了中级用户了。在下篇文章，我会介绍像“kill”,"ps","grep"等等命令，期待吧，我不会让你失望的。