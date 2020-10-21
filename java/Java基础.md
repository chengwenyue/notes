# Java基础



## 1. 数据类型

### 基本类型

1. Java有八种基本类型 byte/1 ,char/2 short/2 int/4 float/4 long/8 dubble/8 boolean/~ (字节)
2. boolean 只有两个值：true、false，可以使用 1 bit 来存储，但是具体大小没有明确规定。JVM 会在编译时期将 boolean 类型的数据转换为 int，使用 1 来表示 true，0 表示 false。JVM 支持 boolean 数组，但是是通过读写 byte 数组来实现的。

### 包装类型

1. 每一个基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成。

包装类型在自动拆箱是会出现NullPointerException

```java
Integer b = null;
int c = b;
```

2. 自动装箱时如果值是在缓存池内的范围内，那么会使用缓存池对象

   ``` java
   Integer b = 12;
   Integer c = 12;
   System.out.println("b == c :" + (b == c));// true
   Integer x = 128;
   Integer y = 128;
   System.out.println("x == y :" + (x == y));// false
   ```

在编码中需要注意所有的相同类型的包装类对象之间值的比较，全部使用equals方法比较。

### 缓存池

1. new Integer(123) 与 Integer.valueOf(123) 的区别在于：

- new Integer(123) 每次都会新建一个对象；
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

Integer.valueOf源码如下：

```java 
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

2. 编译器会在自动装箱过程调用 valueOf() 方法，如果值在缓存池的大小内，则引用的都是同一个对象。

```java
Integer b = 12;
Integer c = 12;
System.out.println("b == c :" + (b == c));// true
Integer x = 128;
Integer y = 128;
System.out.println("x == y :" + (x == y));// false
```

3. 各基本类型的缓存池范围
   - boolean values true and false
   - all byte values
   - short values between -128 and 127
   - int values between -128 and 127
   - char in the range \u0000 to \u007F (0-127)

## 2. String

String值不可变的好处

1. hash值只需要计算一次
2. 能使用String Pool 缓存字符串字面量。
3. 安全性，保证传参的过程中不可变
4. 线程安全

String，StringBuilder，StringBuffer

1. 可变性，String不可变，StringBuilder，StringBuffer可变
2. 线程安全，String和StringBuffer是线程安全的，StringBuilder不安全。StringBuffer使用synchronized同步

## 3. StringPool



字符串常量池（String Pool）保存着所有字符串字面量（literal strings），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 intern() 方法在运行过程将字符串添加到 String Pool 中。

```java
// 实际上有3个 String对象，一个是 缓存在String Pool中的"abc"
// 另外两个是"abc"的新“引用”，实际上也是在堆上分配的String对象，但是value值是一样的
String s1 = new String("abc");
String s2 = new String("abc");
System.out.println("s1 == s2 :" + (s1 == s2));  // false
// 通过反射得到的两者的value值相同
System.out.println("s1.value == s2.value :" + (reflectGetValue(s1) == reflectGetValue(s1))); // true
//反射获取value
public static char[] reflectGetValue(String s) {
    try {
        Field field = String.class.getDeclaredField("value");
        field.setAccessible(true);
        return (char[]) field.get(s);
    } catch (Exception e) {
        e.printStackTrace();
        return null;
    }
}
```

当一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。

```java
// 返回字符串常量池中与之相等的应用，如果没有则将当前字符串加入常量池并返回
String s4 = s2.intern();
String s3 = s1.intern();
System.out.println("s3 == s4 : " +(s4 == s3));
```

## 4. Object中通用的方法



```java
public native int hashCode()

public boolean equals(Object obj)

protected native Object clone() throws CloneNotSupportedException

public String toString()

public final native Class<?> getClass()

protected void finalize() throws Throwable {}

public final native void notify()

public final native void notifyAll()

public final native void wait(long timeout) throws InterruptedException

public final void wait(long timeout, int nanos) throws InterruptedException

public final void wait() throws InterruptedException
```

