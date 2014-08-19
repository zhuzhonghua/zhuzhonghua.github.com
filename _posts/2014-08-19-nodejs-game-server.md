---
layout: post
title : 使用Nodejs开发网络游戏服务器
category : essays
tags : [essays 杂谈 nodejs Game Server]
---
{% include JB/setup %}

---

Nodejs基于Google的V8引擎，用于开发后端。以前Js只是用于前端编程。从此之后，前端和后端就统一了，都使用js来开发，从此也模糊了前后端的划分。

Nodejs天生的异步编程，也就天生满足异步Socket，所以非常适合做网络服务。

至于效率问题，在此不想多说了。

Pomelo也是使用Nodejs来开发网络服务的，不过它的目标是大而全，这里的目标是做小而精，只提供最简单的功能。

[https://gist.github.com/zhuzhonghua/8265e1ecd8acf1570af5](https://gist.github.com/zhuzhonghua/8265e1ecd8acf1570af5)

既然开发网络游戏，那么最基本的应该有个Socket对象。

Nodejs本身提供了net, Socket等对象，但是我们只需要

1. 监听端口
2. 接收数据包
3. 发送数据包
4. 检测连接断开等事件
5. 连接别的网络服务

所以需要在原生的Socket上抽象一层。

---
相关的函数，参考[https://gist.github.com/zhuzhonghua/8265e1ecd8acf1570af5](https://gist.github.com/zhuzhonghua/8265e1ecd8acf1570af5)

既然是监听，那么需要提供一个最基本的Acceptor，参数就是port。

    function Acceptor(port, cb)
    {
        this.port = port;
        this.server = null;
        this.newSockCB = cb;
     
        this.server = net.createServer(this.newSockCB);
        this.server.listen(this.port);
        LOG("acceptor init on port="+this.port);
    }

还需要提供一个回调函数，如果有新的连接过来。

    function Socket(option)
    
再提供一个Socket对象，其中有一些参数，如socket，消息处理函数，错误回调函数。
如果是主动发起的客户端连接，那么在option中提供client这个key，就可以了。
顺便还得提供服务器的IP和Port。

所有收到的网络字节，都会放在一个队列里，取数据时，要把整个队列当成一个整体。
同样发送消息时，也是把数据放在一个队列里，通过事件回调不停的向网络上发送字节。
当然这其中少不了process.nextTick的调用。
