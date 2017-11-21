---
layout: post
title:  ansible+tomcat+memcached+nginx实现web自动部署服务及会话保持
date:   2017-11-20 08:00:00
categories: Linux 
tags:  自动部署  ansible  
---

### 介绍

> 1、简介
> ansible是新出现的自动化运维工具，基于python开发，集合了众多运维工具（puppet、cfengine、chef、func、fabric）的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能。ansible是基于模块工作的，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。主要包括。

- （1）、连接插件connection plugins:负责和被监控端实现通信；

- （2）、host inventory:指定操作的主机，是一个配置文件里面定义监控的主机；

- （3）、各种模块：核心模块、command模块、自定义模块；

- （4）、借助于插件完成记录日志邮件等功能；

- （5）、playbook:剧本执行多个任务时，非必须可以让节点一次性运行多个任务。

> 2、总体架构

![yuanli](/assets/pictures/ansible/yuanli.png)

> 3、特性

- （1）、no agents:不需要在被管控主机上安装任何客户端；

- （2）、no server:无服务器端，使用时直接运行命令即可；

- （3）、modules in any languages:基于模块工作，可使用任意语言开发模块；

- （4）、yaml,not code:使用yaml语言定制剧本playbook;

- （5）、ssh by default:基于SSH工作；

- （6）、strong multi-tier solution:可实现多级指挥；

#### ansible的自动部署的实现

> 实验环境，基于centos7进行操作，这里先将目录的所有文件进行列举

```

/etc/ansible/roles/
├── memcached			与memcached有关的目录
│   ├── handlers		条件动作的设定
│   │   └── main.yml
│   ├── tasks			ansible的任务列表
│   │   └── main.yml
│   └── templates		模板文件
│       └── memcached.j2
└── tomcat				与tomcat有关的设定目录
    ├── files
    │   ├── javolution-5.5.1.jar					*.jar与设定msm的相关的依赖包
    │   ├── memcached-session-manager-1.8.2.jar
    │   ├── memcached-session-manager-tc7-1.8.2.jar
    │   ├── msm-javolution-serializer-1.8.2.jar
    │   ├── msm-sample-webapp-1.0-SNAPSHOT.war		用于测试的web应用程序
    │   └── spymemcached-2.10.2.jar
    ├── handlers
    │   └── main.yml
    ├── tasks				tomcat的任务列表
    │   └── main.yml
    └── templates
        ├── index.jsp.j2	用于进行tomcat的测试页面模板
        └── server.xml.j2	tomcat的配置文件的模板

```

> 接下来进行详细的部署，部署前确保ansible能够和其他主机进行ssh免密码连接

#### tomcat的相关部署

> 1、tasks的main.yml

```
- name: install java JDK   	安装tomcat的必须环境jdk
  yum: name=java-1.8.0-openjdk state=present

- name: install tomcat		安装tomcat
  yum: name={{ item }}  state=present
  with_items: 
  - tomcat
  - tomcat-lib
  - tomcat-webapps
  - tomcat-admin-webapps

- name: install tomcat.conf file	套用模板配置tomcat的配置文件
  template: src={{ item.src  }} dest={{ item.dest  }}
  with_items:
  - { src: 'server.xml.j2',dest: '/etc/tomcat/server.xml' }
  notify: restart tomcat			执行完进行重启tomcat服务

- name: tomcat directory			创建tomcat的测试文件
  file: path=/usr/local/tomcat/webapps/test/WEB-INF state=directory

- name: apply apps					添加测试的web服务
  copy: src=msm-sample-webapp-1.0-SNAPSHOT.war dest=/var/lib/tomcat/webapps/ owner=tomcat group=tomcat
  notify: restart tomcat

- name: link						对上面SNAPSHOT.war自动创建连接
  file: path=/var/lib/tomcat/webapps/msm src=/var/lib/tomcat/webapps/msm-sample-webapp-1.0-SNAPSHOT state=link 

- name: tomcat test page			调用模板设定测试文件
  template: src=index.jsp.j2 dest=/usr/local/tomcat/webapps/test/index.jsp

- name: install msm-jar				将msm依赖的Java包，设定放置到相应的库中
  copy: src={{ item  }}  dest=/usr/share/tomcat/lib/ 
  with_items:
  - javolution-5.5.1.jar
  - memcached-session-manager-tc7-1.8.2.jar
  - spymemcached-2.10.2.jar
  - memcached-session-manager-1.8.2.jar
  - msm-javolution-serializer-1.8.2.jar

- name: start tomcat service		启动tomcat服务
  service: name=tomcat state=started enabled=yes

```
> 这里需要说明，msm-sample-webapp-1.0-SNAPSHOT.war的测试web服务包，并没有设定会话保持功能，只是对/webapps/test的目录进行了，会话保持测试，稍后进行实现

> 2、templates的配置模板

> server.xml.j2
> 在tomcat的server.xml的host下添加一个用于测试的容器

```
<Context path="/test" docBase="/usr/local/tomcat/webapps/test" reloadable="true">
  <Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
    memcachedNodes="n1:172.18.18.10:11211,n2:172.18.15:11211"
    failoverNodes="n1"
    requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"
    transcoderFactoryClass="de.javakaffee.web.msm.serializer.javolution.JavolutionTranscoderFactory"
  />
</Context>

```

> 设定test的测试页面

```
<%@ page language="java" %>
<html>
  <head><title>TomcatA</title></head>
  <body>
    <h1><font color="red">{{ ansible_fqdn }}</font></h1>   #ansible_fqdn为ansibl内建的变量，能够返回目标的主机地址
    <table align="centre" border="1"> 
      <tr>
        <td>Session ID</td>
    <% session.setAttribute("magedu.com","magedu.com"); %>
        <td><%= session.getId() %></td>
      </tr>
      <tr>
        <td>Created on</td>
        <td><%= session.getCreationTime() %></td>
     </tr>
    </table>
  </body>
</html>

```

3、handlers的配置

> handlers的配置相对比较简单，只是进行设定条件触发后的动作

```
#设定重新启动tomcat服务，通常配置文件更改应该进行重启服务操作
- name: restart tomcat
  service: name=tomcat state=restarted

```

#### memcached的相关部署

> 1、memcached.j2的配置

```

# Running on Port 11211
PORT=11211

# Start as memcached daemon
USER="memcached"

# Set max simultaneous connections to 1024
MAXCONN="1024"

# Set log file
GFILE="/var/log/memcached.log"

# Set Memory size to half of all memory
#CACHESIZE="{{ ansible_memtotal_mb / 2 }}"
CACHESIZE=64mb

#Set server IP address
#OPTIONS="-l {{ ansible_default_ipv4['address'] }}"

```

> 2、tasks/main.yml任务列表

``` 
- name: install memcached
  yum: name=memcached state=present

- name: install memcached conf file
  template: src=memcached.j2 dest=/etc/sysconfig/memcached
  notify: restart memcached

- name: start memcached
  service: name=memcached state=started enabled=yes

```


#### 测试

> 在另一台IP为172.18.18.30的主机上配置nginx的反向代理

```
upstream srvs {   在httpdsrvs的组内，添加后端服务器的成员
	server   172.18.18.10:8080 ;
	server	 172.18.18.15:8080 ;
}

#引用时为
location / {
	proxy_pass  srvs;
}

```


> 设定set.yml指定角色有哪些，需要进行执行任务

```
- hosts: web    	指定对哪个组生效，需要先在ansible配置文件进行指定
  remote_user: root	以哪种身份进行下发至各个主机
  roles:
  - tomcat
  - memcached

```

> 执行命令进行任务的下发

```
# ansible-playbook  set.yml
```

![下发图](/assets/pictures/ansible/set.png)
![下发图1](/assets/pictures/ansible/set1.png)
![下发图2](/assets/pictures/ansible/set2.png)

> 通过浏览器输入http://172.18.18.30/test进行测试查看

![webtest](/assets/pictures/ansible/test.gif)

> 进行查看tomcat的自动部署的服务是否成功
> 通过浏览器输入http://172.18.18.30/msm进行测试查看
![下发图3](/assets/pictures/ansible/war.png)

> 当进行部署服务更新的时候将之前设定的连接重新制定即可

##### 如有错误还望批评指正，敬请谅解！


