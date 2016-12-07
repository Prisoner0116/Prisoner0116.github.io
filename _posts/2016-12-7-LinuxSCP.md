---
layout: post
title: Linux SCP命令总结
category: blog
description: linux SCP 命令总结 
---


### SCP命令介绍

> Linux scp命令用于Linux之间复制文件和目录。

> scp是 secure copy的缩写, scp是linux系统下基于ssh登陆进行安全的远程文件拷贝命令。

### SCP从本地复制文件到远程

	scp local_file remote_username@remote_ip:remote_folder 
	或者 
	scp local_file remote_username@remote_ip:remote_file 
	或者 
	scp local_file remote_ip:remote_folder 
	或者 
	scp local_file remote_ip:remote_file 

- 第1,2个指定了用户名，命令执行后需要再输入密码，第1个仅指定了远程的目录，文件名字不变，第2个指定了文件名；
- 第3,4个没有指定用户名，命令执行后需要输入用户名和密码，第3个仅指定了远程的目录，文件名字不变，第4个指定了文件名； 

![scp 示例](/images/scp/scp_1.png)

### SCP从远程复制文件到本地

从远程复制到本地，只要将从本地复制到远程的命令的后2个参数调换顺序即可，如下实例 

	scp cjbatch@10.200.6.224:/home/cjbatch/count.sh /home/cjbatch/count.sh

### 注意

#### 1.如果远程服务器防火墙有为scp命令设置了指定的端口，需要使用 -p 参数来设置命令的端口号，命令如下：
	
	scp命令使用端口号 4588
	scp -p 4588 cjbatch@10.200.6.224:/home/cjbatch/count.sh /home/cjbatch

#### 2.使用scp命令要确保使用的用户具有可读取远程服务器相应文件的权限，否则scp命令是无法起作用的。
