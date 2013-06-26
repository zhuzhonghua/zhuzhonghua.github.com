---
layout: post
title : 豆瓣FM Hunter 更新
category : essays
tags : [essays]
---
{% include JB/setup %}

前两天写的文章，[http://www.zhuzhonghua.com/essays/2013/06/22/doubanfmhunter/](http://www.zhuzhonghua.com/essays/2013/06/22/doubanfmhunter/)。
但是后来在新浪上搭建的服务器不能用了，于是努力实现了本地下载。  


首先参考了别人的代码，把xpi文件(其实就是zip文件)打开，阅读里面下载相关的代码。  


1.点击下载时，弹出对话框

	function getFilePicker(path)
	{
	    var fp = Cc["@mozilla.org/filepicker;1"].getService(Ci.nsIFilePicker)
	    fp.init(win, 'Save Douban Music', fp.modeSave)
		fp.defaultExtension = 'mp3';
		fp.defaultString = path;
		fp.appendFilter('MP3', '*.mp3');
		var rv = fp.show()
		if (rv == fp.returnOK || rv == fp.returnReplace) {
			return fp
		}
	    else{
	        return null;
	    }
	}
这里需要注意的一个地方就是Cc和Ci分别代表了Components.classes和Components.interfaces。
记得前面var {Cc, Ci} = require("chrome")    

参数path其实就是文件名，我在这里使用了标题title。  

2.接下来就要写入文件了。

    var fp = getFilePicker(name);
    if(!fp) return;
    
    var file = fp.file;
    
    var ios = Cc["@mozilla.org/network/io-service;1"].getService(Ci.nsIIOService);
    var url = ios.newURI(u, null, null);
    var channel = ios.newChannelFromURI(url);
    var observer = {
      onStreamComplete : function(aLoader, aContext, aStatus, aLength, aResult) {
          	var stream = Cc["@mozilla.org/network/safe-file-output-stream;1"].createInstance(Ci.nsIFileOutputStream);
              stream.init(file, -1, -1, 0);
              var bstream = Cc["@mozilla.org/binaryoutputstream;1"].createInstance(Ci.nsIBinaryOutputStream);

              bstream.setOutputStream(stream);    
              bstream.writeByteArray(aResult, aLength);
              if (stream instanceof Ci.nsISafeOutputStream) {
                 stream.finish();
              }else {
                  stream.close();
              }
          }
    }
    var sl = Cc["@mozilla.org/network/stream-loader;1"].createInstance(Ci.nsIStreamLoader);
    sl.init(observer);
    channel.asyncOpen(sl, channel);

其实我也是在网上照抄一些代码，大体懂得一些含义，异步加载url地址，获得数据之后，写入文件。  


等以后需要了，再进行更新吧。目前够用了。  


Just For Fun