---
layout: blog 
title: "我的日常使用之Markdown"
---

Markdown 是一种轻量级的标记语言，由John Gruber和Aaron Swartz创建，最初是用来定义一种将文本转换成html文件的工具markdown，项目主页见：[Markdown][0]。它是开源项目，并以BSD-style许可证的许可方式以插件形式或内容管理系统形式发布。
本文就是用Markdown书写的，可能在blog里面不是很好看。将其复制到我推荐的Markdown工具里面，就可以看到效果了。

其他关于Markdown的信息大家可以自己Google，我个人觉得Markdown对我最大的用处在于写笔记和写博客，其简单的书写形式可以让我把注意力放在内容本身而不用去管众多标签问题（我以前是用xml格式写笔记的，用docbook组织内容多饿文档），目前也有很多Markdown+disqus维护博客的例子：

>[Julialang][3]

>[Worldhello][4]

注意：理解Markdown呈现稳定的机制一定不能死记硬背什么会有段落，什么会怎样缩进。而应该将相应的效果对应到html的标签上，想着html对相应的标签会产生什么样的效果。要知道Markdown本身就是为了将文档转换为html而生的。

##工具篇：
下面推荐几个编辑Markdown的工具

* Windows：[MarkdownPad][5]

* Linux:[ReText][6]

##使用篇：
###一、段落分配

    #标题一
    ##标题二
    ###标题三
    ####标题四
    #####标题五
    ######标题六

最大这能到标题六

这会产生代码：

    <h1>标题一</h1>
    <h2>标题</h2>
    <h3>标题一</h3>
    <h4>标题一</h4>
    <h5>标题一</h5>
    <h6>标题一</h6>

两个段落之间用一个空行间隔。这里和上面就是两个段落,产生的html代码中会用<p>标签包裹每个两个空行之间的内容。
    
###二、列表和强调
在写笔记和博客时免不了用到列表，Markdown提供了方便的列表表示方式。

* 列表项，星号后面得有空格
+ 同星号一样效果
- 同上面一样效果，这些是无序列表.
1. 列表下面再紧跟着其他的列表将会被认为是列表的嵌套
2. 有序列表

只有中间有段落隔开后才能开始新一层的列表

1. 新的有序列表 
2. 有序列表可以用"1  2  3  "来排序，但其实无论写什么数字，最终Markdown都会产生html代码
以“ol”标签包裹的列表项，而无序列表则是由“ul”标签包裹的列表项

示例输出html代码

    <ol> <!--同样适用于ul标签-->
        <li>……</li>
        ……
    </ol>


如果想在文章中强调某个词：

    *强调*
    **大强调**
    ***大大强调***

会在产生的html文件中生成如下代码：

    <em>强调</em> 
    <strong> 大强调 </strong> 
    <strong><em>大大强调</em></strong> 

###三、引用和代码


>    有个引用，默认自成一段无需段落留白
>    应用是以“>”开头的后面加上空格或TAB，然后跟引用内容。会产生html代码:

>    `<blockquote>  <p>有个引用</p></blockquote> `


    插入代码
    在书写代码时用个TAB键后跟代码即可。这里我再重申一次：
    不要用写word的思维来看Markdown，想想在html里面写内容时用了TAB键，结果会显示么？

    int main()
    {
        int i;
        i = 0;
        return 0;
    }
    这里的代码块中间不能断，即同一代码块里面的内容都放在同一缩进内
一对\`（撇号，键盘上“1”左边的按键）包裹的内容也可以表示代码。结果会用
> `<p><code></code></p>`

包裹。省去了用缩进。感觉不是很好用只是在表示html代码时比较好用。而是用TAB缩进的内容会被
> `<pre><code></code></pre>`

包裹。

###四、链接和图片
当需要在文中插入链接或图片时，可以使用Markdown提供的链接操作：

* `[]（）` 如：[谷歌](http://google.com "title属性")，三个内容分别是链接内容，目的地url，以及双引号里面的title属性
* `[][]` 如：[新][1]的链接方式，两个内容依次是链接内容和链接id，链接id是像写参考文献一样罗列在文档某处的，我一般放在结尾，很有种写论文的感觉，呵呵。。罗列示例

>    `[0]:  http://daringfireball.net/projects/markdown/ "MarkDown 主页"`

>   `[1]: http://google.com "这样以引用的方式统一组织链接信息"`

用方括号扩住链接ID，然后是冒号，空格，目的url，空格，title属性

插入图片也有两种方式：

* `![]()`    如：["alt属性"](~/images/logo.gif "title属性")
* `![][]` 如： !["alt属性"][2]

可见图片链接和普通的链接非常相似，只是开头多了个惊叹号，链接内容变成了alt属性。

###最后一个觉得有用的标签

下面是分割线：

------------------------------------------------------------------------------

**小结**：

这里是我最近使用Markdown的一些心得，不是cookbook，具体可以见[Markdown项目][0]。如果觉得E文不行可以参见wowubuntu老大的[Markdown: Basics （快速入门）][7] 和 [Markdown 语法说明 (简体中文版)][8]
 

[0]:  http://daringfireball.net/projects/markdown/ "MarkDown 项目主页"
[1]: http://google.com "这样以引用的方式统一组织链接信息"
[2]: ~/images/logo.gif "title属性"
[3]: http://julialang.org/ "JuliaLang"
[4]: http://www.worldhello.net/    "WorldHello"
[5]: http://markdownpad.com/        "MarkdownPad"
[6]: http://sourceforge.net/p/retext/home/ReText/    "Retext"
[7]: http://wowubuntu.com/markdown/basic.html
[8]: http://wowubuntu.com/markdown/index.html#blockquote
