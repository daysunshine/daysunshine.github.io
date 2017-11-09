---
layout: post
title:  tomcat集群及session会话保持
date:   2017-11-09 08:00:00
categories: Linux 
tags:  tomcat  session
---


## session会话保持

- 1.使用会话粘性的方式，代理服务器通过指定的目标资源，将访问同一目标资源的请求每次都访问同一台节点服务器，达到session保持的效果。常用的有通过源地址，通过给客户端设置session首部，通过访问的url来实现。
- 2.使用会话同步的方式将每个节点的会话复制到别的节点中，这样客户端访问每个节点，都有自己的session了，这种方式在集群中节点多的情况下，会严重浪费资源，导致服务器崩溃，所以只能用在小型集群环境中
- 3.使用session server,将用户的session放到一台专门存储session的服务器中，这里我们使用memcached服务来作为session服务器。这种方式的弊端在于如果session服务器只有一台，那就成了单点，所以要使用两台或以上的session服务器，不过memcached服务在集群中使用的是旁挂式工作模式，自己没有主从的功能，只能通过节点服务器将session一起写入不同的session服务器中


> 设定测试页面以供下面实验
> 在tomcat上新建测试目录

```
[root@centos7 tomcat]# mkdir -pv /usr/share/tomcat/webapps/test/{class,lib,WEB-INF}
[root@centos7 tomcat]# vim /usr/share/tomcat/webapps/test/index.jsp    #创建一个主页文件，并添加以下测试内容

#在两个tomcat的主机节点上都进行同样的设定，为了能够看出实验的区别，只是设定的字体颜色有区别，代码如下
<%@ page language="java" %>
<html>
	<head><title>TomcatA</title></head>
	<body>
		<h1><font color="red">TomcatA.a.com</font></h1>
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

### 1、会话粘性方式

> (1)、httpd的会话粘性

> 在/etc/httpd/conf.d/下新建文件为test.conf
> 添加内容如下：

```

Header add Set-Cookie "ROUTEID=.%{BALANCER_WORKER_ROUTE}e; path=/" env=BALANCER_ROUTE_CHANGED

<proxy balancer://tcsrvs>
	BalancerMember http://172.18.100.67:8080 route=TomcatA loadfactor=1  #route=TomcatA设定唯一的标识
	BalancerMember http://172.18.100.68:8080 route=TomcatB loadfactor=2
	ProxySet lbmethod=byrequests
	ProxySet stickysession=ROUTEID
</Proxy>

<VirtualHost *:80>
	ServerName www.a.com
	ProxyVia On
	ProxyRequests Off
	ProxyPreserveHost On
	<Proxy *>
		Require all granted
	</Proxy>
	ProxyPass / balancer://tcsrvs/
	ProxyPassReverse / balancer://tcsrvs/
	<Location />
		Require all granted
	</Location>
</VirtualHost>

```

> (2)、nginx会话粘性

> 在/etc/nginx/nginx.conf中进行修改

```
#在http段中添加自定义的组
upstream webapp {
        server 172.18.100.67:8080;
        server 172.18.100.68:8080;
        hash $request_uri consistent;    #添加这一行，使用请求的uri为目标来做会话粘性，consistent是一致性哈希算法，不加的话只是hash表示静态数组法，也叫做取模法。
        }
		
#server段中设置
server{
	listen  80;
	server_name www.a.com;
	index  index.jsp  index.html;
	location  / {
		proxy_pass http://webapp;
	}
}

```

> (3)、haproxy会话粘性

> 编辑/etc/haproxy/haproxy.cfg

```
#定义前端的frontend
frontend  test 
	bind *:80
	default_backend webapp
	
#定义后端backend
#增加cookie行，然后在server行最后加上cookie识别信息，通常和节点名字一样就好，以便识别
backend webapp
	balance  roundrobin  #使用轮巡算法
	cookie SRV  insert indirect
	server 172.18.100.67:8080 check cookie app1
    server 172.18.100.68:8080 check cookie app2

```

### 2、会话同步方式

> session同步是在节点中设置的，nginx来做前端代理服务器，在/etc/nginx/nginx.conf中将调度算法hash $request_uri consistent;这行注释掉，进行session同步实验。

> 配置启用集群，将下列配置放置于<engine>或<host>中；

```
#确保Engine的jvmRoute属性配置正确
#在/etc/tomcat/server.xml设定jvmRoute
<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcat1">

#设定会话集群
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster" channelSendOptions="8">

	<Manager className="org.apache.catalina.ha.session.DeltaManager"
		expireSessionsOnShutdown="false"
		notifyListenersOnReplication="true"/>

	<Channel className="org.apache.catalina.tribes.group.GroupChannel">
	<Membership className="org.apache.catalina.tribes.membership.McastService"  定义组的关系
			address="228.0.0.4"  #在那个组中进行公告
			port="45564"
			frequency="500"
			dropTime="3000"/>
	<Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"   接收消息
		address="auto"   #设定成自己的服务器地址
		port="4000"
		autoBind="100"
		selectorTimeout="5000"
		maxThreads="6"/>

	<Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">   发送消息
	<Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
	</Sender>
	<Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>
	<Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/>
	</Channel>

	<Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
		filter=""/>
	<Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/>

	<Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
		tempDir="/tmp/war-temp/"
		deployDir="/tmp/war-deploy/"
		watchDir="/tmp/war-listen/"
		watchEnabled="false"/>

	<ClusterListener className="org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener"/>
	<ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
	</Cluster>
```

> 添加test测试程序的web.xml文件，并修改

```
[root@localhost webapps]# cd /usr/share/tomcat/webapps/test/WEB-INF/
[root@localhost WEB-INF]# cp /etc/tomcat/web.xml .
[root@localhost WEB-INF]# vim web.xml
#在web app段中找个位置添加如下内容
<distributable/>

```
**注意：绑定的地址为auto时，会自动解析本地主机名，并解析得出的IP地址作为使用的地址**

> 通过访问nginx代理服务器进行测试session会话的正确性


### 3、session server方式 (memcache、redis)

> 前提设定：
> 两个tomcat节点：172.16.100.7(tomcatA.magedu.com)，172.16.100.8(tomcatB.magedu.com)
> 两个memcached节点：172.16.100.9, 172.16.100.10
> 一个负载均衡节点：172.16.100.6

> 访问流程：Clients-->172.16.100.6-->(tomcatA, tomcatB)

> 下载如下jar文件至各tomcat节点的tomcat安装目录下的lib目录中，其中的${version}要换成你所需要的版本号，tc${6,7,8}要换成与tomcat版本相同的版本号。

- memcached-session-manager-${version}.jar
- memcached-session-manager-tc${6,7,8}-${version}.jar
- spymemcached-${version}.jar
- msm-javolution-serializer-${version}.jar
- javolution-${version}.jar

> 分别在两个tomcat上的某host上定义一个用于测试的context容器，并在其中创建一个会话管理器，如下所示：

```
<Context path="/test" docBase="/usr/local/tomcat/webapps/test" reloadable="true">
      <Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
        memcachedNodes="n1:172.16.100.9:11211,n2:172.16.100.10:11211"   设定后端的
        failoverNodes="n1"
        requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"
        transcoderFactoryClass="de.javakaffee.web.msm.serializer.javolution.JavolutionTranscoderFactory"
      />
</Context>

```

> 提供测试页，参照上面的设定即可

> 在172.16.100.6上配置反向代理的负载均衡内容，如下所示：
> 在/etc/httpd/ocnf.d/test.conf进行配置

```
<proxy balancer://tcsrvs>
	BalancerMember http://172.18.100.67:8080 loadfactor=1  
	BalancerMember http://172.18.100.68:8080 loadfactor=2
	ProxySet lbmethod=byrequests
	ProxySet stickysession=ROUTEID
</Proxy>

<VirtualHost *:80>
	ServerName www.a.com
	ProxyVia On
	ProxyRequests Off
	ProxyPreserveHost On
	<Proxy *>
		Require all granted
	</Proxy>
	ProxyPass / balancer://tcsrvs/
	ProxyPassReverse / balancer://tcsrvs/
	<Location />
		Require all granted
	</Location>
</VirtualHost>

```

> 测试结果，会话的ID不会变，但是后端的主机是通过轮巡的方式进行调度


