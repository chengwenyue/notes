## spring boot启动流程

### 1.使用外部tomcat

先创建容器，在onRefresh方法中，创建并启动WebServer，在ServletContext创建后将DispatcherServlet 动态注册到ServletContext上，然后开始实例化容器中的单例bean，最后当第一个请求进来时DispatcherServlet开始初始化，加载mvc配置，最终映射请求到Controller上。

#### 1.1 ServletContainerInitializer

在servlet3.0+以后应用程序可以不配置web.xml文件，只需要实现ServletContainerInitializer接口，并且在类上加上`@HandlesTypes`注解，注解中放入需要实例化的接口类。

在tomcat加载项目时会自动查找`HandlesTypes`中接口的实现类，并放入`onStartup`的Set<Class<?>> var1参数中。


	public interface ServletContainerInitializer {
	    void onStartup(Set<Class<?>> var1, ServletContext var2) throws ServletException;
	}

#### 1.2 SpringServletContainerInitializer

#### 1.3 WebApplicationInitializer





### 2.使用内嵌tomcat


#### 1.1 ServletContextInitializer