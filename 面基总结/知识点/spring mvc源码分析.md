## 课程主题

spring aop相关源码阅读

spring事务源码阅读

spring mvc执行流程分析



## 课程内容

spring aop相关源码阅读



PointcutAdvisor

​	Advice

​	Pointcut

​		String expression = "execution(* com.kkb.\*.\*.ServiceImpl.*(..))"

​		ClassFilter

​		MethodMacher



### srping mvc

#### spring mvc 理解

spring mvc 底层是通过servlet实现的

Servlet实例对象由谁产生的?

​	servlet容器生产. servlet容器:tomcat, jboss,jetty...

Servlet实例什么时候产生?

​	一种是第一次访问一个请求时候,产生servlet对象,只会产生一次.

​	另一种需要针对servlet特殊配置<load-on-startup>, 就会在tomcat启动的时候产生该实例.

web应用的访问流程是什么?

​	

spring mvc处理请求的流程是什么?



Servlet:服务器端小程序,是专门处理服务器请求和响应结果的规范.