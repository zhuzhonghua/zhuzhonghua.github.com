---
layout: post
title: "sicp-parsk"
date: 2012-06-05 17:15
comments: true
categories: 
---

SCIP上的一个习题    
用递归输出PARSK三角，也有叫杨辉三角的    
个人认为，使用递归更简单一些，不过作为练习，就这样了    
上代码    


    (defun parsk(n)
      (if (= n 1)
          (let ((ret (list 1)))
        (echo-list ret)
        ret)
        (let ((resn (list 1))
          (resn-1 (parsk (- n 1))))
          (do ((i 0 (+ i 1)))
          ((> i (- (length resn-1) 2)) 'done)
        (setq resn (append resn (list (+ (nth i resn-1)
                         (nth (+ i 1) resn-1))))))
          (setq resn (append resn (list 1)))
          (echo-list resn)
          resn)))
        
其中ECHO-LIST是个辅助函数，输出LIST    


    (defun echo-list(lst)
      (progn
        (dolist (obj lst)
          (format t "~A " obj))
        (format t "~%")))
