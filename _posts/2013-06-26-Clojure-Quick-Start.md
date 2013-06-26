---
layout: post
category: Clojure
tags: Clojure
title: Clojure快速入门(一)-windows下环境搭建
description: Clojure是一种基于java虚拟机的动态编程语言。
---

### Clojure特性
    
    1.Clojure非常优雅
    2.Clojure是Lisp的再度崛起
    3.Clojure是一种函数式语言
	4.Clojure简化了并发编程
	5.Clojure与java彼此亲密无间
	6.不同于许多其他流行的动态语言，Clojure运行飞快。
	
### 环境依赖
	
    1.JDK5以上
	2.Maven
	3.Leiningen
	4.Clojure
	
### 下载（jdk与maven省略）
[Clojure1.3下载][Clojure1.3]
[Leiningen下载][Leiningen]我下载的是leiningen-1.5.2-win.zip和leiningen-1.7.1-standalone.jar
    
	 
### 环境配置

    1.Leiningen配置
	    将下载好的leiningen-1.5.2-win.zip解压到c盘根目录中，并且将leiningen-1.7.1-standalone.jar复制到该解压目录中。设置以下环境变量
		
		Path => C:\lein
        LEIN => C:\lein
        LEIN_JAR => C:\lein\leiningen-1.7.0-SNAPSHOT-standalone.jar
		
	    打开命令行输入"lein version".如果配置正确可以看到一行信息。
	
	2.测试Clojure环境
	    输入 "lein repl" 启动REPL（读取-求值-打印循环）
		会显示"user=>"对你提示。
    
	    输入以下内容测试。
	    “(defn hello [name] (str "Hello, " name))”
	
	其中:
	    defn定义了一个函数
	    hello是函数的名称
	    hello函数接受一个名为name的参数
	    str是一个函数调用，把由任意参数组成的列表连接成一个字符串
	
	测试下我们刚刚定义好的函数
	    输入“(hello "87tu")”
	
	
### 小结
    以上就是Clojure基础环境搭建了。下面将探索Clojure。学习语法知识。
	
		
    
    
    

[Clojure1.3]: http://clojure.org/downloads_older "Clojure1.3下载"
[Leiningen]: https://github.com/technomancy/leiningen/downloads "Leiningen下载"
