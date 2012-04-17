---
layout: manual
title:  Introduction
---
科学计算一贯需要要高效率，然而业内的专家有很大一部份在日常工作中使采用更慢的动态语言。
我们有理由相信采用动态语言解决这些问题是有很多好处的，并且不希望减少对动态语言的使用。
有幸的是现代语言设计和编译器技术让排除性能瓶颈，提供一个能产生足够的原型设计和高效的部署性能密集型应用的简单的工作环境成为了可能。
Julia编程语言填补了这一空白。
Julia是一种灵活的，适合科学计算和数值计算并且能和传统的静态编译语言的性能相媲美的动态语言。
Julia通过 [LLVM][]实现的 [just-in-time (JIT) compilation](http://en.wikipedia.org/wiki/Just-in-time_compilation)和类型推断从而达到可选类型，多重调度以及高效的表现。
她是多功能的，结合了命令、函数、和面向对象编程。Julia的语法和[MATLAB®][]非常相似，因此MATLAB®用户能很舒服的使用Julia。然而MATLAB®高效的原型构建和线性代数处理能力限制了他在除此之外其他领域的编程任务。
Julia保持了MATLAB®的简单和对高等级数值计算的经验，同时克服了他在一般性编程领域的限制。
为此，Julia既带有数值计算编程语言的血统，同时也借鉴了很多流行的动态语言，如：[Lisp][], [Perl][], [Python][], [Lua][], and [Ruby][]
尽管有人说动态语言是非类型的，但他们绝对不是说：每个对象，无论是内建的或者用户自定义的都必须有个类型。大部分动态语言含有的类型声明的缺陷意味着程序员不能向编译器指明一个值的类型，甚至是明确的不能说明值的类型。
然而在静态语言中，程序员能而且必须向编译器注明值的类型，类型只在编译器期间而不能在执行期间操作或者表达。
在Julia中，类型本身是运行时的对象，同时也可以传递信息给编译器。
然而一个悠闲的程序员不用明确的指出类型或者多重调度，他们是Julia的核心功能：函数定义在参数类型的组合的基础上，并响应于定义最符合的调度。
这样的模型非常适合在觉得传统的面向对象调用中用首个参数指明其所有者不自然的数值编程。
操作符只是用特殊标记的定义的函数--为了扩展用户定义类型，而为“+”函数定义了一个新的方法。
使得现有的代码可以无缝的使用于新的数据类型。
或许因为运行时的类型判断（通过可选的类型注解增加），或许因为该工程对性能的强烈要求，使得Julia执行效率超过了其他动态语言，甚至是可以和静态编译语言相匹敌。
对于大多数数值问题，速度通常是最重要的：将要处理的数据很容易跟上摩尔定律在过去几十年的步伐。
Julia 的目的在于创建一种集易用、强大、高效的于一身的编程语言。
除了上述特点，Julia相比于其他系统的一些优点还包括：
- 自由并且开源（采用MIT license授权(https://github.com/JuliaLang/julia/blob/master/LICENSE)） 
- 用户自定义数据类型和内建类型一样高效 
- 为并行计算和分布式计算而设计 
- 轻量级“绿色”线程 ([coroutines](http://en.wikipedia.org/wiki/Coroutine))
- 简洁高效的类型系统 
- 对数值和其他类型提供优雅的扩展性转换和改进 
- 对[Unicode](http://en.wikipedia.org/wiki/Unicode)包括[UTF-8](http://en.wikipedia.org/wiki/UTF-8)提供有效的支持
- 直接调用C函数(无需包装和调用特殊函数)
- 拥有管理其他进程的类似shell的高效能力 
- 提过Lisp类型的宏和其他亚程序特性 
[LLVM]:        http://en.wikipedia.org/wiki/Low_Level_Virtual_Machine
[MATLAB®]:     http://en.wikipedia.org/wiki/Matlab
[Mathematica]: http://en.wikipedia.org/wiki/Mathematica
[Lisp]:        http://en.wikipedia.org/wiki/Lisp_(programming_language)
[Perl]:        http://en.wikipedia.org/wiki/Perl_(programming_language)
[Python]:      http://en.wikipedia.org/wiki/Python_(programming_language)
[Lua]:         http://en.wikipedia.org/wiki/Lua_(programming_language)
[Ruby]:        http://en.wikipedia.org/wiki/Ruby_(programming_language)
[C]:           http://en.wikipedia.org/wiki/C_(programming_language)
[Java]:        http://en.wikipedia.org/wiki/Java_(programming_language)
