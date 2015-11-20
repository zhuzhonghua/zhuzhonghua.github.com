---
layout: post
title : GNash的DefineBitsTag.cpp中的StreamAdapter类
category : essays
tags : [gnash definebitstag jpeg streamadapter]
---
{% include JB/setup %}


---
gnash/libcore/swf/DefineBitsTag.cpp 中定义了一个StreamAdapter类    

这个类最重要的一个方法是read，定义时会设置最大长度    
读取的长度超过了最大长度，就截掉    
在读取image data 时非常有用    

在使用libjpeg读image data时，并没有提供固定长度，而是提供了一个buffer，不知道终止位置在哪    
通过StreamAdapter方法，避免读取越界，影响后续tag的读取，甚至宕机    

readDefineBitsJpeg2也就是tagtype=21时，通过StreamAdapter::getFile方法生成IOChannel，最大长度设置为tag的结束位置  
调用Input::readImageData方法读取数据   
根据type为JPEG类型，创建JpegInput  
readScanline时，会调用在JpegInput的构造方法里通过rw_source_IOChannel::setup设置的fill_input_buffer方法    

在fill_input_buffer中，IO_BUF_SIZE指示了buffer大小    

于是读取jpeg数据时，就不会读越界，尤其当swf时compress模式时
