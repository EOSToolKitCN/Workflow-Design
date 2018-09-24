## Promise Pipelining

One of the criticism of traditional RPC systems would be that they’re blocking. Imagine a scenario where you need to call an API ‘A’ and another API ‘B’, then aggregate the results of both the calls and use that result as a parameter to another API ‘C’. Now, the logical way to go about doing this would be to call A and B in parallel, then once both finish, aggregate the result and call C. Unfortunately, in a blocking system, the way to go about is call A, wait for it to finish, call B, wait, then aggregate and call C. This seems like a waste of time, but in absence of asynchronicity, it is impossible. Even with asynchronicity, it gets a little difficult to manage or scale up the system linearly. Fortunately, we have promises.


图1


图2


Futures/Promises can be passed along, waited upon, or chained and joined together. These properties helps make life easier for the programmers working with them. This also reduces the latency associated with distributed computing. Promises enable dataflow concurrency, which is also deterministic, and easier to reason about.

The history of promise pipelining can be traced back to the call-streams in Argus. In Argus, call-streams are a mechanism for communication between distributed components. The communicating entities, a sender and a receiver are connected by a stream, and sender can make calls to receiver over it. Streams can be thought of as RPC, except that these allow callers to run in parallel with the receiver while processing the call. When making a call in Argus, the caller receives a promise for the result. In the paper on Promises by Liskov and Shrira, they mention that having integrated Promises into call streams, next logical step would be to talk about stream composition. This means arranging streams into pipelines where output of one stream can be used as input of the next stream. They talk about composing streams using fork and coenter.

Channels in Joule were a similar idea, providing a channel which connects an acceptor and a distributor. Joule was a direct ancestor to E language, and talked about it in more detail.

```java
t3 := (x <- a()) <- c(y <- b())

t1 := x <- a()
t2 := y <- b()
t3 := t1 <- c(t2)
```

Without pipelining in E, this call will require three round trips. First to send a() to x, then b() to y then finally c to the result t1 with t2 as an argument. But with pipelining, the later messages can be sent with promises as result of earlier messages as argument. This allowed sending all the messages together, thereby saving the costly round trips. This is assuming x and y are on the same remote machine, otherwise we can still evaluate t1 and t2 parallely.

Notice that this pipelining mechanism is different from asynchronous message passing, as in asynchronous message passing, even if t1 and t2 get evaluated in parallel, to resolve t3 we still wait for t1 and t2 to be resolved, and send it again in another call to the remote machine.

Modern promise specifications, like one in JavaScript comes with methods which help working with promise pipelining easier. In JavaScript, a Promise.all method is provided, which takes in an iterable and returns a new Promise which gets resolved when all the promises in the iterable get resolved. There’s also a Promise.race method, which returns a promise which is resolved when the first promise in the iterable gets resolved. Examples using these methods are shown below.

```java
var a = Promise.resolve(1);
var b = new Promise(function (resolve, reject) {
  setTimeout(resolve, 100, 2);
});

Promise.all([p1, p2]).then(values => {
  console.log(values); // [1,2]
});

Promise.race([p1, p2]).then(function(value) {
  console.log(value); // 1
});
```

In Scala, futures have an onSuccess method which acts as a callback to when the future is complete. This callback itself can be used to sequentially chain futures together. But this results in bulkier code. Fortunately, the Scala API has combinators which allow for easier combination of results from futures. Examples of combinators are map, flatMap, filter, withFilter.


