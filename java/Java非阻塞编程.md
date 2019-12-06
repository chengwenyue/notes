# 响应式编程


## 什么是响应式编程（Reactive Programming）

>In computing, reactive programming is an asynchronous programming paradigm concerned with data streams and the propagation of change. This means that it becomes possible to express static (e.g. arrays) or dynamic (e.g. event emitters) data streams with ease via the employed programming language(s), and that an inferred dependency within the associated execution model exists, which facilitates the automatic propagation of the change involved with data flow.

> -- Wikipedia

>译文：
>
> 在计算机领域，响应式编程是一个专注于数据流和变化传递的异步编程范式。这意味着可以使用编程语言很容易地表示静态（例如数组）或动态（例如事件发射器）数据流，并且在关联的执行模型中，存在着可推断的依赖关系，这个关系的存在有利于自动传播与数据流有关的更改。

总结: 响应式编程是一种从数据流和变化出发的解决问题的模式。


JDK1.8原生的类库: CompletableFuture，Stream，Duration。

响应式编程框架: [Reactor 3](https://github.com/reactor/reactor-core) 


## 1. CompletableFuture

CompletableFuture中提供的静态和继承CompletionStage，Future接口

### 1. 创建CompletableFuture对象

异步方式

	public static CompletableFuture<Void>   runAsync(Runnable runnable)
	public static CompletableFuture<Void>   runAsync(Runnable runnable, Executor executor)
	public static <U> CompletableFuture<U>  supplyAsync(Supplier<U> supplier)
	public static <U> CompletableFuture<U>  supplyAsync(Supplier<U> supplier, Executor executor)

Async表示异步，supplyAsync有结果返回，runAsync无结果返回，无Executor则采用默认的ForkJoinPool.commonPool()作为它的线程池。

返回一个已经完成的CompletableFuture

	public static <U> CompletableFuture<U> completedFuture(U value)

### 2. 完成计算

获取结果

	//同步获取结果
	public T    get()
	public T    get(long timeout, TimeUnit unit)

	// 立即获取方法结果，如果没有计算结束则返回传的值
	public T    getNow(T valueIfAbsent) 
	public T    join()

join只返回结果，没有异常抛出，get返回结果，也抛出异常


主动触发计算

	public boolean complete(T  value)
	public boolean completeExceptionally(Throwable ex)

当调用complete或completeExceptionally会使get，join等立即返回结果或抛出异常。


### 3. 计算结果完成时的处理


	public CompletableFuture<T>     whenComplete(BiConsumer<? super T,? super Throwable> action)
	public CompletableFuture<T>     whenCompleteAsync(BiConsumer<? super T,? super Throwable> action)
	public CompletableFuture<T>     whenCompleteAsync(BiConsumer<? super T,? super Throwable> action, Executor executor)
	public CompletableFuture<T>     exceptionally(Function<Throwable,? extends T> fn)

当计算结束时触发，如果手动调用complete可能不会触发。

`exceptionally`当触发异常时调用。

方法不以Async结尾，意味着Action使用相同的线程执行，而Async可能会使用其它的线程去执行(如果使用相同的线程池，也可能会被同一个线程选中执行。


	public <U> CompletableFuture<U>     handle(BiFunction<? super T,Throwable,? extends U> fn)
	public <U> CompletableFuture<U>     handleAsync(BiFunction<? super T,Throwable,? extends U> fn)
	public <U> CompletableFuture<U>     handleAsync(BiFunction<? super T,Throwable,? extends U> fn, Executor executor)


与whenComplete()不同的是这个函数返回CompletableFuture的值是BiFunction返回的值。

### 4. 结果转换


	public <U> CompletableFuture<U> thenApply(Function<? super T,? extends U> fn)
	public <U> CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn)
	public <U> CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)

Apply不处理异常，只做结果转换。


### 5. 消费

**单个消费**

	public CompletableFuture<Void> thenAccept(Consumer<? super T> action)
	public CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action)
	public CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action, Executor executor)


单纯的去消费结果而不会返回新的值，因些计算结果为 Void，会导致下游的Action中参数为空。

**组合消费**

	public <U> CompletableFuture<Void> thenAcceptBoth(CompletionStage<? extends U> other, BiConsumer<? super T,? super U> action)
	public <U> CompletableFuture<Void> thenAcceptBothAsync(CompletionStage<? extends U> other, BiConsumer<? super T,? super U> action)
	public <U> CompletableFuture<Void> thenAcceptBothAsync(CompletionStage<? extends U> other, BiConsumer<? super T,? super U> action, Executor executor)

组合其他的CompletableFuture结果进行消费，必须所有CompletableFuture都完成时才会触发。

**纯消费**
	
	public CompletableFuture<Void> thenRun(Runnable action)
	public CompletableFuture<Void> thenRunAsync(Runnable action)
	public CompletableFuture<Void> thenRunAsync(Runnable action, Executor executor)

以上是彻底的纯消费，完全忽略上游的计算结果。


### 6 组合

**thenCompose**


	public <U> CompletableFuture<U> thenCompose(Function<? super T,? extends CompletionStage<U>> fn)
	public <U> CompletableFuture<U> thenComposeAsync(Function<? super T,? extends CompletionStage<U>> fn)
	public <U> CompletableFuture<U> thenComposeAsync(Function<? super T,? extends CompletionStage<U>> fn, Executor executor)

接收上游数据并且返回一个新的CompletableFuture。

**thenCombine**
	
	public <U,V> CompletableFuture<V> thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)
	public <U,V> CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)
	public <U,V> CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn, Executor executor)


两个CompletionStage是并行执行的，它们之间并没有先后依赖顺序，other并不会等待先前的CompletableFuture执行完毕后再执行。

### 7. acceptEither / applyToEither


	public CompletableFuture<Void> acceptEither(CompletionStage<? extends T> other, Consumer<? super T> action)
	public CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action)
	public CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action, Executor executor)

acceptEither方法是当任意一个 CompletionStage 完成的时候，action 这个消费者就会被执行。这个方法返回 CompletableFuture<Void>

	public <U> CompletableFuture<U> applyToEither(CompletionStage<? extends T> other, Function<? super T,U> fn)
	public <U> CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T,U> fn)
	public <U> CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T,U> fn, Executor executor)

applyToEither和acceptEither的区别是applyToEither有返回值，acceptEither无返回值。

### 8. allOf / anyOf

	public static CompletableFuture<Void> allOf(CompletableFuture<?>... cfs)

所有方法CompletableFuture完成时才往下执行，没有返回值。

	public static CompletableFuture<Object> anyOf(CompletableFuture<?>... cfs)

有一个完成就往下执行。

## 参考博客


- [浅谈响应式编程（Reactive Programming）](https://www.jianshu.com/p/1765f658200a)
- [JDK8新特性之CompletableFuture详解](https://www.jianshu.com/p/547d2d7761db)
- [Future模式之CompletableFuture](https://www.jianshu.com/p/220d05525f27)