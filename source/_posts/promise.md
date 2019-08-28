---
layout: js
title: Promise 到 Generator 再到 Async / Await
date: 2019-08-23 16:50:03
categories:
  - 前端
  - Js
tags: js
photos:
  - 'https://yunpengGit.github.io/code/mock-img/fengbaobao/baoerjierichang1.jpg'
---

#### 1. js 异步处理的

异步最早的解决方案是回调函数，如事件的回调，setInterval/setTimeout 中的回调。但是回到函数有一个很常见的问题，回调地狱。

为了解决回调地狱问题，社区提出了 promise 解决方案，es6 将其写进了语言标准，promise 解决了回调地狱问题，但是 promise 也存在一些问题，如错误能被 try catch，而且使用 promise 的链式调用，其实没有从根本上解决回调地狱的问题，只是换了一种写法。

es6 中引入了 Generator 函数，Generator 是一种异步编程解决方案，Generator 函数是协程在 es6 的实现，最大的特点就是可以交出函数的执行权，Generator 函数可以看出是异步任务的容器，需要暂停的地方，都用 yield 语句注明，但是 Genrator 使用起来比较复杂。

es7 又提出了新的一步解决方案：async / await, async 是 Generator 的函数语法糖，async/await 使得异步代码看起来像同步，异步编程发展的目标就是让异步逻辑的代码看起来像同步一样。

> 回调函数： callback

```javascript
//node读取文件
fs.readFile(xx,'utf-8',function(err. data) {
})
```

回调函数的使用场景（包括但不限于）：

1. 事件回调
2. Node API
3. setTimeout 、setInterval 中的回调函数

异步回调嵌套会导致代码难以维护，并且不方便统一处理错误，不能 try catch 和回调地狱（如读取 A 文本的类容，再根据 A 文本内容读取 B 文本，再根据 B 文本内容读取 C..)

回调地狱：

```javascript
fs.readFile(A, 'utf-8', function() {
  fs.readFile(B, 'utf-8', function() {
    fs.readFile(C, 'utf-8', function() {
      //...
    })
  })
})
```

#### 2. Promise

promise 主要解决回调地狱的问题，promise 最早由社区提出和实现，es6 将其写进语言标准，统一用法，原生提供 Promise 对象；

Promise 解决回调地狱问题：

```javascript
function read(url) {
  return new Promise((resolve, reject) => {
    fs.readFile(url, 'utf-8', (err, data) => {
      if (err) reject(data)
      resolve(data)
    })
  })
}

read(A)
  .then(data => {
    return read(B)
  })
  .then(data => {
    return read(C)
  })
  .catch(reason => {
    console.log(reason)
  })
```

> 思考：

在 Promise之前，你是如何处理异步并发问题的，假设有这样一个需求：读取三个文件内容，都读取成功后，输出最终的结果。有了 Promise 之后，又如何处理呢？[示例](https://github.com/yunpengGit/code/blob/master/js-example/promise之前处理异步.js)

#### 3. Generator

Generator 函数是 ES6 提供的一种异步编程解决方案，整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用 yield 语句注明。

Generator 函数一般配合 yield 或 Promise 使用。Generator 函数返回的是迭代器。

```javascript
function* gen() {
  let a = yield 11
  console.log(a)
  let b = yield 22
  console.log(b)
  let c = yield 33
  console.log(c)
  let d = yield 44
  console.log(d)
}
let t = gen()
//next方法可以带一个参数，该参数就会被当作上一个yield表达式的返回值
//next 调用后返回一个对象{value: yield 后面的值, done: false}
t.next(1) //第一次调用next函数时，传递的参数无效 返回{value:11,done:false}
t.next(2) //a输出2; 返回{value:22,done:false}
t.next(3) //b输出3;
t.next(4) //c输出4;
t.next(5) //d输出5; 返回{value: undefined, done: true}
```

#### 4. Async / Await

async 关键字，**定义的函数是异步函数，返回 promise 对象**

await 操作符用于等待一个 Promise 对象, **只能在异步函数 async function 内部使用**.

```javascript
const fun1 = () => console.log('叭叭叭1')
const fun2 = () => console.log('叭叭叭2')

const fun = async () => {
  // async关键字，定义的函数是异步函数，返回promise对象
  await fun1()
}
fun().then(fun2())
console.log('叭叭叭')
```

> ### async/await 的实现原理

async/await 就是 Generator 的语法糖，就是将 Generator 函数的星号（\*）替换成 async，将 yield 替换成 await。

1. async 函数内置执行器，函数调用之后，会自动执行(Generator 需要手动调用函数 next())，输出最后结果。
2. 更好的语义化，async 和 await，对比星号和 yield。async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。
3. 更广的适用性。co 模块约定，yield 命令后面只能是 Thunk 函数或 Promise 对象，而 async 函数的 await 命令后面，可以是 Promise 对象和原始类型的值。
4. 返回值是 Promise，**<span style="color:purple">async 函数的返回值是 Promise 对象在</span>，Generator 的返回值是 Iterator**，Promise 对象使用起来更加方便。

> async 函数的实现原理，将 Generator 函数和自动执行器，包装在函数里[示例](https://github.com/yunpengGit/code/blob/master/js-example/async.js)

> 使用 async/await 需要注意什么

1. await 命令后面的Promise对象，运行结果可能是 rejected，此时等同于 async 函数返回的 Promise 对象被reject。因此需要加上错误处理，可以给每个 await 后的 Promise 增加 catch 方法；也可以将 await 的代码放在 `try...catch` 中。

2. 多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。

   ```javascript
   //下面两种写法都可以同时触发
   //法一
   async function f1() {
       await Promise.all([
           new Promise((resolve) => {
               setTimeout(resolve, 600);
           }),
           new Promise((resolve) => {
               setTimeout(resolve, 600);
           })
       ])
   }
   //法二
   async function f2() {
       let fn1 = new Promise((resolve) => {
               setTimeout(resolve, 800);
           });
       
       let fn2 = new Promise((resolve) => {
               setTimeout(resolve, 800);
           })
       await fn1;
       await fn2;
   }
   ```

3. await命令只能用在async函数之中，如果用在普通函数，会报错。

4. async 函数可以保留运行堆栈。

   ```javascript
   /**
   * 函数a内部执行异步任务b()。当b()执行的时候，函数a()不会中断，而是继续执行。
   * 等到b()执行结束，可能a()已执行结束，b()所在的上下文环境已经消失了。
   * 如果b()或c()报错，错误堆栈将不包括a()。
   */
   function b() {
       return new Promise((resolve, reject) => {
           console.log('b中的输出')
           setTimeout((resolve)=> {
               resolve('b-promise实例')
           }, 200,resolve)
       });
   }
   function c() {
       console.log('c中的输出')
       throw Error(10);
   }
   const a = () => {
       //异步任务b
       b().then((data) => {
       	console.log(data);
           c()
       });
       //同步输出
       console.log('a中同步输出')
       //没有返回默认返回 undefined;
       return 'a执行结束'
   };
   a();
   /**
       b中的输出
       a中同步输出
       "a执行结束" a()执行结束的返回
       b-promise实例
       c中的输出
       Uncaught (in promise) Error: 10
   */
   
   /**
   * 改成async函数
   */
   const m = async () => {
       var b_result=await b();
       console.log(b_result)
       c();
   };
   m();
   /*
   b中的输出
   Promise {<pending>} a()执行结束的返回(async 后的函数执行返回一个promise对象)
   b-promise实例
   c中的输出
   */
   ```

   

