---
layout: post
category: node-webkit初体验
tags: nodejs webkit
title: node-webkit初体验
description: 可以使用html+js+css开发出本地桌面应用的东东
---

### 应用场景
    
    如果开发的web应用有浏览器限制，比如使用了大量的HTML5/CSS3，这些页面效果在webkit内核的浏览器中浏览最佳，就很适合用这个工具打包一份本地应用。其实就是生成一个webkit浏览器，但是它支持NodeJS。
	
### 缺陷
    
    
	在使用过程中发现它默认给出的浏览器语言是“en-US”，而且还不好更改。已经有很多人提出了这个问题。
    
    不能使用alert，prompt，confirm。弹出的界面无法关闭，可能是由于我的操作系统的问题。


  最后附上官网通道，里面有详细的教程和例子。一看就懂。[node-webkit官网][node-webkit]
[node-webkit]: https://github.com/rogerwang/node-webkit "node-webkit官网"
    
