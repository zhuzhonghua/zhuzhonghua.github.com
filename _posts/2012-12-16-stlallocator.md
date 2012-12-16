---
layout: post
title : 关于STL的内存管理器
category : essays
tags : [essays]
---
{% include JB/setup %}


使用STL的过程中，个人用的最多的数据结构就是


1. std::map, 主要用于查找
2. std::list, 主要用于顺序处理
3. std::vector，固定长度，不怎么做删除操作


目前情况下，这三种数据结构可以满足我的大多数需求。  


关于hashmap我想要说的是，我对于各个平台，hash算法如何实现的没有研究。
但是hash算法再好，也只是对于一些key区间，hash之后的区间能够更紧凑一些。还是会有很多空隙的。  


而且使用hashmap主要是为了常量时间复杂度查找。但是我不知道hash算法需要几个操作。  


如果这样的话，我宁愿使用map，他的二分查找平衡树（这里是红黑树）也很快，1000个元素，最多需要10次比较操作。
而且hash算法的结果区间如果变大，不知道是realloc呢？还是使用其它方法。


STL的内存管理器可以提供更大的灵活性，让程序员控制STL里数据结构的内存分配和释放。
这样可以达到替换系统默认new, delete的操作，减少内存碎片，而且还可以提高效率。  
这里推荐nedmalloc，一个内存分配器，一个人维护二十年，多么专一，多么深度。  


事例代码如下：  


    template<typename _Tp>
    class a_allocator:public std::allocator<_Tp>
    {
    public:
        typedef typename std::allocator<_Tp>::size_type size_type;
        typedef typename std::allocator<_Tp>::difference_type difference_type;
        typedef typename std::allocator<_Tp>::pointer pointer;
        typedef typename std::allocator<_Tp>::const_pointer const_pointer;
        typedef typename std::allocator<_Tp>::reference reference;
        typedef typename std::allocator<_Tp>::const_reference const_reference;
        typedef typename std::allocator<_Tp>::value_type value_type;


> 为了关注我们想关注的分配和释放，这里简单继承系统默认的allocator。
> 当然头文件需要自己包含一下，我这里为了省劲，简单包含了map。


	    // other
	    template<typename _Tp1>
	    struct rebind
	    {
	    	typedef a_allocator<_Tp1> other;
	    };
	    
	    a_allocator(){}
	    a_allocator(const a_allocator &a){}
	    
	    template<typename _Tp1>
	    a_allocator(){}
	    template<typename _Tp1>
	    a_allocator(const a_allocator<_Tp1> &a){}


> 至于为什么必须实现struct rebind，必须使用other，必须实现构造，拷贝构造函数，请自行Google。
> 如果一定要知道，那么可以使用简单暴力有效的自我催眠方法，那就是必须这样写。


	    pointer allocate(size_type __n, const void* =0)
	    {
	    	printf("allocate\n");
	    	return static_cast<_Tp*>(::operator new(__n * sizeof(_Tp)));		
	    }
	    
	    void deallocate(pointer __p, size_type)
	    {
	    	printf("deallocate\n");		
	    	::operator delete(__p);
	    }
	
    };


> 这就是我们千呼万唤始出来的两个函数，分配和释放。
> 为了简便，我直接把系统的实现拷贝过来了，然后加了一句输出证明。
	

> 注意这里的::operator new 并不是我们通常的new，因为它不会调用构造函数。只是作内存分配。
> 然后你就可以使用自己的内存管理器来控制STL容器的内存了。


> 还可以把这两个函数特化一下，来更好的控制内存。   


这里需要提醒几点：


    1. STL只是一个标准，大量的模板使用。各个平台实现不一样。但是接口和行为都是一致的。如果用VS可以直接调试F11查看STL源码。在linux环境下，可以在usr-include-c++-xxx下查看。
    
    
    2. 它跟线程没关系，跟平台也没关系，就把它当作你自己实现的一套大量使用模板的类库。所以如果涉及到多线程问题，要自己搞定。
    
    
    3. 如果这样写，std::list int，还是会分配sizeof(int)大小的内存出来，所以大量的这些操作肯定会产生很多碎片。如果你实现了内存管理器，不管效率还是碎片，it all depends on you.

