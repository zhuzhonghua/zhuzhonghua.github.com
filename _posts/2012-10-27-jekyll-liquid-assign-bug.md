---
layout: post
title : 使用jekyll时，发现liquid的一个小问题
category : essays
tags : [essays]
---
{% include JB/setup %}

想实现的效果是正序排列，而不是默认的逆序排列。  

使用  

    site.posts | sort:"date"   

能够达到效果。

但是使用   

    assign pages_list = site.posts | sort:"date"   

就不行。  


在github的liquid项目里也提了issue, [https://github.com/Shopify/liquid/issues/154](https://github.com/Shopify/liquid/issues/154)   

有人回答说，把空格去掉，也就是    

    assign pages_list = site.posts|sort:"date"   

但是经过测试，还是不行。   

我使用的是jekyll-bootstrap，从github上clone下来的。   

目前，我使用的是如下方式:      

    for item in site.posts reversed     
    endfor    


