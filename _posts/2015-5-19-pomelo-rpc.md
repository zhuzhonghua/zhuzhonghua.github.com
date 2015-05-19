---
layout: post
title : Pomelo中的收发消息及RPC
category : essays
tags : [pomelo rpc socket send recv message]
---
{% include JB/setup %}


---
tcp-mailbox.js中的pro.send方法
每次发送消息，自增加id来标记本条消息

	var id = this.curId++;

同时用该id记录一个timeout，和其对应的callback

	this.requests[id] = cb;
	setCbTimeout(this, id, tracer, cb);

如果远程服务器长时间没有回复消息，则由timeout timer主动回调callback

	clearCbTimeout(mailbox, id);
	if(!!mailbox.requests[id]) {
    	delete mailbox.requests[id];
	}
	logger.error('rpc callback timeout, remote server host: %s, port: %s', mailbox.host, mailbox.port);
	utils.invokeCallback(cb, tracer, new Error('rpc callback timeout'));

清理timeout timer的记录，清理callback的记录。

tcp-mailbox.js中的processMsg处理收到的消息
消息里带着对应的id，清理对应id的timeout timer

	clearCbTimeout(mailbox, pkg.id);

根据id找到对应的callback，并进行清理和回调

	var cb = mailbox.requests[pkg.id];
	if(!cb) {
		return;
	}  
	delete mailbox.requests[pkg.id];

	var tracer = new Tracer(mailbox.opts.rpcLogger, mailbox.opts.rpcDebugLog, mailbox.opts.clientId, pkg.source, pkg.resp, pkg.traceId, pkg.seqId);
	var args = [tracer, null];

	pkg.resp.forEach(function(arg){
		args.push(arg);
	});

	cb.apply(null, args);
