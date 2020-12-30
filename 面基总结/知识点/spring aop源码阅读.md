课程主题

​	apo源码

课程回顾

​	aop基础知识

​		aop是什么:aop是什么?它不是spring独家的

​		aop核心概念:目标对象,代理对象,连接点,切入点,通知,织入,切面/通知器

​	动态代理模式

​		jdk动态代理(默认) --- 针对拥有接口的类进行代理

​		编写代理对象对应的源代码

​		编译源代码为class文件

​		加载class文件到jvm中

​		在jvm中创建带俩对象

​	cglib动态代理			--- 可以对所有类进行动态代理(非final)

​	需要掌握的动态代理常见问题

 1. 如何创建代理对象?

    jdk: Proxy.newInstance(classloader, interfaces,InvocationHandler)

    cglib:

    ​	Enhancer enhancer = new Enhancer();

    ​	enhancer.setSuperClass(...);

    ​	enhancer.setCallback(MethodInterceptor);

    ​	enhancer.create();

	2. 如何执行代理对象

    ​	jdk:

    ​		InvocationHandl.invoke(proxy, Method, args) {

    ​				Method.invoke(target, args);

    ​		}

    ​	cglib:

    ​		MethodInterceptor.intercept(proxy,Method,args, proxyMethod) {

    ​			proxyMethod.invokeSuper(proxy, args);

    ​		}



课程内容:

​	如何使用spring来生产代理对象

 1. 编写目标对象

 2. 编写增强代码(日志功能,事务功能) --- 只是一个纯粹的javabean,没有继承或者实现任何的类

 3. 通过注解或者xml配置来实现aop功能.

    ​	\<aop:config\>标签



阅读spring aop源码

	1. 如何解析\<aop:config\>标签流程阅读 ----> 十个beanDefinition对象
 	2. 解析spring aop产生代理对象的流程源码阅读 ----> jdk或者cglib是如何在spring中产生代理对象
 	3. spring aop产生的代理对象是如何执行的? ----> 搞清楚一个代理对象中多个通知功能是如何执行的.