##课程主题

​    Spring核心概念介绍和手写spring基础容器分析

##课程目标

1. 搞清楚spring全家桶都包含哪些东西

2. 搞清楚spring framework中的各个模块都是什么作用?

3. 搞清楚spring基础容器(BeanFactory)和高级容器(ApplicationContext)的区别

4. 搞清楚BeanPostProcessor和BeanFactoryPostProcessor的区别 

5. 搞清楚BeanFactory和FactoryBean的区别?

6. 搞清楚Ioc,DI,Aop这些核心概念

7. 搞清楚基础容器BeanFactory的继承体系中的类的作用

8. 搞清楚基础容器BeanFactory是如何加载BeanDefinition和并生成Bean实例的



##课程内容

- Ioc(核心中的核心): Inverse of Contol, 控制反转, 对象的创建权力由程序反转给Spring框架

- DI(核心中的核心): Dependency Injection, 依赖注入. 在spring框架负责创建Bean对象是, 动态的将依赖

- AOP: Aspect Oriented Progamming, 面向切面变成.在不修改目标对象的源代码情况下, 增强IOC容器

  spring是BOP编程思想, 在spring里一切都是由bean开始

- Spring容器:值得就是IOC容器,底层也就是一个BeanFactory(就是简单工厂实现)

  - spring容器分为基础容器和高级容器.
  - 基础容器就是BeanFactory体系
  - 高级容器就是ApplicationContext体系
  - BeanFactory和ApplicationContext的区别:
    - ApplicationContext接口是继承与BeanFactory接口
    - 通过BeanFactory创建Bean实例的时机是第一次获取该bean实例(第一次调用getBean方法)时候
    - ApplicationContext创建Bean实例的时机,是初始化的时候,一次性创建所有的单例bean的实例.

- BeanPostProcessor和BeanFactoryPostProcessor的区别

​            BeanFactoryPostProcessor: 可以在bean创建实例之前,对bean的定义信息(BeanDefinition)进行修改

​            BeanPostProcessor: 可以在bean创建实例前后, 对bean对象进行后置处理

- BeanFactory和FactoryBean的区别:

  - BeanFactory: 就是ioc的基础容器, 管理着spring中需要管理的所有的bean

  - Factorybean: 只是spring容器管理的bean的一员, 只是这个bean比较特殊,它可以产生一些指定类型的bean实例.

    ​	

### 基本容器BeanFactory的学习

- 通过手写spring基础容器去了解它

  - 分析如何写spring基础容器

  简单工厂案例:

  // 优化前的BeanFactory

  ```java
public class BeanFactory{
  
  ```

​	public Object getBean(String beanName) {

​		if("xxxService".equals(beanName)) {

​			XXXService xxxService = new XXXService();

​			return xxxService;

​		}

​	}

  }
  ```
  
  

​	优化后的BeanFactory

​```java
public class BeanFactory{

​		// cunfangzhebeanname和bean市里的映射关系

​		private Map<String, Object> singletons = new HashMap();

​		public Object getBean(String beanName) {

​			if("xxxService".equals(beanName)) {

​				// 从map中获取bean实例

​				// 如果获取不到,则执行bean的创建---如何创建bean?

​				// 程序员得告诉spring如何创建bean---->通过配置文件告诉spring

​				// 配置文件会配置很多的bean信息, 所以要简历beanname(key)和bean信息的映射关系

​				// 创建bean需要知道哪些信息呢? class信息, 是否单例, 属性信息.beanname

​				// 创建完成对象之后, 放入单例集合中

​			}

​		}

​	}
  ```



- 配置文件

  

  ```xml
<beans>
  		<bean name="" class="" scope="">
  
  			<property name="" value=""></property>
  
  		</bean>
  
  </beans>
  ```



![image-20191012214859545](/Users/lee/Library/Application Support/typora-user-images/image-20191012214859545.png)





- 了解BeanFactory的继承体系



### BeanFactory的继承体系

#### 体系结构图

![image-20191012224945977](/Users/lee/Library/Application Support/typora-user-images/image-20191012224945977.png)

​		



### spring源码阅读



**确定主线**

BeanDefinition的注册流, bean实例的创建流程

**找入口**

BeanDefinition的注册流程入口:

```java
XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(beanFactory);

Student student = (Student) beanFactory.getBean("student");

public Object getBean(String name) throws BeansException  {

	return doGetBean(name, null, null, false);

}
```



### 高级容器ApplicationContext源码阅读和循环依赖问题解决



#### 课程内容

高级容器ApplicationContext和基础容器的区别

1. BeanFactory是ApplicationContext的顶级接口
2. BeanFactory创建bean实例的时机是第一次回去该bean实例的时候才去创建。
3. ApplicationContext创建bean实例的时机，是初始化的时候，一次性创建所有的单例bean的实例。



**循环依赖**:

​	A对象 --> B对象  B对象 --> A对象

​	循环依赖分为: 构造循环依赖, set方法循环依赖

​	构造循环依赖: 无法解决, 只能修改代码

​	set方法循环依赖: 可以解决, spring就是通过三级缓存来解决的循环依赖.

​	spring中bean实例的创建过程:

	1. 实例化, 也就是new对象
 	2. 属性填充
 	3. 初始化



案例: 

1. A对象完成实例化
2. 之后进行属性填充.
   1. B对象完成实例化
   2. 属性填充
      1. 设置A对象属性,想要获取A对象, 直接从三级缓存中获取A对象, 然后放入二级缓存
   3. B对象初始化
   4. B对象从二级缓存中放入一级缓存
3. A对象初始化
4. A对象冲二级缓存放入一级缓存



**三级缓存的理解**:

- 一级缓存 : singletonObjects (beanName, Object对象)

  - 什么时候添加?
    - 在单例bean完成三部曲之后,添加到一级缓存中
  - 什么作用?
    - 我们去getBean的时候,想要的bean就是放到该缓存中, 不需要每次getBean都创建新的bean实例

- 二级缓存: earlySingletonObjects(beanName, Object对象)

  - 什么作用?
    - 发生循环依赖的时候

- 三级缓存: singletonFactories(beanName, ObjectFactory对象)

  - 什么作用?
    - 就是通过ObjectFactory, 提早的暴露未完成初始化的bean实例的引用.
    - 想要对提早暴露的bean完成一些BeanPostProcessor操作

  ObjectFactory开始存储的bean(由第一步产生的bean),可能是最终的bean, 也可能不是最终的bean

  我们最终想要的bean,想要低啊用ObjectFactory的getObject方法获取, 获取到的这个bean, 也是未完成初始化的, 想要放入提早暴露的bean引用的二级缓存中. 

  - 什么时候添加到三级缓存中?
    - 完成三部曲第一个步骤, 也就是创建bean实例之后,
  - 什么时候从三级缓存中删除?
    - 第一次,从一级缓存中找对象, 没有, 接下来从二级缓存中找, 没有, 接下来从三级缓存中找到, 然后放入二级缓存中.删除三级缓存.



>  org.springframework.context.support.AbstractApplicationContext.refresh();  阅读spring源码的入口







