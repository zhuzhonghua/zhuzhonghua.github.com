---
layout: post
title : 使用Nodejs实时更新内容
category : essays
tags : [nodejs intime update]
---
{% include JB/setup %}


---
如下代码

	> var a = {}
	undefined
	> a[1] = 2
	2
	> a[2] = 3
	3
	> 
		
定义一个变量a，随意赋一些值

	> var b = a
	undefined
	> a={}
	{}
	> b
	{ '1': 2, '2': 3 }
	> 

接下来，用另一个变量b指向a，然后重置a，会看到b并没有跟着重置 

上面的例子是在toplevel中做的，如果把a和b分别放在两个文件呢

	a.js
	
	var a = {};
	function init()
	{
		a[1] = 2;
	    a[2] = 3;
	}
	
	function clear()
	{
		a = {};
	}
	exports.data = a;
	exports.clear = clear;
	init();
	
在a文件中定义数据变量a，定义一个init函数，填充数据，clear函数，清理变量a
	
	b.js
	
	var b = require("./a.js").data;
	
	console.log(b);
	
	setTimeout(function(){
		var a = require("./a.js");
	    a.clear();
	
	    console.log(b);
	}, 1000);

在b文件中引用a文件，并使用其中的data导出变量，然后过一段时间调用a中的清理函数，可以看到结果，跟上面的toplevel一样

无论用什么方法来清理nodejs中的require缓存机制，都没有用

关键在a={}，这并不是清理。

解决方法

1. 要么不要暴露数据，而是暴露函数，比如去掉a.js文件中exports.data = data 
2. 要么重写clear方法, for(var i in a) { delete a[i]; }