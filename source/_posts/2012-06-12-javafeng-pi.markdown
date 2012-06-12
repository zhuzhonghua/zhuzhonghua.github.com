---
layout: post
title: "Java封皮"
date: 2012-06-12 23:00
comments: true
categories: 
---

<a href="http://www.paulgrahamcn.com/article/javacover">Java封皮</a>

译者：<a href="http://www.sophiazheng.com">郑思</a> 转载请注明

这篇文章脱胎于我跟几个程序员的聊天：为什么Java总让人感觉奇怪？先声明，本文绝无批判Java之意，这可是一篇关于黑客嗅觉的案例研究。

 

日积月累，黑客们逐渐进化出神秘而敏锐的嗅觉，他们能闻出愉悦身心的好技术，也能辨出乏味技术的臭味儿。

我想，试着思考为什么Java散发出一种令人不快的气味，并将述诸文字，或许会很有趣。

 

一些读者觉得我的尝试挺有趣，毕竟，我写的东西之前还没人写过。还有一些人建议，最好还是别去碰我不懂的领域，不然我会引火烧身。因此，让我再次澄清一下，下文的主题并不是Java（天哪，这门语言我竟然从来都没码过！），而是黑客的嗅觉（对此我深思熟虑，想了不少）。

 

格言有云：“不可从封面妄自揣测书的内容。”这句话语出有典。有段时期，为了满足不同书商的不同品位，书籍以定制的平装硬皮封面装订。在那些日子里，你无法从封面上辨别一本书。但是，出版业大大地发展了，现今出版商正致力于让你一眼就能从门面上看出书里讲了些什么。

我在书店花了很多时间，我觉得，自己似乎明白了出版商想要通过一本书告诉我的。我没花在书店的时间，大部分都花在电脑前。我同意，在某种程度上，我学会了通过技术的门面去辨别技术的含量。

 

这或许仅仅是运气，但是我还是成功地从不少技术手中给自己赢得了时间，那些技术已被证明不过是个臭蛋。

 

到目前为止，Java对我来说看上去都挺像个臭蛋。

 

我的程序没有一个是用Java写成的，从来没有看过Java的参考书。尽管如此，我还是有一种预感，那就是，Java不会成为一门非常成功的语言。未来或许将揭示我的愚蠢；企图预测科技的走向是门危险的生意。但是，我愿意写下为什么我不喜欢Java的门面，当做时间胶囊，交由未来检验。

 

1、Java炒作过度。实际标准却没有得到提升。没有人不得不推动C语言，或Unix 又或HTML。一个真正的标准趋势在大多数人听到它之前，往往已经建立了。在黑客第六感的扫描仪里，Perl与Java一样伟大，甚至要伟大的多，仅仅是凭借它自身的语言优势。

 

2、Java的目标很低。Gosling明确地说，Java为使用C语言的程序员而设计，设计的并不难。设计该门语言的目标是，让它成为另一个C++。C从更高级的语言中提取了一些概念。就像Sitcom的创建者，又或垃圾食品、三天两夜游的创建者那样，Java语言的创建者有意识地为一群不如他们聪明的人设计了一个产品。从历史上来说，那些为其他人使用的语言下场很惨淡：cobol、PL/I, Pascal, Ada,C++。优秀的语言都为自己的创建器所设计：比如C、Perl，Smalltalk，Lisp。

 

3、Java别有有心。有人曾经说过，如果人们只在有话要说的时候，才写书，而不是在他们想写的时候写书，世界将会美好得多。诚如此言，我们在大多数时间里听到Java，并不是因为它是一门编程语言，而是因为它是Sun打破微软统治地位的战略计划的一部分。

 

4、没有程序员喜欢Java。C语言、Perl、Python、Smalltalk还有Lisp的程序员都喜欢自己所使用的语言。我还没听人说过他们发自内心热爱Java。

【2001年就有Python了？我勒个去。无知家庭主妇再次被震撼了。】

5、人们被迫使用Java。我认识一些使用Java的人，他们使用Java出于被迫。要么是用Java写的程序容易融到资金，要么是客户希望他们用Java编写程序，要么是管理人员让他们使用Java。这些人都是聪明人。假如某项技术很不错，他们绝对会二话不说，主动采用。

 

6、设计Java的人太多了。最好的编程语言通常都是由开发的小团体设计出来的。而Java似乎是由某个委员会运行管理的。如果时间证明Java是门不错的语言，这将是历史上头一个由委员会设计出优秀语言。

 

7、Java带着官僚主义的习性。凭我对Java浅薄的了解，该门语言在执行时有许多协议。一门好的语言真的不需如此。好语言让你能做出你想要的东西，还不会挡你的路。

 

8、Java是个伪君子。 现在，Sun假设Java就是基层，努力让Java看上去像Perl或Python，是一门开源语言。实际上，它在巨型公司的控制之下。因此，这门语言的单调乏味与一家大公司如出一辙。

9、Java为大型企业而设计。大型组织与黑客的目标并不相同。大型企业需要适合于平庸程序员组成的大型团队所使用的语言。这样的语言拥有速度限制，防止傻瓜损害他们的进度，就好像跟在U型牵引卡车后面。而黑客们只想要权力。历史上，为大型机构设计的语言（PL/I, Ada)输给了黑客们所使用的语言（C，perl). 原因: 今日的年轻黑客就是明日的CTO。

 

10、喜欢Java的人有问题。基本上，我所钦佩的程序员没有一个人喜欢Java。谁喜欢Java？那些身穿西服的商务人士，他们甚至不懂一门语言与另一门语言的差别，但他们了解，他们持续不断地听到Java的名字；大公司的程序员，他们看到有比C + +更好语言就会惊讶；还有叽叽喳喳的本科生，他们随时准备喜欢上一门帮助他们得到工作的语言（考试究竟会考些什么？）。这些人的意见跟风摇摆。

11、Java的父公司已经风烛残年。Sun的商业模式在两方面都遭到了削弱。与台式机使用同一类型的英特尔处理器很便宜，而且运行速度也足够当服务器了。作为Solaris服务器操作系统，FreeBSD似乎是个不错的开放系统。Sun的广告暗示，你需要工业强度的Sun服务器。如果广告是真的，那么雅虎该第一个买下Sun。但我在那里工作的时候，这里服务器不过是使用了FreeBSD的英特尔盒。Sun的未来大有问题。如果Sun的运行陷入困境，Java也会被拖下水。

 

12、国防部喜欢Java。国防部鼓励开发人员使用Java。这在我看来，这糟的不能再糟了。国防部保卫国家的工作干得很不赖（虽然价格不菲），但他们喜欢计划、程序和协议。他们的文化是黑客文化的对立面; 软件的问题上，他们往往会赌错。上一次，国防部最喜欢的编程语言是Ada。

记住，本文对Java的内容并未置评，但我批评了它的门面。我对Java了解不多，因此谈不上喜欢或不喜欢。上文讲述了我丝毫不渴望了解它的原因。

本文可能看起来稍显傲慢，因为我在了解语言之前，就甚至试图评论它。但是，这是所有程序员都必须做的事情。我们有太多的技术，不可能每样都学习。你要学会花时间从外在的迹象判断，这一门技巧是否值得你花时间学习。我也同样傲慢地放弃了COBOL，ADA，Visual Basic，IBM AS400，VRML，ISO 9000，SET协议，VMS系统，Novell NetWare与CORBA等等。它们只是看起来很可疑。

我对Java的判断可能并不正确。它或许是一门由某个大公司扳倒竞争对手的语言；是由委员会设计、符合“主流”口味、捧到天上的语言；是一门被国防部宠爱的语言，同时仍然是一个整洁，优美，功能强大的语言，让我发自内心地喜欢用它编程。确有此种可能。但看上去可能甚微。
=============================
Java's Cover April 2001

This essay developed out of conversations I've had with several other programmers about why Java smelled suspicious. It's not a critique of Java! It is a case study of hacker's radar.

Over time, hackers develop a nose for good (and bad) technology. I thought it might be interesting to try and write down what made Java seem suspect to me.

Some people who've read this think it's an interesting attempt to write about something that hasn't been written about before. Others say I will get in trouble for appearing to be writing about things I don't understand. So, just in case it does any good, let me clarify that I'm not writing here about Java (which I have never used) but about hacker's radar (which I have thought about a lot).



The aphorism "you can't tell a book by its cover" originated in the times when books were sold in plain cardboard covers, to be bound by each purchaser according to his own taste. In those days, you couldn't tell a book by its cover. But publishing has advanced since then: present-day publishers work hard to make the cover something you can tell a book by.

I spend a lot of time in bookshops and I feel as if I have by now learned to understand everything publishers mean to tell me about a book, and perhaps a bit more. The time I haven't spent in bookshops I've spent mostly in front of computers, and I feel as if I've learned, to some degree, to judge technology by its cover as well. It may be just luck, but I've saved myself from a few technologies that turned out to be real stinkers.

So far, Java seems like a stinker to me. I've never written a Java program, never more than glanced over reference books about it, but I have a hunch that it won't be a very successful language. I may turn out to be mistaken; making predictions about technology is a dangerous business. But for what it's worth, as a sort of time capsule, here's why I don't like the look of Java:

1. It has been so energetically hyped. Real standards don't have to be promoted. No one had to promote C, or Unix, or HTML. A real standard tends to be already established by the time most people hear about it. On the hacker radar screen, Perl is as big as Java, or bigger, just on the strength of its own merits.

2. It's aimed low. In the original Java white paper, Gosling explicitly says Java was designed not to be too difficult for programmers used to C. It was designed to be another C++: C plus a few ideas taken from more advanced languages. Like the creators of sitcoms or junk food or package tours, Java's designers were consciously designing a product for people not as smart as them. Historically, languages designed for other people to use have been bad: Cobol, PL/I, Pascal, Ada, C++. The good languages have been those that were designed for their own creators: C, Perl, Smalltalk, Lisp.

3. It has ulterior motives. Someone once said that the world would be a better place if people only wrote books because they had something to say, rather than because they wanted to write a book. Likewise, the reason we hear about Java all the time is not because it has something to say about programming languages. We hear about Java as part of a plan by Sun to undermine Microsoft.

4. No one loves it. C, Perl, Python, Smalltalk, and Lisp programmers love their languages. I've never heard anyone say that they loved Java.

5. People are forced to use it. A lot of the people I know using Java are using it because they feel they have to. Either it's something they felt they had to do to get funded, or something they thought customers would want, or something they were told to do by management. These are smart people; if the technology was good, they'd have used it voluntarily.

6. It has too many cooks. The best programming languages have been developed by small groups. Java seems to be run by a committee. If it turns out to be a good language, it will be the first time in history that a committee has designed a good language.

7. It's bureaucratic. From what little I know about Java, there seem to be a lot of protocols for doing things. Really good languages aren't like that. They let you do what you want and get out of the way.

8. It's pseudo-hip. Sun now pretends that Java is a grassroots, open-source language effort like Perl or Python. This one just happens to be controlled by a giant company. So the language is likely to have the same drab clunkiness as anything else that comes out of a big company.

9. It's designed for large organizations. Large organizations have different aims from hackers. They want languages that are (believed to be) suitable for use by large teams of mediocre programmers-- languages with features that, like the speed limiters in U-Haul trucks, prevent fools from doing too much damage. Hackers don't like a language that talks down to them. Hackers just want power. Historically, languages designed for large organizations (PL/I, Ada) have lost, while hacker languages (C, Perl) have won. The reason: today's teenage hacker is tomorrow's CTO.

10. The wrong people like it. The programmers I admire most are not, on the whole, captivated by Java. Who does like Java? Suits, who don't know one language from another, but know that they keep hearing about Java in the press; programmers at big companies, who are amazed to find that there is something even better than C++; and plug-and-chug undergrads, who are ready to like anything that might get them a job (will this be on the test?). These people's opinions change with every wind.

11. Its daddy is in a pinch. Sun's business model is being undermined on two fronts. Cheap Intel processors, of the same type used in desktop machines, are now more than fast enough for servers. And FreeBSD seems to be at least as good an OS for servers as Solaris. Sun's advertising implies that you need Sun servers for industrial strength applications. If this were true, Yahoo would be first in line to buy Suns; but when I worked there, the servers were all Intel boxes running FreeBSD. This bodes ill for Sun's future. If Sun runs into trouble, they could drag Java down with them.

12. The DoD likes it. The Defense Department is encouraging developers to use Java. This seems to me the most damning sign of all. The Defense Department does a fine (though expensive) job of defending the country, but they love plans and procedures and protocols. Their culture is the opposite of hacker culture; on questions of software they will tend to bet wrong. The last time the DoD really liked a programming language, it was Ada.

Bear in mind, this is not a critique of Java, but a critique of its cover. I don't know Java well enough to like it or dislike it. This is just an explanation of why I don't find that I'm eager to learn it.

It may seem cavalier to dismiss a language before you've even tried writing programs in it. But this is something all programmers have to do. There are too many technologies out there to learn them all. You have to learn to judge by outward signs which will be worth your time. I have likewise cavalierly dismissed Cobol, Ada, Visual Basic, the IBM AS400, VRML, ISO 9000, the SET protocol, VMS, Novell Netware, and CORBA, among others. They just smelled wrong.

It could be that in Java's case I'm mistaken. It could be that a language promoted by one big company to undermine another, designed by a committee for a "mainstream" audience, hyped to the skies, and beloved of the DoD, happens nonetheless to be a clean, beautiful, powerful language that I would love programming in. It could be, but it seems very unlikely.

