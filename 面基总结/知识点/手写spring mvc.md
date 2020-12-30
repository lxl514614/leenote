课程主题

手写spring mvc框架



## 课程目标

1. 掌握DispatcherServlet的作用
2. 掌握Handler的作用以及实现方式
3. 掌握HandlerMapping的作用以及实现方式
4. 掌握HandlerAdapter的作用以及实现方式
5. 掌握策略模式和适配器模式

## 课程内容

1. 分析如何手写Spring mvc框架
   1. 使用java去实现BS结构的服务,必然会使用servlet.
2. 手写spring mvc框架需求
   1. 添加用
   2. 查询用户

#### 分析使用servlet实现BS结构服务处理的弊端

1. 对于程序员来说,会编写大量的servlet代码, 很不爽.
2. servlet是由tomcat产生的, 产生过多的servlet对象, 会导致tomcat本身启动的时候有压力



### 策略模式

策略模式:

​	主要是为了解决if....else这种代码

角色:

​	环境类

​	抽象策略类

​	具体策略类

使用技巧:

	1. 简历策略集合
 	2. 初始化策略集合
 	3. 遍历策略集合
 	4. 提供一个方法可以判断该策略算法合适



HandlerAdapter类:

​	作用:将DispatcherServlet类不想见的Handler类, 适配成统一的HandlerAdapter类型

使用了适配器模式

​	为什么使用适配器?

​	当DispatcherServlet类中想使用现成的Handler类时, 发现Handler类型不统一,DispatcherServlet

