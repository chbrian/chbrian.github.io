---
layout: post
title: "OpenStack虚拟机resize操作配置与说明"
description: ""
category: cloud
tags: [cloud, openstack]
---
{% include JB/setup %}

在OpenStack下面配了一天的resize，终于成功了。在这里记录一下，也给大家做参考。

之前有玩过OpenStack里面的live-migration操作，配置起来也挺麻烦的。
今天想玩玩migration，又因为migration和resize在OpenStack代码实现中其实是一回事，所以就直接操作resize了。

首先，resize顾名思义就是改变instance的flavor。因为flavor涉及到cpu、memory、disk等很多资源，因此可以用来给instance做scale up/down。 OpenStack把它和migration绑在一块实现，就是说明resize在操作过程中instance会宕机。不像live-migration那样可以实现downtime趋近于0。

其次，需要注意的是resize在`nova.conf`里有一个配置项：

	allow_resize_to_same_host=False

默认是False，把False改为True之后就能在实例所在的host上resize，否则就要迁移到别的host上再进行resize。这一点也正说明了resize和migration在本质上是同一个操作。

使用resize最大的问题是在migration的过程中使用ssh从一个host拷贝到另一个host上。这就需要两边的host都通过ssh认证。而默认的情况显然是没有认证的。


下面是详细的配置步骤：

1.首先需要切换到nova用户

	usermod -s /bin/bash nova  
	su nova

2.创建`.ssh`文件夹，注意文件夹需要在`/var/lib/nova/`下创建，别到`/root/`下了。然后进到这个文件夹。

	mkdir -p -m 700 .ssh 
	cd /var/lib/nova/.ssh

3.取消身份验证

	vi config
	Host *
	  StrictHostKeyChecking no
	  UserKnownHostsFile=/dev/null
	  
4.生成ssh key，并拷贝为`authorized_keys`
	
	ssh-keygen -f id_rsa -b 1024 -P ""
	cp -a /var/lib/nova/.ssh/id_rsa.pub /var/lib/nova/.ssh/authorized_keys
	
5.拷贝到其他计算节点HOSTIP
	
	scp /var/lib/nova/.ssh/id_rsa root@HOSTIP:/var/lib/nova/.ssh/id_rsa
	scp /var/lib/nova/.ssh/authorized_keys root@HOSTIP:/var/lib/nova/.ssh/authorized_keys
	
6.在每个计算节点更改文件权限，因为resize是nova用户执行的，所以一定要改成nova。

	chown nova:nova /var/lib/nova/.ssh/id_rsa /var/lib/nova/.ssh/authorized_keys
	chown nova:nova /var/lib/nova/.ssh/id_rsa /var/lib/nova/.ssh/authorized_keys
	
之后就可以正常使用resize功能了。

这里再啰嗦两句，设置`.ssh`文件夹以及`authorized_keys`文件的权限时一定要小心，我就是因为权限一不小心设置错了，导致运行resize时一直报错。

参考资料：
1. 孔兄的[nova resize修改实例配置报错的解决办法](http://blog.csdn.net/lynn_kong/article/details/8891247)
2. [OpenStack Ask](https://ask.openstack.org/en/question/10335/ssh-resize/)