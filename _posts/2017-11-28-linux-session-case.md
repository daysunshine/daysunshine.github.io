---
layout: post
title:  负载均衡集群中的Session方案
date:   2017-11-28 08:00:00
categories: Linux 
tags:  session  
---

`转载`

### 前言

在我们给Web站点使用负载均衡之后，必须面临的一个重要问题就是Session的处理办法，无论是PHP、Python、Ruby还是Java，只要使用服务器保存Session，在做负载均衡时都需要考虑Session的问题。


**分享目录：**

1. 问题在哪里？如何处理？
2. 会话保持（案例：Nginx、Haproxy）
3. 会话复制（案例：Tomcat）
4. 会话共享（案例：Memcached、Redis）


**问题在哪里？**

从用户端来解释，就是当一个用户第一次访问被负载均衡代理到后端服务器A并登录后，服务器A上保留了用户的登录信息；当用户再次发送请求时，根据负载均衡策略可能被代理到后端不同的服务器，例如服务器B，由于这台服务器B没有用户的登录信息，所以导致用户需要重新登录。这对用户来说是不可忍受的。所以，在实施负载均衡的时候，我们必须考虑Session的问题。

在负载均衡中，针对Session的处理，我们一般有以下几种方法：

- Session 保持
- Session 复制
- Session 共享

### **会话保持**

Session保持（会话保持）是我们见到最多的名词之一，通过会话保持，负载均衡进行请求分发的时候保证每个客户端固定的访问到后端的同一台应用服务器。会话保持方案在所有的负载均衡都有对应的实现。而且这是在负载均衡这一层就可以解决Session问题。

**Nginx 做负载均衡的Session保持**

对于Nginx可以选用Session保持的方法实行负载均衡，nginx的upstream目前支持5种方式的分配方式，其中有两种比较通用的Session解决方法，ip_hash和url_hash。注意：后者不是官方模块，需要额外安装。

**ip_hash**

每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，达到了Session保持的方法。

例：

> upstream bakend {
>    ip_hash;
>    server192.168.0.11:80;
>    server192.168.0.12:80;
>  }

**Haproxy做负载均衡的Session保持**

    Haproxy作为一个优秀的反向代理和负载均衡软件，也提供了多种Session保持的方法，下面列举了两种最常用的：****

**源地址 Hash**

haroxy 将用户IP经过hash计算后指定到固定的真实服务器上（类似于nginx 的ip hash 指令）

> 配置指令:balancesource

**使用cookie进行识别 **

也就是Haproxy在用户第一次访问的后在用户浏览器插入了一个Cookie，用户下一次访问的时候浏览器就会带上这个Cookie给Haproxy，Haproxy进行识别。

> 配置指令:cookie  SESSION_COOKIE  insert indirect nocache

配置例子如下：

> cookie SERVERID insert indirect nocache
>
> server web01 192.168.56.11:8080 check cookie web01
>
> server web02 192.168.56.12:8080 check cookie web02

**会话保持的缺点：**

会话保持看似解决了Session同步的问题，但是却带来的一些其它方面的问题：

- 负载不均衡了：由于使用了Session保持，很显然就无法保证负载绝对的均衡。
- 没有彻底解决问题：如果后端有服务器宕机，那么这台服务器的Session丢失，被分配到这台服务请求的用户还是需要重新登录。

### **会话复制**

既然，我们的目标是所有服务器上都要保持用户的Session，那么将每个应用服务器中的Session信息复制到其它服务器节点上是不是就可以呢？这就是Session的第二中处理办法：会话复制。

  会话复制在Tomcat上得到了支持，它是基于IP组播（multicast）来完成Session的复制，Tomcat的会话复制分为两种：

- 全局会话复制：利用Delta Manager复制会话中的变更信息到集群中的所有其他节点。
- 非全局复制：使用Backup Manager进行复制，它会把Session复制给一个指定的备份节点。

    不过，这里我不准备来解释会话复制的Tomcat配置，如果有需求可以参考Tomcat官方文档，主要是因为会话复制不适合大的集群。根据笔者在生产的实践案例，当时是在集群超过6个节点之后就会出现各种问题，不推荐生产使用。

### **会话共享**

既然会话保持和会话复制都不完美，那么我们为什么不把Session放在一个统一的地方呢，这样集群中的所有节点都在一个地方进行Session的存取就可以解决问题。

**Session存放到哪里？**

对于Session来说，肯定是频繁使用的，虽然你可以把它存放在数据库中，但是真正生产环境中我更推荐存放在性能更快的分布式KV数据中，例如：Memcached和Redis。


**PHP设置Session共享**

如果你使用的是PHP那么恭喜你，配置非常的简单。PHP通过两行配置就可以把Session存放在Memcached或者Redis中，当然你要提前配置好他们。修改php.ini：

> session.save_handler = memcache
>
> session.save_path = "tcp://192.168.56.11:11211"

**使用Redis存储Session**

> session.save_handler = redis
>
> session.save_path ="tcp://localhost:6379"

提醒：别忘了给PHP安装memcache或者redis插件。

**Tomcat设置Session共享**

我们可以使用MSM（Memcached Session Manager）来实现同样把Session存放到Memcache中，GIthub地址如下：[https://github.com/magro/memcached-session-manager](undefined)目前支持Tomcat 6.x7.x和8.x的版本。

如果你想使用Redis，刚好也有开源的可以用，但是遗憾的是暂时不支持Tomcat 8.x的版本：[https://github.com/jcoleman/tomcat-redis-session-manager](undefined)

**Django设置Session共享**

在Django中Session是通过一个中间件管理的。如果要在应用程序中使用Session，需要在settings.py中的MIDDLEWARE_CLASSES变量中加入’django.contrib.sessions.middleware.SessionMiddleware’ 。Django的Session引擎可以将Session存放在三个地方，分别是：数据库、缓存、文件。

**使用数据库保存Session**

如果你想使用数据库支持的会话，你需要添加'django.contrib.sessions'到你的INSTALLED_APPS设置中。在配置完成之后，请运行manage.py migrate来安装保存会话数据的一张数据库表。

**使用缓存保持Session**

对于简单的缓存会话：

可以设置SESSION_ENGINE 为"django.contrib.sessions.backends.cache"。此时会话数据将直接存储在你的缓存中。然而，缓存数据将可能不会持久：如果缓存填满或者缓存服务器重启，缓存数据可能会被清理掉。

> 若要持久的缓存数据：

可以设置SESSION_ENGINE为"django.contrib.sessions.backends.cached_db"。它的写操作使用缓存，对缓存的每次写入都将再写入到数据库。对于读取的会话，如果数据不在缓存中，则从数据库读取。两种会话的存储都非常快，但是简单的缓存更快，因为它放弃了持久性。大部分情况下，cached_db后端已经足够快，但是如果你需要榨干最后一点的性能，并且接受会话数据丢失的风险，那么你可使用cache而不是cached_db

**使用文件保存Session**

使用文件保存Session不再我们的讨论之类，因为很难进行共享，PHP默认也是将Session存放在/tmp目录下。

