# Spring 


## 1. ApplicationContext

ApplicationContext 继承自 BeanFactory，但是它不应该被理解为 BeanFactory 的实现类，而是说其内部持有一个实例化的 BeanFactory（DefaultListableBeanFactory）。以后所有的 BeanFactory 相关的操作其实是委托给这个实例来处理的。

## 2. DefaultListableBeanFactory

![https://javadoop.com/post/spring-ioc#BeanFactory](https://www.javadoop.com/blogimages/spring-context/3.png)

DefaultListableBeanFactory基本实现所有的BeanFactory接口，所以在ApplicationContext中默认的BeanFactory代理为DefaultListableBeanFactory实例对象。

## 3.BeanDefinition

BeanDefinition 就是 Spring 的 Bean，我们自己定义的各个 Bean 其实会转换成一个个 BeanDefinition 存在于 Spring 的 BeanFactory 中

