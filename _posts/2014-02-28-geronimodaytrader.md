---
layout: post
title: "在Geronimo上搭建Daytrader Benchmark服务"
description: ""
category: benchmark
tags: [cloud, geronimo, daytrader, banchmark]
---
{% include JB/setup %}

想试一试Daytrader作为benchmark究竟如何，就尝试在Geronimo上搭建了一个。

操作系统 ubuntu12.04 64位

#部署Geronimo
部署Geronimo花了我好多功夫，主要问题是最新版的`Geronimo3.0.1`不给力，有bug，尝试了很久都不行。退回到`Geronimo2.2.1`就没问题，下面简单说一下搭建过程。

首先，当然是装jre，保险起见用oracle的jre。安装文件就用网上的源好了。

	add-apt-repository ppa:webupd8team/java
	apt-get update
	apt-get install oracle-java6-installer
	
然后，定义JAVA_HOME, JRE_HOME, 把$JAVA_HOME/bin加到PATH中去。
	
	vi /root/.bashrc
	export JAVA_HOME=/usr/lib/jvm/java-6-oracle
	export JRE_HOME=/usr/lib/jvm/java-6-oracle/jre
	export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
	
下载`Geronimo2.2.1`，解压后进入下面bin文件夹。就可以运行程序了。
	
	bin/geronimo run

未完待续
