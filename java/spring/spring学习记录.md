# Spring 

## 概念

IOC（控制反转） DI（依赖注入）

在Spring官网上又这么一句话：

> IoC is also known as dependency injection (DI)

IoC也被称为DI

## Configuration Metadata 配置元数据

1. xml-based Spring 1.0 
2. Annotation-based Spring 2.5
	
	xml文件+ @Autowired + @Required 等注解

3. Java-based Spring 3.0

	无xml文件
	使用@Configuration, @Bean, @Import等

## 1. ApplicationContext

ApplicationContext 继承自 BeanFactory，但是它不应该被理解为 BeanFactory 的实现类，而是说其内部持有一个实例化的 BeanFactory（DefaultListableBeanFactory）。以后所有的 BeanFactory 相关的操作其实是委托给这个实例来处理的。

## 2. DefaultListableBeanFactory

![https://javadoop.com/post/spring-ioc#BeanFactory](https://www.javadoop.com/blogimages/spring-context/3.png)

DefaultListableBeanFactory基本实现所有的BeanFactory接口，所以在ApplicationContext中默认的BeanFactory代理为DefaultListableBeanFactory实例对象。

## 3.BeanDefinition

BeanDefinition 就是 Spring 的 Bean，我们自己定义的各个 Bean 其实会转换成一个个 BeanDefinition 存在于 Spring 的 BeanFactory 中



## 4. InstantiationAwareBeanPostProcessor和BeanPostProcessor

Bean后置处理器


https://blog.csdn.net/lk1728183655/article/details/80565269

## 5. bean的作用域scope

singleton ： 单个对象

spring先初始化单例对象，并放到单例池中，每次拿的时候都从map中拿。

如果singleton中依赖了prototype对象，则原型实例是提供给单例的bean的唯一实例。

prototype ： 原型对象

每次拿的时候就初始化。


## 5 spring的生命周期回调（ Lifecycle Callbacks）

### 5.1 对象初始化回调（Initialization Callbacks）

对象初始化回调有3中方式

1. 实现InitializingBean接口
2. 使用@PostConstruct 
3. @Bean(initMethod = "init")

组合式生命周期机制

调用顺序如下：

1. Methods annotated with @PostConstruct

2. afterPropertiesSet() as defined by the InitializingBean callback interface

3. A custom configured init() method



## Spring 可能的面试题

### 1.LookUp的用法

### 2.Spring 注入第三方bean的方法

有三种方法

- FactoryBean
- @Bean
- BeanFactory中的方法（有一定的局限性，会导致其他bean依赖不了这个bean）

### 3. FactoryBean和普通的Bean的区别

1. FactoryBean可以自己控制java对象的创建，
2. 普通的Bean是将Java Class交给Spring管理，并且这个类的对象创建过程也交给Spring管理




