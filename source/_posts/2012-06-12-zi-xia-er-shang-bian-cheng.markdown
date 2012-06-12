---
layout: post
title: "自下而上编程"
date: 2012-06-12 22:47
comments: true
categories: 
---
<a href="http://www.paulgrahamcn.com/article/programmingbottomup">programming bottom-up</a>

1993
（本文来自On Lisp的前言）

这是一个永恒的编程原则，程序的函数不要太长。如果某些函数超出了屏幕，那么它会变成一团麻，这很容易掩盖错误，就像大城市很容易掩盖罪犯一样。这种软件将难以阅读，难以测试，难以调试。

按照这一原则，大块程序必须分成小块程序，并且，程序越大，越要分。那么如何划分呢？传统的做法是自上而下设计: 程序的目的是做这样七件事情，所以分成七个子程序。第一个子程序要做四件事情，那么再分四个子例程。这样持续下去，直到整个程序拥有正确的粒度，每部分都足够做一些真正的事情，还要足够小，这样把它理解为一个单元。

然而有经验的lisp程序员使用不同的方式来分割程序。就像自上而下设计一样，他们遵循自下而上设计--让语言适应问题。在lisp中，你不仅面向语言编写程序，你还会面向程序构建程序。当你在写程序时，你会想，我希望lisp有这样这样的操作符。于是你就把它写好了。之后你意识到使用另外一个新的操作符会简化另外一部分程序的设计，等等。语言和程序彼此交织在一起。就像敌对国家的边界一样，语言和程序的边界也在不停重画，直到山川、河流为止，也就是你的问题的自然边界。最后，你的程序看起来好像就是为这个问题设计的一样。当语言和程序彼此适合后，你的工作也就结束了，代码干净，短小，高效。

值得强调的是，自下而上设计并不只是一种不同的方式。当你自下而上编程时，你会得到一段不一样的程序。不像单一庞大的程序，你会得到一个更大的语言，拥有更多抽象操作符，使用它写了一段短小的程序。不是门梁，而是拱形。

在一段经典的代码中，一旦你抽象出了账簿部分，剩下的就更短了；你构建的语言越高级，从上到下的路程就更短。这带来还能多优势：

通过让语言做更多的工作，自下而上设计会生成更小更灵活的程序。短小的程序并不需要被分成很多组件，越少的组件意味着程序更加易读和修改。更少的组件意味着，组件之间更少的联系，并且，有更少的错误。工业设计师努力减少机器中移动部件的数量，有经验的lisp程序员使用自下而上设计减少程序的大小和复杂性。

自下而上设计促进代码的可重用性。当你写两个或更多程序时，你在第一个程序中写的很多工具也会在接下来的的程序中很有用。一旦你获得了一个很大的工具集，写新的程序往往只需要写原始的lisp程序的一部分努力。

自下而上设计使得程序更容易阅读，这种抽象类型的实例要求读者理解一个通用的操作；功能抽象的程序要求读者理解子程序的用途。

因为这会迫使你经常查找你代码的模式，自下而上设计帮助你理清程序设计的想法。程序中两个不相干的组件在形式上看起来很像的话，你就会注意到这其中的相似性，然后以更简单的方式重新设计程序。

除了lisp，自下而上设计在其他语言中会达到一定的深度。不管什么时候看到库函数，自下而上就会出现。然而在这方面，lisp会给你更大的能力，提高语言在使用lisp方式中的比重--这样看来，lisp不仅仅是一种不同的编程语言，而是一种完全不同的编程方式。

事实上，这种开发方式，跟适合小团体。同时，它也突破了小团队的局限。在人月神话中，frederick brooks提出，一组程序员的生产力并不会随着人数的增加而线性增加。当团队的人数增加时，个人程序员的生产力就下降了。lisp编程的经历会用更激动人心的方式来叙述：当团队人数减少时，单个程序员的生产力就提升了。相对来说，小团队胜利了，只是因为小。当小团队使用了lisp的技术后，就会彻底完胜。

===============================

programming bottom-up
1993
(This essay is from the introduction to On Lisp. The red text explains the origins of Arc's name.)

It's a long-standing principle of programming style that the functional elements of a program should not be too large. If some component of a program grows beyond the stage where it's readily comprehensible, it becomes a mass of complexity which conceals errors as easily as a big city conceals fugitives. Such software will be hard to read, hard to test, and hard to debug.

In accordance with this principle, a large program must be divided into pieces, and the larger the program, the more it must be divided. How do you divide a program? The traditional approach is called top-down design: you say "the purpose of the program is to do these seven things, so I divide it into seven major subroutines. The first subroutine has to do these four things, so it in turn will have four of its own subroutines," and so on. This process continues until the whole program has the right level of granularity-- each part large enough to do something substantial, but small enough to be understood as a single unit.

Experienced Lisp programmers divide up their programs differently. As well as top-down design, they follow a principle which could be called bottom-up design-- changing the language to suit the problem. In Lisp, you don't just write your program down toward the language, you also build the language up toward your program. As you're writing a program you may think "I wish Lisp had such-and-such an operator." So you go and write it. Afterward you realize that using the new operator would simplify the design of another part of the program, and so on. Language and program evolve together. Like the border between two warring states, the boundary between language and program is drawn and redrawn, until eventually it comes to rest along the mountains and rivers, the natural frontiers of your problem. In the end your program will look as if the language had been designed for it. And when language and program fit one another well, you end up with code which is clear, small, and efficient.

It's worth emphasizing that bottom-up design doesn't mean just writing the same program in a different order. When you work bottom-up, you usually end up with a different program. Instead of a single, monolithic program, you will get a larger language with more abstract operators, and a smaller program written in it. Instead of a lintel, you'll get an arch.

In typical code, once you abstract out the parts which are merely bookkeeping, what's left is much shorter; the higher you build up the language, the less distance you will have to travel from the top down to it. This brings several advantages:

By making the language do more of the work, bottom-up design yields programs which are smaller and more agile. A shorter program doesn't have to be divided into so many components, and fewer components means programs which are easier to read or modify. Fewer components also means fewer connections between components, and thus less chance for errors there. As industrial designers strive to reduce the number of moving parts in a machine, experienced Lisp programmers use bottom-up design to reduce the size and complexity of their programs.
Bottom-up design promotes code re-use. When you write two or more programs, many of the utilities you wrote for the first program will also be useful in the succeeding ones. Once you've acquired a large substrate of utilities, writing a new program can take only a fraction of the effort it would require if you had to start with raw Lisp.

Bottom-up design makes programs easier to read. An instance of this type of abstraction asks the reader to understand a general-purpose operator; an instance of functional abstraction asks the reader to understand a special-purpose subroutine. [1]

Because it causes you always to be on the lookout for patterns in your code, working bottom-up helps to clarify your ideas about the design of your program. If two distant components of a program are similar in form, you'll be led to notice the similarity and perhaps to redesign the program in a simpler way.
Bottom-up design is possible to a certain degree in languages other than Lisp. Whenever you see library functions, bottom-up design is happening. However, Lisp gives you much broader powers in this department, and augmenting the language plays a proportionately larger role in Lisp style-- so much so that Lisp is not just a different language, but a whole different way of programming.

It's true that this style of development is better suited to programs which can be written by small groups. However, at the same time, it extends the limits of what can be done by a small group. In The Mythical Man-Month, Frederick Brooks proposed that the productivity of a group of programmers does not grow linearly with its size. As the size of the group increases, the productivity of individual programmers goes down. The experience of Lisp programming suggests a more cheerful way to phrase this law: as the size of the group decreases, the productivity of individual programmers goes up. A small group wins, relatively speaking, simply because it's smaller. When a small group also takes advantage of the techniques that Lisp makes possible, it can win outright.

[1] "But no one can read the program without understanding all your new utilities." To see why such statements are usually mistaken, see Section 4.8.