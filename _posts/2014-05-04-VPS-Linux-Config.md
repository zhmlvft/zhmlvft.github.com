---
layout: post
category: Linux Tomcat集群环境搭建
tags: Linux Tomcat集群环境搭建
title: Linux Tomcat集群环境搭建（基于MySql，Java7,Tomcat7,Nginx,memcached）
description: Linux Tomcat集群环境搭建
---

	
### 环境准备
    
	随便搞一台Linux主机，纯净版的(搞清楚是多少位的)。修改root密码 passwd root
    
	 
### 下载
    在根目录新建一个downloadfiles文件夹。
	cd downloadfiles/
	
	#mysql 下载
    
 	wget 'mysal tar下载地址（去官网找）'（我安装的是MySql5.5.37）
	
	#jdk下载
	
	wget 'jdk rpm下载地址'  （我安装的是7u55）

	#tomcat下载
	
	wget 'tomcatXXX.tar.gz下载地址' （apache-tomcat-7.0.53.tar.gz）

	

	
  



### 安装配置MySql
    
   		cd downloadfiles/
		
		tar -xvf MySqlxxx.tar
		
		检查环境是否已安装MySql
		
		rpm -qa|grep mysql
	
		有记录就  rpm -e --nodeps 该记录

		whereis mysql

		有目录则删之。系统老版的MySql已卸载的干干净净。
		
		在downloadfiles目录下有我们刚刚解压出来的一堆MySQLxxx.rpm，看需要安装之。
	
		用做数据库服务器我就安装MySQL-serverxxx.rpm和MySQL-clientxxx.rpm。
		
		命令：rpm -ivh MySQL-serverxxx.rpm
			 rpm -ivh MySQL-clientxxx.rpm 

		安装结束输入  service mysql start
		
		启动好之后就能连接。命令：mysql -uroot -p  回车再回车就行。

		进入MySql界面。需要修改密码与开启远程连接。

		先输入show databases;看看输出有木有mysql数据库。

		--------------------------------------------------------------

		有的话直接 use mysql;
			update user set password=PASSWORD('新密码') and host='%' where user='root';
			FLUSH PRIVILEGES;
		检查下是否已添加host
			select host from user where user='root';
		看记录中是否有 % 有就退出
			exit;
		重启mysql
			service mysql restart;
		
		
		-------------------------------------------------------------

	
		如果show databases结果中没有mysql，别着急。可以先关闭mysql服务
	
		service mysql stop

		然后输入mysqld_safe --skip-grant-tables  
		
		再开一个终端，输入mysql -uroot  回车

		show databases;就能看到mysql库了。
		
		有的话直接 use mysql;
			update user set password=PASSWORD('新密码') and host='%' where user='root';
			FLUSH PRIVILEGES;
		检查下是否已添加host
			select host from user where user='root';
		看记录中是否有 % 有就退出
			exit;
		杀mysql进程  pkill mysql
		重启mysql
			service mysql restart;
		
		-------------------------------------------------------------
		
		配置utf8编码

		在/etc目录中看看是否有my.cnf文件。没有就输入

		whereis mysql
		
		在结果中找到mysql的安装目录。进入目录  copy一个 my-xxx.cnf文件至etc下面。

		cp -r /mysql安装目录/my-small.cnf /etc
	
		编辑my.cnf文件，
		
		1、在[client]字段里加入default-character-set=utf8
		2、在[mysqld]字段里加入character-set-server=utf8
		3、在[mysql]字段里加入default-character-set=utf8

		保存退出，并重启mysql服务。至此，mysql基本配置已完成。
		
		
### 安装JDK
	
	先确定系统有没有JDK，有的话删除之。
	
	删除命令
	
	1）卸载系统自带的jdk版本： 
	   查看自带的jdk： 
	   #rpm -qa|grep gcj 
	   可能看到如下类似的信息： 
	   libgcj-4.1.2-44.el5 
	   java-1.4.2-gcj-compat-1.4.2.0-40jpp.115 
	   使用rpm -e --nodeps 命令删除上面查找的内容： 
	   #rpm -e –nodeps java-1.4.2-gcj-compat-1.4.2.0-40jpp.115 
	
	2）卸载rpm安装的jkd版本 
	   查看安装的jdk： 
	   #rpm -qa|grep jdk 
	   可能看到如下类似的信息： 
	   jdk-1.6.0_22-fcs 
	   卸载： 
	   #rpm -e --nodeps jdk-1.6.0_22-fcs   
	-----------------------------------------------------------------------
	删除结束之后就能安装新版了
	
	cd downloadfiles/

	rpm -ivh jdk-xxx.rpm
	
	安装结束
	
	
    
### 安装配置Tomcat
    
	cp -r /downloadfiles/apache-tomcat-xxxx.tar.gz /usr/

	cd /usr
	
	tar -xvf apache-tomcat-xxx.tar.gz
	
	重命名解压出来的tomcat文件夹为tomcat8080

	安装结束
	
	配置tomcat
	
	修改conf/server.xml,增加URIEncoding="UTF-8"。配置虚拟目录context(如果需要)。

	修改bin/catalina.sh文件

	在cygwin=false上面加入以下配置。根据机器性能修改参数。

	AVA_OPTS="-server -Xms1400m -Xmx1400m -Xss512K -XX:+AggressiveOpts -XX:+UseBiasedLocking -XX:PermSize=128m -XX:MaxPermSize=256m -XX:+DisableExplicitGC -XX:MaxTenuringThreshold=31 -XX:+UseConcMarkSweepGC -XX:+UseParNewGC  -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:LargePageSizeInBytes=128m  -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -Djava.awt.headless=true" 


    配置结束
	
	复制一份tomcat用作集群。
	
	cp -r /usr/tomcat8080/ /usr/tomcat8090
	
	将/usr/tomcat8090/bin/catalina.sh 与startup.sh 中的CATALINA_BASE和
	
	CATALINA_HOME更改为 CATALINA_BASE1与CATALINA_HOME1
	
	------------------------------------------------------------
	
	打开/usr/tomcat8090/conf/server.xml文件，将8080端口改为8090

	端口：8005->8006
    <Server port="8006" shutdown="SHUTDOWN"> 
	
	端口：8009->8010 
	<Connector port="8010" enableLookups="false" redirectPort="8443" protocol="AJP/1.3" /> 

	-------------------------------------------------------------

	




### 安装配置nginx
	1)环境准备
		安装环境必须有gcc、gcc-c++、zlib、openssl、pcre、
	2)下载安装
		cd downloadfiles/
		wget http://nginx.org/download/nginx-1.7.0.tar.gz
		tar -xvf nginx-1.7.0.tar.gz
		cd nginx-1.7.0
		./configure --with-http_stub_status_module --prefix=/opt/nginx/ 
		
		如果在configure中出现错误信息。少什么就在configure后面加什么。
	
		通过之后执行
		
		make
		
		make install
	 3）配置

		配置文件在/opt/nginx/conf/nginx.conf
		
		配置文件比较复杂。看需求配置。

附上我的一个配置文件 [nginx.conf下载地址][nginx_conf_Download],<br />
		
		
### 配置环境变量
	
	vi /etc/profile
	
	在最后加入以下配置
	
	JAVA_HOME=/usr/java/jdk1.7.0_55
	CLASSPATH=.:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
	CATALINA_HOME=/usr/tomcat8080
	CATALINA_BASE=/usr/tomcat8080
	CATALINA_HOME1=/usr/tomcat8090
	CATALINA_BASE1=/usr/tomcat8090
	PATH=${PATH}:${JAVA_HOME}/bin
	export JAVA_HOME CLASSPATH
	
	保存退出之后  source /etc/profile

### 安装memcached
	
	cd downloadfiles/
	
	wget https://memcached.googlecode.com/files/memcached-1.4.15.tar.gz
	
	tar -xvf memcached-1.4.15.tar.gz 
 	
	cd memcached-1.4.15
  	
	./configure --prefix=/opt/memcached

	安装过程中可能会提示缺少libevent
	
	ls -a /usr/lib |grep libevent  确认是否有libevent
	
	有的话直接
	
	yum install libevent-devel

	再./configure --prefix=/opt/memcached

	make
	
	make install
	
	启动memcached
	
	cd /opt/memcached/bin/
	
	./memcached -d -m 32 -l localhost -p 11211 -u root

	./memcached -d -m 32 -l localhost -p 11311 -u root

	如果是root用户启动加上-u root参数


### 配置集群
	 tomcat集群中主要将session统一交给memcached管理。
	 
	MSM(memcached-session-manager) 支持tomcat6 和tomcat7 ，利用 Value（Tomcat 阀）
	对Request进行跟踪。Request请求到来时，从memcached加载session，Request请求结束时，
	将tomcat session更新至memcached，以达到session共享之目的， 支持 sticky  和 non-
	sticky 模式。
	
	Sticky 模式：tomcat session 为 主session， memcached 为备 session。Request请求
	到来时， 从memcached加载备 session到 tomcat (仅当tomcat jvmroute发生变化时，否则直
	接取tomcat session)；Request请求结束时，将tomcat session更新至memcached，以达到主
	备同步之目的。
		
	 Non-Sticky模式：tomcat session 为 中转session， memcached1 为主 
	sessionmemcached 2 为备session。Request请求到来时，从memcached 2加载备 session 
	到 tomcat，（当 容器 中还是没有session 则从memcached1加载主 session 到 tomcat， 
	这种情况是只有一个memcached节点，或者有memcached1 出错时），Request请求结束时，将
	tomcat session更新至主memcached1和备memcached2，并且清除tomcat session 。以达到主
	备同步之目的。

	多个tomcat各种序列化策略配置如下：
	
	一、java默认序列化tomcat配置
	conf/context.xml添加
	<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
	   memcachedNodes="n1:192.168.100.208:11211 n2:192.168.100.208:11311"     
	lockingMode="auto"
	sticky="false" 
	requestUriIgnorePattern= ".*\.(png|gif|jpg|css|js)$"    
	sessionBackupAsync= "false"   
	sessionBackupTimeout= "100"      
	transcoderFactoryClass="de.javakaffee.web.msm.JavaSerializationTranscoderFactory"    
	    />
	tomcat目录lib增加jar包：
	spymemcached-2.10.3.jar
	memcached-session-manager-1.7.0.jar
	memcached-session-manager-tc7-1.7.0.jar
	
	二、javolution序列化tomcat配置
	conf/context.xml添加
	<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
	   memcachedNodes="n1:192.168.100.208:11211 n2:192.168.100.208:11311"   
	lockingMode="auto"
	sticky="false" 
	requestUriIgnorePattern= ".*\.(png|gif|jpg|css|js)$"    
	sessionBackupAsync= "false"   
	sessionBackupTimeout= "100"  
	copyCollectionsForSerialization="true"   
	transcoderFactoryClass="de.javakaffee.web.msm.serializer.javolution.JavolutionTranscoderFactory"    
	    />
	tomcat目录lib增加jar包
	msm-javolution-serializer-cglib-1.3.0.jar
	msm-javolution-serializer-jodatime-1.3.0.jar
	spymemcached-2.10.3.jar
	javolution-5.4.3.1.jar
	msm-javolution-serializer-1.7.0.jar
	memcached-session-manager-1.7.0.jar
	memcached-session-manager-tc7-1.7.0.jar
	
	三、xstream序列化tomcat配置
	conf/context.xml添加
	<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
	   memcachedNodes="n1:192.168.100.208:11211 n2:192.168.100.208:11311"   
	lockingMode="auto"
	sticky="false" 
	requestUriIgnorePattern= ".*\.(png|gif|jpg|css|js)$"    
	sessionBackupAsync= "false"   
	sessionBackupTimeout= "100"
	transcoderFactoryClass="de.javakaffee.web.msm.serializer.xstream.XStreamTranscoderFactory"    
	    />
	tomcat目录lib增加jar包
	xmlpull-1.1.3.1.jar
	xpp3_min-1.1.4c.jar
	xstream-1.4.6.jar
	msm-xstream-serializer-1.7.0.jar
	spymemcached-2.10.3.jar
	memcached-session-manager-1.7.0.jar
	memcached-session-manager-tc7-1.7.0.jar
	
	四、flexjson序列化tomcat配置
	conf/context.xml添加
	<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
	   memcachedNodes="n1:192.168.100.208:11211 n2:192.168.100.208:11311"     
	lockingMode="auto"
	sticky="false" 
	requestUriIgnorePattern= ".*\.(png|gif|jpg|css|js)$"    
	sessionBackupAsync= "false"   
	sessionBackupTimeout= "100"      
	transcoderFactoryClass="de.javakaffee.web.msm.serializer.json.JSONTranscoderFactory"    
	    />
	tomcat目录lib增加jar包
	flexjson-3.1.jar
	msm-flexjson-serializer-1.7.0.jar
	spymemcached-2.10.3.jar
	memcached-session-manager-1.7.0.jar
	memcached-session-manager-tc7-1.7.0.jar
	
	五、kryo序列化tomcat配置
	conf/context.xml添加
	<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
	   memcachedNodes="n1:192.168.100.208:11211 n2:192.168.100.208:11311"   
	lockingMode="auto"
	sticky="false" 
	requestUriIgnorePattern= ".*\.(png|gif|jpg|css|js)$"    
	sessionBackupAsync= "false"   
	sessionBackupTimeout= "100"    
	copyCollectionsForSerialization="true"   
	transcoderFactoryClass="de.javakaffee.web.msm.serializer.kryo.KryoTranscoderFactory"    
	    />
	tomcat目录lib增加jar包
	kryo-1.04.jar
	minlog-1.2.jar
	asm-3.2.jar
	reflectasm-1.01.jar
	kryo-serializers-0.11.jar
	msm-kryo-serializer-1.7.0.jar
	spymemcached-2.10.3.jar
	memcached-session-manager-1.7.0.jar
	memcached-session-manager-tc7-1.7.0.jar
	
	各种情况配置如上，据说kryo序列化效率比较快，未做测试。	

### 测试
	
    至此，集群中基本的一些配置已完成，可以写个jsp页面观察在一台tomcat宕机的情况下 
	
	sessionid是否变化。
	
	
[nginx_conf_Download]: http://pan.baidu.com/s/1sjrsK9Z "nginx.conf下载地址"
	
    
    
    
