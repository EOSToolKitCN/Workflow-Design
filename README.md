### 关于我们

<p>
  请关注我们的微信公众号: <strong>GENEREOS</strong>
</p>
<p>
  我们的官方网址: <a href="http://eostoolkit.vip">官网主页</a>.
</p>
<p>
  我们的Github: <a href="https://github.com/EOSToolKitCN">Github</a>.
</p>
<p>
  我们的Reddit: <a href="https://www.reddit.com/user/GENEREOS-CN">Reddit</a>.
</p>
<p>
  我们的Medium: <a href="https://medium.com/@eostoolkitcn">Medium</a>.
</p>
<p>
  请加入我们的Discord参与讨论: <a href="https://discord.gg/zBHJQA6">Discord</a>.
</p>

----

<br>

### 概要说明

```java
Block One 最近宣布, 他们将投票给那些给EOS发展带来重要贡献的超级节点, 希望超级节点可以对EOS生态发展持续做出贡献。
我们制作了这个工具网站帮助更多的人使用EOS, 我们想做的更多, 更好。
```

```java
我们一直在思考什么可以赋能EOS网络, 帮助它变得更加强大。
受到Corda R3的启发, 基于EOS网络的工作流引擎可能带来极大的改变。
BTC,ETH,EOS等公共区块链网络无法成为商业的一部分的主要原因是隐私保护和数据隔离的问题。
商业银行无法忍受敏感交易数据共享, 这也是他们选择Ripple,Stellar的原因。
```

```java
基于EOS网络的工作流引擎可以在局部节点实现信息交换和网络协同(借鉴: Corda的NOTARY)。
虽然Ripple,Stellar专链专用可以解决跨境汇款的隐私和协作, 但是EOS网络这样的开放公链在未来才有更大的可能性。
````

```java
如果你是Corda R3, Fabric 或者是密码学方面的专家; 如果对工作流引擎的设计和开发非常感兴趣;
如果对EOS网络的未来充满信息; 请联系我们: - <a href="mailto:eostoolkitcn@gmail.com">eostoolkitcn@gmail.com</a>
```

```java
基于EOS网络的工作流引擎设计和代码会在GitHub上实时更新: <a href="https://github.com/EOSToolKitCN">GitHub</a>
如果您认为这是有意义的事情, 请登陆 http://eostoolkit.vip 支持我们。
做为回报我们会空投WORK TOKEN给您。
千里之行始于足下。
```

----

<br>

### Workflow Engine设计汇总

所有设计文档实时更新在本Git, 可以查看md结尾的markdown文件查看。

Workflow Engine是在EOS网络链外协同多方参与机构共识的网络, 自身也是分布式系统的一种。
Workflow Engine的设计难点在 工作流节点间异步交互的任务处理。
所以设计从5方面展开, 团队内部逐一讨论优化, 也希望大家可以在Discord中参与讨论:

#### 章节一: 工作流引擎的异步任务处理设计

异步任务处理有多种实现方式可以选择,我们在Git中也fork了twitter的finagle 和 facebook的folly两个开源项目的代码。
finagle和folly都是业内公认比较成熟的异步处理框架, 因为在facebook和twitter的实际生产中使用所以稳定性和性能都是毋庸置疑的。

这一节的引擎异步任务处理设计通过: [Future/Promise的设计发展](http://dist-prog-book.com/chapter/2/futures.html) 寻找灵感, 结合finagle和folly的优势, 试图寻找适合EOS网络工作流引擎自己的异步任务处理模式。


[1.1EventLoop: 基于消息事件驱动的模式](https://github.com/EOSToolKitCN/workflow-design/blob/master/EventLoop.md)
[1.2ThreadPool: 基于线程池的模式](https://github.com/EOSToolKitCN/Workflow-Design/blob/master/ThreadPool.md)


<br>

#### 章节二: 工作流引擎通信协议设计

//TODO

<br>

#### 章节三: 工作流任务的编程界面定义


//TODO

<br>

#### 章节四: 工作流任务的编译器设计

//TODO

<br>

#### 章节五: 辅助套件: SDK和CLI的设计

//TODO


<br>

持续更新......

----

<br>

Best Regards
