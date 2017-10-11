    Title: Realm Of Racket-猜数字
    Date: 2017-10-11T13:45:01
    Tags: RealmOfRacket,Lisp,Racket

(A First Racket Program)  

猜数字，最简单，最古老的游戏  
游戏虽然简单，但是用上了二分查找的方法  

假设数字是18，大概过程是这样  

	>(guess)  
	50  
	>(smaller)  
	25  
	>(smaller))  
	12  
	>(bigger)  
	18  


三个基本功能，(guess),(smaller),(bigger)  

首先定义上下边界  
用define定义变量  

	>(define lower 1)  
	>lower  
	1  
	>(define upper 100)  
	>upper  
	100  


用define定义函数, 基本形式是  

	(define (function-name argument-name ...)
	  function-body-expression
	  function-body-expression
	...)


guess的定义如下  

	(define (guess)
	  (quotient (+ lower upper) 2))

guess函数没有参数，直接写(guess), 接下来就是取lower和upper的中间值  

smaller的定义  

	(define (smaller)
	  (set! upper (max lower (sub1 (guess))))
	  (guess))


set!读作set-bang，基本形式  

	(set! variable expression)

给variable赋值expression  
smaller先算出新的upper，然后再赋值给upper，然后再展示新的(guess)值  

bigger的定义  

	(define (bigger)
	  (set! lower (min upper (add1 (guess))))
	  (guess))

与smaller类似，计算新的lower，然后展示新的(guess)  

main函数  

	(define (start n m)
	  (set! lower (min n m))
	  (set! upper (max n m))
	  (guess))


跟上面的guess对比，start有参数，(start n m)  
使用min和max，参数的顺序可以任意  

假设我们猜的数字是19  

	>(start 1 30)
	15
	>(bigger))
	23
	>(smaller)
	19)


总结：  
1. 使用define定义变量和函数，函数区分有参数和无参数  
2. 使用set!(set-bang)改变变量的值  



