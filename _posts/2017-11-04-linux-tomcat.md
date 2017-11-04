---
layout: post
title:  Tomcat管理界面及配置文件详解
date:   2017-11-04 09:00:00
categories: Linux 
tags:  Tomcat 
---

## 介绍

> Tomcat很受广大程序员的喜欢，因为它运行时占用的系统资源小，扩展性好，支持负载平衡与邮件服务等开发应用系统常用的功能；而且它还在不断的改进和完善中，任何一个感兴趣的程序员都可以更改它或在其中加入新的功能。
> Tomcat是一个小型的轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP程序的首选。对于一个初学者来说，可以这样认为，当在一台机器上配置好Apache服务器，可利用它响应对HTML页面的访问请求。实际上Tomcat部分是Apache服务器的扩展，但它是独立运行的，所以当你运行tomcat时，它实际上作为一个与Apache独立的进程单独运行的。
> 这里的诀窍是，当配置正确时，Apache为HTML页面服务，而Tomcat实际上运行JSP页面和Servlet。另外，Tomcat和IIS、Apache等Web服务器一样，具有处理HTML页面的功能，另外它还是一个Servlet和JSP容器，独立的Servlet容器是Tomcat的默认模式。不过，Tomcat处理静态HTML的能力不如Apache服务器。

### Tomcat的架构

> Tomcat 6支持Servlet 2.5和JSP 2.1的规范，它由一组嵌套的层次和组件组成，一般可分为以下四类：

- 顶级组件：位于配置层次的顶级，并且彼此间有着严格的对应关系；
- 连接器：连接客户端(可以是浏览器或Web服务器)请求至Servlet容器，
- 容器：包含一组其它组件；
- 被嵌套的组件：位于一个容器当中，但不能包含其它组件

**各常见组件：**

1、`服务器(server)`：Tomcat的一个实例，通常一个JVM只能包含一个Tomcat实例；因此，一台物理服务器上可以在启动多个JVM的情况下在每一个JVM中启动一个Tomcat实例，每个实例分属于一个独立的管理端口。这是一个**顶级组件**。

2、`服务(service)`：一个服务组件通常包含一个引擎和与此引擎相关联的一个或多个连接器。给服务命名可以方便管理员在日志文件中识别不同服务产生的日志。一个server可以包含多个service组件，但通常情下只为一个service指派一个server。

3、`连接器(connectors)`：负责连接客户端（可以是浏览器或Web服务器）请求至Servlet容器内的Web应用程序，通常指的是接收客户发来请求的位置及服务器端分配的端口。默认端口通常是HTTP协议的8080，管理员也可以根据自己的需要改变此端口。还可以支持HTTPS ，默认HTTPS端口为8443。同时也支持AJP，即（A）一个引擎可以配置多个连接器，但这些连接器必须使用不同的端口。默认的连接器是基于HTTP/1.1的Coyote。同时，Tomcat也支持**AJP**、JServ和JK2连接器。

**容器类组件：**

4、`引擎(Engine)`：引擎通是指处理请求的Servlet引擎组件，即**Catalina Servlet**引擎，它检查每一个请求的HTTP首部信息以辨别此请求应该发往哪个host或context，并将请求处理后的结果返回的相应的客户端。严格意义上来说，容器不必非得通过引擎来实现，它也可以是只是一个容器。如果Tomcat被配置成为独立服务器，默认引擎就是已经定义好的引擎。而如果Tomcat被配置为Apache Web服务器的提供Servlet功能的后端，默认引擎将被忽略，因为Web服务器自身就能确定将用户请求发往何处。一个引擎可以包含多个host组件。

5、`主机(Host)`：主机组件类似于Apache中的虚拟主机，但在Tomcat中只支持基于FQDN的“虚拟主机”。一个引擎至少要包含一个主机组件。

6、**上下文(Context)**：Context组件是最内层次的组件，它表示Web应用程序本身。配置一个Context最主要的是指定Web应用程序的根目录，以便Servlet容器能够将用户请求发往正确的位置。Context组件也可包含自定义的错误页，以实现在用户访问发生错误时提供友好的提示信息。

**被嵌套类(nested)组件**：

> 这类组件通常包含于容器类组件中以提供具有管理功能的服务，它们不能包含其它组件，但有些却可以由不同层次的容器各自配置。

7、`阀门(Valve)`：用来拦截请求并在将其转至目标之前进行某种处理操作，类似于Servlet规范中定义的过滤器。Valve可以定义在任何容器类的组件中。Valve常被用来记录客户端请求、客户端IP地址和服务器等信息，这种处理技术通常被称作请求转储(request dumping)。请求转储valve记录请求客户端请求数据包中的HTTP首部信息和cookie信息文件中，响应转储valve则记录响应数据包首部信息和cookie信息至文件中。

8、`日志记录器(Logger)`：用于记录组件内部的状态信息，可被用于除Context之外的任何容器中。日志记录的功能可被继承，因此，一个引擎级别的Logger将会记录引擎内部所有组件相关的信息，除非某内部组件定义了自己的Logger组件。

9、`领域(Realm)`：用于用户的认证和授权；在配置一个应用程序时，管理员可以为每个资源或资源组定义角色及权限，而这些访问控制功能的生效需要通过Realm来实现。Realm的认证可以基于文本文件、数据库表、LDAP服务等来实现。Realm的效用会遍及整个引擎或顶级容器，因此，一个容器内的所有应用程序将共享用户资源。同时，Realm可以被其所在组件的子组件继承，也可以被子组件中定义的Realm所覆盖。


###  Tomcat的安装(centos7)

> 需要先安装JDK,然后安装Tomcat及相关的应用
> 默认的安装Linux自带的即可 

```
yum  -y install java-1.8.0-openjdk
yum install Tomcat-lib Tomcat Tomcat-admin-webapps Tomcat-webapps Tomcat-docs-webapp Tomcat-webapps

```
> 在安装时其他的依赖的包会自动安装

> 启动Tomcat服务

```
systemctl start Tomcat
```

> 浏览器打开http://Tomcat_ip:8080/

![Tomcat-test](/assets/pictures/tomcat/test.png)

> 手动安装

```
tar -xvf apache-Tomcat-8.5.20.tar.gz -C /usr/local
cd /usr/local
link -sv apache-Tomcat-8.5.20 Tomcat
cat > /etc/profile.d/Tomcat.sh << EOF
export CATALINA_BASE=/usr/local/Tomcat
export PATH=\$CATALINA_BASE/bin:\$PATH
EOF
. /etc/profile.d/Tomcat.sh
useradd -r Tomcat
chown -R Tomcat:Tomcat /usr/local/Tomcat/
su - Tomcat -c 'catalina.sh start'

```

### Tomcat 目录结构

**Tomcat的目录结构：**

- `bin`：脚本，及启动时用到的类。
- `conf`：配置文件目录。
- `lib`：库文件，Java类库，jar。
- `logs`：日志文件目录。
- `temp`：临时文件目录。
- `webapps`：webapp的默认目录。
- `work`：工作目录。存放编译后的字节码文件。

**Tomcat的配置目录文件构成：**

- `server.xml`：主配置文件；
- `web.xml`：每个webapp只有“部署”后才能被访问，它的部署方式通常由web.xml进行定义，其存放位置为WEB-INF/目录中；此文件为所有的webapps提供默认部署相关的配置；
- `context.xml`：每个webapp都可以专用的配置文件，它通常由专用的配置文件context.xml来定义，其存放位置为WEB-INF/目录中；此文件为所有的webapps提供默认配置；
- `Tomcat-users.xml`：用户认证的账号和密码文件；
- `catalina.policy`：当使用-security选项启动Tomcat时，用于为Tomcat设置安全策略；
- `catalina.properties`：Java属性的定义文件，用于设定类加载器路径，以及一些与JVM调优相关参数；
- `logging.properties`：日志系统相关的配置。

**JSP Webapp的组织结构：**

**webapps的根目录：**

- `index.jsp`：主页；
- `WEB-INF/`：当前webapp的私有资源路径；通常用于存储当前webapp的web.xml和context.xml配置文件；
- `META-INF/`：类似于WEB-INF/；
- `classes/`：类文件，当前webapp所提供的类；
- `lib/`：类文件，当前webapp所提供的类，被打包为jar格式；

**webapp归档格式：**

- `.war`：webapp
- `.jar`：EJB的类打包文件；
- `.rar`：资源适配器类打包文件；
- `.ear`：企业级webapp；

> 部分摘自[http://www.yulongjun.com/linux/20170830-02-tomcat-manage/](http://www.yulongjun.com/linux/20170830-02-tomcat-manage/)


### 配置文件的详解

> server.xml文件中可定义的元素非常多，包括Server, Service, Connector, Engine, Cluster, Host, Alias, Context, Realm, Valve, Manager, Listener, Resources, Resource, ResourceEnvRef, ResourceLink, WatchedResource, GlobalNameingResources, Store, Transaction, Channel, Membership, Transport, Member, ClusterListener等。

> 下面简单介绍几个常用组件：

#### 1、`Server组件`

> 文件中定义的：

```
	<Server port=”8005” shutdown=”SHUTDOWN”>
```
> 这会让Tomcat6启动一个server实例（即一个JVM），它监听在8005端口以接收shutdown命令。各Server的定义不能使用同一个端口，这意味着如果在同一个物理机上启动了多个Server实例，必须配置它们使用不同的端口。这个端口的定义用于为管理员提供一个关闭此实例的便捷途径，因此，管理员可以直接telnet至此端口使用SHUTDOWN命令关闭此实例。不过，基于安全角度的考虑，这通常不允许远程进行。

> Server的相关属性：

```
className: 用于实现此Server容器的完全限定类的名称，默认为org.apache.catalina.core.StandardServer；
port: 接收shutdown指令的端口，默认仅允许通过本机访问，默认为8005；
shutdown：发往此Server用于实现关闭Tomcat实例的命令字符串，默认为SHUTDOWN；
```
> 可通过telnet连接到本地的控制8005端口执行SHUTDOWWN命令，可以将Tomcat服务关掉
> 通常需要进行设置将此功能关闭，防止他人进行破坏

```
	<Server port=”-1” shutdown=”4b7c093031536a90f2dc”>
```
> 字符串通过`openssl rand -hex 10`生成
	
#### 2、`Service组件`

> Service主要用于关联一个引擎和与此引擎相关的连接器，每个连接器通过一个特定的端口和协议接收入站请求交将其转发至关联的引擎进行处理。困此，Service要包含一个引擎、一个或多个连接器。

> 如上面示例中的定义：

```
	<Service name=”Catalina”>
```
> 上面定义了一个名为Catalina的Service，此名字也会在产生相关的日志信息时记录在日志文件当中。
>Service相关的属性：

```
className： 用于实现service的类名，一般都是org.apache.catalina.core.StandardService。
name：此服务的名称，默认为Catalina；
```

#### 3、`Connector组件`

> 进入Tomcat的请求可以根据Tomcat的工作模式分为如下两类：
> Tomcat作为应用程序服务器：请求来自于前端的web服务器，这可能是Apache, IIS, Nginx等；
> Tomcat作为独立服务器：请求来自于web浏览器；

> Tomcat应该考虑工作情形并为相应情形下的请求分别定义好需要的连接器才能正确接收来自于客户端的请求。一个引擎可以有一个或多个连接器，以适应多种请求方式。

> 定义连接器可以使用多种属性，有些属性也只适用于某特定的连接器类型。一般说来，常见于server.xml中的连接器类型通常有4种：
- (1) HTTP连接器
- (2) SSL连接器
- (3) AJP 连接器
- (4) proxy连接器

> `server.xml中定义的HTTP连接器`

```
<Connector port="8080" protocol="HTTP/1.1"
      maxThreads="150" connectionTimeout="20000"
      redirectPort="8443"/>
```    

> `server.xml中定义的共享线程池HTTP连接器`

```
<Connector executor="TomcatThreadPool"
               port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

> `server.xml中定义的HTTPS连接器`

```
<Connector executor="TomcatThreadPool"
               port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

> `定义了多个属性SSL连接器`

```
<Connector port="8443"
    maxThreads="150" minSpareThreads="25" maxSpareThreads="75"
    enableLookups="false" acceptCount="100" debug="0" scheme="https" secure="true"
    clientAuth="false" sslProtocol="TLS" />
```

> `server.xml中定义的AJP连接器`

```
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
```
 
> 上面定义连接器时可以配置的属性非常多，但通常定义HTTP连接器时必须定义的属性只有“port”，定义AJP连接器时必须定义的属性只有"protocol"，因为默认的协议为HTTP。
> 以下为常用属性的说明：
	- (1) address：指定连接器监听的地址，默认为所有地址，即0.0.0.0；
	- (2) maxThreads：支持的最大并发连接数，默认为200；
	- (3) port：监听的端口，默认为0；
	- (4) protocol：连接器使用的协议，默认为HTTP/1.1，定义AJP协议时通常为AJP/1.3；
	- (5) redirectPort：如果某连接器支持的协议是HTTP，当接收客户端发来的HTTPS请求时，则转发至此属性定义的端口；
	- (6) connectionTimeout：等待客户端发送请求的超时时间，单位为毫秒，默认为60000，即1分钟；
	- (7) enableLookups：是否通过request.getRemoteHost()进行DNS查询以获取客户端的主机名；默认为true,一般将其关闭以便能够提高响应速度
	- (8) acceptCount：设置等待队列的最大长度；通常在Tomcat所有处理线程均处于繁忙状态时，新发来的请求将被放置于等待队列中；



#### 4、`Engine组件`

> Engine是Servlet处理器的一个实例，即servlet引擎，默认为定义在server.xml中的Catalina。Engine需要defaultHost属性来为其定义一个接收所有发往非明确定义虚拟主机的请求的host组件。
> 如前面示例中定义的：

```
	<Engine name="Catalina" defaultHost="localhost">
```
> 常用的属性定义：

```
defaultHost：Tomcat支持基于FQDN的虚拟主机，这些虚拟主机可以通过在Engine容器中定义多个不同的Host组件来实现；
			 但如果此引擎的连接器收到一个发往非非明确定义虚拟主机的请求时则需要将此请求发往一个默认的虚拟主机进行处理，
			 因此，在Engine中定义的多个虚拟主机的主机名称中至少要有一个跟defaultHost定义的主机名称同名；
name：Engine组件的名称，用于日志和错误信息记录时区别不同的引擎；
Engine容器中可以包含Realm、Host、Listener和Valve子容器。
```

> 虚拟主机定义示例：

```
<Engine name="Catalina" defaultHost="localhost">
  <Host name="localhost" appBase="webapps">
    <Context path="" docBase="ROOT"/>
    <Context path="/bbs" docBase="/web/bss" reloadable="true" crossContext="true"/>
  </Host>
  
  <Host name="mail.magedu.com" appBase="/web/mail">
    <Context path="" docBase="ROOT"/>
  </Host>
</Engine>

```

> 主机别名定义：
> 如果一个主机有两个或两个以上的主机名，额外的名称均可以以别名的形式进行定义
> 如下：

```
<Host name="www.magedu.com" appBase="webapps" unpackWARs="true">
  <Alias>magedu.com</Alias>
</Host>
```

### 5、`Realm组件`

> 一个Realm表示一个安全上下文，它是一个授权访问某个给定Context的用户列表和某用户所允许切换的角色相关定义的列表。因此，Realm就像是一个用户和组相关的数据库。定义Realm时惟一必须要提供的属性是classname，它是Realm的多个不同实现，用于表示此Realm认证的用户及角色等认证信息的存放位置。
> 认证模块，这里调用的`GlobalNamingResources`里名为`UserDatabase`的`Resourse`。用来给Manager和Host-Manager提供认证服务。

```
<Realm className="org.apache.catalina.realm.LockOutRealm">
   <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
          resourceName="UserDatabase"/>
</Realm>
```

#### 6、`Host组件`

> 位于Engine容器中用于接收请求并进行相应处理的主机或虚拟主机
> 如前面示例中的定义：

```
      <Host name="localhost" appBase="webapps"
            unpackWARs="true" autoDeploy="true">
      </Host>
```

> 常用属性说明：
	- (1) appBase：此Host的webapps目录，即存放非归档的web应用程序的目录或归档后的WAR文件的目录路径；可以使用基于$CATALINA_HOME的相对路径；
	- (2) autoDeploy：在Tomcat处于运行状态时放置于appBase目录中的应用程序文件是否自动进行deploy；默认为true；
	- (3) unpackWars：在启用此webapps时是否对WAR格式的归档文件先进行展开；默认为true；

> 在实际当中直接将由程序员交由我们的war文件直接添加至webapps下即可，会自动进行部署
 
> 示例：

```
# 创建目录
# mkdir -pv /appdata/webapps
# mkdir -pv /appdata/webapps/ROOT/{lib,WEB-INF}
# touch /appdata/webapps/ROOT/index.jsp
# 添加测试内容
```

`index.jsp`：

```
<%@ page language="java" %>
<%@ page import="java.util.*" %>
<html>
  <head>
    <title>JSP test page.</title>
  </head>
  <body>
    <% out.println("Hello,world!"); %>
  </body>
</html>
```

> 修改server.xml：

```
<Host name="www.a.com" appBase="/appdata/webapps" 
      unpackWARs="true" autoDeploy="true">
</Host>
```

浏览器访问www.a.com:8080即可访问到这个页面

![helloworld](/assets/pictures/tomcat/helloworld.png)


#### 7、`Context组件`

> 包含在Host标签里。
> 如果在一个主机上部署多个app应用的话，Context就可以用来定义不同app对应的路径。

```
<Context path="/PATH" docBase="/PATH/TO/SOMEDIR" reloadable=""/>
```

> 把`/PATH/TO/SOMEDIR`映射到 request URI `/PATH`

- 如果`/PATH/TO/SOMEDIR`不带`/`，则是相对路径，相对的是Host的appBase。
- 如果`/PATH/TO/SOMEDIR`带`/`，则是操作系统的绝对路径。

> 常用的属性定义有：

- 1) docBase：相应的Web应用程序的存放位置；也可以使用相对路径，起始路径为此Context所属Host中appBase定义的路径；切记，docBase的路径名不能与相应的Host中appBase中定义的路径名有包含关系，比如，如果appBase为deploy，而docBase绝不能为deploy-bbs类的名字；
- 2) path：相对于Web服务器根路径而言的URI；如果为空“”，则表示为此webapp的根路径；如果context定义在一个单独的xml文件中，此属性不需要定义；
- 3) reloadable：是否允许重新加载此context相关的Web应用程序的类；默认为false；

如果在上面Host localhost 内部加入一个Context：

```
<Host name="localhost" appBase="/appdata/webapps" 
      unpackWARs="true" autoDeploy="true">
      <Context path="/test1" docBase="/app/test1" reloadable=""/>
      <Context path="/app1" docBase="/app/app1" reloadable=""/>
</Host>
```

> 其实我们完全可以直接使用带有目录名称的绝对路径就可以访问，没有必要使用context再次去定义。

#### 8、`Valve组件`

Valve存在多种类型：

- 定义访问日志：`org.apache.catalina.valves.AccessLogValve`
- 定义访问控制：`org.apache.catalina.valves.RemoteAddrValve`

```
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
       prefix="localhost_access_log." suffix=".txt"
       pattern="%h %l %u %t &quot;%r&quot; %s %b" />
```

> `"%h %l %u %t "%r" %s %b"`为日志格式，`&quot`表示的是引号，并放在prefix定义的和suffix定义的中间

> RemoteHostValve和RemoteAddrValve可以分别用来实现基于主机名称和基于IP地址的访问控制，控制本身可以通过allow或deny来进行定义，这有点类似于Apache的访问控制功能；如下面的Valve则实现了仅允许本机访问/probe：

```
  <Context path="/probe" docBase="probe">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\.0\.0\.1"/>
  </Context>
```

> 其中相关属性定义有:
- 1) className：相关的java实现的类名，相应于分别应该为org.apache.catalina.valves.RemoteHostValve或org.apache.catalina.valves.RemoteAddrValve；
- 2) allow：以逗号分开的允许访问的IP地址列表，支持正则表达式，因此，点号"."用于IP地址时需要转义；仅定义allow项时，非明确allow的地址均被deny；
- 3) deny: 以逗号分开的禁止访问的IP地址列表，支持正则表达式；使用方式同allow；


### Tomcat的管理界面

> tomcat的管理界面有三个

![jiemian](/assets/pictures/tomcat/jiemian.png)

> 在上面的页面，我们可以看到3个选项，作用为：

- Server Status：tomcat服务器状态，包括内存信息，进程信息等等。
- Manager App：Web App的管理页面，可以开、关、重载、部署、卸载 Web App
- Host Manager：虚拟主机的管理页面，可以对虚拟主机进行添加删除修改操作。

#### `Manager App`

> 选项在点击时会提示输入账号密码，点击取消提示如何操作，添加相对应的代码

![tishi](/assets/pictures/tomcat/tishi.png)

> 参照提示在/etc/tomcat/tomcat-users.xml文件内添加代码

> 提示共有4个角色:

- manager-gui - allows access to the HTML GUI and the status pages
- manager-script - allows access to the text interface and the status pages
- manager-jmx - allows access to the JMX proxy and the status pages
- manager-status - allows access to the status pages only

```
<role rolename="manager-gui"/>
<user username="tomcat" password="centos" roles="manager-gui"/>

```

> 对应的需要哪个角色就可以添加到roles里面,即可访问对应的界面选项

> server status 界面

![server-status](/assets/pictures/tomcat/server-status.png)

> 可以观察两种连接方式http、jsp的连接状态

> manager-app 界面

![manager-app](/assets/pictures/tomcat/manager-app.png)

> 展示后端Web App的管理页面

> host-manager 界面

![vhost](/assets/pictures/tomcat/vhost.png)

> 虚拟主机的管理页面，可以对虚拟主机进行添加删除修改操作



#### 范例实现

```
#nginx的反向代理 
		Client (http) --> nginx (reverse proxy)(http) --> tomcat  (http connector)
		
		location / {
			proxy_pass http://www.a.com:8080;
		}
		
		location ~* \.(jsp|do)$ {
			proxy_pass http://www.a.com:8080;
		}
		
LAMT：Linux Apache(httpd) MySQL Tomcat 
		httpd的代理模块：
			proxy_module
			proxy_http_module：适配http协议客户端；
			proxy_ajp_module：适配ajp协议客户端；
			
		Client (http) --> httpd (proxy_http_module)(http) --> tomcat  (http connector)
		Client (http) --> httpd (proxy_ajp_module)(ajp) --> tomcat  (ajp connector)
		Client (http) --> httpd (mod_jk)(ajp) --> tomcat  (ajp connector)
#http的反向代理	
proxy_http_module代理配置示例：
	<VirtualHost *:80>
		ServerName      www.a.com
		ProxyRequests Off   关闭正向代理
		ProxyVia        On  开启反向代理
		ProxyPreserveHost On 开启将前端主机的报文，保留主机头部就是www.a.com用来与后端的对应的主机名称相对应
		<Proxy *>
			Require all granted
		</Proxy>
		ProxyPass / http://www.a.com:8080/    请求代理的根代理到设定的服务器上
		ProxyPassReverse / http://www.a.com:8080/  当客户端重新发请求时保留代理信息
		<Location />
			Require all granted
		</Location>
	</VirtualHost>
#ajp的反向代理			
proxy_ajp_module代理配置示例：
	<VirtualHost *:80>
		ServerName      www.a.com
		ProxyRequests Off
		ProxyVia        On
		ProxyPreserveHost On
		<Proxy *>
			Require all granted
		</Proxy>
			ProxyPass / ajp://www.a.com:8009/ 
			ProxyPassReverse / ajp://www.a.com:8009/ 
		<Location />
			Require all granted
		</Location>
	</VirtualHost>


```

#### 至此tomcat已介绍完毕，如有错误，望批评指正，敬请谅解!
