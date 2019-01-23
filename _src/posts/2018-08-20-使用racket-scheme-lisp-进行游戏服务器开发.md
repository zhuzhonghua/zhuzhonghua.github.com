    Title: 使用Racket(Scheme/Lisp)进行游戏服务器开发
    Date: 2018-08-20T18:16:54
    Tags: Racket,Scheme,Lisp,网络,游戏,服务器,后端,后台,开发

[Github仓库地址](https://github.com/zhuzhonghua/racket_gameserver)  

Racket作为Lisp方言，从它们的Blog[Racket Blog](https://blog.racket-lang.org/index.html)就可以看出，社区非常活跃。  

安装好Racket之后，使用racket main.rkt就可以运行服务器了。  

附带一个使用nodejs的测试客户端，node testClient.js 可以连接上Racket服务器。  

协议使用的Protobuf  

(serve port-no)是主入口函数，port-no是服务器需要监听的端口。  

(game-loop) 在单独线程中运行，使用custodians(守护)机制，来释放所有系统资源，streams，线程等  

game-loop的处理流程  

1. 接收新来的连接(accept-conn)  
2. 接收并处理客户端发送的消息(read-conn)  
3. 发送给客户端的消息(flush-conn)  

在read/write连接时，如果出错，使用with-handlers来处理网络异常。  

在op.rkt中定义了示例消息号  
在route.rkt中定义了消息路由，哪个消息号的消息，由哪个函数处理  
在proto.rkt中定义了反序列化的消息，以供游戏逻辑代码使用  

上面的定义比较简单粗暴，但是原理都是类似的  


录了一些视频，一点一点讲解，从零开始写这个服务器代码  
在持续更新中  

[Youtube地址](https://www.youtube.com/playlist?list=PL3gfPn2A9HNGqA6RjVQhpNW8TjaRqOlEt)  

[人人讲地址](https://h5.renrenjiang.cn/#/column?cid=19930&su=5799319)  
