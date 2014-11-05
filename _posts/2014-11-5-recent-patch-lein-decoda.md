---
layout: post
title : 最近做的几个patch，关于decoda和lein
category : essays
tags : [essays decoda lein clojure lua 调试]
---
{% include JB/setup %}

---

最近需要用写Lua代码，需要一个方便的调试工具。
网上搜索了一下，有人自己写的lua调试器，还有decoda，甚至还有收费的lua调试器。

那个收费的lua调试器太难用了，各种BUG。
于是准备试用decoda。

decoda是开源的，试用效果不错，就是每次需要手动填一些参数。
于是修改了它的源码，把这些参数保存在一个默认的default.xml中。

顺便提一下，我没有用decoda来写代码，只是用它调试。

[patch1](https://github.com/zhuzhonghua/decoda/commit/c3b12fe32651baecd1d4da7cd7e8f0276594a2fc#diff-25d902c24283ab8cfbac54dfa101ad31)

---
最近在学习clojure
一直使用的都是

    java -cp clojure-1.6.0.jar clojure.main
    
或者

    java -cp clojure-1.6.0.jar clojure.main test.clj
    
这两种方式在测试 
看到别人使用lein，发现它没有运行单个普通文件，类似lein repl的一个功能，于是做了一个patch

[patch2](https://github.com/zhuzhonghua/leiningen/commit/3a66434bdd72c3af34112b0ea632f9293f33285d)

