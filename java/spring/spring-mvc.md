##Spring mvc

### 1. @EnableWebMvc和Spring boot中WebMvcAutoConfiguration

**webmvc配置如下：**

MVC configuration (interceptors, formatters, view controllers, and other features)

**webmvc配置生效规则：**

1. @EnableWebMvc+extends WebMvcConfigurationAdapter，在扩展的类中重写父类的方法即可，这种方式会屏蔽springboot的@EnableAutoConfiguration中的设置

2. extends WebMvcConfigurationSupport，在扩展的类中重写父类的方法即可，这种方式会屏蔽springboot的@EnableAutoConfiguration中的设置

3. implements WebMvcConfigurer，在扩展的类中重写父类的方法即可，这种方式依旧使用springboot的@EnableAutoConfiguration中的设置



**@EnableWebMvc源码：**

	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.TYPE)
	@Documented
	@Import(DelegatingWebMvcConfiguration.class)
	public @interface EnableWebMvc {
	}

EnableWebMvc会注入DelegatingWebMvcConfiguration，而DelegatingWebMvcConfiguration会加在所有
WebMvcConfigurer的实现类。

	@Configuration
	public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
	
		private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();
	
		// 读取WebMvcConfigurer实现类，从而控制WebMVC的配置
		@Autowired(required = false)
		public void setConfigurers(List<WebMvcConfigurer> configurers) {
			if (!CollectionUtils.isEmpty(configurers)) {
				this.configurers.addWebMvcConfigurers(configurers);
			}
		}


**WebMvcAutoConfiguration源码:**

	@Configuration
	@ConditionalOnWebApplication(type = Type.SERVLET)
	@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
	@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
	@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
	@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class,
	        ValidationAutoConfiguration.class })
	public class WebMvcAutoConfiguration {



可以看到，当`WebMvcConfigurationSupport`类不存在的时候，该自动装配类才会创建出来，也就是说，如果我们使用@EnableWebMvc,@EnableWebMvc就相当于导入了WebMvcConfigurationSupport类，这个时候，spring boot的自动装配就不会发生了，这个时候，我们能用的，只有WebMvcConfigurationSupport提供的若干个配置

至此，可以说明三条规则的前两条，无论是使用@EnableWebMvc还是扩展WebMvcConfigurationSupport类，spring都会创建一个WebMvcConfigurationSupport的类，进而屏蔽掉自动装配类WebMvcAutoConfiguration。

那么第三条规则呢？为何扩展WebMvcConfigurationAdapter(Java8 有接口默认方法后，该类被废弃，转为使用WebMvcConfigurer接口，下文我们统一用WebMvcConfigurer)不会屏蔽掉spring boot的自动装配呢？

这要再说WebMvcConfigurer的工作方式了，实际上，使用@EnableWebMvc引入的类不是WebMvcConfigurationSupport，而是DelegatingWebMvcConfiguration。
DelegatingWebMvcConfiguration继承了WebMvcConfigurationSupport，并且DelegatingWebMvcConfiguration通过依赖注入，持有Spring 容器所有WebMvcConfigurer实现类的一个List，所有对spring mvc的自定义的WebMvcConfigurer，都会挂到DelegatingWebMvcConfiguration上去。

而WebMvcAutoConfiguration内部，创建了一个继承了DelegatingWebMvcConfiguration的内部类EnableWebMvcConfiguration，这个类，一方面，提供了缺失的WebMvcConfigurationSupport的功能，另一方面，就起到了收集所有WebMvcConfigurer，调用它们的方法的作用。
