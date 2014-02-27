---
layout: post
title: "OpenStack�����resize����������˵��"
description: ""
category: cloud
tags: [cloud, openstack]
---
{% include JB/setup %}

��OpenStack��������һ���resize�����ڳɹ��ˡ��������¼һ�£�Ҳ��������ο���

֮ǰ�����OpenStack�����live-migration��������������Ҳͦ�鷳�ġ�
����������migration������Ϊmigration��resize��OpenStack����ʵ������ʵ��һ���£����Ծ�ֱ�Ӳ���resize�ˡ�

���ȣ�resize����˼����Ǹı�instance��flavor����Ϊflavor�漰��cpu��memory��disk�Ⱥܶ���Դ����˿���������instance��scale up/down�� OpenStack������migration����һ��ʵ�֣�����˵��resize�ڲ���������instance��崻�������live-migration��������ʵ��downtime������0��

��Σ���Ҫע�����resize��`nova.conf`����һ�������

	--allow_resize_to_same_host=False

Ĭ����False����False��ΪTrue֮�������ʵ�����ڵ�host��resize�������ҪǨ�Ƶ����host���ٽ���resize����һ��Ҳ��˵����resize��migration�ڱ�������ͬһ��������

ʹ��resize������������migration�Ĺ�����ʹ��ssh��һ��host��������һ��host�ϡ������Ҫ���ߵ�host��ͨ��ssh��֤����Ĭ�ϵ������Ȼ��û����֤�ġ�


��������ϸ�����ò��裺

1.������Ҫ�л���nova�û�

	usermod -s /bin/bash nova  
	su nova

2.����`.ssh`�ļ��У�ע���ļ�����Ҫ��`/var/lib/nova/`�´�������`/root/`���ˡ�Ȼ���������ļ��С�

	mkdir -p -m 700 .ssh 
	cd /var/lib/nova/.ssh

3.ȡ�������֤

	vi config
	Host *
	  StrictHostKeyChecking no
	  UserKnownHostsFile=/dev/null
	  
4.����ssh key��������Ϊ`authorized_keys`
	
	ssh-keygen -f id_rsa -b 1024 -P ""
	cp -a /var/lib/nova/.ssh/id_rsa.pub /var/lib/nova/.ssh/authorized_keys
	
5.��������������ڵ�HOSTIP
	
	scp /var/lib/nova/.ssh/id_rsa root@HOSTIP:/var/lib/nova/.ssh/id_rsa
	scp /var/lib/nova/.ssh/authorized_keys root@HOSTIP:/var/lib/nova/.ssh/authorized_keys
	
6.��ÿ������ڵ�����ļ�Ȩ�ޣ���Ϊresize��nova�û�ִ�еģ�����һ��Ҫ�ĳ�nova��

	chown nova:nova /var/lib/nova/.ssh/id_rsa /var/lib/nova/.ssh/authorized_keys
	chown nova:nova /var/lib/nova/.ssh/id_rsa /var/lib/nova/.ssh/authorized_keys
	
֮��Ϳ�������ʹ��resize�����ˡ�

�����ن������䣬����`.ssh`�ļ����Լ�`authorized_keys`�ļ���Ȩ��ʱһ��ҪС�ģ��Ҿ�����ΪȨ��һ��С�����ô��ˣ���������resizeʱһֱ����