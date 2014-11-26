---
layout: post
title : 使用自定义协议plus回调函数，支持RPC
category : essays
tags : [protobuf 回调函数 rpc episode master 中央服务器 附属服务器]
---
{% include JB/setup %}


---

在多进程服务器中，往往会有一个中央服务器，称为master
其他附属服务器可以使用RPC机制，很方便的调用master的服务
但是也会有类似master主动推送消息给附属服务器的情况
这时如果再使用rpc时，就得在附属服务器上再监听一个rpc端口
这样附属服务器和master服务器之间就会有两个socket连接
这样也不太方便使用自定义协议如protobuf，来实现master与其他服务器之间的通信
与episode中的基本通信机制也不太兼容
所以需要取巧，换一种写法，来达到rpc的效果，master和各个附属服务器也只有一个socket连接
master依然还是中间服务器，也只需要在master配置一个监听端口

具体使用示例，参考

[testChildServer](https://github.com/zhuzhonghua/Episode/blob/master/test/testChildServer.js)

[testMasterServer](https://github.com/zhuzhonghua/Episode/blob/master/test/testMasterServer.js)




