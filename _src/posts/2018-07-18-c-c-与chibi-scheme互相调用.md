    Title: c/c++与chibi-scheme互相调用
    Date: 2018-07-18T07:56:25
    Tags: Lisp,scheme,chibi-scheme

测试环境是mac

chibi-scheme是scheme/lisp的一种实现，适合作为脚本语言嵌入到c/c++程序中

[chibi-scheme官方文档](http://synthcode.com/scheme/chibi/#h2_EmbeddinginC)写的很清楚，只是没有实例
[外国友人写的相关教程](http://klamp.works/2016/02/05/chibi-scheme-1.html)结合实例，写的很清楚

[代码库地址](https://github.com/zhuzhonghua/chibi-scheme.git) 里的c2scheme目录是示例代码

大体分成三步

1. 加载scheme文件  
2. 调用scheme函数代码，传递参数，接收返回值  
3. scheme代码调用c/c++代码，传递参数，接收返回值


callme.scm

	(import (chibi))
	(define (say-hello)
	  (display "hello world from scheme")
	  (newline))


#加载scheme文件

	obj1 = sexp_c_string(ctx, "callme.scm", -1);
	sexp_load(ctx, obj1, NULL);

#调用scheme函数代码，传递参数，接收返回值

调用scheme函数代码

	sexp_eval_string(ctx, "(say-hello)", -1, NULL);

传递参数

	arg_val = sexp_make_fixnum(4);
  arg_sym = sexp_intern(ctx, "arg", -1);
	sexp_env_define(ctx, sexp_context_env(ctx), arg_sym, arg_val);
	
	file_path = sexp_c_string(ctx, "callme.scm", -1);
  sexp_load(ctx, file_path, NULL);

接收返回值

	ret = sexp_eval_string(ctx, "(square arg)", -1, NULL);
	int result = -1;
	if (sexp_integerp(ret)) {
	  result = sexp_unbox_fixnum(ret);
	}

callme.scm

	(define (square a)
	  (* a a))


#scheme代码调用c/c++代码，传递参数，接收返回值

callme.scm

	(define (say-hello)
		(display "hello world from scheme")
		(newline)
		(hello-user "chibi-scheme"))

