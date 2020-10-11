# Java集合

## 1. Map



### 1.1 HashMap



#### 1.1.1 HashMap类字段



静态的默认字段，内容如下：

```java
// 默认初始化容量  MUST be a power of two.
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
// 最大容量 MUST be a power of two <= 1<<30.
static final int MAXIMUM_CAPACITY = 1 << 30;
// 默认负载因子 
static final float DEFAULT_LOAD_FACTOR = 0.75f;
// 将链表转换成红黑树的最小阈值
static final int TREEIFY_THRESHOLD = 8;
// 在扩容时拆分红黑树时，将树转成链表的最小阈值
static final int UNTREEIFY_THRESHOLD = 6;
// 能进行TREEIFY的最小容量阈值
static final int MIN_TREEIFY_CAPACITY = 64;
```

成员变量

```java
/* ---------------- Fields -------------- */
// Hash桶数组
transient Node<K,V>[] table;
/**
 * Holds cached entrySet(). Note that AbstractMap fields are used
 * for keySet() and values().
 */
transient Set<Map.Entry<K,V>> entrySet;
// Map大小
transient int size;
//记录当前Map被修改的次数，当遍历Map时如果，有修改，就会快速失败，抛出ConcurrentModificationException
transient int modCount;
// Map扩容的阈值 大小为 capacity * load factor
int threshold;
// Map的负载因子
final float loadFactor;
```



#### 1.1.2 初始化 

HashMap的构造函数有4个，其中构造参数为loadFactor（负载因子），initialCapacity（初始容量，向2的倍数取整）。

1. 默认构造函数 ，loadFactor = DEFAULT_LOAD_FACTOR（0.75f）
2. 有一个initialCapacity的构造函数
3. initialCapacity和loadFactor的构造函数
4. Map集合的构造方法

#### 1.1.3 HashMap中的静态函数

```java
// 取高16位与低16之间的 XOR（异或）操作，有利于让hash跟散列
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

 

#### 1.1.4 Map添加数据

JDK1.7使用的链表头插发，源码如下：

```java
void createEntry(int hash, K key, V value, int bucketIndex) {
    // 获取桶中的链表头
    Entry<K,V> e = table[bucketIndex];
    // 将新的节点赋值给桶，并将旧得节点e，赋值给当前节点得next
    table[bucketIndex] = new Entry<>(hash, key, value, e);
    size++;
}
```

 JDK1.8使用得时尾插法，源码如下：

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    //1. 获取hash所在的头节点
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        //2. 比较hash值，如果相同比较key，key值相同则替换value
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
         //3. 红黑树插入
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            //4. 链表插入
            for (int binCount = 0; ; ++binCount) {
                // 找到链表的尾节点，并将当前节点插入
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

改变的原因如下：

1. JDK1.8使用尾插的原因个人理解是为了计算链表长度，这样在链表过长时可以将其转换为红黑树，而JDK1.7没有使用红黑树，头插法能提高效率。
2. JDK1.7是用单链表进行的纵向延伸，当采用头插法时会容易出现逆序且环形链表死循环问题。但是在JDK1.8之后是因为加入了红黑树使用尾插法，能够避免出现逆序且链表死循环的问题。



1.1.5 Map的树化



1.1.6 Map的扩容





### 1.2 ConcurrentHashMap



JDK1.7 采用分段锁降低锁的粒度，思路是将一个大的桶数组分成多个小的段，每一段单独控制同步锁，这样提高了并发的性能。而1.8使用CAS+synchronized实现

JDK1.7的扩容是每个段单独扩容，并且每个段初始化使用的是s0段的负载因子和桶大小。计算大小时将每个段的count相加得到。

JDK1.8的扩容是直接将桶数组翻倍，并采用分区迁移数据，其他线程可以同时协助数据迁移。计算大小时采用并发累加器（类似LongAdder）。

#### 1.2.1 JDK1.7

