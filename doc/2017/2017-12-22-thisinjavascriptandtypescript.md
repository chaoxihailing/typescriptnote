# JavaScript this 

参考资料
[Understanding JavaScript Function Invocation and "this"](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)

最基础的用法
原文：
1. Make an argument list (`argList`) out of parameters 1 through the end
2. The first parameter is `thisValue`
3. Invoke the function with `this` set to `thisValue` and the `argList` as its argument list

For example
``` js
function hello(thing){
    console.log(this + " says hello " + thing); 
}

hello.call("YEhuada", "world") //=> Yehuada says hello world
```

As you can see, we invoked the hello method with this set to "Yehuda" and a single argument "world". This is the core primitive of JavaScript function invocation. You can think of all other function calls as desugaring to this primitive. (to "desugar" is to take a convenient syntax and describe it in terms of a more basic core primitive).

大意是，thisValue即 this被设置为Yehuada， argList, 即参数world，被传入进去。 这是一个基础的原型，每个函数调用，都会脱糖（desugars）成为这样。就是最终转换成为这样的形式

###  简单的扩展(simple function invocation)

原文：
Obviously, invoking functions with call all the time would be pretty annoying. JavaScript allows us to invoke functions directly using the parens syntax (hello("world"). When we do that, the invocation desugars:

``` js
function hello(thing){
    console.log("Hello" + thing);
}

// this: 可以这样子调用
helle("world")

// desugars to: 被转换化成为
hello.call(window, "world");

```

This behavior has changed in ECMAScript 5 only when using strict mode[2]: 

``` js

// this:
hello("world")

// desugars to :
hello.call(undefined, "world");
```

The short version is: a function invocation like fn(...args) is the same as fn.call(window [ES5-strict: undefined], ...args).

Note that this is also true about functions declared inline: (function() {})() is the same as (function() {}).call(window [ES5-strict: undefined).

// 问题： function declared inline 有什么作用

### 成员函数（Number Functions）
The next very common way to invoke a method is as a member of an object (person.hello()). In this case, the invocation desugars:

调用对象里面的函数

``` js
var person = {
    name: "Brendan Eich",
    hello: function(thing){
        console.log(this + "says hello" + thing);
    }
}

// this
person.hello("world")

// desugars to this
person.hello.call(person, "world");
```

Note that it doesn't matter how the hello method becomes attached to the object in this form. Remember that we previously defined hello as a standalone function. Let's see what happens if we attach is to the object dynamically:

单独写成一个独立函数，再附加到对象

``` js

function hello(thing){
    console.log(this + "says hello" + thing);
}

person = { name: "Brench Eich"}
person.hello = hello;

person.hello("world") // still desugars to person.hell.call(person, "world")

hello("world"); // "[object DOMWindow] world"
```

Notice that the function doesn't have a persistent notion of its 'this'. It is always set at call time based upon the way it was invoked by its caller.

注意： 上面的函数没有将this绑定某一个特定的环境中，而是随着调用者环境而变化。

### 使用函数绑定（Function.prototype.bind）
Because it can sometimes be convenient to have a reference to a function with a persistent this value, people have historically used a simple closure trick to convert a function into one with an unchanging this:

可以使用闭包的小技巧，去持久化一个function，this的引用

``` js 
var person = {
  name: "Brendan Eich",
  hello: function(thing) {
    console.log(this.name + " says hello " + thing);
  }
}

var boundHello = function(thing) { return person.hello.call(person, thing);}

boundHello("world");
```

优化一下
``` js
var bind = function(func, thisValue) {
    return function() {
        return func.apply(thisbValue, arguments);
    }
}

var boundHello = bind(person.hello, person);
boundHello("world") // “Brendan Eich says hello world”
```

In order to understand this, you just need two more pieces of information. First, arguments is an Array-like object that represents all of the arguments passed into a function. Second, the apply method works exactly like the call primitive, except that it takes an Array-like object instead of listing the arguments out one at a time.

Our bind method simply returns a new function. When it is invoked, our new function simply invokes the original function that was passed in, setting the original value as this. It also passes through the arguments.

我们的bind方法只是返回一个新的函数。当它被调用时，我们的新函数只是调用传入的原始函数，将原始值设置为this。它也通过参数。

Because this was a somewhat common idiom, ES5 introduced a new method bind on all Function objects that implements this behavior:

This is most useful when you need a raw function to pass as a callback:

``` js
var person = {
  name: "Alex Russell",
  hello: function() { console.log(this.name + " says hello world"); }
}

$("#some-div").click(person.hello.bind(person));

// when the div is clicked, "Alex Russell says hello world" is printed
```
