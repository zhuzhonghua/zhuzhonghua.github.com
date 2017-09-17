    Title: 在项目中使用Nodejs
    Date: 2014-08-02T00:00:00

最近玩起了nodejs，准备用nodejs+cpp一起来写一套服务器，应对当前的需求；多进程的框架，进程间通信使用tcp socket。

先来分享一段自己写的log相关的代码
[https://gist.github.com/d2367390a8134578db41.git](https://gist.github.com/zhuzhonghua/d2367390a8134578db41)

在应用中如下使用

> LOG("recv a message "+123);

> ERROR("error! wrong option");

> CRASH("exception ")

如果需要其他类型的日志，只需要照着模板再写一套即可，比如INFO什么的。

> 1. 其中使用了fs.appendFile来写文件。
> 2. 文件名需要添加日期后缀，这样避免一个文件过大，导致阅读不方便。
> 3. 每一行日志前都有一段时间标签，方便以后查日志。
> 4. 在调用LOG时，根据队列的长度，来确定是否触发appendFile事件，以及什么时候再次触发。这样能保证单个文件，同时只有一个写操作。

>> 4.1 如果LOG之后，发现队列长度只有1，那么说明这是第一条或者当前唯一的一条日志，那么应该触发appenFile。
>> 4.2 如果在还没写完的情况下，多次调用LOG，那么队列长度肯定要大于1。appendFile事件肯定已经在4.1的情况下触发了，只需要在appendFile完成之后，再次触发appendFile就可以继续写当前缓存的日志。这是通过process.nextTick来实现的。本次的LOG操作就不用再触发appendFile了。

针对这种nodej这种单线程异步事件操作，在很多需要IO的地方都可以用这种思路，比如写Socket操作这种。

前面分享的代码有很多是冗余的，因为也简单够用，所以也没整理。

轻拍砖。
