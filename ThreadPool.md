## Thread Pool

A thread pool is an abstraction that gives users access to a group of ready, idle threads which can be given work. Thread pool implementations take care of worker creation, management, and scheduling, which can easily become tricky and costly if not handled carefully. Thread pools come in many different flavors, with many different techniques for scheduling and executing tasks, and with fixed numbers of threads or the ability of the pool to dynamically resize itself depending on load.

A classic thread pool implementation is Java’s Executor, which is an object which executes the Runnable tasks. Executors provide a way of abstracting out how the details of how a task will actually run. These details, like selecting a thread to run the task, how the task is scheduled are managed by the underlying implementation of the Executor interface.

Similar to Executor, Scala includes is a ExecutionContexts as part of the scala.concurrent package. The basic intent behind Scala’s ExecutionContext is the same as Java’s Executor; it is responsible for efficiently executing computations concurrently without requiring the user of the pool to have to worry about things like scheduling. Importantly, ExecutionContext can be thought of as an interface; that is, it is possible to swap in different underlying thread pool implementations and keep the same thread pool interface.

While it’s possible to use different thread pool implementations, Scala’s default ExecutionContext implementation is backed by Java’s ForkJoinPool; a thread pool implementation that features a work-stealing algorithm in which idle threads pick up tasks previously scheduled to other busy threads. The ForkJoinPool is a popular thread pool implementation due to its improved performance over Executors, its ability to better avoid pool-induced deadlock, and for minimizing the amount of time spent switching between threads.

Scala’s futures (and promises) are based on this ExecutionContext interface to an underlying thread pool. While typically users use the underlying default ExecutionContext which is backed by a ForkJoinPool, users may also elect to provide (or implement) their own ExecutionContext if they need a specific behavior, like blocking futures.

In Scala, every usage of a future or promise requires some kind of ExecutionContext to be passed along. This parameter is implicit, and is usually ExecutionContext.global (the default underlying ForkJoinPool ExecutionContext). For example, a creating and running a basic future:

```java
implicit val ec = ExecutionContext.global
val f : Future[String] = Future { “hello world” }
```java

In this example, the global execution context is used to asynchronously run the created future. As mentioned earlier, the ExecutionContext parameter to the Future is implicit. That means that if the compiler finds an instance of an ExecutionContext in so-called implicit scope, it is automatically passed to the call to Future without the user having to explicitly pass it. In the example above, ec is put into implicit scope through the use of the implicit keyword when declaring ec.

As mentioned earlier, futures and promises in Scala are asynchronous, which is achieved through the use of callbacks. For example:


```java
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



 

