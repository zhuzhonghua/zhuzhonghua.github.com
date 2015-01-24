---
layout: post
title : luasocket简单使用入门
category : essays
tags : [lua luasocket cocos2d-x quick-cocos2d-x socket]
---
{% include JB/setup %}


---
使用luasocket可以简单的完成游戏中对于网络的需求
简单，小巧，不用自己再去写一遍c++的socket层，对于懒人，很适用

[https://gist.github.com/zhuzhonghua/653de49ab24f5915e12d](https://gist.github.com/zhuzhonghua/653de49ab24f5915e12d)

创建socket，设置参数，尤其是timeout，这样可以避免阻塞
    
    local sockInst = socket.tcp();  
    sockInst:settimeout(0);
    sockInst:setoption('keepalive', true)
    
--- 
不再使用时需要close

    sockInst:close()
    
---
连接对应的ip和port，根据代码可以看到两个提示来表明连接已经成功

    local __succ, __status = sockInst:connect("127.0.0.1", 40234)
	
	if __succ == 1 or __status == "already connected" then	
    	print('tryconnect socket connected')
    	onConnected()
	else
		print('====================================socket connected fail try again');
		connStatus = SOCK_STATUS.CONNECTING
	end  
	
---
在update中调用select，来检测是否有数据到达，或者是否可以发送数据了
    
	if connStatus == SOCK_STATUS.CONNECTING or connStatus == SOCK_STATUS.CONNECTED then
		-- body
		local arr = {sockInst}
		local r, s, e = socket.select(arr, arr, 0)
 
		if r and #r >= 1 and r[1] == sockInst then  
		    local recvData, recvError, recvParticialData = sockInst:receive(1024);  
		  
		    if recvError == 'closed' then  
		    	print('recv socket disconnect ')
		        ClientSocket.onDisconnect()
		        return;  
		    end  
 
		    if recvData and #recvData > 0 then  
		    	print('recvdata len='..#recvData)
		        recvBuffer = recvBuffer .. recvData 
		    elseif recvParticialData and #recvParticialData > 0 then  
		    	print('recvparticialdata len='..#recvParticialData)
		        recvBuffer = recvBuffer .. recvParticialData
		    end
		end  
 
		if s and #s >= 1 and s[1] == sockInst then  
		    if connStatus==SOCK_STATUS.CONNECTING then  
		        -- 执行到此，说明连接已经成功了  
		        --print('select send socket connect success');
		        --onConnected()
		    end  
		  
		    -- 可以调用send
		    if connStatus == SOCK_STATUS.CONNECTED then
		    	handleOutData()
		    end		
		end
	end	
	
---
1. 判断连接是否成功，看quick-cocos2d-x官方的例子上，这个地方可以判断连接成功，但实际测的效果来看，ios，服务器未开，也可以进入这段代码，所以干脆注释掉，只是用connect中的返回值来判别，连接是否成功

        if connStatus==SOCK_STATUS.CONNECTING then  
		        -- 执行到此，说明连接已经成功了  
		        --print('select send socket connect success');
		        --onConnected()
		end  
		
2. 强关服务器，luasocket无法监测到连接已经断开，所以需要在heartbeat中自己判断连接是否有效，不停发送heartbeat，然后根据上次接收消息的时间，来判断是否连接还有效

3. 当监测到连接已经断开(比如地铁信号不好)，需要再次连接服务器时，需要创建新的socket，而不能继续使用原来的socket调用connect方法
