---
layout: post
category: java
tags: List取交集
title: Java求2个list的交集
description: 在实际应用中经常会遇到需要提取2个list的交集的场景。
---

### 准备数据
    
    //下面初始化list使用了google的guava包。
    
    List<String> data1 = Lists.newArrayList("a","b","c","d","e","f","g");
    
    List<String> data2 = Lists.newArrayList("d","f","g","i","j","k","m");

### 使用retainAll API求交集

    List<String> tmpData = Lists.newArrayList(data2);
    
    tmpData.retainAll(data1);
    
    System.out.println("交集："+tmpData);
		
