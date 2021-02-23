# Java垃圾收集



JDK1.8 默认的垃圾收集器为：

Parallel Scavenge和Parallel Old(PS Mark Sweep)



### 1. 对象内存分配策略



1. **新对象在eden区分配**：大多数对象的分配是在eden区的（有特殊情况），如果eden区不够分配，发生一次YoungGC。
2. **大对象直接进入老年代**：JVM提供一个阈值-XX:PretenureSizeThreshold=n，默认值为0，也就是所有的对象都先在eden区分配。由于复制算法在大对象复制上性能比较低，所以需要设置一个阈值。

> 需要占用大量连续内存空间的jaa对象是大对象，比如很长的字符串和数组。

3. **长期存活的对象进入老年代**：YoungGC后对象的年龄加1，当对象的年龄超过年龄阈值（-XX:MaxTenuringThreshold，默认 15）后对象进入老年代

4. **动态年龄判定**：新生代对象的年龄可能没达到阈值（MaxTenuringThreshold 参数指定）就晋升老年代。如果 Young GC 之后，新生代存活对象达到相同年龄所有对象大小的总和大于任意 Survivor 空间（S0+S1空间）的一半，此时 S0 或者 S1 区即将容纳不了存活的新生代对象。年龄大于或等于该年龄的对象就可以直接进入老年代，无须等到 MaxTenuringThreshold 中要求的年龄。

   > 动态年龄计算：Hotspot遍历所有对象时，按照年龄从小到大对其所占用的大小进行累积，当累积的某个年龄大小超过了survivor区的一半时，取这个年龄和MaxTenuringThreshold中更小的一个值，作为新的晋升年龄阈值。