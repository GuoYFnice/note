# 你不知道的JavaScript

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

