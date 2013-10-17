---
layout: post
category: Liferay Portal
tags: Liferay Portal
title: Liferay Portal教程系列[一]环境搭建
description: Liferay Portal是一款开源的Portal
---

### 前言
    
    最近想研究下Portal,Portal可以把任何项目当成portlet组件集成进来。今天开始就对Liferay Portal细细研究。每天学习一点。
	
### 环境准备
    
	1、JDK1.7.0_15配置好JAVA_HOME
	
	2、eclipse
	
	3、Tomcat7.0.40解压至"F:/bundles/tomcat-7.0.40"目录（需要跟LiferayPortal_Src指定的tomcat目录一致）配置好CATALINA_HOME
	
	编辑"F:\bundles\tomcat-7.0.40\conf\catalina.properties"，将
	
	common.loader=${catalina.base}/lib,${catalina.base}/lib/*.jar,${catalina.home}/lib,${catalina.home}/lib/*.jar 替换为
	
	common.loader=${catalina.base}/lib,${catalina.base}/lib/*.jar,${catalina.home}/lib,${catalina.home}/lib/*.jar,${catalina.home}/lib/ext,${catalina.home}/lib/ext/*.jar
	
	在server.xml中加入URIEncoding="UTF-8"。
	
	在"F:/bundles/tomcat-7.0.40/bin/"目录中创建setenv.bat文件，内容为：
	
	if exist "%CATALINA_HOME%/jre@java.version@/win" (
		if not "%JAVA_HOME%" == "" (
		   set JAVA_HOME=
		)
		set "JRE_HOME=%CATALINA_HOME%/jre@java.version@/win"
	)
	set "JAVA_OPTS=%JAVA_OPTS% -Djava.net.preferIPv4Stack=true -Dorg.apache.catalina.loader.WebappClassLoader.ENABLE_CLEAR_REFERENCES=false -Duser.timezone=GMT -Xmx1024m -XX:MaxPermSize=256m"
	
	
    
	 
### 下载
    
  [LiferayPortal下载地址][LiferayPortal_Src_Download]，选择社区版Portal Source下载。
    
	下载结束后，解压至f:根目录。打开eclipse导入工程。


### 编译部署	
	
	准备工作：
	
	eclipse需要添加ant里面缺少的ecj.jar至ant环境中。设置方法"window--preferences--ant--runtime--ant home entries--add external jars"
	
	找到LiferayPortal目录中的ecj.jar添加进去
	
	找到工程根目录的build.xml右击选择"Run as--External Tools Configrations"
	
	在jre Tab页的VM arguments行加上 -Xms512m -Xmx1024m  
	
	在Targets Tab页勾选All，然后apply最后run。等待clean compile deploy。。
	
    
### 启动 
    
	全部执行完成之后，执行"F:\bundles\tomcat-7.0.40\bin\startup.bat"启动tomcat，启动完毕就可以在浏览器输入
	
	http://localhost:8080/ 访问刚刚部署的LifeRayPortal了
	

### 切换至MySql数据库
    
	很简单，只需要在工程里面找到"portal-ext.properties"（需要更改配置，把第一行去掉）文件，将该文件copy至
	
	"F:\bundles\tomcat-7.0.40\webapps\ROOT\WEB-INF\classes\"目录中即可
	
	再重启tomcat就可以了。
    
    
    
    
    

[LiferayPortal_Src_Download]: http://www.liferay.com/downloads/liferay-portal/overview "LiferayPortal下载地址"
