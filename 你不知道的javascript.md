# 你不知道的JavaScript

看书想到什么就记录什么

（var a = 2）变量赋值阶段对执行两个操作，首先编译器会声明一个变量（如果之前没有声明过），然后在运行时引擎会在作用域查找该变量，如果能找到就进行赋值。

LHS：赋值操作的目标是谁（找到变量容器的本身）

RHS：谁是赋值操作的源头（得到某某某的值）

如果查找的目的是对变量进行赋值，那么就会使用LHS。如果目的是获取变量的值，就会使用RHS

不成功的RHS会导致抛出ReferenceError，不成功的LHS会自动的创建一个全局变量（变量提升）

作用域查找会在找到第一个匹配的标识符时停止  ！！

 区分函数声明和表达式最简单的方法是看 function 关键字出现在声明中的位
置（不仅仅是一行代码，而是整个声明中的位置）。如果 function 是声明中
的第一个词，那么就是一个函数声明，否则就是一个函数表达式。  

```javascript
函数声明
function a(){
	alert(1)
}
函数表达式
1.
(function a(){
	alert(1)
})()
2.
a = function(){
	alert(1)
}
```

匿名函数——只有函数声表达式才可以匿名，匿名函数的缺点

1. 匿名函数在栈追踪中不会显示出有意义的函数名，使得调试很困难。  

2. 如果没有函数名，当函数需要引用自身时只能使用已经过期的 arguments.callee 引用，
   比如在递归中。另一个函数需要引用自身的例子，是在事件触发后事件监听器需要解绑
   自身。  
3. 匿名函数省略了对于代码可读性 / 可理解性很重要的函数名。一个描述性的名称可以让
   代码不言自明。  

一个 let 可以发挥优势的典型例子就是之前讨论的 for 循环。

```
for (let i=0; i<10; i++) {
console.log( i );
}
console.log( i ); // ReferenceError
```

for 循环头部的 let 不仅将 i 绑定到了 for 循环的块中，事实上它将其重新绑定到了循环
的每一个迭代中，确保使用上一个循环迭代结束时的值重新进行赋值。  

for 循环头部的 let 不仅将 i 绑定到了 for 循环的块中，事实上它将其重新绑定到了循环
的每一个迭代中，确保使用上一个循环迭代结束时的值重新进行赋值。  

```
解决的办法之一
for (var i=0; i<10; i++) {
settimeout(function(){
console.log( i );
},1000)
}
```

函数提升优先于变量提升

我们习惯将 var a = 2; 看作一个声明，而实际上 JavaScript 引擎并不这么认为。它将 var a
和 a = 2 当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。
这意味着无论作用域中的声明出现在什么地方，都将在代码本身被执行前首先进行处理。
可以将这个过程形象地想象成所有的声明（变量和函数）都会被“移动”到各自作用域的
最顶端，这个过程被称为提升。  

闭包——当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行，这时
就产生了闭包。  

## this

调用位置——  调用位置就是函数在代码中被调用的位置  

最重要的是要分析调用栈（就是为了到达当前执行位置所调用的所有函数）。我们关心的
调用位置就在当前正在执行的函数的前一个调用中  

```javascript
function baz(){
//当前的调用栈是baz
//因此调用位置是全局作用域
    console.log('baz')
    bar()  //bar的调用位置
}
function bar(){bar
//当前的调用栈是baz -> bar
//因此调用位置是baz
    console.log('bar')
    foo()  //foo的调用位置
}
function foo(){
//当前的调用栈是baz -> bar -> foo
//因此调用位置是bar
    console.log('foo')
}
baz()  //baz的调用位置
```



绑定规则

1、默认绑定

```javascript
function foo(){
	console.log(this.a)
}
var a = 2 
foo() //2
```

那么我们怎么知道这里应用了默认绑定呢？可以通过分析调用位置来看看 foo() 是如何调
用的。在代码中， foo() 是直接使用不带任何修饰的函数引用进行调用的，因此只能使用
默认绑定，无法应用其他规则。  

2、隐式绑定

```javascript
function foo(){
	console.log(this.a)
}
var obj = {
	a:2,
    foo:foo
}
obj.foo() //2
```

调用位置会使用 obj 上下文来引用函数，因此你可以说函数被调用时 obj 对象“拥
有”或者“包含”它。当 foo() 被调用时，它的落脚点确实指向 obj 对象。当函数引
用有上下文对象时， 隐式绑定规则会把函数调用中的 this 绑定到这个上下文对象。因为调
用 foo() 时 this 被绑定到 obj，因此 this.a 和 obj.a 是一样的  

3、显式绑定

```javascript
function foo(){
	console.log(this.a)
}
var obj = {
	a:2
}
foo.call(obj) //2
```

4、new绑定

使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

1. 创建（或者说构造）一个全新的对象。
2. 这个新对象会被执行 [[ 原型 ]] 连接。
3. 这个新对象会绑定到函数调用的 this。
4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。  

优先级

默认绑定最低的

显示绑定大于隐式绑定

new绑定大于隐式绑定

1. 函数是否在 new 中调用（new 绑定）？如果是的话 this 绑定的是新创建的对象。
   var bar = new foo()
2. 函数是否通过 call、 apply（显式绑定）或者硬绑定调用？如果是的话， this 绑定的是
   指定的对象。
   var bar = foo.call(obj2)
3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话， this 绑定的是那个上
   下文对象。
   var bar = obj1.foo()
4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到
   全局对象。
   var bar = foo()  



 

## with

with 通常被当作重复引用同一个对象中的多个属性的快捷方式，可以不需要重复引用对象
本身。

```javascript
var obj = { 
  a: 1,
	b: 2,
	c: 3 
 };
// 单调乏味的重复 "obj" obj.a = 2;
obj.b = 3;
obj.c = 4;
// 简单的快捷方式 with (obj) {
         a = 3;
         b = 4;
         c = 5;
}
但实际上这不仅仅是为了方便地访问对象属性。考虑如下代码:
function foo(obj) { with (obj) {
a = 2; }
}
var o1 = { a: 3
};
var o2 = { b: 3
};
     foo( o1 );
     console.log( o1.a ); // 2
foo( o2 );
console.log( o2.a ); // undefined
console.log( a ); // 2——不好，a 被泄漏到全局作用域上了!
```

with 可以将一个没有或有多个属性的对象处理为一个完全隔离的词法作用域，因此这个对
象的属性也会被处理为定义在这个作用域中的词法标识符。

## eval(..) 

数可以接受一个字符串为参数，并将其中的内容视为好像在书
写时就存在于程序中这个位置的代码。换句话说，可以在你写的代码中用程序生成代码并
运行，就好像代码是写在那个位置的一样。

```javascript
function foo(str, a) { eval( str ); // 欺骗! console.log( a, b );
}
var b = 2;
foo( "var b = 3;", 1 ); // 1, 3
```



