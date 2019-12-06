## groovy学习


## java 和groovy不同的地方

### 1. Default imports 默认引用的包

All these packages and classes are imported by default, i.e. you do not have to use an explicit import statement to use them:

以下的包是默认引用的，不需要在显示引用他们

	java.io.*
	
	java.lang.*
	
	java.math.BigDecimal
	
	java.math.BigInteger
	
	java.net.*
	
	java.util.*
	
	groovy.lang.*
	
	groovy.util.*

### 2. Multi-methods 重载方法选择

In Groovy, the methods which will be invoked are chosen at runtime. This is called runtime dispatch or multi-methods. It means that the method will be chosen based on the types of the arguments at runtime. In Java, this is the opposite: methods are chosen at compile time, based on the declared types.

在Groovy中方法是在运行时选择的，在Java中是在编译期选择的

The following code, written as Java code, can be compiled in both Java and Groovy, but it will behave differently:

	int method(String arg) {
	    return 1;
	}
	int method(Object arg) {
	    return 2;
	}
	Object o = "Object";
	int result = method(o);

In Java, you would have:

	assertEquals(2, result);
	由于o在编译期是Object类型，所以会调用method(Object arg)方法。
Whereas in Groovy:
	
	assertEquals(1, result);
	由于o在运行期是String类型，所以会调用method(String arg)方法。

### 3. Array initializers 数组初始化方式


In Groovy, the { …​ } block is reserved for closures. That means that you cannot create array literals with this syntax:

因为{ …​ }在Groovy中是闭包的概念，所以需要使用[ …​ ]初始化数组。

	int[] array = { 1, 2, 3}
You actually have to use:

	int[] array = [1,2,3]

---

参考

- [http://groovy-lang.org/differences.html](http://groovy-lang.org/differences.html)

## Groovy语法

### 1.Keywords 关键字

Groovy相比java新增的关键字

	def 	
	in
	trait
	as
### 2. Identifiers 标识符

Identifiers start with a letter, a dollar or an underscore. They cannot start with a number.

标识符以字母，$，_ 开头，不能以数字开头。

	def name
	def item3
	def with_underscore
	def $dollarStart

### 3. Quoted identifiers 引号

引号可在点分表达式后面，用于获取属性值。

`'` 单引号代表纯文本

`"` 双引号可代表文本，也可内嵌表达式

	def map = [:]
	// 普通文本
	map."an identifier with a space and double quotes" = "ALLOWED"
	map.'with-dash-signs-and-single-quotes' = "ALLOWED"
	
	assert map."an identifier with a space and double quotes" == "ALLOWED"
	assert map.'with-dash-signs-and-single-quotes' == "ALLOWED"

	//执行表达式
	def firstname = "Homer"
	map."Simpson-${firstname}" = "Homer Simpson"
	
	assert map.'Simpson-Homer' == "Homer Simpson"

### 4.String 

单引号`'`字符串

	'a single-quoted string'

字符串连接

	'ab' == 'a' + 'b'

三引号字符串可以跨行

	'''a triple-single-quoted string'''

支持转义字符


	Escape sequence		Character
	\t 		tabulation
	
	\b 		backspace
	
	\n 		newline
	
	\r 		carriage return
	
	\f 		formfeed
	
	\\ 		backslash
	
	\' 		single quote within a single-quoted string (and optional for triple-single-quoted and double-quoted strings)
	
	\" 		double quote within a double-quoted string (and optional for triple-double-quoted and single-quoted strings)

支持Unicode escape sequence字符

	'The Euro currency symbol: \u20AC'