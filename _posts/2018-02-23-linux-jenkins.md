---
layout: post
title:  Jenkins + SVN + Maven自动构建部署
date:   2018-02-23 08:00:00
categories: Linux 
tags: Jenkins maven svn
---


## 介绍

> 1、简介

> Jenkins是一个开源软件项目，是基于Java开发的一种持续集成工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能

> 功能

- 1、持续的软件版本发布/测试项目。

- 2、监控外部调用执行的工作。

> 2、持续集成概念

> 持续集成是一个开发的实践，需要开发人员定期集成代码到共享存储库（比如说svn或git等）。这个概念是为了消除发现的问题，后来出现在构建生命周期的问题。持续集成要求开发人员有频繁的构建。最常见的做法是，每当一个代码提交时，构建应该被触发。

> 3、原理图

![jenkins-yuanli](/assets/pictures/jenkins/jenkins-yuanli.png)

## 基于Jenkins、Maven及SVN实现持续集成

> 1、基础环境

> jenkins所依赖的环境需要是jdk1.8或更高的版本，本文基于jdk1.8及tomcat8.0版本已经安装完成下进行操作

> 安装的步骤

 > 1、安装Maven并配置
 
 > 2、安装并启动Jenkins
 
 > 3、登录Jenkins设定相关的构建配置


### 安装Maven

> 本文所用的maven的版本为3.5.2，[可点击此处下载.](http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz)

> 1、下载完成后进行解压并重命名

```
tar xvf apache-maven-3.5.2-bin.tar.gz
mv apache-maven-3.5.2-bin.tar.gz maven 

```

> 2、设定maven的环境变量

> 编辑配置文件

```
vim  /ete/profile.d/my_profile.sh

#输入以下内容
MAVEN_HOME=/root/maven
export MAVEN_HOME
PATH=$PATH:$MAVEN_HOME/bin

```

> 保存并使配置文件生效

```
.  /ete/profile.d/my_profile.sh
```

> 3、测试是否安装成功

```
[root@centos6 ~]# mvn -v
Apache Maven 3.5.2 (138edd61fd100ec658bfa2d307c43b76940a5d7d; 2017-10-18T15:58:13+08:00)
Maven home: /root/maven
Java version: 1.8.0_144, vendor: Oracle Corporation
Java home: /usr/local/jdk/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-696.el6.x86_64", arch: "amd64", family: "unix"

```

### 安装及配置Jenkins

> 本文Jenkins的版本为jenkins-2.89.3-1.1，[可点击此处下载](https://pkg.jenkins.io/redhat-stable/jenkins-2.89.3-1.1.noarch.rpm)

> 1、安装

```
rpm -ivh  jenkins-2.89.3-1.1.noarch.rpm 

#将安装后生成的jenkins.war包移动至tomcat的webapps目录下
#启动tomcat

```

**访问本地的tomcat的Jenkins项目http://localhost:8080/jenkins进行配置**

> 按照提示进行安装
> 将提示位置的生成的密码输入对话框

![jenkins](/assets/pictures/jenkins/jenkins(1).png)

> 安装默认插件

![jenkins](/assets/pictures/jenkins/jenkins(2).png)
![jenkins](/assets/pictures/jenkins/jenkins(3).png)
> 2、安装成功登录后安装必要插件
- (1)  Maven Integration plugin
- (2)  Deploy to container Plugin

> 点击 "系统管理"--> "管理插件"-->"可选插件" 搜索上述两个插件并点击"立即安装"

> 3、配置jenkins全局环境变量 

> 点击   系统管理--> 全局工具配置
> 将本地的jdk及maven的家目录位置填写进jenkins中，如下图

![jenkins](/assets/pictures/jenkins/jenkins(4).png)
![jenkins](/assets/pictures/jenkins/jenkins(5).png)


> 4、设定maven的settings.xml配置文件

> 编辑文件

```
vim  maven/conf/settings.xml

```
> 将配置文件中的对应位置修改成自己的设定，如下图

![jenkins](/assets/pictures/jenkins/jenkins(6).png)
![jenkins](/assets/pictures/jenkins/jenkins(7).png)


### 构建Jenkins自动部署

> 点击左侧的`"构建"`来进行构建项目，如图

![jenkins](/assets/pictures/jenkins/jenkins(8).png)

> 点击确定后进行相关的设定


> - (1)、`General`

![jenkins](/assets/pictures/jenkins/jenkins(9).png)

> - (2)、`源码管理`

![jenkins](/assets/pictures/jenkins/jenkins(10).png)

> - (3)、`构建触发器`

![jenkins](/assets/pictures/jenkins/jenkins(11).png)

> - (4)、`构建环境`

![jenkins](/assets/pictures/jenkins/jenkins(12).png)

> - (5)、`Build`

![jenkins](/assets/pictures/jenkins/jenkins(13).png)


> 点击下方进行保存并应用


### 构建测试

> 点击 "立即构建"-->下方构建编号(或进度条)-->Console Output查看构建详细信息

![jenkins](/assets/pictures/jenkins/jenkins(14).png)
![jenkins](/assets/pictures/jenkins/jenkins(15).png)

> 构建成功后的控制台输出提示

![jenkins](/assets/pictures/jenkins/jenkins(16).png)


### 配置Jenkins自动发布

> 增加构建步骤，将maven打包好的项目自动部署至tomcat/webapps下
> 测试构建成功后，点击项目名称，进入"配置"模块中在最后增加一部构建后的操作，将打好的war包发送至指定的目录下

> "Deploy to container Plugin"插件需要通过属于manager-script组的Tomcat管理用户将war包发布到Tomcat服务器上，默认没有这样的用户，需要在TOMCAT_HOME/conf/tomcat-users.xml添加manager-script组和相应的用户，增加如下两行：

```
vim TOMCAT_HOME/conf/tomcat-users.xml

#插入如下内容
<role rolename="manager-script"/>
<user username="tomcat" password="tomcat" roles="manager-script"/>

```

`此处的用户名及密码自己指定`
- `注：配置好后需要重启Tomcat才能生效`

> - (6)、`构建后操作`

> 在“构建后操作”中增加“Deploy war/ear to container”选项,配置如下图：

![jenkins](/assets/pictures/jenkins/jenkins(17).png)
![jenkins](/assets/pictures/jenkins/jenkins(18).png)

> 构建成功后进行发布：如图

![jenkins](/assets/pictures/jenkins/jenkins(19).png)


> 进入tomcat下的webapps内,进行验证查看

![jenkins](/assets/pictures/jenkins/jenkins(20).png)

> 通过时间的对比可以看到项目已经更新了


## Jenkins的安装部署已完成，本文如有错误还望批评指正！








