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
2. 递归调用； chain.procee()  ---> interceptor.intercept(Chain)：变种责任链模式，每个拦截器可以填充 Response 的部分，一直到最后一个拦截器处理完成；

**拦截器：**

**RetryAndFollowUpInterceptor：**重定向拦截器，通过 recovery 函数来判断是否需要重试，其中包含了几个条件，任何条件触发都会停止循环并抛出异常；重定向函数 followUpRequest，根据返回的响应码做出对应处理，是否重定向处理，如果重定向处理，则把请求信息重新包装为 Request 返回给调用者；

**BridgeInterceptor：** 主要是构建请求的 HEAD 信息，以及 gzip 信息的处理；

**CacheInterceptor：** 缓存拦截器，根据网络环境和本地缓存情况决定是否进行请求，已经是否需要更新或者删除本地缓存的功能；

**ConnectInterceptor：** 真正处理链接的拦截器，可以看到实际上调用的时 RealConnection 这个类，里面包含了建立请求用到的 Socket 连接等属性（没错，OKhttp 底层使用的时 Socket 连接）,负责通过连接池来建立 http 连接;

**CallServerInterceptor：** 最后一个拦截器，因为是最后一层拦截器，是没有调用 chain.procced 函数的，只做了返回处理，该拦截器主要是通过 OKIO 读写网络数据；

