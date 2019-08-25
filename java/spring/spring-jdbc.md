##1. DataSource自动注入

**起始类为：**

	org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration

此类在`spring-boot-autoconifg-XXX.jar`中的`META-INF/spring-factories`被加入到spring boot自动注入配置中。
配置如下：

	# Auto Configure
	org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
	... 省略 
	org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
	... 省略 

**DataSourceAutoConfiguration中分为两类DataSource注入**

1.spring 内部DataSource注入
 

	@Configuration
	@Conditional(EmbeddedDatabaseCondition.class)
	@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
	@Import(EmbeddedDataSourceConfiguration.class)
	protected static class EmbeddedDatabaseConfiguration {

	}

2.第三方 DataSource注入

	@Configuration
	@Conditional(PooledDataSourceCondition.class)
	@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
	@Import({ DataSourceConfiguration.Hikari.class, DataSourceConfiguration.Tomcat.class,
			DataSourceConfiguration.Dbcp2.class, DataSourceConfiguration.Generic.class,
			DataSourceJmxConfiguration.class })
	protected static class PooledDataSourceConfiguration {

	}

**第三方 DataSource注入实现**

源码位置：`org.springframework.boot.autoconfigure.jdbc.DataSourceConfiguration`

其中spring实现的第三方连接池配置有`Tomcat`,`Hikari`,`Dbcp2`。

如果未选择这三种连接池的话，spring会使用下面代码初始化第三方连接池。

	/**
	 * Generic DataSource configuration.
	 */
	@ConditionalOnMissingBean(DataSource.class)
	@ConditionalOnProperty(name = "spring.datasource.type")
	static class Generic {
		@Bean
		public DataSource dataSource(DataSourceProperties properties) {
			return properties.initializeDataSourceBuilder().build();
		}
	}

至此spring 会依据spring.datasource配置进行连接池的初始化，并加入到容器中。

## 2. springboot 事务

### 2.1 使用springboot事务

1.开启事务注解`@EnableTransactionManagement`，使用方式是在spring boot启动类上添加。


2.在业务方法上添加`@Transactional`注解。


### 2.2 源码解析

注解`@EnableTransactionManagement`部分源码如下：

	@Target(ElementType.TYPE)
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	@Import(TransactionManagementConfigurationSelector.class)
	public @interface EnableTransactionManagement {


由`TransactionManagementConfigurationSelector`类处理配置，默认使用`PROXY`模式代理，
注入`AutoProxyRegistrar`和`ProxyTransactionManagementConfiguration`配置。



	@Override
	protected String[] selectImports(AdviceMode adviceMode) {
		switch (adviceMode) {
			case PROXY:
				return new String[] {AutoProxyRegistrar.class.getName(),
						ProxyTransactionManagementConfiguration.class.getName()};
			case ASPECTJ:
				return new String[] {determineTransactionAspectClass()};
			default:
				return null;
		}
	}


**ProxyTransactionManagementConfiguration**是自动注入spring boot事务的核心配置。
共注入3个对象。

 

 
