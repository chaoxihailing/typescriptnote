# 函数
函数基本的元素： 形参（parameter）,实参，每次调用还有拥有另一个值，本次调用的上下文 -- this 关键字

如果函数挂载在一个对象上，作为对象的一个属性，就称为对象的方法。当通过这个对象来调用函数时，该对象就是此次调用的上下文（context），就是该函数的this值。用于初始化一个新创建的对象的函数称为构造函数（constructor）

在JavaScript里面，函数即对象。JavaScript可以把函数赋值给变量，或者作为参数传递给其他函数。也可以给它们设置属性，设置调用它们的方法。

JavaScript的函数可以嵌套在其他函数定义，这个它们可以访问它们被定义时所处的作用域的任何变量。这个意味着JavaScript函数构成一个闭包（closure）。

*函数定义表达式*
函数定义表达式定义一个JavaScript函数，表达式的值是这个新定义的函数
`var square = function(x) { return x*x }`

*函数声明*
``` js
function funcname(args){
    statements
}
```

函数声明语句，和函数定义表达式的不同。两种方法都创建了新的函数对象，但是函数声明语句的函数名是一个变量。变量指向函数对象。和通过var声明变量一样，函数定义语句中的函数被显示地“提前”到脚本或者函数的顶部，因此它们在整个脚本和函数内都是可见的。使用var的话，只有变量声明提前，变量的初始化代码依旧在原来的位置。而使用函数声明语句的话，函数名称和函数体均提前。脚本中的所有函数和函数所有嵌套的函数都会在当前上下文其他代码之前声明。也就是说，可以在声明一个JavaScript函数之前调用它。

函数声明语句“被前提”到外部脚本，或者外部函数作用域的顶部。所以这个方式声明的函数，可以被它定义之前出现的代码所调用。但是表达式定义函数在定义之前无法调用，因为变量的声明提前了，但是给变量的赋值不会提前。

*嵌套函数*
嵌套函数的变量作用域规则： 它们可以它们（或者多重嵌套）的函数参数和变量

##### 函数调用
函数在定义的时候不会执行，只有在调用它们Nederland时候，有4种方式
- 作为函数
- 作为方法
- 作为构造函数
- 通过它们的call()或者apply()方法间接调用

方法调用和函数调用有一个重要的区别，即调用上下文。
方法和this关键字是面向对象编程范例的核心。任何函数作为方法调用实际都会传入一个隐式的实参，这个实参是一个对象，方法调用的母体就是这个对象。方法调用的语法表明函数将基于一个对象进行操作

```
rect.setSize(width, height);
setRectSize(rect, width, heigth);
```

*方法链*
当方法的返回值是一个对象，这个对象还可以再调用它的方法，这种方法调用的序列种（通常称为“链”或者是“级联”）

当方法不需要返回值时，最好直接返回this，如果在api一直采用这种方式（每个方法都返回this），使用pai就可以进行“链式调用”风格的编程，也可以称为流


*构造函数调用*
如果函数或者方法调用前带有关键字new，它就构成构造函数的调用。

构造函数调用创建一个新的空对象，这个对象继承自构造函数的prototype属性。构造函数试图初始化这个新创建到达对象，并将这个对象作为其调用上下文。以此构造函数可以使用this关键字来引用这个新创建的对象。

*call and apply*
对象即函数，函数即对象，其中call()和apply()可以间接地调用函数。两个方法都允许显示指定调用所需要的this值。也就是说，任何函数都可以作为任何对象的方法来调用，哪怕这个函数不是那个对象的方法。call()使用它自有的实参列表作为函数的实参，apply()方法则要求数据的形式传入参数

*函数的实参和形参*
JavaScript中函数定义并未指定函数形参的类型，函数调用也未对传入的实参值做任何类型的检查。JavaScript函数调用甚至不检查传入形参的个数。所以要默认处理


### 闭包
和其他大多数现代编程语言一样，JavaScript也采用词法作用域（lexical scoping），也就是说，函数的执行依赖于变量作用域，这个作用域是在函数定义时决定的，而不是函数调用时决定的。为了实现这种词法作用域，JavaScript函数对象的内部状态不仅包含函数的代码逻辑，还必须引用当前的作用域链。函数对象可以通过作用域相互关联起来，函数体内部的变量都可以保存在函数作用域内，这用特性被称为“闭包”。

当调用函数时闭包所指向的作用域链和定义函数时的作用域链不是同一个作用域链时，事情就会变得非常微妙。当一个函数嵌套了另外一个函数，外部函数将嵌套的函数对象作为返回值的时候，往往会发生这样的事。

理解闭包首先要了解嵌套函数的词法作用域规则

我们将作用域链描述为一个对象列表，而不是绑定的栈，每次调用JavaScript函数的时候,都会为之创建一个新的对象用来保存局部变量，把这个对象添加至作用域链中。每当函数返回的时候，就从作用域链中将这个绑定变量的对象删除。如果不存在嵌套的函数，也没有其他引用指向这个绑定对象，它就会被当作垃圾回收掉。如果定义了嵌套函数，每个嵌套函数都各自有对应一个作用域链，而且这个作用域链指向一个变量绑定对象。如果这些嵌套函数对象在外部函数保存下来，那么它们也会和所指向的变量绑定对象一样当作垃圾回收。但是如果这个函数定义了嵌套的函数，并将它作为返回值，或者存储在某处的属性里，这时就会有一个外部引用指向这个嵌套的函数。它就不会被当作垃圾回收，而且它指向的变量绑定对象也不会被当作垃圾回收。