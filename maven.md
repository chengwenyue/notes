
## maven日常记录

#### maven中跳过单元测试
	mvn install -Dmaven.test.skip=true

	-DskipTests，不执行测试用例，但编译测试用例类生成相应的class文件至target/test-classes下。
	
	-Dmaven.test.skip=true，不执行测试用例，也不编译测试用例类。

不执行测试用例，但编译测试用例类生成相应的class文件至target/test-classes下。


#### maven多模块结构
Maven 3支持Maven项目的多模块（multi-modules）结构。这样的Maven项目也被称为聚合项目，通常由一个父模块和若干个子模块构成。

其中，父模块必须以pom打包类型，同时以<modules>给出所有的子模块。父模块的POM示例如下：

	...  
	<packaging>pom</packaging>  
	  
	<modules>  
	  <module>my-frontend-project</module>  
	  <module>my-service-project</module>  
	  <module>my-backend-project</module>  
	</modules>  
	... 


#### maven 编译时动态获取properties参数

pom.xml文件中properties标签中的属性可以在编译时自动替换.properties文件的属性
如下例子：

**jdbc.properties文件：**

	jdbc.driverClassName=${jdbc.driverClassName}
	jdbc.url=${jdbc.url}
	jdbc.username=${jdbc.username}
	jdbc.password=${jdbc.password}
	jdbc.validationQuery=${jdbc.validationQuery}

**pom文件：**

	<properties>
	   <jdbc.driverClassName>com.mysql.jdbc.Driver</jdbc.driverClassName>
	   <jdbc.url>jdbc:mysql://localhost/stock?createDatabaseIfNotExist=true&useUnicode=true&characterEncoding=utf-8&autoReconnect=true</jdbc.url>
	   <jdbc.username>root</jdbc.username>
	   <jdbc.password></jdbc.password>
	   <jdbc.validationQuery>SELECT 1 + 1</jdbc.validationQuery>
	</properties>

我们执行` mvn package `后，在 `target/classes/jdbc.properties` 里可以看到配置文件被成功替换。

参考如下博客：


[学习Maven之Properties Maven Plugin](https://blog.csdn.net/bao19901210/article/details/52400890)
