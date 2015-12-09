---
layout: post
title : 在egret中使用protobuf作为通信协议
category : essays
tags : [egret protobuf import]
---
{% include JB/setup %}


---
egret是什么  
[http://www.egret.com/](http://www.egret.com/)

[http://bbs.egret-labs.org/thread-5800-1-1.html](http://bbs.egret-labs.org/thread-5800-1-1.html) 
[http://bbs.egret-labs.org/thread-915-1-1.html](http://bbs.egret-labs.org/thread-915-1-1.html) 
[http://bbs.egret-labs.org/thread-6260-1-1.html](http://bbs.egret-labs.org/thread-6260-1-1.html) 

使用websocket，再配合Protobuf，通信消息的大小可以节省很多。 
上面找了很多例子，在项目里使用，无法编译通过 
egret build -e 总是提示 no such file or directory xxxx/bin/protobuf	

最后尝试了各种关键词，baidu, google各种搜索，发现了这个       
[http://edn.egret.com/cn/index.php?g=&m=article&a=index&id=172](http://edn.egret.com/cn/index.php?g=&m=article&a=index&id=172)  

这里是关于第三方的配置，使用。  
原来是2.5.x之后，第三方库的使用方式变化了。 

在前面的几个例子也可以看到，主要使用dcodeIO/protobuf中的几个文件， Long.js, ByteBufferAB.js, Protobuf.js 再配合一个Protobuf.d.ts，就可以自己编译一个protobuf库，用在egret项目中。 

提供一个做好的protobuf库  
[protobuf-egret.zip](/assets/protobuf.zip)  

解压之后不要和egret项目放一起，单独放在一个比如third-party的目录中。  
在egret项目的egretProperties.json的modules中添加  

		{
			"name":"protobuf" , 
			"path":"xxx/protobuf"
		} 

这里的path就是刚才解压的Protobuf目录  

建立proto文件 

test.proto 

	message OP_TEST{
		optional int32 type=1;
		optional string key=2;
	}
	
把test.proto放在default.res.json中，并命名 message_proto 

	var message = dcodeIO.ProtoBuf.loadProto(RES.getRes("message_proto"));  

取到对应的proto  

	var msgTemplate = message.build("OP_TEST");
	
找到自己写的消息结构  

	var inst = new msgTemplate({type:123,key:"temp"});  

构造好了消息实例  

	var msgBody: egret.ByteArray = new egret.ByteArray(inst.toArrayBuffer());  

转化成可以在egret.WebSocket中发送的bytearray  

解析消息  

	var packet = proto.decode(msgBody.buffer);  
	console.log(packet);  

------  

在Proto中使用import时，总是出错，还没找到方法。  
不过当前至少可用了。把所有的消息放在了一个proto文件中
