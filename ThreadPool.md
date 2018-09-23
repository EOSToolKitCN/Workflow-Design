## Thread Pool

A thread pool is an abstraction that gives users access to a group of ready, idle threads which can be given work. Thread pool implementations take care of worker creation, management, and scheduling, which can easily become tricky and costly if not handled carefully. Thread pools come in many different flavors, with many different techniques for scheduling and executing tasks, and with fixed numbers of threads or the ability of the pool to dynamically resize itself depending on load.

线程池是一种抽象形式, 让用户可以方便的使用一组空闲的线程为其工作。线程池实现了工作线程的创建,管理和调度; 但是如果处理不当很容易导致问题。
线程池有很多风格: 不同的调度算法, 可变数量的线程数, 线程池动态大小调整等。

A classic thread pool implementation is Java’s Executor, which is an object which executes the Runnable tasks. Executors provide a way of abstracting out how the details of how a task will actually run. These details, like selecting a thread to run the task, how the task is scheduled are managed by the underlying implementation of the Executor interface.

一个经典的线程池实现就是Java的Executor这个类, 负责执行实现了Runnable接口的任务。Executor将任务的执行细节抽象的很好。
细节是指: 如何选择一个线程去执行任务等等。Executor在Java中是一个接口, 它的底层实现了任务被调度和管理的具体操作。

Similar to Executor, Scala includes is a ExecutionContexts as part of the scala.concurrent package. The basic intent behind Scala’s ExecutionContext is the same as Java’s Executor; it is responsible for efficiently executing computations concurrently without requiring the user of the pool to have to worry about things like scheduling. Importantly, ExecutionContext can be thought of as an interface; that is, it is possible to swap in different underlying thread pool implementations and keep the same thread pool interface.

类似于Java中的Executor, Scala在scala.concurrent包中也提供了类似的实现:ExecutionContexts。Scala的ExecutionContexts所要完成的功能类似于Java的Executor。
它负责保证任务高效的并行运行,而不像一般线程池那样需要用户担心线程的调度问题。重要的是, ExecutionContext在Scala中是接口的形式;这样底层可以有多种实现方式, 对外的接口保持一致。

While it’s possible to use different thread pool implementations, Scala’s default ExecutionContext implementation is backed by Java’s ForkJoinPool; a thread pool implementation that features a work-stealing algorithm in which idle threads pick up tasks previously scheduled to other busy threads. The ForkJoinPool is a popular thread pool implementation due to its improved performance over Executors, its ability to better avoid pool-induced deadlock, and for minimizing the amount of time spent switching between threads.

虽然可以用不同的方式实现线程池, Scala的ExecutionContext默认实现方式以Java的ForkJoinPool为基础。
线程池实现的一个重点是work-stealing算法的实现: 如何保证空闲线程优先执行任务而不是将任务分配给繁忙的线程。
ForkJoinPool是一种比较普遍的实现方式,而且它的性能优于Executors;在预防死锁和线程切换开销上做的更好。


Scala’s futures (and promises) are based on this ExecutionContext interface to an underlying thread pool. While typically users use the underlying default ExecutionContext which is backed by a ForkJoinPool, users may also elect to provide (or implement) their own ExecutionContext if they need a specific behavior, like blocking futures.

Scala的Future/Promise实现就是基于ExecutionContext接口底层的线程池。
通常用户可以使用这个基于ForkJoinPool实现的ExecutionContext默认实现类, 也可以因为自己的特殊需要实现自己的ExecutionContext实现类, 比如: 需要阻塞的Future等。


In Scala, every usage of a future or promise requires some kind of ExecutionContext to be passed along. This parameter is implicit, and is usually ExecutionContext.global (the default underlying ForkJoinPool ExecutionContext). For example, a creating and running a basic future:

在Scala中如果要使用Future/Promise需要ExecutionContext的某个实现类传递。
参数的传递一般是隐形的, 通常的做法: ExecutionContext.global(ForkJoinPool的默认实现)。
下面通过Scala的代码举例说明如何使用future:


```scala
implicit val ec = ExecutionContext.global
val f : Future[String] = Future { “hello world” }

```


In this example, the global execution context is used to asynchronously run the created future. As mentioned earlier, the ExecutionContext parameter to the Future is implicit. That means that if the compiler finds an instance of an ExecutionContext in so-called implicit scope, it is automatically passed to the call to Future without the user having to explicitly pass it. In the example above, ec is put into implicit scope through the use of the implicit keyword when declaring ec.


在这个例子里: 


As mentioned earlier, futures and promises in Scala are asynchronous, which is achieved through the use of callbacks. For example:



```scala
implicit val ec = ExecutionContext.global

val f = Future {
  Http("http://api.fixed.io/latest?base=USD").asString
}

f.onComplete {
  case Success(response) => println(response)
  case Failure(t) => println(t.getMessage())
}
```



In this example, we first create a future f, and when it completes, we provide two possible expressions that can be invoked depending on whether the future was executed successfully or if there was an error. In this case, if successful, we get the result of the computation an HTTP string, and we print it. If an exception was thrown, we get the message string contained within the exception and we print that.




So, how does it all work together?




As we mentioned, Futures require an ExecutionContext, which is an implicit parameter to virtually all of the futures API. This ExecutionContext is used to execute the future. Scala is flexible enough to let users implement their own Execution Contexts, but let’s talk about the default ExecutionContext, which is a ForkJoinPool.




ForkJoinPool is ideal for many small computations that spawn off and then come back together. Scala’s ForkJoinPool requires the tasks submitted to it to be a ForkJoinTask. The tasks submitted to the global ExecutionContext is quietly wrapped inside a ForkJoinTask and then executed. ForkJoinPool also supports a possibly blocking task, using the ManagedBlock method which creates a spare thread if required to ensure that there is sufficient parallelism if the current thread is blocked. To summarize, ForkJoinPool is an really good general purpose ExecutionContext, which works really well in most of the scenarios.



 

