---
layout: post
title: "as3绕任意点旋转"
date: 2012-06-12 15:42
comments: true
categories: 
---

OK， 我们先写一段代码
	var g:Graphics = graphics;

	var startX:Number = 100;
	var startY:Number = 100;

	var originX:Number = 125;
	var originY:Number = 125;

	g.beginFill(0xff0000);
	g.drawRect(startX, startY, 300, 50);
	g.endFill();

	var shape:Shape = new Shape();
	shape.x = startX;
	shape.y = startY;

	shape.graphics.beginFill(0x000000);
	shape.graphics.drawRect(0, 0, 300, 50);
	shape.graphics.endFill();

	addChild(shape);

解释一下的话，就是以100,100为点画一个矩形，300, 50.
先用红色的画，再用黑色的画，之后我们要让shape绕着125,125点自转。

于是看起来的效果就是，红色的矩形作为标准，不动。
黑色的矩形绕着125,125自转。

开始转了

	var angle:Number = 10;
	var raidan:Number = angle * (Math.PI/180);
	var arr:Array = [Math.cos(radian), -Math.sin(radian), Math.sin(radian), Math.cos(radian)];

	var timer:Timer = new Timer(1);

	timer.addEventListener(TimerEvent.TIMER, function(e:Event):void
	{
		shape.rotation += angle;

		var x1:Number = shape.x - originX;
		var y1:Number = shape.y - originY;

		var x:Number = arr[0] * x1 + arr[1] * y1;
		var y:Number = arr[2] * x1 + arr[3] * y1;

		shape.x = x + originX;
		shape.y = y + originY;
	});

	timer.start()

解释一下：
使用shape.rotation.rotation += angle
来旋转图形

但是这是围绕它本身的0,0点，也就是100, 100点旋转的，我们是要围绕125,125点旋转

其实，不管围绕哪一点旋转，转的角度都是一样，只是位移不一样，也就是shape的x y 坐标不同。
于是我们只需要移动x, y 坐标就行了。

既然我们是期望围绕125,125旋转，那么100,100点也得围绕125,125旋转，于是计算出了100,100点围绕125,125点旋转后的点
位移过去OK了。