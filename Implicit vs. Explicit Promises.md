## Implicit vs. Explicit Promises


We define implicit promises as ones where we don’t have to manually trigger the computation vs Explicit promises where we have to trigger the resolution of future manually, either by calling a start function or by requiring the value. This distinction can be understood in terms of what triggers the calculation: With implicit promises, the creation of a promise also triggers the computation, while with explicit futures, one needs to triggers the resolution of a promise. This trigger can in turn be explicit, like calling a start method, or implicit, like lazy evaluation where the first use of a promise’s value triggers its evaluation.

首先定义隐式和显式的Promise, 隐式是不需要手工触发future计算结果的模式, 显式是需要手工触发future计算结果的模式。
两者的差别可以通过是什么触发了计算来判定: 隐式的promise在创建的时候同时触发计算, 但是显式的需要手动触发。


The idea for explicit futures were introduced in the Baker and Hewitt paper. They’re a little trickier to implement, and require some support from the underlying language, and as such they aren’t that common. The Baker and Hewitt paper talked about using futures as placeholders for arguments to a function, which get evaluated in parallel, but when they’re needed. MultiLisp also had a mechanism to delay the evaluation of the future to the time when it’s value is first used, using the defer construct. Lazy futures in Alice ML have a similar explicit invocation mechanism, the first thread touching a future triggers its evaluation.

在Baker和Hewitt的论文里首次介绍了显式future. 


An example of explicit futures would be (from AliceML):

```java
fun enum n = lazy n :: enum (n+1)
```

This example generates an infinite stream of integers and if stated when it is created, will compete for the system resources.

Implicit futures were introduced originally by Friedman and Wise in a paper in 1978. The ideas presented in that paper inspired the design of promises in MultiLisp. Futures are also implicit in Scala and JavaScript, where they’re supported as libraries on top of the core languages. Implicit futures can be implemented this way as they don’t require support from language itself. Alice ML’s concurrent futures are also an example of implicit invocation.

In Scala, we can see an example of an implicit future when making an HTTP request.

```java
val f = Future {
  Http("http://api.fixer.io/latest?base=USD").asString
}

f onComplete  {
  case Success(response) => println(response.body)
  case Failure(t) => println(t)
}
```

This sends the HTTP call as soon as it the Future is created. In Scala, although the futures are implicit, Promises can be used to have an explicit-like behavior. This is useful in a scenario where we need to stack up some computations and then resolve the Promise.



```java
val p = Promise[Foo]()

p.future.map( ... ).filter( ... ) foreach println

p.complete(new Foo)
```

Here, we create a Promise, and complete it later. Between creation and completion we stack up a set of computations which then get executed once the promise is completed.

我们创建一个promise然后完成它。在创建和完成之间有一系列计算任务在promise完成之后触发执行。

