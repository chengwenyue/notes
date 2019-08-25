# java 注解

## 1. 注解定义

注解通过 `@interface`关键字进行定义

	public @interface MyAnnotation {
	}

## 2.元注解

元注解是可以注解到注解上的注解，或者说元注解是一种基本注解，但是它能够应用到其它的注解上面。

如果难于理解的话，你可以这样理解。元注解也是一张标签，但是它是一张特殊的标签，它的作用和目的就是给其他普通的标签进行解释说明的。

元标签有 @Retention、@Documented、@Target、@Inherited、@Repeatable 5 种。

### 2.1 @Retention

Retention 的英文意为保留期的意思。当 @Retention 应用到一个注解上的时候，它解释说明了这个注解的的存活时间。

它的取值如下：

	RetentionPolicy.SOURCE 注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视。
	RetentionPolicy.CLASS 注解只被保留到编译进行的时候，它并不会被加载到 JVM 中。
	RetentionPolicy.RUNTIME 注解可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们。

### 2.2 @Target

@Target 指定了注解运用的地方。

有下面的取值：
	
	ElementType.ANNOTATION_TYPE 可以给一个注解进行注解
	ElementType.CONSTRUCTOR 可以给构造方法进行注解
	ElementType.FIELD 可以给属性进行注解
	ElementType.LOCAL_VARIABLE 可以给局部变量进行注解
	ElementType.METHOD 可以给方法进行注解
	ElementType.PACKAGE 可以给一个包进行注解
	ElementType.PARAMETER 可以给一个方法内的参数进行注解
	ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举

### 2.3 @Inherited

Inherited 是继承的意思，但是它并不是说注解本身可以继承，而是说如果一个超类被 @Inherited 注解过的注解进行注解的话，那么如果它的子类没有被任何注解应用的话，那么这个子类就继承了超类的注解。
如下：

	@Inherited
	@Retention(RetentionPolicy.RUNTIME)
	@interface Test {}
	
	
	@Test
	public class A {}
	
	
	public class B extends A {}

注解 Test 被 @Inherited 修饰，之后类 A 被 Test 注解，类 B 继承 A,类 B 也拥有 Test 这个注解。

### 2.4 @Repeatable

Repeatable 自然是可重复的意思。@Repeatable 是 Java 1.8 才加进来的，所以算是一个新的特性。
使用如下：

	@interface Persons {
		Person[]  value();
	}
	
	
	@Repeatable(Persons.class)
	@interface Person{
		String role default "";
	}
	
	
	@Person(role="artist")
	@Person(role="coder")
	@Person(role="PM")
	public class SuperMan{
		
	}

## 注解的属性

注解的属性也叫做成员变量。注解只有成员变量，没有方法。注解的成员变量在注解的定义中以“无形参的方法”形式来声明，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。

定义如下：

	@Target(ElementType.TYPE)
	@Retention(RetentionPolicy.RUNTIME)
	public @interface TestAnnotation {
		
		int id() default -1;
		
		String msg() default "Hi";
	
	}

如果一个注解内仅仅只有一个名字为 value 的属性时，应用这个注解时可以直接接属性值填写到括号内。
例如：

	public @interface Check {
		String value();
	}
	
	@Check("hi")

如果一个注解没有任何属性,那么在应用这个注解的时候，括号都可以省略。比如

	public @interface Perform {}

	@Perform
	public void testMethod(){}
