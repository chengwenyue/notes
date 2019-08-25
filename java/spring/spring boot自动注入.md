##spring boot自动注入

**1. 启动类上的`@SpringBootApplication`注解源码**

	@Target(ElementType.TYPE)
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	@Inherited
	@SpringBootConfiguration
	@EnableAutoConfiguration
	@ComponentScan(excludeFilters = {
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM,
				classes = AutoConfigurationExcludeFilter.class) })
	public @interface SpringBootApplication {

其中`@EnableAutoConfiguration`即为开启自动注入功能注解。
spring boot会根据引入的包和组件自动往容器中注入可能需要使用的JavaBean。


**2. `@EnableAutoConfiguration`的源码如下：**

	@Target(ElementType.TYPE)
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	@Inherited
	@AutoConfigurationPackage
	@Import(AutoConfigurationImportSelector.class)
	public @interface EnableAutoConfiguration {
	
		String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";
	
		/**
		 * Exclude specific auto-configuration classes such that they will never be applied.
		 * @return the classes to exclude
		 */
		Class<?>[] exclude() default {};
	
		/**
		 * Exclude specific auto-configuration class names such that they will never be
		 * applied.
		 * @return the class names to exclude
		 * @since 1.3.0
		 */
		String[] excludeName() default {};
	
	}

具体原理解析参考博客[SpringBoot之@EnableAutoConfiguration注解](https://blog.csdn.net/zxc123e/article/details/80222967)


