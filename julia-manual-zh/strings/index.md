---
layout: manual
title:  Strings
---
字符串是一串有限的字符集。
当然，不是很好的就能定义什么是一个字符。
字符通常就是说英语的人们熟悉的字母`A`, `B`, `C`,等,以及数字和通用符号。
这些字符集中的字符对应[ASCII][]中规定的整形数0-127。
然而，还有其他很多非英语语言，以及在ASCII字符集上变种和修改过的字符集如Cyrillic和Greek,和ASSIIC和英语无关的语言如中文 Arabic, Hebrew, Hindi, Japanese, and Korean.
[Unicode][]标准规定的关于什么是一个字符的复杂性，被众多人接受如何定义字符的解决办法。
根据你的需求，你可以选择忽视字符的复杂性而只选择ASCII，或者处理所有字符，又或者当需要是处理非ASIIC文本。
Julia 使得处理ASCII文本简单有效，同时也能同样简单高效的处理Unicode文本。
典型的，你可以书写C风格的字符代码处理ASCII字符串，他们会在表达和语义上如期望的一样。
如果上述代码遇到非ASCII文本时，它会产生一条简明的出错信息，而不是悄无声息的出现一个错误的结果，让你摸不着头脑。当这个错误出现时，修改代码使得他可以处理非ASIIC文本非常的简单。

Julia字符串有两个值得注意的高级特性:

- `字符串`是一个抽象的而非具体的对象-许多不同的表述可以实现`字符串`接口,但他们可以很容易的一起使用并透明的交互。
通过关键字`String`任何字符串类型都可以被用在任何函数中。
- 和C和Java相似，但不同于其他动态语言，Julia有个first-class类型 `Char` 表示一个单独的字符 。
他仅仅是一个表示Unicode字符的32位整形数。
-  和Java一样，字符串的值是不可变的：
`字符串`对象的值是不可改变的。
当创建一个不同的字符串值时，只能通过其他字符串的部分创建一个新的字符串。
- 从概念上说，字符串是一个返回指向字符下标的 *未完成函数* --返回一些索引值，而不是字符值,并抛出异常。
这样使得字符串编码表示为字节索引而不是字符索引，从而使得可变宽度编码的Unicode字符串能实施有效和简单的高效的索引。
- Julia支持所有的[Unicode][] 字符集：
文字字符串通常是 [ASCII][] 或者 [UTF-8][] ，但是其他外部来源编码也可以被简单高效的支持。

[Unicode]:    http://en.wikipedia.org/wiki/Unicode
[Code point]: http://en.wikipedia.org/wiki/Code_point
[ASCII]:      http://en.wikipedia.org/wiki/ASCII
[UTF-8]:      http://en.wikipedia.org/wiki/UTF-8


##  字符

一个 `Char`值表示一个单一的字符： 
它只是一个特殊的文字代表和被解析为[Unicode code point](http://en.wikipedia.org/wiki/Code_point)具有适当的算术行为的32位整形数。
下面演示一个 `Char` 值的输入和显示：

    julia> 'x'
    'x'

    julia> typeof(ans)
    Char

你可以很容易的将一个 `Char` 转换到其对应的整形值：

    julia> int('x')
    120

    julia> typeof(ans)
    Int32

你也可以很容易的将一个整形值转换到其对应的 `Char`值:

    julia> char(120)
    'x'


并不是所有的整形值都是合法的Unicode代码点，但是上面的
`Char`类型转换并没有检查每一个字符值的合法性，如果想要检查被转换的值是一个合法的Unicode代码的值，可以使用函数`safe_char`函数来代替：

    julia> char(0xd800)
    '???'

    julia> safe_char(0xd800)
    invalid Unicode code point: U+d800

    julia> char(0x110000)
    '\U110000'

    julia> safe_char(0x110000)
    invalid Unicode code point: U+110000

通过这么写，合法的Unicode代码点范围为`U+00` 到 `U+d7ff`以及`U+e000`到 `U+10ffff`.
这些没有被分配实际涵义的值，也不是一定都能被被应用程序解析，但是他们都是合法的Unicode字符。

当输入Unicode字符时，可以用`\u`接四个十六进制数或者`\U`跟八个十六进制数(最长的有效值需为六):

    julia> '\u0'
    '\0'

    julia> '\u78'
    'x'

    julia> '\u2200'
    '∀'

    julia> '\U10ffff'
    '\U10ffff'

Julia 使用你系统的本地和语言设置来决定打印什么样的字符以及使用哪些使用通用格式打印从而避免 `\u` or `\U`输入格式。 除了这些Unicode转意形式,所有的f [C 传统转义字符集](http://en.wikipedia.org/wiki/C_syntax#Backslash_escapes)也可以被使用:



    julia> int('\0')
    0

    julia> int('\t')
    9

    julia> int('\n')
    10

    julia> int('\e')
    27

    julia> int('\x7f')
    127

    julia> int('\177')
    127

    julia> int('\xff')
    255

和任何整形数一样，你也可以对`Char`类型进行算术和比较运算：

    julia> 'x' - 'a'
    23

    julia> 'A' < 'a'
    true

    julia> 'A' <= 'a' <= 'Z'
    false

    julia> 'A' <= 'X' <= 'Z'
    true


对`Char`的运算一般会产生整形值。
要创建一个新 `Char`值,需要明确将其转换成`Char`类型:

    julia> 'A' + 1
    66

    julia> char(ans)
    'B'

## 字符串基础

一下是一个用字符串字面值初始话的一个变量：

    julia> str = "Hello, world.\n"
    "Hello, world.\n"

如果想得到字符串中的某个字符可以通过下标索引来得到：

    julia> str[1]
    'H'

    julia> str[6]
    ','

    julia> str[end]
    '\n'



Julia的下标是从1开始的：第一个元素的下标是1而不是0，假如字符串的长度为n,那么最后一个元素的下标是n，而不是n-1。

在任何索引表达式内，关键字`end`可以作为`length(x)`缩写,`x`是被索引的对象,它可以是字符串，数组或其他可以索引的对象。 
可以对`end`进行算术和其他运算操作，就像对待普通的值一样：

    julia> str[end-1]
    '.'

    julia> str[end/2]
    ' '

    julia> str[end/3]
    'o'

    julia> str[end/4]
    'l'

当使用一个小于1或者大于`end`的索引时会导致一个错误：

    julia> str[0]
    in next: arrayref: index out of range

    julia> str[end+1]
    in next: arrayref: index out of range

你也可以用索引范围获得一个子串：

    julia> str[4:9]
    "lo, wo"

注意 `str[k]` 和 `str[k:k]`的区别:

    julia> str[6]
    ','

    julia> str[6:6]
    ","

前者是一个 `Char`类型的字符,而后者是只含有一个字符的字符串。 
在Julia中这点是非常不同的。

## Unicode 和UTF-8

Julia 完全支持 Unicode 字符和字符串。
如 [上面讨论的](#Characters), 在字符字面值中,Unicode代码点可以使用`\u` and `\U`转义符来表示 , 也可以使用所有标准的C转义字符
因此可以像下面这样书写字符串字面值:

    julia> s = "\u2200 x \u2203 y"
    "∀ x ∃ y"
    
Unicode字符是被显示为转义字符还是特殊的字符，取决于你的终端的本地设置和他所支持的Unicode字符。 非ASCII字符串字面值采用UTF-8编码。 UTF-8 是一个可便宽度编码，意味着不是所有的字符都用于相同的字节宽度。
在UTF-8中, ASCII字符 — i.e. 代码点值小于 0x80 (128) — 和他们在ASCII中的编码一致，使用一个byte,而代码点值大于0x80的使用多字节编码 — 最大为4字节每个字符。
这意味着不是每一个指向UTF-8的字符串的索引都是一个合法的指向一个字符的索引。如果你指向了一个不合法的字节索引，将会产生一个错误：

    julia> s[1]
    '∀'

    julia> s[2]
    invalid UTF-8 character index

    julia> s[3]
    invalid UTF-8 character index

    julia> s[4]
    ' '

在这点上字符`∀`
是一个3字节字符，因此指向2和3的索引是一个无效的值，下一个合法的字符索引是4。

由于可变长度的编码， `strlen(s)` 和 `length(s)` 不总是表现的一样:
`strlen(s)` 得到`s`里面的字符数，而`length(s)`得到`s`里最大的合法的索引。
如果反复的用 1 到 `length(s)`
索引指向`s`,并返回的字符序列，如果没有出现异常的话，将得到字符串中所有的字符序列集。
因此，我们可以确认 `strlen(s) <= length(s)`，因为每个字符串中的字符肯定拥有他自己的索引。
下面是一个低效但是简单的遍历字符串 `s` 所有字符的代码：

    julia> for i = 1:length(s)
             try
               println(s[i])
             catch
               # ignore the index error
             end
           end
    ∀

    x

    ∃

    y

空行包含了空格。
幸运的是，上述的ugly的语法不是遍历一个字符唯一的方法，因为可以用字符串最为可迭代对象,并且没有异常出现:

    julia> for c = s
             println(c)
           end
    ∀

    x

    ∃

    y


UTF-8不是Julia支持的唯一的编码格式，增加对其他编码格式的支持是非常方便快速的，但是目前该主题超过了本文档讨论的范围。
关于UTF-8编码的更多讨论见[自己数组字面值](#Byte+Array+Literals),这里提供了更高级的内容。

## 填补 

最常用，最有用的字符串操作之一就是连接：O

    julia> greet = "Hello"
    "Hello"

    julia> whom = "world"
    "world"

    julia> strcat(greet, ", ", whom, ".\n")
    "Hello, world.\n"

然而这样连接字符串难免让人生厌。
为了减少对`strcat`函数的调用, Julia提供了像Perl中的填补字符串操作符`$`:

    julia> "$greet, $whom.\n"
    "Hello, world.\n"

这中方式更可读更方便且和上面的字符串连接效果想同-系统将会重写这个显而易见的字符串字面值到一个连接了这些字符串的变量中。

`$`后面最短的完整的表达式是将要插入字符串的表达式的值，因此可以用括号将任何表达式的结果插入到字符串中：

    julia> "1 + 2 = $(1 + 2)"
    "1 + 2 = 3"

然而，表达式不必非包含在括号里面。
下面的示例中，由于一个字面值数组表达式知道遇到了于"["相匹配的"]"才算结束，所以你可以像下面这样插入一个数组：

    julia> x = 2; y = 3; z = 5;

    julia> "x,y,z: $[x,y,z]."
    "x,y,z: [2,3,5]."


字符串连接和插入都要调用普通得的字符串函数将对象转换成字符串格式。 多数非字符串对象在交互式命令解析器里面都会被转换成字符串对象，

    julia> v = [1,2,3]
    [1,2,3]

    julia> "v: $v"
    "v: [1,2,3]"

函数 `string`表示了`String` 和`Char`值,因此可以将他们插入到其他字符串中而不会被引用和转义。

    julia> c = 'x'
    'x'

    julia> "hi, $c"
    "hi, x"

当要在字符串中插入 `$` , 使用反斜杠进行转义：

    julia> print("I have \$100 in my account.\n")
    I have $100 in my account.

## 通用操作

可以用标准的比较操作符比较字符串字面值：

    julia> "abracadabra" < "xylophone"
    true

    julia> "abracadabra" == "xylophone"
    false

    julia> "Hello, world." != "Goodbye, world."
    true

    julia> "1 + 2 = 3" == "1 + 2 = $(1 + 2)"
    true


用函数`strchr`可以搜索某个特定字符的索引：

    julia> strchr("xylophone", 'x')
    1

    julia> strchr("xylophone", 'p')
    5

    julia> strchr("xylophone", 'z')
    char not found

用第三个参数可以设定搜索的起始位置：

    julia> strchr("xylophone", 'o')
    4

    julia> strchr("xylophone", 'o', 5)
    7

    julia> strchr("xylophone", 'o', 8)
    char not found

另一个常用的字符串操作的函数 `repeat`:

    julia> repeat(".:Z:.", 10)
    ".:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:."

一些其他有用的函数有：

- `length(str)` 得到了字符串`str`可索引的最大索引。
- `strlen(str)` 得到字符串 `str`含有的字符数;他*不是* 和`length(str)`完全相同。
- `i = start(str)` 得到了字符串`str`中第一个合法的起始位置（典型的是1）
- `c, j = next(str,i)` 返回索引`i`的下一个合法的索引的值和该位置上的字符，字符放在c里，索引放在i里。
使用`start` 和 `length`,可以重复的操作`str`里面的字符。
使用 `length` 和`start`可以从反方向重复的操作字符串`str`里面的字符。 
- `ind2chr(str,i)` 返回字符串中索引为j的字符是字符串中的第几个字符。
- `chr2ind(str,j)` 返回字符串中第i个字符的索引。返回字符串中第i个字符的索引。


## 非标准的字符串字面值

有时会出现这样的情况，当你想创建或者用字符串时，但是在标准字符串中又没有相应的字符串适合需求。
在次情况下，Julia提供了*非标准字符串字面值*。
一个非标准字符串字面值看起来像一个通常的双引号括起来的字符串字面值，但是紧跟着一个前缀标识符，使得表现的不像一个字符串字面值。

对普通的Julia字符串的的表现有两种类型的解释：
插值和转义
(escaping是将一个非标准字符串表达为像`\n`的序列，然而转换则是解释这一转义序列的过程。)有时会遇到很方便的处理一种或两种的情况。
为此，Julia提供了三种非标准字符串字面值类型：

- `E"..."` 解释转义字符但是不插入，从而使得`$`不起作用表述为正常的字符而不是表达插入的意思。
- `I"..."` 执行插入，但是不会解析转义字符。
- `L"..."` 即不插入也不解析转义字符。

设想一下，你想书写包含`$`的字符串但是不想插入其后面的字符。你可以按上述办法在`$`的前面加上一个反斜杠。
然而，这样做显得有点冗长。
用`E`做前缀的非标转字符串字面值不会执行插入操作。

    julia> E"I have $100 in my account.\n"
    "I have \$100 in my account.\n"



这让你可以在字符串中包含 `$`但又不会导致其引起的插入操作，同时还避免了在其前面增加前缀`\`.
转义字符序列，如`\n`仍然如通常的一样，因此 '\n' 会被解释为一个表示为新行的字符。

换句话说， `I"..."` 字符串字面值会导致插入但是不会转义：

    julia> I"I have $100 in my account.\n"
    "I have 100 in my account.\\n"

表达式`100`的值被插入至字符串并产生100的十进制的字符串表示法 —  `"100"` (抱歉，这样说可能导致些许混乱。)。
结尾处的`\n`字符别解析为一个斜杠加上字符 `n` ,而不是被解释为一个表示换行的字符。

第三中非标准字符串不解析双引号中的所有字符串：
`L"..."`形式示例： 

    julia> L"I have $100 in my account.\n"
    "I have \$100 in my account.\\n"

`$`和 `\n`都没有被特殊的解释。
### 字节数组字面值

有些字符串字面值更本就没有传教新的字符串。
在[下一节](#Regular+Expressions), 我们将看到正则表达式被书写成非标准字符串字面值。
然而，另一个游泳的非标准字符串字面值是字节字符串子面值: `b"..."`。
这个形式使得你可以用字符串的形式表示字节数组字面值 — i.e. 一串`Uint8`值。
这个约定使得大写字符开头的非标准字面值产生了真的字符串对象，而那些小写字面开头的则不是字符串对象而是字符数组或者是被翻译的正则表达式。
针对字节数组的规则如下：

- ASCII字符和 ASCII 转义字符产生一个字节。
- `\x` 和八进制转义字符产生*字节* ，其值为转义后的值。
- Unicode转义字符产生UTF-8代码点的字节序。

前面所述的使用`\x`和使用小于0x80(128)表示八进制数虽然有些重复，但是这些规则使得我们可以很容易的使用ASCII字符，任意的字节值和UTF-8序列表示字节数组。
示例如下：

    julia> b"DATA\xff\u2200"
    [68,65,84,65,255,226,136,128]

ASCII字符串"DATA" 表示了字节序 68, 65, 84, 65.
`\xff` 产生了单个的字节 255.
Unicode转义字符`\u2200` 使用UTF-8编码产生三个字节226, 136, 128.
注意上述产生的字节数组不相当于一个合法的UTF-8字符串-如果将这个当成一个正则表达式将产生一个语法错误：

    julia> "DATA\xff\u2200"
    syntax error: invalid UTF-8 sequence

同样，注意 `\xff`和`\uff`之间的重要区别:
前者转义序列表示*byte 255*, 而后者转义序列表示 采用UTF-8编码的二字节*code point 255*：

    julia> b"\xff"
    [255]

    julia> b"\uff"
    [195,191]

在字符字面值，掩盖了这种区别，并且`\xff`表示代码点225,因为字符*通常*表示代码点。
然而在字符串中，`\x`通常转义为字符而不是代码点，但是`\u`和`\U`通常被转换成编码为一个或多个字符的代码点。
对于代码点小于 `\u80`, UTF-8编码的每个代码点是由`\x`转义的单个字节，因此忽视区别是安全的。
对于转义 `\x80`到`\xff`和与其相对的`\u80`到`\uff`,却有个很大的不同：
前者转义所有的单字节除非有非常具体的后续字节使得没有形成有效的UTF-8数据，而后者则转义所有的采用两个字节编码的Unicode代码点。

如果感觉到混乱的话，请参阅["The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets"](http://www.joelonsoftware.com/articles/Unicode.html).
这是对 Unicode和 UTF-8非常好的介绍,可以帮助读者减少困惑。

在字节数组字面值中，对象采用二进制形式插入而不是字符串表达式：

    julia> msg = "Hello."
    "Hello."

    julia> len = uint16(length(msg))
    6

    julia> b"$len$msg"
    [6,0,72,101,108,108,111,46]

这里开头的额两个字节是字符串“Hello.”本地的 (little-endian on x86) 二进制表示，被编码成一个无符号的16位整形值，而后面的额字节则编码成ASCII值。

## 正则表达式

Julia 提供了Perl完整的正则表达式，参见 [PCRE](http://www.pcre.org/)库.
正则表达式通过两个方式和字符串相关：
最明显的关联是通过正则表达是搜索字符串中符合正则表达式的字符串；
另一个关联是正则表达式本身被当作一个字符串被输入，然后被传递到一个状态机中被用来在字符串中搜索匹配的项。
在Julia中,正则表达式的输入采用`r`跟上非标准的字符串字面值
最基本的没有任何选项的正则表达式，仅仅使用 `r"..."`即可:

    julia> r"^\s*(?:#|$)"
    r"^\s*(?:#|$)"

    julia> typeof(ans)
    Regex

使用函数`matches`来检查字符串是否匹配正则表达式:

    julia> matches(r"^\s*(?:#|$)", "not a comment")
    false

    julia> matches(r"^\s*(?:#|$)", "# a comment")
    true

若你所见，`matches`根据所给正则表达式是否匹配字符串而返回true或者false。
有时，你可能不仅想知道字符串是否匹配，还想知道他是*怎么*匹配的。
可以使用函数`match`来得到相应的信息：

    julia> match(r"^\s*(?:#|$)", "not a comment")

    julia> match(r"^\s*(?:#|$)", "# a comment")
    RegexMatch("#")

如果正则表达式不匹配所给字符串，, `match`（译者注：原文中为matches应该写错了） 返回`nothing` — 一个特殊的值，在交互式命令解析器里面不打印出来。
除了不可以打印外，它是一个完整的值并且可以通过程序测试：

    m = match(r"^\s*(?:#|$)", line)
    if m == nothing
      println("not a comment")
    else
      println("blank or comment")
    end

如果正则表达式匹配了所给的字符串，函数`match`将返回一个`RegexMatch`对象.
返回额对象记录了如何匹配，包括了部分匹配的子序列以及可能存在的能捕获的子序列。
该例只捕获了匹配的子序列，但是如果我们想捕获注释后每的非空字符串。
可以按下面的代码做：

    julia> m = match(r"^\s*(?:#\s*(.*?)\s*$|$)", "# a comment ")
    RegexMatch("# a comment ", 1="a comment")

可以从一个`RegexMatch`对象中得到如下信息：

- 匹配的整个子序列： `m.match`
- 作为字符串元组的被捕获的子序列: `m.captures`
- 匹配处的开始偏移量: `m.offset`
- 作为一个容器的被捕获的子序列的开始偏移量: `m.offsets`

当没有捕获匹配时，`m.captures` 内容为 `nothing` , 同时 `m.offsets`为0偏移 (这里重申Julia是基于1的索引，因此对于字符串的0偏移和合法的).
见下面一组示例：

    julia> m = match(r"(a|b)(c)?(d)", "acd")
    RegexMatch("acd", 1="a", 2="c", 3="d")

    julia> m.match
    "acd"

    julia> m.captures
    ("a","c","d")

    julia> m.offset
    1

    julia> m.offsets
    [1,2,3]

    julia> m = match(r"(a|b)(c)?(d)", "ad")
    RegexMatch("ad", 1="a", 2=nothing, 3="d")

    julia> m.match
    "ad"

    julia> m.captures
    ("a",nothing,"d")

    julia> m.offset
    1

    julia> m.offsets
    [1,0,2]

捕获的结果用元组表示可以方便使用户用元组非构造语法将结果绑定到局部变量上：

    julia> first, second, third = m.captures
    ("a",nothing,"d")

    julia> first
    "a"

可以通过在双引号后面通过组合一些开关 `i`, `m`, `s`, 以及 `x`来改变正则表示的行为。
这些开关于Perl中的含有一样意义，可以参见[perlre manpage](http://perldoc.perl.org/perlre.html#Modifiers):

    i   忽略大小写匹配。

        如果本地匹配生效，代码点小于255的将会映射到本地，
	而更大值的代码点将会采用Unicode规则。然而跨越Unicode
	规则/非Unicode规则的边界（oeds 255/256）将不会成功。

    m   将字符串当作多行对待。使得 "^" 和"$"不在匹配字符串的开始
    	和结尾，而表示字符串所在行的开始和结尾。

    s   将字符串当作单个的行。是额 "."匹配任何字符，甚至是一个新行。

        和作为 /ms一样, 使得 "."匹配任何字符，却任允许
        "^" 和"$" 匹配字符串的开始和结尾。

    x   让正则表达式解析器忽略大部分空格，既不是反斜杠，也不在字符类
    	中。可以使用这个让你的正则表达式更可读。`#`字符任被当作元字符
	匹配注释，和普通的代码中一样。

下面的示例中，正则表达式打开了所有的3个开关:

    julia> r"a+.*b+.*?d$"ism
    r"a+.*b+.*?d$"ims

    julia> match(r"a+.*b+.*?d$"ism, "Goodbye,\nOh, angry,\nBad world\n")
    RegexMatch("angry,\nBad world")

