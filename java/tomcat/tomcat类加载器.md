# 类加载器



## Java类加载机制



Java默认提供3个类加载器，1. 系统类（bootstrap）加载器，2.扩展类加载器，3.应用类加载器

系统类加载器是jvm初始化的，在Java种无法获取，调用getParent会返回null，系统类加载器加载路径为

JRE_HOME\lib，JRE_HOME\classes等，如下所示：

[E:\idea\Java\jdk1.8.0_101\jre\lib\resources.jar, E:\idea\Java\jdk1.8.0_101\jre\lib\rt.jar, E:\idea\Java\jdk1.8.0_101\jre\lib\sunrsasign.jar, E:\idea\Java\jdk1.8.0_101\jre\lib\jsse.jar, E:\idea\Java\jdk1.8.0_101\jre\lib\jce.jar, E:\idea\Java\jdk1.8.0_101\jre\lib\charsets.jar, E:\idea\Java\jdk1.8.0_101\jre\lib\jfr.jar, E:\idea\Java\jdk1.8.0_101\jre\classes]

扩展类加载器是Java扩展类的加载器，用于加载%JRE_HOME%\lib\ext下的包和类，如下所示

[E:\idea\Java\jdk1.8.0_101\jre\lib\ext, C:\WINDOWS\Sun\Java\lib\ext]

扩展类加载器可以在代码中获取。

应用类加载器用于加载应用的class和lib包，加载路径为 class.path。



### 加载顺序

1. 系统类（bootstrap）加载器，2.扩展类加载器，3.应用类加载器



### 双亲委派机制

一个类加载器在加载类时，首先是看自己有没有加载过，如果已经加载过了就直接返回，如果没有则要“委派”给父类加载器去加载，如果父类加载器加载不到，才自己进行加载，如果自己也加载不到，就会对外抛出ClassNotFoundException异常。

jvm默认的应用类加载器的父加载器为扩展类加载器，扩展类加载器的父类是系统类（bootstrap）加载器。

```java
// 应用类加载器
final ClassLoader appLoader = Main.class.getClassLoader();
System.out.println(appLoader);//sun.misc.Launcher$AppClassLoader
// 扩展类加载器能在代码中获取
ClassLoader ext = appLoader.getParent();
System.out.println(ext); // sun.misc.Launcher$ExtClassLoade
// 系统类加载器不能在代码中获取
final ClassLoader sysLoader = ext.getParent();
System.out.println(sysLoader); // null
```

双亲委派机制的好处

1. 防止类被重复加载
2. 安全因素，防止java系统类例如String类，被自定义的加载器篡改，出现安全隐患，保护核心类库。

如何打破双亲委派机制？

使用自定义加载器，并重写loadClass方法，在loadClass方法中先调用自己的加载逻辑，自己如果加载不到，在调用父类加载器。

loadClass和findClass的区别？

loadClass中实现了双亲委派机制，findClass是具体的类加实现逻辑。loadClass在父加载加载不到时会调用findClass完成自己的类加载过程。可参考部分Java源码。

```java
protected Class<?> loadClass(String name, boolean resolve)
    throws ClassNotFoundException {
    synchronized (getClassLoadingLock(name)) {
        // First, check if the class has already been loaded
        // 1. 判断类是否已经被加载过了
        Class<?> c = findLoadedClass(name);
        // 2. 调用父类加载器加载，完成双亲委派机制
        if (c == null) {
            long t0 = System.nanoTime();
            try {
                if (parent != null) {
                    c = parent.loadClass(name, false);
                } else {
                    c = findBootstrapClassOrNull(name);
                }
            } catch (ClassNotFoundException e) {
                // ClassNotFoundException thrown if class not found
                // from the non-null parent class loader
            }
			// 3. 父加载器没有加载到，则需要调用findClass自己加载该类
            if (c == null) {
                // If still not found, then invoke findClass in order
                // to find the class.
                long t1 = System.nanoTime();
                c = findClass(name);

                // this is the defining class loader; record the stats
                sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                sun.misc.PerfCounter.getFindClasses().increment();
            }
        }
        if (resolve) {
            resolveClass(c);
        }
        return c;
    }
}
// 空方法，需要子类自己实现逻辑
protected Class<?> findClass(String name) throws ClassNotFoundException {
    throw new ClassNotFoundException(name);
}
```



不同的类加载器加载的同一个类，得到的类是相同的吗？

不同，因为不同的类加载器都有自己存储类的地方，这个地方在内容的不同位置，所以加载的类也不一样。

### 全盘委托机制

如果一个类是由某一类加载器加载，那么该类的依赖的类都由该类加载器加载，比如A类被某一加载器加载，那么A类中使用的B类也会被改加载器加载。



## Tomcat类加载器

### tomcat的3个顶级类加载器

tomcat在Bootstrap中定义了3个类加载器，这3个类加载器负责tomcat核心类加载过程，除了加载Bootstrap的AppClassLoader类外，剩余所有的tomcat类都会由这3个类加载。需要注意的是，在源码启动中，由于没有lib目录，所以在调试中会发现tomcat类都是由AppClassLoader加载的，这跟运行环境的tomcat类加载有点不同。

```java
// 公共类加载器
ClassLoader commonLoader = null;
// catalina启动加载器
ClassLoader catalinaLoader = null;
// 共享类加载器
ClassLoader sharedLoader = null;
```

这3个类加载器会在Bootstrap初始化时创建，代码如下：

```java
private void initClassLoaders() {
    try {
        // commonLoader加载{catalina.base}/lib/*.jar;{catalina.home}/lib/*.jar
        // 由于是源码启动，所以commonLoader未加载任何class
        commonLoader = createClassLoader("common", null);
        if (commonLoader == null) {
            // no config file, default to this loader - we might be in a 'single' env.
            commonLoader = this.getClass().getClassLoader();
        }
        // 默认都是空的，使用的是commonLoader
        // Catalina 实际是由commonLoader的父加载器AppClassLoader加载的
        // 根据全盘委托机制，Catalina中初始化的对象（Service/Server等）也都是由AppClassLoader加载
        catalinaLoader = createClassLoader("server", commonLoader);
        sharedLoader = createClassLoader("shared", commonLoader);
    } catch (Throwable t) {
        handleThrowable(t);
        log.error("Class loader creation threw exception", t);
        System.exit(1);
    }
}
```

commonLoader是Tomcat中最顶级的类加载器，它负载加载tomcat的核心jar包，也就是catalina.base/lib和catalina.home/lib的jar包，其中CATALINA_HOME是Tomcat的安装目录，CATALINA_BASE是Tomcat的工作目录。

commonLoader加载的路径配置在catalina.properties文件中，默认如下所示：

```properties
common.loader="${catalina.base}/lib","${catalina.base}/lib/*.jar","${catalina.home}/lib","${catalina.home}/lib/*.jar"
```

catalinaLoader继承自commonLoader，负责加载server启动时所需要的类，例如Catalina类等。

catalinaLoader加载的路径配置在catalina.properties文件中，默认如下所示：

```properties
server.loader=
```

sharedLoader也继承自commonLoader，主要负责为web应用提供共享的类，例如运行时环境servlet-api.jar，websocket-api.jar以及tomcat提供的类。每一个web应用都有自己的类加载器，它们都继承sharedLoader，间接继承commonLoader，这样commonLoader和sharedLoader提供的类都能供web应用去使用，减少web应用的依赖大小。

catalinaLoader加载的路径配置在catalina.properties文件中，默认如下所示：

```properties
shared.loader=
```

### tomcat web应用类加载器

