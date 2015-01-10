---
layout: post
title : 手机游戏中附近的人的简易做法
category : essays
tags : [near game mobile]
---
{% include JB/setup %}


---
随着手机游戏的推广，越来越多的游戏中嵌入了附近的人的功能
只不过这个功能在游戏中和在社交APP中并不同，要更简单，只是一个互动的社交玩法，没有社交APP中那么精确，而且附近的人应该也没有那么多

使用MongoDB的Geospatial indexing功能，可以简单的完成这个功能

[简单示例](https://gist.github.com/zhuzhonghua/e553d67e018da7bb875a)

这里使用http提供服务，也可以集成到自己的项目中，通过别的方式提供此功能

    $maxDistance: 2000*20
    
可以根据自己的需要限定范围，单位是米    

    updateLoc
    
如果需要存储其他的数据，修改updateLoc相关的代码

参考
[http://docs.mongodb.org/manual/reference/operator/query/nearSphere/#op._S_nearSphere](http://docs.mongodb.org/manual/reference/operator/query/nearSphere/#op._S_nearSphere)
[http://docs.mongodb.org/manual/tutorial/build-a-2dsphere-index/](http://docs.mongodb.org/manual/tutorial/build-a-2dsphere-index/)
[http://docs.mongodb.org/manual/core/2dsphere/](http://docs.mongodb.org/manual/core/2dsphere/)
[http://docs.mongodb.org/manual/tutorial/build-a-2dsphere-index/](http://docs.mongodb.org/manual/tutorial/build-a-2dsphere-index/)
