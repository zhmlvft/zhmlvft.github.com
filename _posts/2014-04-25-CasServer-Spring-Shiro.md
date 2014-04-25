---
layout: post
category: 单点登录
tags: Shiro 权限 CAS 单点登录
title: Spring整合Shiro与CAS
description: Spring整合Shiro与CAS
---

### 前言
    
    当系统分割成n个子系统时，单点登录能统一登录接口。做到一处登录，全系统通行的目的。下面是一些基础配置。
	
### 环境准备
    
	1、maven3，settings.xml指向maven.oschina.net私服
	
	2、eclipse
	
    
	 
### 下载
    
  [CAS-Server War包构建下载地址][Cas-Server_war_Download],<br />

  [sso-test下载地址][spring_cas_shiro_Download],<br />

  下完导入eclipse中。


### 配置cas-server
    
    一、mvn clean install cas-overlay-3.5.x-master 工程，将目录
       
       /target/cas-overlay-3.5.x/WEB-INF/下的cas-servlet.xml、cas.properties。
       
       /target/cas-overlay-3.5.x/WEB-INF/spring-configuration/warnCookieGenerator.xml这3个文件复制到
       
       cas-overlay-3.5.x-master/src/main/webapp/WEB-INF/对应的目录中来。
       
       #复制过来的文件修改之后再打包就会替换原有war包中的文件。
    
    二、配置jdnc验证
        在工程pom.xml中加入以下依赖
		
		<dependency>
		     <groupId>org.jasig.cas</groupId>
		     <artifactId>cas-server-support-jdbc</artifactId>
		     <version>3.5.2</version>
		</dependency>
        <dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.14</version>
			<type>jar</type>
		</dependency>
		
		打开cas-overlay-3.5.x-master/src/main/webapp/WEB-INF/deployerConfigContext.xml
        找到
		<bean class="org.jasig.cas.authentication.handler.support.SimpleTestUsernamePasswordAuthenticationHandler" />
        
        并注释掉。在下方加入以下配置（根据自己表结构更改）
        
        <bean class="org.jasig.cas.adaptors.jdbc.QueryDatabaseAuthenticationHandler">
			<property name="dataSource" ref="dataSource"></property>
			<property name="sql" value="select password from user_info where id=?"></property>
            <!-- 密码使用md5加密加上该配置 -->
			<property name="passwordEncoder" ref="MD5PasswordEncoder"></property>
		</bean>
        
        注释掉
        <sec:user-service id="userDetailsService">
	        <sec:user name="jle" password="notused" authorities="ROLE_ADMIN" />
	        <sec:user name="jleleu" password="notused" authorities="ROLE_ADMIN" />
	    </sec:user-service>
        
        在下方加入
        <sec:user-service id="userDetailsService">
	        <sec:user name="@@THIS SHOULD BE REPLACED@@" password="notused" authorities="ROLE_ADMIN" />
	    </sec:user-service>

        在整个文件最下方</beans>标签之前加入以下配置
        
        <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
			<property name="driverClassName"><value>com.mysql.jdbc.Driver</value></property>
			<property name="url"><value>jdbc:mysql://localhost/sso_data?useUnicode=true&amp;characterEncoding=utf8&amp;zeroDateTimeBehavior=convertToNull</value></property>
			<property name="username"><value>root</value></property>
			<property name="password"><value>love</value></property>
		</bean>
	    <!-- 密码使用md5加密加上该配置 -->
		<bean id="MD5PasswordEncoder" class="org.jasig.cas.authentication.handler.DefaultPasswordEncoder">
			<constructor-arg index="0">
			<value>MD5</value>
			</constructor-arg>
		</bean>
   
	三、开启http验证
        默认情况下，cas只允许https登录验证。以下配置可开启http
        
        打开cas-overlay-3.5.x-master/src/main/webapp/WEB-INF/deployerConfigContext.xml
		
		找到
		<bean class="org.jasig.cas.authentication.handler.support.HttpBasedServiceCredentialsAuthenticationHandler"
					p:httpClient-ref="httpClient" />
     	
		添加p:requireSecure="false"属性。

		打开cas-overlay-3.5.x-master/src/main/webapp/WEB-INF/spring-configuration/
		中的ticketGrantingTicketCookieGenerator.xml和warnCookieGenerator.xml
		将里面的p:cookieSecure改为false
	
	
    
### 启动 
    
	cmd模式
		1.cd至sso-test目录输入mvn clean install antrun:run jetty:run
		2.cd至cas-overlay-3.5.x-master目录输入mvn clean install jetty:run
	
	在eclipse中启动不说了。
	
	启动好之后访问http://localhost:9090/sso。
    
    
    
    
    

[Cas-Server_war_Download]: https://github.com/leleuj/cas-overlay-3.5.x "CAS-Server War包构建下载地址"


[spring_cas_shiro_Download]: https://github.com/zhmlvft/sso-test "Spring整合Shiro与CAS例子"
