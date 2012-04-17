---
layout: manual
title:  Integers and Floating-Point Numbers
---
整形数和浮点型数是算法和计算最基本的组成部分。这些值的内建表示法称作元数据,而整形和浮点型数字面值则被称为立即数。如'1'是一个整形数而'1.0'是一个浮点型数。
他们在内存中的二进制表示为一个元数据对象。Julia提供了大量的元数据对象以及一个在这些元数据上定义完整的算术和为操作符。
以下是Julia提供的元数据类型：

- **整型:**
    - `Int8` — signed 8-bit integers ranging from –2<sup>7</sup> to 2<sup>7</sup> – 1.
    - `Uint8` — unsigned 8-bit integers ranging from 0 to 2<sup>8</sup> – 1.
    - `Int16` — signed 16-bit integers ranging from –2<sup>15</sup> to 2<sup>15</sup> – 1.
    - `Uint16` — unsigned 16-bit integers ranging from 0 to 2<sup>16</sup> – 1.
    - `Int32` — signed 32-bit integers ranging from –2<sup>31</sup> to 2<sup>31</sup> – 1.
    - `Uint32` — unsigned 32-bit integers ranging from 0 to 2<sup>32</sup> – 1.
    - `Int64` — signed 64-bit integers ranging from –2<sup>63</sup> to 2<sup>63</sup> – 1.
    - `Uint64` — unsigned 64-bit integers ranging from 0 to 2<sup>64</sup> – 1.
    - `Bool` — either `true` or `false`, which correspond numerically to 1 and 0.
    - `Char` — a 32-bit numeric type representing a [Unicode character](http://en.wikipedia.org/wiki/Unicode) (see [Strings](../strings) for more details).

- **浮点型:**
    - `Float32` — [IEEE 754 32-bit floating-point numbers][Float32].
    - `Float64` — [IEEE 754 64-bit floating-point numbers][Float64].

另为，Julia在这些元数据之上提供了完全的[复数和有理数](../complex-and-rational-numbers)。所以数据类型之间的操作无需特殊的声明，得益于Julia提供的一个非常灵活的类型转换系统。更重要的是，这个类型转换系统（详见：[Conversion and Promotion](../conversion-and-promotion)）可以适应用户扩展需求，因此用户自定义类型也可以互相操作就像Julia提供的元数据类型一样。
## 整数: 

字面整数以标准的方式提供:

    julia> 1
    1

    julia> 1234
    1234

字面整数对应的内置类型取决于系统是32位架构还是6位架构：

    # 32-bit system:
    julia> typeof(1)
    Int32

    # 64-bit system:
    julia> typeof(1)
    Int64

类型`Int`是系统本地整形的一个缩写：

    # 32-bit system:
类似的, `Uint`是系统本地无符号整形的缩写：

    # 32-bit system:
    julia> Uint
    Uint32

    # 64-bit system:
    julia> Uint
    Uint64
如果一个字面整形值大于一个32位整形所能表示的范围，那么它的类型将会被自动设置为64-bit而不管是在32位机器上还是64位机器上:

    # 32-bit or 64-bit system:
    julia> typeof(3000000000)
    Int64

同样，如果一个字面整形值大于一个`Int64`但是小于‘Uint64'所能表示的最大数，那么它将被表示为一个'Uint64'类型的整数:

    # 32-bit or 64-bit system:
    julia> 12345678901234567890
    0xab54a98ceb1f0ad2

    julia> typeof(ans)
    Uint64
使用无符号整形数的另为一种办法是在输入和输出是在16进制字符'0-91-f'之前加上前缀'0x'(也可以使用'A-F')。该无符号数的大小由给出的16进制数大小来决定:

    julia> 0x1
    0x01

    julia> typeof(ans)
    Uint8

    julia> 0x123
    0x0123

    julia> typeof(ans)
    Uint16

    julia> 0x1234567
    0x01234567

    julia> typeof(ans)
    Uint32

    julia> 0x123456789abcdef
    0x0123456789abcdef

    julia> typeof(ans)
    Uint64

这一情形常出现在用户使用无符号16进制字面值表示整形值时，一个典型的情况是用它表示数字字节序比用十进制整形值要好
内建元数据类型所能表示的最小和最大值可以通过函数`typemin`和 `typemax`得到:

    julia> (typemin(Int32), typemax(Int32))
    (-2147483648,2147483647)

    julia> for T = {Int8,Uint8,Int16,Uint16,Int32,Uint32,Int64,Uint64}
             println("$(lpad(T,6)): [$(typemin(T)),$(typemax(T))]")
           end
      Int8: [-128,127]
     Uint8: [0x00,0xff]
     Int16: [-32768,32767]
    Uint16: [0x0000,0xffff]
     Int32: [-2147483648,2147483647]
    Uint32: [0x00000000,0xffffffff]
     Int64: [-9223372036854775808,9223372036854775807]
    Uint64: [0x0000000000000000,0xffffffffffffffff]

如上面呈现的，`typemin` 和`typemax`的返回值表示了所给类型的值的范围。
上面的表达式用了一些我们还没有提到的特性，包括有[for loops](../control-flow#Repeated+Evaluation:+Loops), [strings](../strings), 和[string interpolation](../strings#Interpolation), but should be easy enough to understand for people coming from most mainstream programming languages.
## 浮点数： 

字面浮点数也是以标准形式呈现:

    julia> 1.0
    1.0

    julia> 1.
    1.0

    julia> 0.5
    0.5

    julia> .5
    0.5

    julia> -1.23
    -1.23

    julia> 1e10
    1e+10

    julia> 2.5e-4
    0.00025

以上的结果都是`Float64`类型值. 没有`Float32`类型的字面浮点数,但是可以很容易转换到 `Float32`类型: 
    julia> float32(-1.5)
    -1.5

    julia> typeof(ans)
    Float32
有三个特殊的标准浮点数没有和实数相统一

- `Inf` — 正无穷 — 一个比所有的有限浮点数都大的值
- `-Inf` — 负无穷— 一个比所有的有限浮点数都小的值
- `NaN` — 无效数— 一个不可以和任何浮点数比较的数（包括它自己）

对无限浮点数之间以及和其他浮点数之间的关系的进一步讨论见[Numeric Comparison](../mathematical-operations#Numeric+Comparisons).
[IEEE 754 standard](http://en.wikipedia.org/wiki/IEEE_754-2008), 下面的浮点值是对浮点数运算的结果：

    julia> 1/0
    Inf

    julia> -5/0
    -Inf

    julia> 0.000001/0
    Inf

    julia> 0/0
    NaN

    julia> 500 + Inf
    Inf

    julia> 500 - Inf
    -Inf

    julia> Inf + Inf
    Inf

    julia> Inf - Inf
    NaN

    julia> Inf/Inf
    NaN
函数`typemin` 和`typemax`也适用于浮点数:

    julia> (typemin(Float32),typemax(Float32))
    (-Inf,Inf)

    julia> (typemin(Float64),typemax(Float64))
    (-Inf,Inf)

注意：`Float32` 数`NaN`, `Inf` 和 `-Inf`和它们的`Float64`类型表现一致.

浮点类型数同样支持`eps`函数,他计算出下一个最大的能被替代的值于'1.0‘的差。

    julia> eps(Float32)
    1.192092896e-07

    julia> eps(Float64)
    2.22044604925031308e-16

上面的值把 `2^-23` 还有 `2^-52`分别表示为  `Float32` 和 `Float64` 类型值.
函数`eps`也可以用一个浮点数做参数,他计算出该浮点值与下一个可替代的浮点值的差的绝对值。也就是说
`eps(x)`产生了一个与'x'相同类型的值，使得`x + eps(x)`是下一个比'x'大的可替代的浮点

    julia> eps(1.0)
    2.22044604925031308e-16

    julia> eps(1000.)
    1.13686837721616030e-13

    julia> eps(1e-27)
    1.79366203433576585e-43

    julia> eps(0.0)
    4.94065645841246544e-324

正如你看到的，下一个可替代的浮点数值对于较小的数是小的，而对于较大的数是个大数,或者说，下一个可替代的浮点数是在实数域上靠近零的数，并且当原来的数原离零时它呈指数增长。
Julia定义 `eps(1.0)`和 `eps(Float64)`相等，因为`1.0`是一个64-bit浮点数值。

### 背景知识和参考

关于浮点数更详尽和透彻的阐述参见 John D. Cook的
[article](http://www.johndcook.com/blog/2009/04/06/anatomy-of-a-floating-point-number/)
以及关于同样主题的
[introduction](http://www.johndcook.com/blog/2009/04/06/numbers-are-a-leaky-abstraction/)
关于一些导致这样的实数表示法与理想的表示方法不同的原因。
一个优秀且有深度的关于当浮点数及其计算精度的讨论见David Goldberg的论文 [What Every Computer Scientist Should Know About Floating-Point Arithmetic](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.102.244&rep=rep1&type=pdf).
更多的有关浮点数历史，基本原理，讨论关于浮点数以及许多其他关于数值计算的主题见[collected writings](http://www.cs.berkeley.edu/~wkahan/) of [William Kahan](http://en.wikipedia.org/wiki/William_Kahan), commonly known as the "Father of Floating-Point".
这里或许有你特别感兴趣的 [和Old Man of Floating-Point的一次接触](http://www.cs.berkeley.edu/~wkahan/ieee754status/754story.html).

## 数值系数

为了使数学公式和表达式更清楚，Julia允许在变量前紧跟一个数值系数（意味着乘法）。这让多项式的表达式更清晰。

    julia> x = 3
    3

    julia> 2x^2 - 3x + 1
    10

    julia> 1.5x^2 - .5x + 1
    13.0

也可以将数值系数放在圆括号前面作为其系数。

    julia> 2(x-1)^2 - 3(x-1) + 1
    3

另外，用圆括号括住的表达式可以作为变量的系数，表示该表达式与变量的积:

    julia> (x-1)x
    6

既不能将两个表达式并连，也不可以将一个变量作为表达式的系数，如有必要，应该在其间加上乘法运算符：

    julia> (x-1)(x+1)
    type error: apply: expected Function, got Int64

    julia> x(x+1)
    type error: apply: expected Function, got Int64

这些表达式会被解析为函数调用： 当表达式后面紧跟一个圆括号时都不被解析为一个数值字面值，而会被解析为一个用括号内数做参数调用该函数的结果。因此上面的两种情况，由于括号左边的不是函数，从而导致错误。

上述语法显著的减少了在写数学公式时引起的直观错误。
注意:当用上述方法书写乘法表达式时，不能在数值字面值和标识符或者表达式之间加任何空格。

### 语法冲突

两个数值字面值导致并列字面值系数语法错误：
16进制整形字面值与浮点数的科学计数发。下面上述引起的一些语法冲突示例：

- 16进制整形字面值表达式`0xff`会被认为是'0'和变量'xff'相乘。
- 浮点数表达式`1e10`会被认为是'1'乘以变量'e10',该情形同样适用含'E'的浮点表达式。

在上述的两种情况下，我们可以通过将他们解释为数值字面值来解决语法冲突问题：

- 以`0x`开头的表达式就是16进制字面值。
- 以数字开头并且紧跟着一个`e`或者`E`就是浮点数字面值。
