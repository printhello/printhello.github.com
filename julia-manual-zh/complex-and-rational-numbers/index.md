---
layout: manual
title:  Complex and Rational Numbers
---


Julia预先提供了复数和有理数类型，并且提供了[Mathematical Operations](../mathematical-operations)讨论的所有数学操作。同时Julia定义的类型提升使得数学操作可以在任何原生的或者组合的类型上，无论是原生类型还是复合类型都可以得到预期的结果。

## 复数

全局常量`im`与复数 *i* 相绑定, 表示-1的平方根。
使用`i`作为一个全局常量名是不明智的，因为这样使得它在任何地方都被当作一个变量，从而引起混乱。
由于Julia 允许将数值字面值作为[juxtaposed with identifiers as
coefficients](../integers-and-floating-point-numbers#Numeric+Literal+Coefficients),这极大的方便了复数语法的书写，使得它就和通常的复数书写方法一致。


    julia> 1 + 2im
    1 + 2im



你可以在复数上进行所有的标准的算术操作:

    julia> (1 + 2im)*(2 - 3im)
    8 + 1im

    julia> (1 + 2im)/(1 - 2im)
    -0.6 + 0.8im

    julia> (1 + 2im) + (1 - 2im)
    2 + 0im

    julia> (-3 + 2im) - (5 - 1im)
    -8 + 3im

    julia> (-1 + 2im)^2
    -3 - 4im

    julia> (-1 + 2im)^2.5
    2.729624464784009 - 6.9606644595719im

    julia> (-1 + 2im)^(1 + 1im)
    -0.27910381075826657 + 0.08708053414102428im

    julia> 3(2 - 5im)
    6 - 15im

    julia> 3(2 - 5im)^2
    -63 - 60im

    julia> 3(2 - 5im)^-1
    0.20689655172413793 + 0.5172413793103449im

Julia的类型提升机制确保了不同类型数据之间的组合操作也可以很好的工作：

    julia> 2(1 - 1im)
    2 - 2im

    julia> (2 + 3im) - 1
    1 + 3im

    julia> (1 + 2im) + 0.5
    1.5 + 2.0im

    julia> (2 + 3im) - 0.5im
    2.0 + 2.5im

    julia> 0.75(1 + 2im)
    0.75 + 1.5im

    julia> (2 + 3im) / 2
    1.0 + 1.5im

    julia> (1 - 3im) / (2 + 2im)
    -0.5 - 1.0im

    julia> 1 + 3/4im
    1.0 + 0.75im



注意：`3/4im` 语法上解析为`3/4*im`,和`(3/4)*im`相等，而不等于`3/(4*im)`,因为乘法和除法具有同样优先级并且是左结合。

Julia 提供了标准函数来操作复数：

    julia> real(1 + 2im)
    1

    julia> imag(1 + 2im)
    2

    julia> conj(1 + 2im)
    1 - 2im

    julia> abs(1 + 2im)
    2.23606797749979

    julia> abs2(1 + 2im)
    5

和通常认为的一样，复数的绝对值是该值点到达0的距离。
函数 `abs2` 专门用来操作复数,它不接受平方根从而可以返回 where it avoids taking a square root and can thus return a value of the same type as the real and imaginary parts of its argument.
The full gamut of other mathematical functions are also defined for complex numbers:

    julia> sqrt(im)
    0.7071067811865476 + 0.7071067811865475im

    julia> sqrt(1 + 2im)
    1.272019649514069 + 0.7861513777574233im

    julia> cos(1 + 2im)
    2.0327230070196656 - 3.0518977991517997im

    julia> exp(1 + 2im)
    -1.1312043837568138 + 2.471726672004819im

    julia> sinh(1 + 2im)
    -0.48905625904129374 + 1.4031192506220407im


注意：数学函数处理实数时通常返回实数，而处理复数时通常返回复数。因此,当传给`sqrt`函数的参数为`-1`时，得到`-1+0im`,尽管 `-1 == -1 + 0im`:

    julia> sqrt(-1)
    NaN

    julia> sqrt(-1 + 0im)
    0.0 + 1.0im

如果想用变量构建一个复数，用数值字面值作系数是行不通的，即使明确的书写出乘法操作符也不行：

    julia> a = 1; b = 2; a + b*im
    1 + 2im

用上面的方法构建复数是不推荐的，最好使用`complex`函数，将复数的实部和虚部分别传给该函数来构建复数。

    julia> complex(a,b)
    1 + 2im

这种使用变量参数构建复数应该作为首选方法，因为他更高效。除此之外，前种构建方法还会导致`b`的值可能产生意外的现象：

    julia> 1 + Inf*im
    NaN + Inf*im

    julia> 1 + NaN*im
    NaN + NaN*im

这些结果是自然并且无法避免的复数乘法和IEEE-754 浮点数运算的干扰。
直接使用函数`complex`构建复数则可以得到直观的结果。

    julia> complex(1,Inf)
    complex(1.0,Inf)

    julia> complex(1,NaN)
    complex(1.0,NaN)

换句话说，我们认为与`im`相乘得到的是没有意义的复数结果，因此和预想的结果不同。

## 有理数

Julia提供了有理数类型来操作整形数表示的有理数。
有理数通过操作符 `//`来构建:

    julia> 2//3
    2//3

如果分子和分母有公因子，那么他们将会被约分,消去公因子。

    julia> 6//9
    2//3

    julia> -4//8
    -1//2

    julia> 5//-15
    -1//3

    julia> -4//-12
    1//3

这一整形数表示的额有理数的形式是统一的，从而比较有理数时，可以通过比较分子和分母来得到结果。
要想得到标准形式有理数的分子和分母可以分别使用函数`num` 和 `den`:

    julia> num(2//3)
    2

    julia> den(2//3)
    3

直接比较分子和分母是没有必要的，因为Julia在有理数上定义了标准的算术和比较操作。

    julia> 2//3 == 6//9
    true

    julia> 2//3 == 9//27
    false

    julia> 3//7 < 1//2
    true

    julia> 3//4 > 2//3
    true

    julia> 2//4 + 1//6
    2//3

    julia> 5//12 - 1//4
    1//6

    julia> 5//8 * 3//12
    5//32

    julia> 6//5 / 10//7
    21//25

有理数可以很方便的转换成浮点数:

    julia> float(3//4)
    0.75

当转换时需要注意，`a == 0` and `b == 0`，这里`a`,`b`分别表示分子和分母中的整数:

    julia> isequal(float(a//b), a/b)
    true

转换无穷有理数也是可以的:

    julia> 5//0
    1//0

    julia> float(ans)
    Inf

    julia> -3//0
    -1//0

    julia> float(ans)
    -Inf

但是不能将转换一个NaN有理数:

    julia> 0//0
    invalid rational: 0//0

同样，Julia的类型提升机制使得有理数和其他类型数据之间的运算自然而简单。

    julia> 3//5 + 1
    8//5

    julia> 3//5 - 0.5
    0.1

    julia> 2//7 * (1 + 2im)
    2//7 + 4//7im

    julia> 2//7 * (1.5 + 2im)
    0.42857142857142855 + 0.5714285714285714im

    julia> 3//2 / (1 + 2im)
    3//10 - 3//5im

    julia> 1//2 + 2im
    1//2 + 2//1im

    julia> 1 + 2//3im
    1//1 + 2//3im

    julia> 0.5 == 1//2
    true

    julia> 0.33 == 1//3
    false

    julia> 0.33 < 1//3
    true

    julia> 1//3 - 0.33
    0.0033333333333332993

