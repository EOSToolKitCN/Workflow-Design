## Thread Model

The Oz programming language introduced an idea of dataflow concurrency model. In Oz, whenever the program comes across an unbound variable, it waits for it to be resolved. This dataflow property of variables helps us write threads in Oz that communicate through streams in a producer-consumer pattern. The major benefit of dataflow based concurrency model is that it’s deterministic - same operation called with same parameters always produces the same result. It makes it a lot easier to reason about concurrent programs, if the code is side-effect free.

OZ这个多范性的程序设计语言介绍了一种数据流同步模型。在OZ中当遇到未绑定变量时会等待它直到被解析。
在OZ程序中变量的数据流属性帮助我们以生产者-消费者的模式通过流在线程间通信。
以并发模型为基础的数据流的主要特点是它的确定性: 相同的操作符操作相同的变量一定产生相同的结果。
这很容易的解释了并发程序。


Alice ML is a dialect of Standard ML with support for lazy evaluation, concurrent, distributed, and constraint programming. The early aim of Alice project was to reconstruct the functionalities of Oz programming language on top of a typed programming language. Building on the Standard ML dialect, Alice also provides concurrency features as part of the language through the use of a future type. Futures in Alice represent an undetermined result of a concurrent operation. Promises in Alice ML are explicit handles for futures.

Alice ML是一种支持延后计算(将一个表达式的值计算向后拖延直到这个表达式真正被使用的时候), 并发, 分布式 和 约束编程的标准ML dialect。
Alice项目早期的目的是类型化编程语言的基础上重构OZ的语言功能。
基于标准的ML dialect构建, Alice提供了通过使用future实现并发特性的能力。
Future在Alice中代表了一个并发操作还未确定的结果。
Promise在Alice中是Future的显式处理。


Any expression in Alice can be evaluated in it’s own thread using spawn keyword. Spawn always returns a future which acts as a placeholder for the result of the operation. Futures in Alice ML can be thought of as functional threads, in a sense that threads in Alice always have a result. A thread is said to be touching a future if it performs an operation that requires the value future is a placeholder for. All threads touching a future are blocked until the future is resolved. If a thread raises an exception, the future is failed and this exception is re-raised in the threads touching it. Futures can also be passed along as values. This helps us achieve the dataflow model of concurrency in Alice.

Alice中的任何表达式都可以使用spawn关键字当前线程里进行计算。 
spawn通过返回一个future表达类似操作结果的一个占位符, 如果有线程计算需要使用这个future的结果那么它会先阻塞等待future执行完成。
如果一个线程抛出了异常, 这个future就会失败并且通知相关线程。future也可以以一个确定的值返回并通知相关线程, 这样就很容易在Alice中实现并发数据流模型。

Alice also allows for lazy evaluation of expressions. Expressions preceded with the lazy keyword are evaluated to a lazy future. The lazy future is evaluated when it is needed. If the computation associated with a concurrent or lazy future ends with an exception, it results in a failed future. Requesting a failed future does not block, it simply raises the exception that was the cause of the failure.

Alice也支持延后计算的特性, 通过lazy标注表达式会返回一个 lazy future。
如果lazy future最后在运算过程中抛出了异常, 整个计算结果都会以异常结束。
失败的future并不会导致阻塞。





 

