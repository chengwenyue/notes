
### maven日常记录

#### maven中跳过单元测试
	mvn install -Dmaven.test.skip=true


	<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <configuration>
          <skip>true</skip>
        </configuration>
      </plugin>
    </plugins>
  	</build>


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