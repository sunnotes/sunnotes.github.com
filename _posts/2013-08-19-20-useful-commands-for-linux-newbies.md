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
