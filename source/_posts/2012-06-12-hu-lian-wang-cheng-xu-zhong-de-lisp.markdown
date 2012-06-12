---
layout: post
title: "互联网程序中的lisp"
date: 2012-06-12 22:58
comments: true
categories: 
---

<a href="http://www.paulgrahamcn.com/article/lispinweb">互联网程序中的lisp</a>

互联网程序中的lisp

摘录自2001年4月在剑桥，MA的BBNlabs所作演讲
理想的语言

一个使用lisp写互联网程序的原因是：你“能”使用lisp。 当你写下一个只会在个人服务器上运行的程序时，你想用什么语言，就用什么语言。

在很长一段时间里，并没有太多语言可供撰写应用程序的程序员选择。编写应用程序意味着编写运行在桌面上的软件。 有一种固执的偏见认为，桌面软件必须使用与操作系统相同的语言编写。 例如，十年前，所有的应用程序都是用C语言撰写的。

现在，基于Web的应用使得上述情况发生了改变。只要你控制得了服务器， 你就可以用任何你想用的语言来写软件。这对你来说心安理得：因为你同时拥有操作系统和编译器的源代码。 如果语言与操作系统之间出了问题，你可以自己修复。

然而，新的自由是一把双刃剑。自由意味着你必须做出选择。 在以前，选择语言很容易。如果你是一个软件项目的负责人，而有些讨厌的家伙建议你使用一种你从来没用的语言来写软件，你只需对他们说：“那种语言一点儿也不实用，”就能结束争论。

现在，基于互联网的应用改变了一切。此刻，你对语言的选择权不得不屈服于市场的力量。 如果你还想对这股改变的趋势装作视而不见，还使用c或c++， 就像我们竞争对手的选择一样，那你就是在准备被时代抛弃。创业小公司只有使用更强大的语言才能制胜。

渐进式开发

使用lisp语言开发的软件带着某种开发风格。它的传统之一是采用渐进式的发展：尽快开始编写程序，越快越好，哪怕该程序暂时什么事情也完成不了。然后逐渐为程序添加主要功能，每一次改写之前的代码还能继续运行。

我觉得，这种编写方式让你迅速高效地编写出软件。 Lisp能将迅速高效的工作方式带入了使用它编写出的程序里，因为Lisp程序员已经这么工作了30年之久。

Viaweb编辑器是一个典型的渐进开发的产物。它始于一个120行的小程序，目的是为了生成网站。在我刚刚写完的一本书里的需要以网站为例子，于是我写了Viaweb。 最终，Viaweb编辑器成长为有约25,000行代码的程序。 我并不认为我有一两天时间能让停止现有代码，好让自己坐下来，改写所有代码。

整个开发过程是一个逐渐变化的长期过程。 这种发展方式非常符合网络程序的滚动发行方式。 这也是更高效的泛型编程方法。

互动的TopLevel

Lisp的互动TopLevel有助于快速开发软件。 但对我们而言，Lisp最大的优势或许是发掘bug。 正如我之前所说，在网络程序中，用户数据存储在你的服务器上，通常会不断重现出bug。

当客服拿着编辑器的错误报告来找我的时候，我会把Lisp代码装入解释器中，并以用户的账号登陆。 如果我能重现错误，我会中断循环，看看到底问题出在哪里。 通常，我可以立即修复错误，并发布一个修复补丁。 当我说“立即”，我的意思是，投诉的用户还没有挂断他的手机呢。

迅速修复错误为我们占据了相当有利的竞争地位。 如果我们能够快速找到并修正错误，而用户仍在讲电话， 我们就给用户成功留下了这一印象：程序的错误不过是他们自己想象出来的罢了。所以，有时我们告诉客服，请让用户重新登录，检查一下问题是否存在。 当然，等用户重新登录，他们使用的已经是新发布的版本了，错误早已修复，程序运行良好。 我知道这么做有点“卑鄙”，但同时也很有趣。

HTML的宏

Lisp的宏是我们的另一制胜点。我们在viaweb编辑器中广泛的使用了宏。 准确地描述，我们所使用的是一个“大宏”。我们非常依赖lisp；而宏会告诉你这种依赖究竟达到了何种程度。 因为，根本没有其他语言能拥有这样的宏。

一个我们使用宏的目的是生成HTML。宏与HTML彼此有种天然的默契。因为HTML是一个前缀表达式，这一点很像Lisp。HTML是递归的，这一点也很像Lisp。 因此，我们能在宏调用中调用宏，从生成最复杂的HTML，并且进程仍然可控。

嵌入语言

我们使用宏的另一大用途是嵌入语言——我们称其为Rtml。 （关于Rtml究竟代表些什么，解释版本不一。其实我是以Robert Morris的名字为它命名的。Rober Morris是Viaweb的共同创始人，他的用户名是Rtm）(Robert Morris, the other founder of Viaweb, whose username is Rtm.) 。我们软件的每一页面都是由RTML所写的程序生成的。 我们将这些程序称为模版，这样听起来就没那么吓人。其实，它们是如假包换的Lisp程序。Rtml是一个宏与内置的Lisp操作符的组合。

用户可以编写自己的Rtml模板，来实现他们所期望的网页外观。 我们生成了一个结构编辑器来操纵模板，这个编辑器很像用户在Interlisp中所使用的结构化编辑器。 对用户来说，他不必在自由模式下输入，只需要把现成的代码剪切、粘贴起来即可，不必担心语法错误。 这同时意味着，我们不需要显示底层的S表达式的括号：我们通过缩进来表示。 这样，这门语言就更易懂了。

我们特意设计Rtml，让它避免了运行错误： 每个Rtml程序都会生成一些网页，你可以不断调试网页，直至最后生成你想要的前端效果。

最开始，我们设想我们的用户是网站顾问，还设想他们会频繁使用Rtml。 因此，我们提供了不少默认模板，内含分块与项目网页等等， 目的是用户可以使用、修改，从而生成他想要的页面。

事实上，网络顾问并不喜欢Viaweb。顾问们，通常来说，都喜欢使用一般人很难上手的产品，这才能保证顾问会得到持续雇佣。 顾问们会访问我们的网站，发现我们的软件很容易使用， 它能让任何人在五分钟内建立起网上商店，他们便会说，我怎么可能用这么简单的玩意儿呢？ 因此，我们没在顾问身上赚到钱。相反，用户大部分都是终端用户，他们自己就是商人。 他们喜欢设计自己网站的感觉。他们不想学编程。他们只使用默认模板。

因此，Rtml最终没有变成程序的主界面。 它扮演了两个角色。首先，对于老练的用户来说，Rtml是只安全阀，提供完成内置模板无法提供的功能。在做viaweb的过程中，有人提出了一个相当有用的意见：用户始终希望升级，即使他们永远不会真的去用。 Rtml就是我们的升级路线。如果你想，你就能绝对控制您网页的每一个角落。

几百个用户中只有一个人会真的撰写自己的模板。这就提供给Rtml第二个优势。 通过查看这些用户如何修改内置模板，我们就可以知道我们需要还能给他们添加些什么。 我们最终的目标是：用户不需要使用Rtml。我们的内置模板应该提供出人们想要的一切内容。 以新的视角来观察，Rtml担任了警告者，它告诉我们，软件中还缺些什么。

编写者——我们是从Rtml的第三个优点中受益最多的人。 即使我们是唯一使用Rtml的人，设计这门语言也值了。 在软件中设计这样的一个抽象层为我们挣得了竞争优势。 首先，它使我们软件的设计干净、简介。我们的竞争对手使用C或Perl代码生成网页，而我们拥有高层语言来生成网页，同时指定页面样式。 这使得代码更干净，更容易修改。 我已经提到过，网络程序是通过渐进式的修改来发布的，渐进式修改要求我们了解修改的严重性。通过将代码分层设计，我们能更优雅地处理修改。 底层修改（也就是修改Rtml本身）影响甚广，也很少发生，通常需要编程者深思熟虑。而修改上层（模板代码）可以快速执行，无需担心太多后果。

Rtml很像Lisp，主要由Lisp宏组成。它的在线编辑器操作S表达式。 当人们运行模板时，模板就被编译成lisp函数。

Rtml在很大程度上依赖于关键字参数，因为那时我一直在思考Common Lisp的关键字参数，这一部分颇为棘手。由于网络软件的发布方式，我们必须设计易修改的软件。 Rtml本身很容易修改，就像该软件的其他部分一样。 大多数Rtml操作符设计为采用关键字参数。这一设定帮了我们大忙。 如果我要添加一个操作符的行为方式，只需要添加一个新的关键字参数， 现有的模板仍然可用。少数的Rtml操作符没有采取关键字参数， 因为我没想到日后有改变这些操作符的需求，可是结果总让我悔不当初。 如果我能从头再来，我想修改的事情之一是，让一切Rtml操作符都采用关键字参数。

事实上，我们在编辑器里嵌入了好几门其他语言。 其中一门语言是用以描述图片的，我们并没有将其暴露给用户。 Viaweb包含由C语言撰写的图像生成器，能够接收对图像的描述、创建图像、 并返回它的URL。我们也是使用S表达式来描述这些图像的。

闭包模拟子程序

使用网页作为UI的问题之一，是web页面的无状态属性。 我们使用词法闭包来模拟子程序行为。 如果你理解延续，那么我们所做的事情可以解释为， 我们以延续传递风格编写软件。

当大多数网络软件生成一个页面上的链接时，往往设想， 假如用户点击该链接，那么程序将要调用该CGI脚本及其参数。 而我们的软件生成链接时，则设想假如用户点击该链接，程序将运行这段代码。 这一段代码可以是任意一段代码，可能（事实上，通常情况下）也可以包含任意变量，并从上下文获取值。

我们是通过宏做到这一点的。宏带着包含闭包的初始参数，紧跟着一段代码。 代码将通过一个唯一ID存储在全局散列表中，这段代码的输出将通过文中的代码生成，并出现在一个链接里，链接的url包含了散列键。假若链接被点击，我们的软件会找到并调用相应的代码，然后继续这一循环。假如代码是闭包，则引用其上下文。 我们飞快地编写了CGI脚本。

到目前为止，我所说的听起来还很空洞，所以让我给你举个例子， 感受一下这一技术带来的显著差异。

在网络程序中，用户经常需要编辑多属性的对象。多数属性都可以表示为表单域或菜单。 例如，假设你编辑的对象代表一个人，你可能得到一个域储存姓名； 一个选择职称的菜单框，等等。

现在，有些对象带有Color属性，我们该怎么办？ 假如沿用普通的CGI脚本，那么这些属性都将集中在一个表单里， 底部带着个”更新”按钮，这么一来操作就很麻烦了。 你也可以使用一个文本字段，让用户手动输入RGB值， 但终端用户都不喜欢这么操作。 要不然，你可以设置一个包含可选颜色的菜单，但你必须限制颜色的数量。又或者你可以提供标准的色彩表，那么你需要256个菜单项，每个菜单项的名字都需要独一无二的命名。

Viaweb能够将一个颜色作为样本来代表当前值， 并跟随一个“修改”按钮。假设用户点击“修改”按钮， 他们就会来到带有色彩表的页面进行选择。 选择后，他们可以回到编辑对象属性的页面，发现颜色已经改变。 这就是我所说的闭包模拟子程序行为。 看起来，软件让用户选择一个颜色，并返回。实际并非如此；程序生成一个新的CGI调用，但对用户而言，我们正在处理一个子程序。我们写代码的方式表达出，假如用户点击了链接，并前往颜色选择页面，最后返回原有页面。 我只举了我们使用该技术的一个例子。 总而言之，这一技术让我们的程序看起来远比竞争对手更复杂。

===================================

Lisp in Web-Based Applications

Paul Graham

(This is an excerpt of a talk given at BBN Labs in Cambridge, MA, in
April 2001.)


Any Language You Want

One of the reasons to use Lisp in writing Web-based applications
is that you *can* use Lisp.  When you're writing software that is
only going to run on your own servers, you can use whatever language
you want.

For a long time programmers didn't have a lot of choice about what
language to use for writing application programs.  Until recently,
writing application programs meant writing software to run on
desktop computers.  In desktop software there was a strong bias
toward writing the application in the same language as the operating
system.  Ten years ago, for all practical purposes, applications
were written in C.

With Web-based applications, that changes.  You control the servers,
and you can write your software in any language you want.  You can
take it for granted now that you have the source code of both your
operating system and your compilers.  If there does turn out to be
any kind of problem between the language and the OS, you can fix
it yourself.

This new freedom is a double-edged sword, however.  Having more
choices means that you now have to think about which choice to
make.  It was easier in the old days.  If you were in charge of a
software project, and some troublesome person suggested writing
the software in a different language from whatever you usually
used, you could just tell them that it would be impractical, and
that would be the end of it.

Now, with server-based applications, everything is changed.  You're
now subject to market forces in what language you choose.  If you
try to pretend that nothing has changed, and just use C and C++,
like most of our competitors did, you are setting yourself up for
a fall.  A little startup using a more powerful language will eat
your lunch.


Incremental Development

There is a certain style of software development associated with
Lisp.  One of its traditions is incremental development:  you start
by writing, as quickly as possible, a program that does almost
nothing.  Then you gradually add features to it, but at every step
you have working code.

I think this way you get better software, written faster.  Everything
about Lisp is tuned to this style of programming, because Lisp
programmers have worked this way for at least thirty years.

The Viaweb editor must be one of the most extreme cases of incremental
development.  It began with a 120-line program for generating Web
sites that I had used in an example in a book that I finished just
before we started Viaweb.  The Viaweb editor, which eventually grew
to be about 25,000 lines of code, grew incrementally from this
program.  I never once sat down and rewrote the whole thing.  I
don't think I was ever more than a day or two without running code.
The whole development process was one long series of gradual changes.

This style of development fits well with the rolling releases that
are possible with Web-based software.  It's also a faster way to
get software written generally.


Interactive Toplevel

Lisp's interactive toplevel is a great help in developing software
rapidly.  But the biggest advantage for us was probably in finding
bugs.  As I mentioned before, with Web-based applications you have
the users' data on your servers and can usually reproduce bugs.

When one of the customer support people came to me with a report
of a bug in the editor, I would load the code into the Lisp
interpreter and log into the user's account.  If I was able to
reproduce the bug I'd get an actual break loop, telling me exactly
what was going wrong.  Often I could fix the code and release a
fix right away.  And when I say right away, I mean while the user
was still on the phone.

Such fast turnaround on bug fixes put us into an impossibly tempting
position.  If we could catch and fix a bug while the user was still
on the phone, it was very tempting for us to give the user the
impression that they were imagining it.  And so we sometimes (to
their delight) had the customer support people tell the user to
just try logging in again and see if they still had the problem.
And of course when the user logged back in they'd get the newly
released version of the software with the bug fixed, and everything
would work fine.  I realize this was a bit sneaky of us, but it
was also a lot of fun.


Macros for Html

Lisp macros were another big win for us.  We used them very
extensively in the Viaweb editor.  It could accurately be described
as one big macro.  And that gives you an idea of how much we depended
on Lisp, because no other language has macros in the sense that
Lisp does.

One way we used macros was to generate Html.  There is a very
natural fit between macros and Html, because Html is a prefix
notation like Lisp, and Html is recursive like Lisp.  So we had
macro calls within macro calls, generating the most complicated
Html, and it was all still very manageable.


Embedded Languages

Another big use for macros was the embedded language we had for
describing pages, called Rtml.  (We made up various explanations
for what Rtml was supposed to stand for, but actually I named it
after Robert Morris, the other founder of Viaweb, whose username
is Rtm.)

Every page made by our software was generated by a program written
in Rtml.  We called these programs templates to make them less
frightening, but they were real programs.  In fact, they were Lisp
programs.  Rtml was a combination of macros and the built-in Lisp
operators.

Users could write their own Rtml templates to describe what they
wanted their pages to look like.  We had a structure editor for
manipulating these templates, a lot like the structure editor they
had in Interlisp.  Instead of typing free-form text, you cut and
pasted bits of code together.  This meant that it was impossible
to get syntax errors.  It also meant that we didn't have to display
the parentheses in the underlying s-expressions:  we could show
structure by indentation.  By this means we made the language look
a lot less threatening.

We also designed Rtml so that there could be no errors at runtime:
every Rtml program yielded some kind of Web page, and you could
debug it by hacking it until it produced the page you meant it to.

Initially we expected our users to be Web consultants, and we
expected them to use Rtml a lot.  We provided some default templates
for section pages and item pages and so on, and the idea was that
the users could take them and modify them to make whatever pages
they wanted.

In fact it turned out that Web consultants didn't like Viaweb.
Consultants, as a general rule, like to use products that are too
hard for their clients to use, because it guarantees them ongoing
employment.  Consultants would come to our Web site, which said
all over it that our software was so easy to use that it would let
anyone make an online store in five minutes, and they'd say, there's
no way we're using that.  So we didn't get a lot of interest from
Web consultants.  Instead the users all tended to be end-users,
the actual merchants themselves.  They loved the idea of being in
control of their own Web sites.  And this kind of user did not want
to do any kind of programming.  They just used the default templates.

So Rtml didn't end up being the main interface to the program.  It
ended up playing two roles.  First of all, it was an escape valve
for the really sophisticated users, who wanted something our built-in
templates couldn't provide.  Somewhere in the course of doing
Viaweb, someone gave me a very useful piece of advice: users always
want an upgrade path, even though as a rule they'll never take it.
Rtml was our upgrade path.  If you wanted to, you could get absolute
control over everything on your pages.

Only one out of every couple hundred users actually wrote their
own templates.  And this led to the second advantage of Rtml.  By
looking at the way these users modified our built-in templates, we
knew what we needed to add to them.  Eventually we made it our goal
that no one should ever have to use Rtml.  Our built-in templates
should do everything people wanted.  In this new approach, Rtml
served us as a warning sign that something was missing in our
software.

The third and biggest win from using Rtml was the advantage we
ourselves got from it.  Even if we had been the only people who
used Rtml, it would have been very much worth while writing the
software that way.  Having that extra layer of abstraction in our
software gave us a big advantage over competitors.  It made the
design of our software much cleaner, for one thing.  Instead of
just having bits of actual C or Perl code that generated our Web
pages, like our competitors, we had a very high-level language for
generating Web pages, and our page styles specified in that.  It
made the code much cleaner and easier to modify.  I've already
mentioned that Web-based applications get released as a series of
many small modifications.  When you do that you want to be able to
know how serious any given modification is.  By dividing your code
into layers, you get a better handle on this.  Modifying stuff in
lower layers (Rtml itself) was a serious matter to be done rarely,
and after much thought.  Whereas modifying the top layers (template
code) was something you could do quickly without worrying too much
about the consequences.

Rtml was a very Lispy proposition.  It was mostly Lisp macros, to
start with.  The online editor was, behind the scenes, manipulating
s-expressions.  And when people ran templates, they got compiled
into Lisp functions by calling compile at runtime.

Rtml even depended heavily on keyword parameters, which up to that
time I had always considered one of the more dubious features of
Common Lisp.  Because of the way Web-based software gets released,
you have to design the software so that it's easy to change.  And
Rtml itself had to be easy to change, just like any other part of
the software.  Most of the operators in Rtml were designed to take
keyword parameters, and what a help that turned out to be.  If I
wanted to add another dimension to the behavior of one of the
operators, I could just add a new keyword parameter, and everyone's
existing templates would continue to work.  A few of the Rtml
operators didn't take keyword parameters, because I didn't think
I'd ever need to change them, and almost every one I ended up
kicking myself about later.  If I could go back and start over from
scratch, one of the things I'd change would be that I'd make every
Rtml operator take keyword parameters.

We had a couple embedded languages within the editor, in fact.
Another one, which we didn't expose directly to the users, was for
describing images.  Viaweb included an image generator, written in
C, that could take a description of an image, create that image,
and return its url.  We used s-expressions to describe these images
as well.


Closures Simulate Subroutines

One of the problems with using Web pages as a UI is the inherent
statelessness of Web sessions.  We got around this by using lexical
closures to simulate subroutine-like behavior.  If you understand
about continuations, one way to explain what we did would be to
say that we wrote our software in continuation-passing style.

When most web-based software generates a link on a page, it tends
to be thinking, if the user clicks on this link, I want to call
this cgi script with these arguments.  When our software generated
a link, it could think, if the user clicks on this link, I want to
run this piece of code.  And the piece of code could an arbitrary
piece of code, possibly (in fact, usually) containing free variables
whose value came from the surrounding context.

The way we did this was to write a macro that took an initial
argument expected to be a closure, followed by a body of code.
The code would then get stored in a global hash table under a unique
id, and whatever output was generated by the code in the body would
appear within a link whose url contained that hash key.  If that
link was the next one clicked on, our software would find and call
the corresponding bit of code, and the chain would continue.
Effectively we were writing cgi scripts on the fly, except that
they were closures that could refer to the surrounding context.

So far this sounds very theoretical, so let me give you an example
of where this technique made an obvious difference.  One of the
things you often want to do in Web-based applications is edit an
object with various types of properties.  Many of the properties
of an object can be represented as form fields or menus.  If you're
editing an object representing a person, for example, you might
get a field, for their name, a menu choice for their title, and so
on.

Now what happens when some object has a property that is a color?
If you use ordinary cgi scripts, where everything has to happen on
one form, with an Update button at the bottom, you are going to
have a hard time.  You could use a text field and make the user
type an rgb number into it, but end-users don't like that.  Or you
could have a menu of possible colors, but then you have to limit
the possible colors, or otherwise even to offer just the standard
Web colormap, you'd need 256 menu items with barely distinguishable
names.

What we were able to do, in Viaweb, was display a color as a swatch
representing the current value, followed by a button that said
"Change."  If the user clicked on the Change button they'd go to
a page with an imagemap of colors to choose among.  And after they
chose a color, they'd be back on the page where they were editing
the object's properties, with that color changed.  This is what I
mean about simulating subroutine-like behavior.  The software could
behave as if it were returning from having chosen a color.  It
wasn't, of course; it was making a new cgi call that looked like
going back up a stack.  But by using closures, we could make it
look to the user, and to ourselves, as if we were just doing a
subroutine call.  We could write the code to say, if the user clicks
on this link, go to the color selection page, and then come back
here.  This was just one of the places were we took advantage of
this possibility.  It made our software visibly more sophisticated
than that of our competitors.

