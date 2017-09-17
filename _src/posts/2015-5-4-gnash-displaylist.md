    Title: GNash中的两种displaylist
    Date: 2015-05-04T00:00:00
    Tags: SWF

PlaceObject2Tag对应的tag是SWF.h中的PLACEOBJECT,PLACEOBJECT2,PLACEOBJECT3    
在PlaceObject2Tag.cpp的PlaceObject2Tag::loader中读取完毕后，调用addControlTag，放到Movie中的m_playerlist中

MovieClip.cpp中MovieClip::executeFrameTags通过 _def->getPlaylist(frame) 取出对应的playlist.
循环调用对应的executeState或者executeActions方法

在执行PlaceObject2Tag::executeState时，根据对应的类型会对应修改MovieClip中的_displayList。这是一个Displaylist。
这个playlist对象维护当前可见的所有对象，而每个frame对应的playelist只是维护状态和执行对应的动作。

MovieClip的display会调用DisplayList的display方法

DisplayList的display方法会调用当前可显示对象对应的display方法。
