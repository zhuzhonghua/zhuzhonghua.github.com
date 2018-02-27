    Title: Realm Of Racket - (big-bang)
    Date: 2018-01-14T14:43:32
    Tags: RealmOfRacket,Lisp,Racket

#(big-bang)  #

上一次的程序是命令行式的，这次写一些GUI(gooey)的程序  
这次使用Racket里自带的2htdp/universe 2htdp/image  

	#lang racket
	(require 2htdp/universe 2htdp/image)
	
	(big-bang 0
		(on-tick add-3-to-state)
		(to-draw draw-a-ufo-onto-an-empty-scene))  
		

big-bang 创造了一个World, World是由状态和视图组成  

	(define (add-3-to-state current-state)
		(+ current-state 3))  
		
add-3-to-state 把当前状态+3，然后返回，big-bang会把add-3-to-state返回的值标记为下一个状态  

	(define (draw-a-ufo-onto-an-empty-scene current-state)
		(place-image IMAGE-of-UFO (/ WIDTH 2) current-state
			(empty-scene WIDTH HEIGHT)))  
			
draw-a-ufo-onto-an-empty-scene 把ufo的图片根据 current-state 绘制到特定位置  
当图片落地时，动画得停下来  

	(define (state-is-300 current-state)
		(>= current-state 300))
		
	(big-bang 0
		(on-tick add-3-to-state)
		(to-draw draw-a-ufo-onto-an-empty-scene)
		(stop-when state-is-300))
		
使用state-is-300和big-bang的stop-when来修改World的状态和视图  


	(big-bang state-expression
		(to-draw draw-function)
		(on-tick tick-function)
		(on-key key-function)
		(stop-when stop-function optional-last-scene))
		
		
big-bang大致长这样，state-expression是WOrld的初始状态，to-draw是把当前状态绘制到屏幕上，on-tick就是每tick调用的函数，on-key是当有输入操作，如键盘鼠标或者其他输入设备，调用的函数，在key-function里可以对状态进行修改，stop-when就是想让世界停下来的一个函数判断  


该示例的完整源码在 {Racket的安装目录}/share/pkgs/realm/chapter5/ufo-source.rkt里  


#写一个GUI版本的猜数字游戏  #

好的数据结构会让程序有很大的不同  

	struct interval (small big))
	
代表猜数字的上下限  

	(define HELP-TEXT
		(text " for larger numbers, for smaller ones"
			TEXT-SIZE
			"blue"))
	(define HELP-TEXT2
		(text "Press = when your number is guessed; q to quit."
			TEXT-SIZE
			"blue"))
	(define COLOR "red")
	
游戏指令  

	(define MT-SC
		(place-image/align
			HELP-TEXT TEXT-X TEXT-UPPER-Y "left" "top"
			(place-image/align
				HELP-TEXT2 TEXT-X TEXT-LOWER-Y "left" "bottom"
				(empty-scene WIDTH HEIGHT))))
				
定义常量，作为游戏的背景  

	(define (start lower upper)
		(big-bang (interval lower upper)
			(on-key deal-with-guess)
			(to-draw render)
			(stop-when single? render-last-scene)))
			
这个可以作为Main函数  

	(define (deal-with-guess w key)
		(cond [(key=? key "up") (bigger w)]
			[(key=? key "down") (smaller w)]
			[(key=? key "q") (stop-with w)]
			[(key=? key "=") (stop-with w)]
			[else w]))  
			
deal-with-guess 根据不同的key返回不同的状态  


	(define (smaller w)
		(interval (interval-small w)
			(max (interval-small w) (sub1 (guess w)))))  
			
	(define (bigger w)
		(interval (min (interval-big w) (add1 (guess w)))
			(interval-big w)))  
			
smaller和bigger都是创建了新的interval结构体  

	(define (guess w)
		(quotient (+ (interval-small w) (interval-big w)) 2))  
		
guess函数取结构的上下限，加起来然后/2  

	(define (render w)
		(overlay (text (number->string (guess w)) SIZE COLOR) MT-SC))  
		
render把猜的数字展示到MT-SC定义的背景上  

	(define (render-last-scene w)
		(overlay (text "End" SIZE COLOR) MT-SC))
		
当游戏结束调用的函数  

	(define (single? w)
		(= (interval-small w) (interval-big w)))  
		
single?比较上下限，决定是否结束游戏  

猜数字的GUI版本的完整代码在 {Racket的安装目录}/share/pkgs/realm/chapter5/source.rkt  

