## 1. wait、notify和notifyAll 以及 synchronized

生产者消费者模型是我们学习多线程知识的一个经典案例，一个典型的生产者消费者模型如下：

    public void produce() {
        synchronized (this) {
            while (mBuf.isFull()) {
                try {
                    wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            mBuf.add();
            notifyAll();
        }
    }

    public void consume() {
        synchronized (this) {
            while (mBuf.isEmpty()) {
                try {
                    wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            mBuf.remove();
            notifyAll();
        }

    }

这段代码很容易引申出来两个问题：一个是wait()方法外面为什么是while循环而不是if判断，另一个是结尾处的为什么要用notifyAll()方法，用notify()行吗。

很多人在回答第二个问题的时候会想当然的说notify()是唤醒一个线程，notifyAll()是唤醒全部线程，但是唤醒然后呢，不管是notify()还是notifyAll()，最终拿到锁的只会有一个线程，那它们到底有什么区别呢？

其实这是一个对象内部锁的调度问题，要回答这两个问题，首先我们要明白java中对象锁的模型，JVM会为一个使用内部锁（synchronized）的对象维护两个集合，Entry Set和Wait Set，也有人翻译为锁池和等待池，意思基本一致。

对于Entry Set：如果线程A已经持有了对象锁，此时如果有其他线程也想获得该对象锁的话，它只能进入Entry Set，并且处于线程的BLOCKED状态。

对于Wait Set：如果线程A调用了wait()方法，那么线程A会释放该对象的锁，进入到Wait Set，并且处于线程的WAITING状态。

还有需要注意的是，某个线程B想要获得对象锁，一般情况下有两个先决条件，一是对象锁已经被释放了（如曾经持有锁的前任线程A执行完了synchronized代码块或者调用了wait()方法等等），二是线程B已处于RUNNABLE状态。

那么这两类集合中的线程都是在什么条件下可以转变为RUNNABLE呢？

对于Entry Set中的线程，当对象锁被释放的时候，JVM会唤醒处于Entry Set中的某一个线程，这个线程的状态就从BLOCKED转变为RUNNABLE。

对于Wait Set中的线程，当对象的notify()方法被调用时，JVM会唤醒处于Wait Set中的某一个线程，这个线程的状态就从WAITING转变为RUNNABLE；或者当notifyAll()方法被调用时，Wait Set中的全部线程会转变为RUNNABLE状态。所有Wait Set中被唤醒的线程会被转移到Entry Set中。

然后，每当对象的锁被释放后，那些所有处于RUNNABLE状态的线程会共同去竞争获取对象的锁，最终会有一个线程（具体哪一个取决于JVM实现，队列里的第一个？随机的一个？）真正获取到对象的锁，而其他竞争失败的线程继续在Entry Set中等待下一次机会。

有了这些知识点作为基础，上述的两个问题就能解释的清了。

首先来看第一个问题，我们在调用wait()方法的时候，心里想的肯定是因为当前方法不满足我们指定的条件，因此执行这个方法的线程需要等待直到其他线程改变了这个条件并且做出了通知。那么为什么要把wait()方法放在循环而不是if判断里呢，其实答案显而易见，因为wait()的线程永远不能确定其他线程会在什么状态下notify()，所以必须在被唤醒、抢占到锁并且从wait()方法退出的时候再次进行指定条件的判断，以决定是满足条件往下执行呢还是不满足条件再次wait()呢。

就像在本例中，如果只有一个生产者线程，一个消费者线程，那其实是可以用if代替while的，因为线程调度的行为是开发者可以预测的，生产者线程只有可能被消费者线程唤醒，反之亦然，因此被唤醒时条件始终满足，程序不会出错。但是这种情况只是多线程情况下极为简单的一种，更普遍的是多个线程生产，多个线程消费，那么就极有可能出现唤醒生产者的是另一个生产者或者唤醒消费者的是另一个消费者，这样的情况下用if就必然会现类似过度生产或者过度消费的情况了，典型如IndexOutOfBoundsException的异常。所以所有的java书籍都会建议开发者永远都要把wait()放到循环语句里面。

然后来看第二个问题，既然notify()和notifyAll()最终的结果都是只有一个线程能拿到锁，那唤醒一个和唤醒多个有什么区别呢？

耐心看下面这个两个生产者两个消费者的场景，如果我们代码中使用了notify()而非notifyAll()，假设消费者线程1拿到了锁，判断buffer为空，那么wait()，释放锁；然后消费者2拿到了锁，同样buffer为空，wait()，也就是说此时Wait Set中有两个线程；然后生产者1拿到锁，生产，buffer满，notify()了，那么可能消费者1被唤醒了，但是此时还有另一个线程生产者2在Entry Set中盼望着锁，并且最终抢占到了锁，但因为此时buffer是满的，因此它要wait()；然后消费者1拿到了锁，消费，notify()；这时就有问题了，此时生产者2和消费者2都在Wait Set中，buffer为空，如果唤醒生产者2，没毛病；但如果唤醒了消费者2，因为buffer为空，它会再次wait()，这就尴尬了，万一生产者1已经退出不再生产了，没有其他线程在竞争锁了，只有生产者2和消费者2在Wait Set中互相等待，那传说中的死锁就发生了。

但如果你把上述例子中的notify()换成notifyAll()，这样的情况就不会再出现了，因为每次notifyAll()都会使其他等待的线程从Wait Set进入Entry Set，从而有机会获得锁。

其实说了这么多，一句话解释就是之所以我们应该尽量使用notifyAll()的原因就是，notify()非常容易导致死锁。当然notifyAll并不一定都是优点，毕竟一次性将Wait Set中的线程都唤醒是一笔不菲的开销，如果你能handle你的线程调度，那么使用notify()也是有好处的。

---

**参考**

- [https://www.jianshu.com/p/25e243850bd2](https://www.jianshu.com/p/25e243850bd2)


## java Atomic原子操作


Java从JDK1.5开始提供了java.util.concurrent.atomic包，方便程序员在多线程环境下，无锁的进行原子操作。原子变量的底层使用了处理器提供的原子指令，但是不同的CPU架构可能提供的原子指令不一样，也有可能需要某种形式的内部锁,所以该方法不能绝对保证线程不被阻塞。

原子类：

- 原子更新基本类型类： AtomicBoolean，AtomicInteger，AtomicLong，AtomicReference
- 原子更新数组类：AtomicIntegerArray，AtomicLongArray
- 原子更新引用类型：AtomicMarkableReference，AtomicStampedReference，AtomicReferenceArray
- 原子更新字段类：AtomicLongFieldUpdater，AtomicIntegerFieldUpdater，AtomicReferenceFieldUpdater

LockFree算法
通常是三个部分组成
1. 循环
2. CAS
3. 回退

### CAS

CAS，Compare and Swap即比较并交换。 java.util.concurrent包借助CAS实现了区别于synchronized同步锁的一种乐观锁。乐观锁就是每次去取数据的时候都乐观的认为数据不会被修改，所以不会上锁，但是在更新的时候会判断一下在此期间数据有没有更新。CAS有3个操作数：内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。CAS的关键点在于，系统在硬件层面保证了比较并交换操作的原子性，处理器使用基于对缓存加锁或总线加锁的方式来实现多处理器之间的原子操作。


### CAS的优缺点

cas优点：如一描述在并发量不是很高时cas机制会提高效率。

cas缺点：
1、cpu开销大，在高并发下，许多线程，更新一变量，多次更新不成功，循环反复，给cpu带来大量压力。
2、只是一个变量的原子性操作，不能保证代码块的原子性。
3、ABA问题  CAS只检查值是否改变，不判断值改变的过程。增加版本号来解决，jdk提供AtomicStampedReference。

---
**参考**

- [https://blog.csdn.net/u010983881/article/details/80415779](https://blog.csdn.net/u010983881/article/details/80415779)
- [用Atomic实现可以等待的锁](https://zhuanlan.zhihu.com/p/33127453)