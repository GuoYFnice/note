# es6笔记

## 闭包
**通过函数字面量创建的函数对象包含一个连到外部上下文的连接**——**闭包**
```javascript
function(){
    var i = 0;
    function(){
        return i;
    }
}
```
一个内部函数除了可以访问自己的参数和变量，同时它也能自由访问把它嵌套在其中的父函数的参数与变量。





## Apply      call

JS中，call和apply同为回调函数，功能完全相同，只是传递的参数不一样。下面进行简单的解析：

一、call

call有两个主要：给函数传递参数；扩充函数的作用域。扩充函数作用域很重要，即改变函数的上下文（改变this的指向）。下面简单介绍一下，代码如下：

```javascript
window.job = 'teacher';
showJob = () => {
    console.log(this.job);
}
showJob();      //teacher
```

上述代码中，this指向window，即this=window对象。当然，如果在代码中加入严格模式：

```javascript
'use strict';
```

此时会报错，因为在严格模式下，es6语法需要babel(**babel是一个javascript转译器。**)进行转译。看如下一段代码：

```javascript
'use strict';
window.job = 'teacher';
function showJob() {
	console.log(this.job);
}
showJob(); 
```

此时，代码会报错：“job undefined”，原因严格模式下，此时的this指向undefined。现在忽略这种情况，回到问题，如果不想this指向window，要怎么改变函数中的this，看下列代码：

```javascript
window.job = 'teacher';
const studentObj = {'job': 'student'};
function showJob() {
    console.log(this.job);
}
showJob.call(studentObj);      //student
```

在showJob调用的时候，函数后面多加一个call，然后在参数中放入this指向的对象即可。call在调用时使用，也叫动态绑定。**call的重要功能，通俗讲就是改变this的指向**。

二、apply

apply的功能和call完全一样，只是传递参数不一样，这也是他们的唯一区别，下面简单介绍一下：

代码段1：

```javascript
function sum(num1, num2){
    return num1 + num2;
}
function callSum(num1, num2){
    return sum.call(null, num1, num2);
}
callSum(10, 20);   //30
```

代码段2：

```javascript
function sum(num1, num2){
    return num1 + num2;
}
function callSum(num1, num2){
    return sum.apply(null, [num1, num2]);
}
callSum(10, 20);   //30
```

上述两个代码段的功能一样。

注意：此时this作为调用对象不能忽略，如果要使用默认对象则可以使用null作为第一个参数。

总结：

1、call和apply的功能相同，只是传递的参数的方式不一样，而我们在传递参数的时候经常是传入arguments对象，如下：

```javascript
return sum.apply(null, arguments)
```

arguments它本身就是一个类数组，所以这种情形apply应用广点。

**apply传递数组参数，call传递参数列表**

2、**call的主要作用是扩展函数的作用域，即改变this指向。**





## 变量声明const和let

在ES6之前，我们都是用`var`关键字声明变量。无论声明在何处，都会被视为声明在**函数的最顶部**(不在函数内即在**全局作用域的最顶部**)。这就是函数**变量提升**例如:
```javascript
function aa() {
    if(flag) {
        var test = 'hello man'
    } else {
        console.log(test)
    }
  }
```

以上的代码实际上是：

```javascript
  function aa() {
    var test // 变量提升，函数最顶部
    if(flag) {
        test = 'hello man'
    } else {
        //此处访问 test 值为 undefined
        console.log(test)
    }
    //此处访问 test 值为 undefined
  }
```

所以不用关心flag是否为 `true` or `false`。实际上，无论如何 test 都会被创建声明。

接下来ES6主角登场：
 我们通常用 `let` 和 `const` 来声明，`let` 表示**变量**、`const` 表示**常量**。`let` 和 `const` 都是块级作用域。怎么理解这个块级作用域？

- 在一个函数内部
- 在一个代码块内部

> 说白了只要在**{}花括号内**的代码块即可以认为 `let` 和 `const` 的作用域。

看以下代码：

```javascript
  function aa() {
    if(flag) {
       let test = 'hello man'
    } else {
        //test 在此处访问不到
        console.log(test)
    }
  }
```

`let` 的作用域是在它所在当前代码块，但不会被提升到当前函数的最顶部。

再来说说 `const`
 `const` 声明的变量必须提供一个值，而且会被认为是常量，意思就是它的值被设置完成后就不能再修改了。

```javascript
    const name = 'lux'
    name = 'joe' // 再次赋值此时会报错
```

还有，如果 `const` 的是一个对象，对象所包含的值是可以被修改的。抽象一点儿说，就是对象所指向的地址不能改变，而变量成员是可以修改的。

```javascript
    const student = { name: 'cc' }
    // 没毛病
    student.name = 'yy'
    // 如果这样子就会报错了
    student  = { name: 'yy' }
```

说说TDZ(暂时性死区)，想必你早有耳闻。

```javascript
    {
        console.log(value) // 报错
        let value = 'lala'
    }
```

我们都知道，JS引擎扫描代码时，如果发现变量声明，用 `var` 声明变量时会将声明提升到函数或全局作用域的顶部。但是 `let` 或者 `const`，会将声明关进一个小黑屋也是TDZ(暂时性死区)，只有执行到变量声明这句语句时，变量才会从小黑屋被放出来，才能安全使用这个变量。

哦了，说一道面试题

```javascript
    var funcs = []
    for (var i = 0; i < 10; i++) {
        funcs.push(function() { console.log(i) })
    }
    funcs.forEach(function(func) {
        func()
    })
```

这样的面试题是大家很常见，很多同学一看就知道输出十次10
**因为变量i在循环的每次迭代中都被共享了 因此最后调用后的值会是打印10此10**
 但是如果我们想依次输出0到9呢？
 有两种解决方法
-立即执行函数
-使用let声明

```javascript
    // ES5知识，我们可以利用“立即调用函数”解决这个问题
    var funcs = []
    for (var i = 0; i < 10; i++) {
        funcs.push(
          (function(value) {
            return function() {
                console.log(value)
            }
        })(i)
      )
    }
    funcs.forEach(function(func) {
        func()
    })
  // 再来看看es6怎么处理的
    const funcs = []
    for (let i = 0; i < 10; i++) {
        funcs.push(function() {
            console.log(i)
        })
    }
    funcs.forEach(func => func())
```





## 变量声明提升

### 1、变量定义

可以使用var定义变量，变量如果没有赋值，那变量的初始值为`undefined`。

### 2、变量作用域

变量作用域指变量起作用的范围。变量分为全局变量和局部变量。全局变量在全局都拥有定义；而局部变量只能在函数内有效。 
 在函数体内，同名的局部变量或者参数的优先级会高于全局变量。也就是说，如果函数内存在和全局变量同名的局部变量或者参数，那么全局变量将会被局部变量覆盖。 
 **所有不使用var定义的变量都视为全局变量**

### 3、函数作用域和声明提前

JavaScript的函数作用是指在函数内声明的所有变量在函数体内始终是有定义的，也就是说**变量在声明之前已经可用**，所有这特性称为`声明提前（hoisting）`，即JavaScript函数里的所有声明（只是声明，但不涉及赋值）都被提前到函数体的顶部，而变量赋值操作留在原来的位置。如下面例子： 
 *注释：声明提前是在JavaScript引擎的预编译时进行，是在代码开始运行之前。*

```javascript
var scope = 'global';
function f(){
    console.log(scope);
    var scope = 'local';
    console.log(scope);
}123456
```

由于函数内声明提升，所以上面的代码实际上是这样的

```javascript
var scope = 'global';
function f(){
    var scope;    //变量声明提升到函数顶部
    console.log(scope);
    scope = 'local';    //变量初始化依然保留在原来的位置
    console.log(scope);
}1234567
```

经过这样变形之后，答案就就非常明显了。由于scope在第一个console.log(scope)语句之前就已经定义了，但是并没有赋值，因此此时scope的指是`undefined`.第二个console.log(scope)语句之前，scope已经完成赋值为’local’，所以输出的结果是`local`。





## 函数声明提升

### 1、函数的两种创建方式

- 函数声明
- 函数表达式

**函数声明语法**

```javascript
f('superman');
function f(name){
    console.log(name);
}1234
```

运行上面的程序，控制台能打印出`supemran`。 
 **函数表达式语法**

```javascript
f('superman');
var f= function(name){
    console.log(name);
}1234
```

运行上面的代码，会报错`Uncaught ReferenceError: f is not defined(…)`,错误信息显示说f没有被定义。 
  为什么同样的代码，函数声明和函数表达式存在着差异呢？ 
 这是因为，函数声明有一个非常重要的特征：`函数声明提升`，函数声明语句将会被提升到外部脚本或者外部函数作用域的顶部（是不是跟变量提升非常类似）。正是因为这个特征，所以可以把函数声明放在调用它的语句后面。如下面例子，最终的输出结果应该是什么？：

```javascript
var getName = function(){
    console.log(2);
}
function getName (){
    console.log(1);
}
getName();1234567
```

可能会有人觉得最后输出的结果是`1`。让我们来分析一下，这个例子涉及到了`变量声明提升`和`函数声明提升`。正如前面说到的函数声明提升，函数声明`function getName(){}`的声明会被提前到顶部。而函数表达式`var getName = function(){}`则表现出变量声明提升。因此在这种情况下，getName也是一个变量，因此这个变量的声明也将提升到底部，而变量的赋值依然保留在原来的位置。需要注意的是，函数优先，虽然函数声明和变量声明都会被提升，但是函数会首先被提升，然后才是变量。因此上面的函数可以转换成下面的样子:

```javascript
function getName(){    //函数声明提升到顶部
    console.log(1);
}
var getName;    //变量声明提升
getName = function(){    //变量赋值依然保留在原来的位置
    console.log(2);
}
getName();    // 最终输出：212345678
```

所以最终的输出结果是：`2`。在原来的例子中，函数声明虽然是在函数表达式后面，但由于函数声明提升到顶部，因此后面getName又被函数表达式的赋值操作给覆盖了，所以输出`2`。





## 字符串
ES6模板字符简直是开发者的福音啊，解决了 ES5 在字符串功能上的痛点。

第一个用途，基本的字符串格式化。将表达式嵌入字符串中进行拼接。用${}来界定。

```javascript
    //ES5 
    var name = 'lux'
    console.log('hello' + name)
    //es6
    const name = 'lux'
    console.log(`hello ${name}`) //hello lux
    
```

第二个用途，在ES5时我们通过反斜杠(\)来做多行字符串或者字符串一行行拼接。ES6反引号(``)直接搞定。

```javascript
    // ES5
    var msg = "Hi \
    man!
    "
    // ES6
    const template = `<div>
        <span>hello world</span>
    </div>`
```

对于字符串 ES6+ 当然也提供了很多厉害也很有意思的方法😊 说几个常用的。

```javascript
    // 1.includes：判断是否包含然后直接返回布尔值
    const str = 'hahay'
    console.log(str.includes('y')) // true

    // 2.repeat: 获取字符串重复n次
    const str = 'he'
    console.log(str.repeat(3)) // 'hehehe'
    //如果你带入小数, Math.floor(num) 来处理
    // s.repeat(3.1) 或者 s.repeat(3.9) 都当做成 s.repeat(3) 来处理

    // 3. startsWith 和 endsWith 判断是否以 给定文本 开始或者结束
    const str =  'hello world!'
    console.log(str.startsWith('hello')) // true
    console.log(str.endsWith('!')) // true
    
    // 4. padStart 和 padEnd 填充字符串，应用场景：时分秒
    setInterval(() => {
        const now = new Date()
        const hours = now.getHours().toString()
        const minutes = now.getMinutes().toString()
        const seconds = now.getSeconds().toString()
        console.log(`${hours.padStart(2, 0)}:${minutes.padStart(2, 0)}:${seconds.padStart(2, 0)}`)
    }, 1000)
```

关于模板字符串现在比较常出现的面试题有两道。同学们不妨写试试看？

- 模拟一个模板字符串的实现。

```javascript
    let address = '北京海淀区'
    let name = 'lala'
    let str = '${name}在${address}上班...'
    // 模拟一个方法 myTemplate(str) 最终输出 'lala在北京海淀区上班...'
    function myTemplate(str) {
        // try it
    }
    console.log(myTemplate(str)) // lala在北京海淀区上班...
```

- 实现标签化模板(自定义模板规则)。

```javascript
    const name = 'cc'
    const gender = 'male'
    const hobby = 'basketball'
    // 实现tag最终输出 '姓名：**cc**，性别：**male**，爱好：**basketball**'
    function tag(strings) {
        // do it
    }
    const str = tag`姓名：${name}，性别：${gender}，爱好：${hobby}`
    console.log(str) // '姓名：**cc**，性别：**male**，爱好：**basketball**'
```





## 函数

### 函数默认参数

在ES5我们给函数定义参数默认值是怎么样？

```javascript
    function action(num) {
        num = num || 200
        //当传入num时，num为传入的值
        //当没传入参数时，num即有了默认值200
        return num
    }
```

但细心观察的同学们肯定会发现，num传入为0的时候就是false，但是我们实际的需求就是要拿到num = 0，此时num = 200 明显与我们的实际想要的效果明显不一样

ES6为参数提供了默认值。在定义函数时便初始化了这个参数，以便在参数没有被传递进去时使用。

```javascript
    function action(num = 200) {
        console.log(num)
    }
    action(0) // 0
    action() //200
    action(300) //300
```

### 箭头函数

ES6很有意思的一部分就是函数的快捷写法。也就是箭头函数。

箭头函数最直观的三个特点。

- 不需要 `function` 关键字来创建函数
- 省略 `return` 关键字
- 继承当前上下文的 `this` 关键字

```javascript
//例如：
    [1,2,3].map(x => x + 1)
    
//等同于：
    [1,2,3].map((function(x){
        return x + 1
    }).bind(this))
```

**说个小细节。**

当你的函数**有且仅有**一个参数的时候，是可以省略掉括号的。当你函数返回**有且仅有**一个表达式的时候可以省略{} 和 return；例如:

```javascript
    var people = name => 'hello' + name
    //参数name就没有括号
```

作为参考

```javascript
    var people = (name, age) => {
        const fullName = 'hello' + name
        return fullName
    } 
    //如果缺少()或者{}就会报错
```

要不整一道笔试题？哈哈哈哈哈哈哈哈。我不管我先上代码了

```javascript
    // 请使用ES6重构以下代码
    
    var calculate = function(x, y, z) {
      if (typeof x != 'number') { x = 0 }
      if (typeof y != 'number') { y = 6 }

      var dwt = x % y
      var result

      if (dwt == z) { result = true }
      if (dwt != z) { result = false }
      
      return result
    }
    const calculate = (x, y, z) => {
      x = typeof x !== 'number' ? 0 : x
      y = typeof y !== 'number' ? 6 : y
      return x % y === z
    }
```





## 拓展的对象功能

对象初始化简写

ES5我们对于对象都是以**键值对**的形式书写，是有可能出现键值对重名的。例如：

```javascript
    function people(name, age) {
        return {
            name: name,
            age: age
        };
    }
```

键值对重名，ES6可以简写如下：

```javascript
    function people(name, age) {
        return {
            name,
            age
        };
    }
```

ES6 同样改进了为对象字面量方法赋值的语法。ES5为对象添加方法：

```javascript
    const people = {
        name: 'lux',
        getName: function() {
            console.log(this.name)
        }
    }
```

ES6通过省略冒号与 `function` 关键字，将这个语法变得更简洁

```javascript
    const people = {
        name: 'lux',
        getName () {
            console.log(this.name)
        }
    }
```

**ES6 对象提供了 `Object.assign()`这个方法来实现浅复制。**
 `Object.assign()` 可以把任意多个源对象自身可枚举的属性拷贝给目标对象，然后返回目标对象。第一参数即为目标对象。在实际项目中，我们为了不改变源对象。一般会把目标对象传为{}

```javascript
    const objA = { name: 'cc', age: 18 }
    const objB = { address: 'beijing' }
    const objC = {} // 这个为目标对象
    const obj = Object.assign(objC, objA, objB)

    // 我们将 objA objB objC obj 分别输出看看
    console.log(objA)   // { name: 'cc', age: 18 }
    console.log(objB) // { address: 'beijing' }
    console.log(objC) // { name: 'cc', age: 18, address: 'beijing' }
    console.log(obj) // { name: 'cc', age: 18, address: 'beijing' }

    // 是的，目标对象ObjC的值被改变了。
    // so，如果objC也是你的一个源对象的话。请在objC前面填在一个目标对象{}
    Object.assign({}, objC, objA, objB)
```





## 更方便的数据访问--解构

数组和对象是JS中最常用也是最重要表示形式。为了简化提取信息，ES6新增了**解构**，这是将一个数据结构分解为更小的部分的过程

ES5我们提取对象中的信息形式如下：

```javascript
    const people = {
        name: 'lux',
        age: 20
    }
    const name = people.name
    const age = people.age
    console.log(name + ' --- ' + age)
```

是不是觉得很熟悉，没错，在ES6之前我们就是这样获取对象信息的，一个一个获取。现在，解构能让我们从对象或者数组里取出数据存为变量，例如

```javascript
    //对象
    const people = {
        name: 'lux',
        age: 20
    }
    const { name, age } = people
    console.log(`${name} --- ${age}`)
    //数组
    const color = ['red', 'blue']
    const [first, second] = color
    console.log(first) //'red'
    console.log(second) //'blue'
```

要不来点儿面试题，看看自己的掌握情况？

```javascript
    // 请使用 ES6 重构一下代码

    // 第一题
    var jsonParse = require('body-parser').jsonParse

    // 第二题
    var body = request.body
    var username = body.username
    var password = body.password
    // 1.
    import { jsonParse } from 'body-parser'
    // 2. 
    const { body, body: { username, password } } = request
```





## Spread Operator 展开运算符

ES6中另外一个好玩的特性就是Spread Operator 也是三个点儿...接下来就展示一下它的用途。

组装对象或者数组

```javascript
    //数组
    const color = ['red', 'yellow']
    const colorful = [...color, 'green', 'pink']
    console.log(colorful) //[red, yellow, green, pink]
    
    //对象
    const alp = { fist: 'a', second: 'b'}
    const alphabets = { ...alp, third: 'c' }
    console.log(alphabets) //{ "fist": "a", "second": "b", "third": "c"
}
```

有时候我们想获取数组或者对象除了前几项或者除了某几项的其他项

```javascript
    //数组
    const number = [1,2,3,4,5]
    const [first, ...rest] = number
    console.log(rest) //2,3,4,5
    //对象
    const user = {
        username: 'lux',
        gender: 'female',
        age: 19,
        address: 'peking'
    }
    const { username, ...rest } = user
    console.log(rest) //{"address": "peking", "age": 19, "gender": "female"
}
```

对于 Object 而言，还可以用于组合成新的 Object 。(ES2017 stage-2 proposal) 当然如果有重复的属性名，右边覆盖左边

```javascript
    const first = {
        a: 1,
        b: 2,
        c: 6,
    }
    const second = {
        c: 3,
        d: 4
    }
    const total = { ...first, ...second }
    console.log(total) // { a: 1, b: 2, c: 3, d: 4 }
```





## import 和 export

import导入模块、export导出模块

```javascript
//全部导入
import people from './example'

//有一种特殊情况，即允许你将整个模块当作单一对象进行导入
//该模块的所有导出都会作为对象的属性存在
import * as example from "./example.js"
console.log(example.name)
console.log(example.age)
console.log(example.getName())

//导入部分
import {name, age} from './example'

// 导出默认, 有且只有一个默认
export default App

// 部分导出
export class App extend Component {};
```

以前有人问我，导入的时候有没有大括号的区别是什么。下面是我在工作中的总结：

```javascript
1.当用export default people导出时，就用 import people 导入（不带大括号）

2.一个文件里，有且只能有一个export default。但可以有多个export。

3.当用export name 时，就用import { name }导入（记得带上大括号）

4.当一个文件里，既有一个export default people, 又有多个export name 或者 export age时，导入就用 import people, { name, age } 

5.当一个文件里出现n多个 export 导出很多模块，导入时除了一个一个导入，也可以用import * as example
```





## 数组的一些函数

- `array.pop()`——移除最后一个并返回数组
- `array.push()`——从后加入一个返回数组长度
- `array.revere()`——反转数组顺序
- `array.shift()`——移除数组中第一个
- `array.slice(start,end)`——返回start至end的数组元素
- `array.sort()`——按大小排列数组元素顺序
- `array.splice(start,deleteCount,item...)`——移除元素，并用新的元素代替
- `array.unshift()`——从头加入一个返回数组长度





## 原型链





## **Class**

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过`class`关键字，可以定义类。

```javascript
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);
```

```javascript
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

**`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。**

**生成类的实例对象的写法，与 ES5 完全一样，也是使用`new`命令。如果忘记加上`new`，像函数那样调用`Class`，将会报错。**

**类中不存在变量提升**



## **Symbol**

ES6 引入了一种新的原始数据类型`Symbol`，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，前六种是：`undefined`、`null`、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。

**注意，`Symbol`函数前不能使用`new`命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。也就是说，由于 Symbol 值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。**

注意，`Symbol`函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的`Symbol`函数的返回值是不相等的。

```javascript
// 没有参数的情况
let s1 = Symbol();
let s2 = Symbol();

s1 === s2 // false

// 有参数的情况
let s1 = Symbol('foo');
let s2 = Symbol('foo');

s1 === s2 // false
```







## 其他杂项

判断一个变量是true或者flase 使用！！

箭头函数没有argument对象

\是转义字符

**对象通过引用传递，他永远不会被复制**

**凡是通过 new Function() 创建的对象都是函数对象，其他的都是普通对象。**

扩展运算符（ spread ）是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```javascript
console.log(...[1, 2, 3])
// 1 2 3
console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5
[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]
```





## Promise

> 在promise之前代码过多的回调或者嵌套，可读性差、耦合度高、扩展性低。通过Promise机制，扁平化的代码机构，大大提高了代码可读性；用同步编程的方式来编写异步代码，保存线性的代码逻辑，极大的降低了代码耦合性而提高了程序的可扩展性。

说白了就是用同步的方式去写异步代码。

发起异步请求

```javascript
    fetch('/api/todos')
      .then(res => res.json())
      .then(data => ({ data }))
      .catch(err => ({ err }));
```

今天看到一篇关于面试题的很有意思。

```javascript
    setTimeout(function() {
      console.log(1)
    }, 0);
    new Promise(function executor(resolve) {
      console.log(2);
      for( var i=0 ; i<10000 ; i++ ) {
        i == 9999 && resolve();
      }
      console.log(3);
    }).then(function() {
      console.log(4);
    });
    console.log(5);
```

当然以上promise的知识点，这个只是冰山一角。需要更多地去学习了解一下。





## Cookie、 LocalStorage 与 SessionStorage

### 基本概念

#### **Cookie**

Cookie 是小甜饼的意思。顾名思义，cookie 确实非常小，它的大小限制为4KB左右。它的主要用途有保存登录信息，比如你登录某个网站市场可以看到“记住密码”，这通常就是通过在 Cookie 中存入一段辨别用户身份的数据来实现的。

#### **localStorage**

localStorage 是 HTML5 标准中新加入的技术，它并不是什么划时代的新东西。早在 IE 6 时代，就有一个叫 userData 的东西用于本地存储，而当时考虑到浏览器兼容性，更通用的方案是使用 Flash。而如今，localStorage 被大多数浏览器所支持，如果你的网站需要支持 IE6+，那以 userData 作为你的 polyfill 的方案是种不错的选择。

#### **sessionStorage**

sessionStorage 与 localStorage 的接口类似，但保存数据的生命周期与 localStorage 不同。做过后端开发的同学应该知道 Session 这个词的意思，直译过来是“会话”。而 sessionStorage 是一个前端的概念，它只是可以将一部分数据在当前会话中保存下来，刷新页面数据依旧存在。但当页面关闭后，sessionStorage 中的数据就会被清空。

 

### 三者的异同

| 特性           | Cookie                                                       | localStorage                                                | sessionStorage                               |
| -------------- | ------------------------------------------------------------ | ----------------------------------------------------------- | -------------------------------------------- |
| 数据的生命期   | 一般由服务器生成，可设置失效时间。如果在浏览器端生成Cookie，默认是关闭浏览器后失效 | 除非被清除，否则永久保存                                    | 仅在当前会话下有效，关闭页面或浏览器后被清除 |
| 存放数据大小   | 4K左右                                                       | 一般为5MB                                                   |                                              |
| 与服务器端通信 | 每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题 | 仅在客户端（即浏览器）中保存，不参与和服务器的通信          |                                              |
| 易用性         | 需要程序员自己封装，源生的Cookie接口不友好                   | 源生接口可以接受，亦可再次封装来对Object和Array有更好的支持 |                                              |

### 应用场景

有了对上面这些差别的直观理解，我们就可以讨论三者的应用场景了。

因为考虑到每个 HTTP 请求都会带着 Cookie 的信息，所以 Cookie 当然是能精简就精简啦，比较常用的一个应用场景就是判断用户是否登录。针对登录过的用户，服务器端会在他登录时往 Cookie 中插入一段加密过的唯一辨识单一用户的辨识码，下次只要读取这个值就可以判断当前用户是否登录啦。曾经还使用 Cookie 来保存用户在电商网站的购物车信息，如今有了 localStorage，似乎在这个方面也可以给 Cookie 放个假了~

而另一方面 localStorage 接替了 Cookie 管理购物车的工作，同时也能胜任其他一些工作。比如HTML5游戏通常会产生一些本地数据，localStorage 也是非常适用的。如果遇到一些内容特别多的表单，为了优化用户体验，我们可能要把表单页面拆分成多个子页面，然后按步骤引导用户填写。这时候 sessionStorage 的作用就发挥出来了。







## Generators

生成器（ generator）是能返回一个**迭代器**的函数。生成器函数也是一种函数，最直观的表现就是比普通的function多了个星号*，在其函数体内可以使用yield关键字,有意思的是函数会在每个yield后暂停。

这里生活中有一个比较形象的例子。咱们到银行办理业务时候都得向大厅的机器取一张排队号。你拿到你的排队号，机器并不会自动为你再出下一张票。也就是说取票机“暂停”住了，直到下一个人再次唤起才会继续吐票。

OK。说说迭代器。当你调用一个generator时，它将返回一个迭代器对象。这个迭代器对象拥有一个叫做next的方法来帮助你重启generator函数并得到下一个值。next方法不仅返回值，它返回的对象具有两个属性：done和value。value是你获得的值，done用来表明你的generator是否已经停止提供值。继续用刚刚取票的例子，每张排队号就是这里的value，打印票的纸是否用完就这是这里的done。

```javascript
    // 生成器
    function *createIterator() {
        yield 1;
        yield 2;
        yield 3;
    }
    
    // 生成器能像正规函数那样被调用，但会返回一个迭代器
    let iterator = createIterator();
    
    console.log(iterator.next().value); // 1
    console.log(iterator.next().value); // 2
    console.log(iterator.next().value); // 3
```

那生成器和迭代器又有什么用处呢？

围绕着生成器的许多兴奋点都与异步编程直接相关。异步调用对于我们来说是很困难的事，我们的函数并不会等待异步调用完再执行，你可能会想到用回调函数，（当然还有其他方案比如Promise比如Async/await）。

生成器可以让我们的代码进行等待。就不用嵌套的回调函数。使用generator可以确保当异步调用在我们的generator函数运行一下行代码之前完成时暂停函数的执行。

那么问题来了，咱们也不能手动一直调用next()方法，你需要一个能够调用生成器并启动迭代器的方法。就像这样子的

```javascript
    function run(taskDef) { //taskDef即一个生成器函数

        // 创建迭代器，让它在别处可用
        let task = taskDef();

        // 启动任务
        let result = task.next();
    
        // 递归使用函数来保持对 next() 的调用
        function step() {
    
            // 如果还有更多要做的
            if (!result.done) {
                result = task.next();
                step();
            }
        }
    
        // 开始处理过程
        step();
    
    }
```

> 生成器与迭代器最有趣、最令人激动的方面，或许就是可创建外观清晰的异步操作代码。你不必到处使用回调函数，而是可以建立貌似同步的代码，但实际上却使用 yield 来等待异步操作结束。





​																			     整合By—GuoYF