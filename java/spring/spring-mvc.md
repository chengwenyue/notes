## Spring mvc

### 1. @EnableWebMvc和Spring boot中WebMvcAutoConfiguration

webmvc配置如下：

MVC configuration (interceptors, formatters, view controllers, and other features)

WebMvcConfigurationSupport类中封装了MVC的各种组件的支持比如：

1. HandlerMapping
2. HandlerAdapter
3. HandlerExceptionResolver
4. ViewResolver

Webmvc配置生效规则：

1. 容器中存在WebMvcConfigurationSupport的bean，此时的WebMvcAutoConfiguration配置会失效，原因是WebMvcAutoConfiguration类上有一个条件注解

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
//当容器中不存在WebMvcConfigurationSupport对象时，此配置类生效
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
      ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {
｝
```

2. 当使用@EnableWebMvc注解时，会注入一个DelegatingWebMvcConfiguration类，此类继承WebMvcConfigurationSupport并扩展了对WebMvcConfigurer的支持。

```java
// 注入DelegatingWebMvcConfiguration
@Import(DelegatingWebMvcConfiguration.class)
public @interface EnableWebMvc {
}
// 继承WebMvcConfigurationSupport并扩展WebMvcConfigurer
@Configuration(proxyBeanMethods = false)
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
   private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();
   @Autowired(required = false)
   public void setConfigurers(List<WebMvcConfigurer> configurers) {
      if (!CollectionUtils.isEmpty(configurers)) {
         this.configurers.addWebMvcConfigurers(configurers);
      }
   }
}
```

3. WebMvcAutoConfiguration中定义了一个类EnableWebMvcConfiguration，此类继承自DelegatingWebMvcConfiguration，也就是说Spring boot默认的mvc配置支持WebMvcConfigurer。

```java
@Configuration(proxyBeanMethods = false)
public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration implements ResourceLoaderAware {}
```

4. 当用户自定义一个类继承 WebMvcConfigurationSupport，并且没有使用@EnableWebMvc注解，那么就会导致WebMvcConfigurer接口的失效。