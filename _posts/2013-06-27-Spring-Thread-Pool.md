---
layout: post
category: Spring 线程池
tags: Thread Pool
title: Spring线程池配置说明
description: 在许多应用中都存在着比较耗时但不影响返回结果的任务。如果同步执行会大大影响系统的体验。
---

### 应用场景
    
    1.上传视频的时候后台自动转换flv格式(JAVE)。
    2.发文的时候通知关注我的人我更新文章了。
    3.And等等。
	
### JDK原生的线程池
    
    public static final ThreadPoolExecutor threadPool = new ThreadPoolExecutor(20, 30, 3,
			TimeUnit.SECONDS, new ArrayBlockingQueue<Runnable>(1),
			new ThreadPoolExecutor.CallerRunsPolicy());
		
		第一个参数是初始化线程池大小;
		第二个是最大线程池大小，当初始化的线程不能满足时，允许扩展到的线程池容量最大数;
		第三个是当前线程池的容量超出初始化线程池的大小，并且存在空闲线程。用来指定过了多少时间自动回收。
		第四个是时间单位
		第五个是workQueue，工作队列。一共有三种。（SynchronousQueue、 LinkedBlockingQueue、ArrayBlockingQueue）
		第六个是指定当线程池里面的线程都在工作。有新的任务进来是要执行的策略。
		    一共有4个
		    AbortPolicy（拒绝抛异常）， 
		    CallerRunsPolicy（等待线程池中有空闲的线程，推荐使用）， 
		    DiscardOldestPolicy（忽略最老的任务，然后执行我们提交的），
		    DiscardPolicy（忽略你提交的任务）。
	
### Spring线程池使用
    
    在spring配置文件加入以下内容，位置就不说了。
    
    xmlns:task="http://www.springframework.org/schema/task"
    
    http://www.springframework.org/schema/task
  	http://www.springframework.org/schema/task/spring-task-3.0.xsd
    
    
    以下策略2选一即可，一般只选第一种
  	1.当线程池满了时新来的线程等待有空闲线程执行
  	<task:annotation-driven executor="executorWithCallerRunsPolicy"/>
    <task:executor id="executorWithCallerRunsPolicy" pool-size="2-5"  queue-capacity="50"  rejection-policy="CALLER_RUNS"/>
    
    
    2.当线程池满了时新来的线程直接抛出异常拒绝执行
  	<task:annotation-driven executor="executorWithPoolSizeRange"/>
    <task:executor id="executorWithPoolSizeRange" pool-size="2-5"  queue-capacity="50" />
    
    以上配置线程池的最大容量为”最大的pool-size+queue-capacity“，即为55。
    
    配置好了之后，只需要在被spring管理的bean中的方法上面加入”@Async“注解即可，是不是很简单。
    
    通过spring的线程池，简化了我们的开发。
    
    
