---
layout: post
title: "lisp-recursive-iteration"
date: 2012-06-05 12:51
comments: true
categories: 
---

在common lisp 中，尾递归的概念确实很好<br>


废话不多说，先上代码<br>


以著名的斐波那契数列为例：<br>


1，递归版<br>
    (defun fib(n)
        (cond ((= n 0) 0)
              ((= n 1) 1)
              (t (+ (fib (- n 1))
                    (fib (- n 2))))))

2，迭代版（尾递归）<br>
    (defun fib(n)
        (fib-iter 1 0 n))

    (defun fib-iter(a b count)
        (if (= count 0)
            b
            (fib-iter (+ a b) a (- count 1))))

使用第二种方式，可以减少堆栈空间的使用
