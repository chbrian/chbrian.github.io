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
部署Geronimo花了我好多功夫，主要问题是最新版的`Geronimo3.0.1`不给力，有bug，尝试了很久都不行。退回到`Geronimo3.0.0`就没问题，下面简单说一下搭建过程。

首先，当然是装jre，保险起见用oracle的jre。安装文件就用网上的源好了。

	add-apt-repository ppa:webupd8team/java
	apt-get update
	apt-get install oracle-java6-installer
	
然后，定义JAVA_HOME, JRE_HOME, 把$JAVA_HOME/bin加到PATH中去。
	
	vi /root/.bashrc
	export JAVA_HOME=/usr/lib/jvm/java-6-oracle
	export JRE_HOME=/usr/lib/jvm/java-6-oracle/jre
	export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
	
下载`Geronimo3.0.0`，解压后进入下面bin文件夹。就可以运行程序了。
	
	bin/geronimo run

登陆 `IPaddress:8080/console`，默认用户名system，密码manager。Geronmio就算是部署好了。
	
#安装daytrader
下载`daytrader-parent-3.0.0`
	
	svn co http://svn.apache.org/repos/asf/geronimo/daytrader/tags/daytrader-parent-3.0.0
	
进入daytrader文件夹，安装
	
	mvn clean install
	
daytrader就安装好了，下面开始部署daytrader。

#部署daytrader
首先先运行geronimo，然后到geronimo/bin文件夹下执行
	
	cd $GERONIMO_HOME/bin
	deploy.sh deploy $DAYTRADER_HOME/javaee6/assemblies/daytrader-ear/target/daytrader-ear-<version>.ear $DAYTRADER_HOME/javaee6/plans/target/classes/daytrader-derby-xa-plan.xml
	
这里我用derby作为db，也可以选用别的db。只不过geronimo已经装好了derby，就不需要重新安装了。

使用以下网址登陆daytrader `IPaddress::8080/daytrader/`。daytrader需要一些初始的配置才能正常使用了。包括重建数据库里的table，自动生成一些股票和用户。然后就可以进入'Trading & Portfolios'自己玩了。


参考资料：
1. http://svn.apache.org/repos/asf/geronimo/daytrader/tags/daytrader-parent-3.0.0/README
2. http://geronimo.apache.org/GMOxDOC30/daytrader-a-more-complex-application.html#daytrader-amorecomplexapplication-Gettingthesource
3. [Apache Geronimo v3.0](http://geronimo.apache.org/GMOxDOC30/index.html)


