# Promise详解

## 1.Promise的基本使用

```javascript
const promise = new Promise((resolve, reject) => {
   resolve('success')
   reject('err')
})

promise.then(value => {
  console.log('resolve', value)
}, reason => {
  console.log('reject', reason)
})
```

Promise 是一个类，在执行这个类的时候会传入一个执行器，这个执行器会立即执行

Promise 会有三种状态

- Pending 等待
- Fulfilled 完成
- Rejected 失败

状态只能由 Pending --> Fulfilled 或者 Pending --> Rejected，且一但发生改变便不可二次修改；

Promise 中使用 resolve 和 reject 两个函数来更改状态；

then 方法内部做但事情就是状态判断

- 如果状态是成功，调用成功回调函数
- 如果状态是失败，调用失败回调函数

## 2.手写promise

### 版本一

```javascript
//promise三个状态的常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
    constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
	executor(this.resolve, this.reject) 
  }
  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;
  // 用箭头函数就可以让this指向当前实例对象,否则this指向window或者undefined
  // 更改成功后的状态
    resolve=(value)=>{
        if(this.status === PENDING){
            this.status = FULFILLED
            this.value = value
        }
    }
  // 更改失败后的状态
    reject=(reason)=>{
        if(this.status === PENDING){
            this.status = REJECTED
            this.reason = reason
        }
    }
    then(onFulfilled, onRejected){
        if(this.status === FULFILLED){
            // 调用成功回调，并且把值返回
            onFulfilled(this.value)
        }
        if(this.status === REJECTED){
            // 调用失败回调，并且把原因返回
            onRejected(this.reason)
        }
    }
}
module.exports = MyPromise;
```

这个版本是可以使用，但是当涉及异步处理时如：

```js
let testPromise = MyPromise((resolve,reject)=>{
    setTimeout(()=>{
        resolve('success')
    },100)
})
testPromise.then(res=>console.log(res))
```

但是打印出的值为空，因为我们的这个then是同步执行的，在执行的时候并没有调用resolve方法

### 版本二

```javascript
//promise三个状态的常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
    constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
	executor(this.resolve, this.reject) 
  }
  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;
  // 存储成功回调函数
  onFulfilledCallback = null;
  // 存储失败回调函数
  onRejectedCallback = null;
  // 用箭头函数就可以让this指向当前实例对象,否则this指向window或者undefined
  // 更改成功后的状态
    resolve=(value)=>{
        if(this.status === PENDING){
            this.status = FULFILLED
            this.value = value
            // 判断成功回调是否存在，如果存在就调用
            this.onFulfilledCallback && this.onFulfilledCallback(value);
        }
    }
  // 更改失败后的状态
    reject=(reason)=>{
        if(this.status === PENDING){
            this.status = REJECTED
            this.reason = reason
            // 判断成功回调是否存在，如果存在就调用
            this.onRejectedCallback && this.onRejectedCallback(reason);
        }
    }
    then(onFulfilled, onRejected){
        if(this.status === FULFILLED){
            // 调用成功回调，并且把值返回
            onFulfilled(this.value)
        }else if(this.status === REJECTED){
            // 调用失败回调，并且把原因返回
            onRejected(this.reason)
        }else if(this.status === PENDING){
            this.onRejectedCallback = onRejected 
            this.onFulfilledCallback = onFulfilled
        }
    }
}
module.exports = MyPromise;
```

现在在执行就会在定时器完成后进行函数调用了

但是如果then方法多次调用，他只能处理最后一个函数

这里我们可以将存储回调函数定义成array

```javascript
//promise三个状态的常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
    constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
	executor(this.resolve, this.reject) 
  }
  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;
  // 存储成功回调函数
  onFulfilledCallback = [];
  // 存储失败回调函数
  onRejectedCallback = [];
  // 用箭头函数就可以让this指向当前实例对象,否则this指向window或者undefined
  // 更改成功后的状态
    resolve=(value)=>{
        if(this.status === PENDING){
            this.status = FULFILLED
            this.value = value
            while (this.onFulfilledCallbacks.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onFulfilledCallbacks.shift()(value)
    		}
        }
    }
  // 更改失败后的状态
    reject=(reason)=>{
        if(this.status === PENDING){
            this.status = REJECTED
            this.reason = reason
            while (this.onRejectedCallback.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onRejectedCallback.shift()(reason)
    		}
        }
    }
    then(onFulfilled, onRejected){
        if(this.status === FULFILLED){
            // 调用成功回调，并且把值返回
            onFulfilled(this.value)
        }else if(this.status === REJECTED){
            // 调用失败回调，并且把原因返回
            onRejected(this.reason)
        }else if(this.status === PENDING){
            this.onRejectedCallback.push(onRejected)
            this.onFulfilledCallback.push(onFulfilled)
        }
    }
}
module.exports = MyPromise;
```

现在就会执行所有的then了

但是好像then是不可以链式调用的，现在就试试怎么实现这个

### 版本三

```javascript
//promise三个状态的常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
    constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
	executor(this.resolve, this.reject) 
  }
  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;
  // 存储成功回调函数
  onFulfilledCallback = [];
  // 存储失败回调函数
  onRejectedCallback = [];
  // 用箭头函数就可以让this指向当前实例对象,否则this指向window或者undefined
  // 更改成功后的状态
    resolve=(value)=>{
        if(this.status === PENDING){
            this.status = FULFILLED
            this.value = value
            while (this.onFulfilledCallbacks.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onFulfilledCallbacks.shift()(value)
    		}
        }
    }
  // 更改失败后的状态
    reject=(reason)=>{
        if(this.status === PENDING){
            this.status = REJECTED
            this.reason = reason
            while (this.onRejectedCallback.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onRejectedCallback.shift()(reason)
    		}
        }
    }
    then(onFulfilled, onRejected){
        if(this.status === FULFILLED){
            // 调用成功回调，并且把值返回
            onFulfilled(this.value)
            const x = onFulfilled(this.value);
            // 传入 resolvePromise 集中处理
            resolvePromise(x, resolve, reject);
        }else if(this.status === REJECTED){
            // 调用失败回调，并且把原因返回
            onRejected(this.reason)
        }else if(this.status === PENDING){
            this.onRejectedCallback.push(onRejected)
            this.onFulfilledCallback.push(onFulfilled)
        }
    }
	function resolvePromise(x, resolve, reject) {
      // 判断x是不是 MyPromise 实例对象
      if(x instanceof MyPromise) {
        // 执行 x，调用 then 方法，目的是将其状态变为 fulfilled 或者 rejected
        // x.then(value => resolve(value), reason => reject(reason))
        // 简化之后
        x.then(resolve, reject)
      } else{
        // 普通值
        resolve(x)
      }
	}
}
module.exports = MyPromise;
```

现在是可以进行链式调用。先判断一下then里的函数是否为异步，如果异步返回一个Then方法，否则直接返回值

但如果返回的是自己的话，就会报错，使用原生的也会报错

### 版本四

```javascript

//promise三个状态的常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
    constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
	executor(this.resolve, this.reject) 
  }
  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;
  // 存储成功回调函数
  onFulfilledCallback = [];
  // 存储失败回调函数
  onRejectedCallback = [];
  // 用箭头函数就可以让this指向当前实例对象,否则this指向window或者undefined
  // 更改成功后的状态
    resolve=(value)=>{
        if(this.status === PENDING){
            this.status = FULFILLED
            this.value = value
            while (this.onFulfilledCallbacks.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onFulfilledCallbacks.shift()(value)
    		}
        }
    }
  // 更改失败后的状态
    reject=(reason)=>{
        if(this.status === PENDING){
            this.status = REJECTED
            this.reason = reason
            while (this.onRejectedCallback.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onRejectedCallback.shift()(reason)
    		}
        }
    }
    then(onFulfilled, onRejected){
        const promise2 = new MyPromise((resolve, reject) => {
          if (this.status === FULFILLED) {
            // 创建一个微任务等待 promise2 完成初始化
            queueMicrotask(() => {
                const x = onFulfilled(this.value);
                // resolvePromise 集中处理，将 promise2 传入
                resolvePromise(promise2, x, resolve, reject);
            }
          } else if (this.status === REJECTED) {
            onRejected(this.reason);
          } else if (this.status === PENDING) {
            this.onFulfilledCallbacks.push(onFulfilled);
            this.onRejectedCallbacks.push(onRejected);
          }
        }) 
        return promise2;
      }
    }
	function resolvePromise(x, resolve, reject) {
     // 如果相等了，说明return的是自己，抛出类型错误并返回
	 if (promise2 === x) {
        return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
      }
      // 判断x是不是 MyPromise 实例对象
      if(x instanceof MyPromise) {
        // 执行 x，调用 then 方法，目的是将其状态变为 fulfilled 或者 rejected
        // x.then(value => resolve(value), reason => reject(reason))
        // 简化之后
        x.then(resolve, reject)
      } else{
        // 普通值
        resolve(x)
      }
	}
}
module.exports = MyPromise;
```

现在应该是一个比较优秀的promise

现在加上异常捕获相关代码

### 版本五

```javascript
//promise三个状态的常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
    constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
    try{
        executor(this.resolve, this.reject) 
    }catch(error){
        // 如果有错误，就直接执行 reject
        this.reject(error)
    }
  }
  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;
  // 存储成功回调函数
  onFulfilledCallback = [];
  // 存储失败回调函数
  onRejectedCallback = [];
  // 用箭头函数就可以让this指向当前实例对象,否则this指向window或者undefined
  // 更改成功后的状态
    resolve=(value)=>{
        if(this.status === PENDING){
            this.status = FULFILLED
            this.value = value
            while (this.onFulfilledCallbacks.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onFulfilledCallbacks.shift()(value)
    		}
        }
    }
  // 更改失败后的状态
    reject=(reason)=>{
        if(this.status === PENDING){
            this.status = REJECTED
            this.reason = reason
            while (this.onRejectedCallback.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onRejectedCallback.shift()(reason)
    		}
        }
    }
    then(onFulfilled, onRejected){
        const promise2 = new MyPromise((resolve, reject) => {
          if (this.status === FULFILLED) {
            // 创建一个微任务等待 promise2 完成初始化
            queueMicrotask(() => {
                try{
                    const x = onFulfilled(this.value);
                    // resolvePromise 集中处理，将 promise2 传入
                    resolvePromise(promise2, x, resolve, reject);   
                }catch(err){
                    reject(err)
                }
            }
          } else if (this.status === REJECTED) {
            onRejected(this.reason);
          } else if (this.status === PENDING) {
            this.onFulfilledCallbacks.push(onFulfilled);
            this.onRejectedCallbacks.push(onRejected);
          }
        }) 
        return promise2;
      }
    }
	function resolvePromise(x, resolve, reject) {
     // 如果相等了，说明return的是自己，抛出类型错误并返回
	 if (promise2 === x) {
        return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
      }
      // 判断x是不是 MyPromise 实例对象
      if(x instanceof MyPromise) {
        // 执行 x，调用 then 方法，目的是将其状态变为 fulfilled 或者 rejected
        // x.then(value => resolve(value), reason => reject(reason))
        // 简化之后
        x.then(resolve, reject)
      } else{
        // 普通值
        resolve(x)
      }
	}
}
module.exports = MyPromise;
```

现在需要把REJECTED以及PENDING状态进行一下异常还有链式的优化,还有就是then里面的传参是可以不传的，需要进行判断一下

```javascript
//promise三个状态的常量
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
    constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
    try{
        executor(this.resolve, this.reject) 
    }catch(error){
        // 如果有错误，就直接执行 reject
        this.reject(error)
    }
  }
  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;
  // 存储成功回调函数
  onFulfilledCallback = [];
  // 存储失败回调函数
  onRejectedCallback = [];
  // 用箭头函数就可以让this指向当前实例对象,否则this指向window或者undefined
  // 更改成功后的状态
    resolve=(value)=>{
        if(this.status === PENDING){
            this.status = FULFILLED
            this.value = value
            while (this.onFulfilledCallbacks.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onFulfilledCallbacks.shift()(value)
    		}
        }
    }
  // 更改失败后的状态
    reject=(reason)=>{
        if(this.status === PENDING){
            this.status = REJECTED
            this.reason = reason
            while (this.onRejectedCallback.length) {
            	// Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
      	    	this.onRejectedCallback.shift()(reason)
    		}
        }
    }
    then(onFulfilled, onRejected){
        onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
        onRejected = typeof onRejected === 'function' ? onRejected : reason => {throw reason};
        const promise2 = new MyPromise((resolve, reject) => {
          if (this.status === FULFILLED) {
            // 创建一个微任务等待 promise2 完成初始化
            queueMicrotask(() => {
                try{
                    const x = onFulfilled(this.value);
                    // resolvePromise 集中处理，将 promise2 传入
                    resolvePromise(promise2, x, resolve, reject);   
                }catch(err){
                    reject(err)
                }
            }
          } else if (this.status === REJECTED) {
            queueMicrotask(() => {
                try{
                    const x = onRejected(this.reason);
                    // resolvePromise 集中处理，将 promise2 传入
                    resolvePromise(promise2, x, resolve, reject);   
                }catch(err){
                    reject(err)
                }
            }
          } else if (this.status === PENDING) {
            this.onFulfilledCallbacks.push(()=>{
                queueMicrotask(() => {
                    try{
                        const x = onFulfilled(this.value);
                        // resolvePromise 集中处理，将 promise2 传入
                        resolvePromise(promise2, x, resolve, reject);   
                    }catch(err){
                        reject(err)
                    }
                }
            });
            this.onRejectedCallbacks.push(()=>{
                queueMicrotask(() => {
                    try{
                        const x = onRejected(this.reason);
                        // resolvePromise 集中处理，将 promise2 传入
                        resolvePromise(promise2, x, resolve, reject);   
                    }catch(err){
                        reject(err)
                    }
                }
            });
          }
        }) 
        return promise2;
      }
    }
	function resolvePromise(x, resolve, reject) {
     // 如果相等了，说明return的是自己，抛出类型错误并返回
	 if (promise2 === x) {
        return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
      }
      // 判断x是不是 MyPromise 实例对象
      if(x instanceof MyPromise) {
        // 执行 x，调用 then 方法，目的是将其状态变为 fulfilled 或者 rejected
        // x.then(value => resolve(value), reason => reject(reason))
        // 简化之后
        x.then(resolve, reject)
      } else{
        // 普通值
        resolve(x)
      }
	}
}
module.exports = MyPromise;
```

