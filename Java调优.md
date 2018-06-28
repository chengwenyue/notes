### Java Ops

	-Xmx258m：设置JVM最大可用内存为258M。
	-Xms516m：设置JVM初始内存为516m。此值可以设置与-Xmx相同，以避免每次垃圾回收完成后JVM重新分配内存。

	JAVA_OPTS='-Xms256m -Xmx512m'