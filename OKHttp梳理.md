OKHttp:

Builder 模式： 比较灵活的参数构造方式； Request、OKHttpClient 的创建；
外观模式： 给业务子类定义统一接口，提供一个代理类综合处理业务；
工厂模式：RealCall Call interface 具体的细节由子类自己去处理；

流程：

RealCall --->

**enqueue（异步）：**

Dispatcher关键成员变量:  

readyAsyncCalls :  异步等待队列，按队列顺序执行；

runningAsyncCalls :  异步执行队列；

runningSyncCalls : 同步执行队列；

这三个队列都是 ArrayDequeue；

Dispatch.enqueue:

- 加入异步等待队列，执行的时候进行筛选，符合条件加入执行队列执行，并从等待队列中移除；

- 筛选的条件是正在执行的异步队列个数小于maxRequest(64)并且请求同一个主机的个数小于maxRequestsPerHost(5) 时；

**execute（同步）：**

- RealCall.execute() 方法；

ps: 异步和同步的方法最后调用的都是 execute 方法，只是异步的是放入线程池中通过子线程来执行；



**execute()  ---> getResponseWithInterceptChain() :**

整个 Okhttp 框架的核心：

1. 把各个业务抽象成调用链上的某一环，特点：高内聚，低耦合，易拓展；
2. 递归调用； chain.procee()  ---> interceptor.intercept();