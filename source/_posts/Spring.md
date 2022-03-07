---
title: spring
date: 2022-03-07 17:00:45
tags: 技术
categories: Spring

---

> 面试题知识点参考: https://blog.csdn.net/ThinkWon/article/details/104397516
> Spring源码阅读环境搭建:https://blog.csdn.net/qq_40670946/article/details/102989709
> 源码解析1：https://www.javadoop.com/post/spring-ioc
> 源码解析2：https://www.cnblogs.com/java-chen-hao/p/11829344.html
> SpringBoot集成其他框架：https://www.cnblogs.com/ityouknow/category/914493.html?page=2

## spring,springmvc,springboot,springcloud区别

```
spring以bean为中心提供IOC和AOP功能
springmvc是mvc框架,相当于增强版的servlet,主要处理web开发的路径映射和视图渲染
springboot以application为中心提供自动配置(约定大于配置)和监控,特点是快速开发,开箱即用
springcloud以service为中心提供服务的注册与发现和服务调用和负载均衡等分布式系统框架
```

## spring

```java
一. 什么是spring?
Spring是轻量级的Java应用开发框架,帮助简化企业级开发而存在

二. 核心思想?
IOC：将创建对象的权力交给Spring工厂,其中涉及到对象属性的依赖注入问题和循环依赖问题,通过DI来实现
DI：依赖注入,在程序运行期间动态的将依赖对象注入到组件中,一般通过构造函数注入(不支持循环依赖)
    和setter注入
AOP：面向切面编程，动态封装一些可重用性代码(切面：封装用于横向插入方法的功能类)，
    通过动态代理的方式，对目标类进行加强，动态生成这目标类的代理对象。

    AOP的相关概念：
        Target(目标)：被增强的对象
        Advice(通知)：切面具体的任务，即拦截后要做的事情
        Joinpoint(连接点)：指的是可以拦截的点(方法)
        Pointcut(切入点)：指的是真正被拦截到的点
        Weaving(织入)：将Advice的应用到Target的过程
        Proxy(代理)：被Advice增强后动态生成的代理对象
    
    AOP与AspectJ的区别：
        AOP基于动态代理,属于运行期的增强
        AspectJ基于字节码操作属于编译期增强,效率更高
        
三. 优势?
    1. IOC降低对象替换复杂性,降耦
    2. AOP更好的复用功能切面,消除样板式代码
    3. 方便集成其他框架使用如mybatis
    
四. spring事务传播机制?
REQUIRED：使用当前事务,若无则新建一个事务否则加入当前事务,子方法必须运行在一个事务中
SUPPORTS：当前有事务，则使用事务，否则不使用事务
MANDATORY：该传播属性强制必须存在一个事务，不存在就抛出异常
REQUIRES_NEW: 当前有事务，则挂起该事务，并且自己创建一个新的事务给自己使用；
    如果当前无事务同 REQUIRED
NOT_SUPPORTED: 当前有事务->事务挂起
NEVER: 当前有事务 ->抛出异常
NESTED:
    如果当前有事务，则开启子事务（嵌套事务），嵌套事务是独立提交或者回滚；
    如果当前没有事务，则同 REQUIRED。
    但是如果主事务提交，则会携带子事务一起提交。
    如果主事务回滚，则子事务会一起回滚。相反，子事务异常，则父事务可以回滚或不回滚。
    举例：领导决策不对，老板怪罪，领导带着小弟一同受罪。小弟出了差错，领导可以推卸责任。


五. 事务实现原理?

spring的事务隔离级别和数据库事务一样,是为了方便业务逻辑控制所以我们采用spring来控制
本质还是依赖数据库的事务

事务有两种实现方式,一是编程式事务(手动开启事务commit事务,不常用)
二是声明式事务(@Transactional,有spring框架内部控制)

实现原理就是AOP,Spring会扫描注解,基于这个类生成代理对象


六. 事务失效的场景?
1. 方法修饰符不为public
2. 异常被捕获
3. bean对象没被spring管理
4. 数据库不支持事务


七. 单例bean是线程安全的吗?

不是,spring bean的默认scope(作用域)是singleton(单例),可修改为prototype,
请求一次就创建一次新实例,这样保证线程安全

那spring是如何保证单例bean线程安全的呢？
使用ThreadLocal将各个线程的局部变量私有化
如果是类变量就需要加锁控制并发

八. bean的作用域
singleton : bean在每个Spring ioc 容器中只有一个实例。
prototype：一个bean的定义可以有多个实例。
request：每次http请求都会创建一个bean,在webApplicationContext情形下有效
session：基于Http session,一个session共享一个bean实例,在webApplicationContext情形下有效
global-session：基于Http session,多个session共享一个bean实例,在webApplicationContext情形下有效

九. 循环依赖
参考资料：https://cloud.tencent.com/developer/article/1497692
Spring支持filed属性注入(setter方法注入),不支持构造器注入循环依赖和作用域
    为`prototype`的 field属性注入循环依赖。
```

## spring的生命周期

![image](https://note.youdao.com/yws/api/personal/file/A04C0A294D7E4FE38BE60009597C48B7?method=download&shareKey=4987bb6477fbc35bd99fb3b607a51707)

![img](https://note.youdao.com/yws/api/personal/file/D3D6A4A03D9B468A9254CC2B4B4C1BEB?method=download&shareKey=5f9c04675181889521ad9f43bd39ab86)

```
ApplicationContext构造方法中有refresh()方法,是spring启动方法

1. BeanDefinitionReader读取bean.xml的配置定义信息,
将这些信息以k为beanName,v为BeanDefinition的形式存储到ConcurrentHashMap中

2. BeanFactory读取BeanDefinition信息,开始进行bean的创建的销毁

3. 通过反射的newInstance实例化此bean(未初始化,只是开辟一个空间)

4. 通过populateBean()方法填充baen属性(如Autowired,Setter等)

5. 之后通过initializeBean()初始化bean
	a. 通过invokeAwareMethods()方法,处理aware接口
	(aware的作用是让bean获得Spring容器的某个服务，如实现ApplicationContextAware接口，
则调用setApplicationContext(ApplicationContext)方法,传入Spring上下文，可对spring上下文进行操作)
	b. 调用BeanPostprocessor的前置后置方法,相当于一种插件,可以自定义bean的一些操作
	c. 调用@PostConstuct注解修饰的方法
	d. 调用init-method方法
	e. 之后初始化完成

6. 当bean使用后调用销毁方法进行清除
	a. @PreDestory
	b. destory-method
	c. finalize
```

## SpringMVC

![img](https://note.youdao.com/yws/api/personal/file/0ACEE7D780CF4A469F50DB20F12386A4?method=download&shareKey=87365ee134c4a62b8e7c248fa8a68432)

## SpringBoot
核心问题：自动装配，启动流程，内嵌tomcat，监听器Listener----->Spring容器

![](https://note.youdao.com/yws/api/personal/file/F05D312070B14972A7F356CF19E05563?method=download&shareKey=e2528ee2f7a86350ce22468f88f2b911)

```
1. 如何理解springboot的starter?

starter是一个jar包,写一个@Configuration类,将bean的定义写在其中,然后在spring.factories
写入该配置类,则springboot启动后会按照约定加载配置类到spring环境中管理(约定大于配置)

2. 自动装配核心注解
@EnableAutoConfiguration 打开自动配置的功能
给容器导入META-INF/spring.factories 里定义的自动配置类。
每一个自动配置类结合对应的 xxxProperties.java 读取配置文件进行自动配置功能

@ComponentScan Spring组件扫描

3. springboot 配置加载顺序
```