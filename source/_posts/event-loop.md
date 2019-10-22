---
title: event-loop
date: 2019-09-02 16:46:48
categories:
  - 前端
  - Js
tags: js
photos:
  - 'https://yunpengGit.github.io/code/mock-img/fengbaobao/baoer2.jpg'
---

#### 事件循环(event-loop)

> 主线程从任务队列不断的循环读取执行事件，这个机制叫称为事件循环。

1. 宏任务（macrotask ）: script（一般执行逻辑，方法调用，console）, setTimeout, setInterval, I/O, UI rendering
2. 微任务（microtask ）: process.nextTick（Nodejs）, Promises,

JS 引擎会将所有的任务按照类别分到这两队列中，

主线程不断从任务队列按顺序读取执行任务，首先看主线程代码队列有没有需要执行的代码，有则执行代码，没有则检查**微任务**队列是否为空（函数执行栈为空），不为空则会执行完所有**微任务**，再进行下一个循环去任务队列读取下一个任务执行。`

> 示例解析

1. 示例

   ```js
   setTimeout(() => {
       //执行后 回调一个宏事件
       console.log('内层宏事件3')
   }, 0)
   console.log('外层宏事件1');
   
   new Promise((resolve) => {
       console.log('外层宏事件2');
       resolve()
   }).then(() => {
       console.log('微事件1');
   }).then(()=>{
       console.log('微事件2')
   })
   
   /*执行结果
   
       外层宏事件1
       外层宏事件2
       微事件1
       微事件2
       Promise {<resolved>: undefined}    //new Promise() 返回一个promise对象
       内层宏事件3
   */ 
   
   /*
   1.首先js进入第一个宏任务进入主线程, 遇到 setTimeout  分发到宏任务Event Queue中
   2.遇到 console.log() 直接执行，输出 "外层宏事件1"
   3.遇到 Promise， new Promise 执行，执行参数箭头函数输出 外层宏事件2"
   4.接着执行then，任务发到 微任务 Event Queue
   5.第一轮宏任务执行结束，查看执行微任务队列
   6.输出"微事件1" "微事件1"，第一轮微任务执行结束
   7.第二轮宏任务开始执行 ，输出 "内层宏事件3"
    */
   ```

2. 示例

   ```js
   async function async1() {
       console.log( 'async1 start' )
       await async2()
       console.log( 'async1 end' )
   }
   async function async2() {
       console.log( 'async2' )
   }
   async1()
   console.log( 'script start' )
   
   /*执行结果
       
       async1 start
       async2
       script start
       async1 end
   */
   
   /*解析
   
   1.js进入主线程进入第一个宏任务，遇到async1，async2，函数声明
   2.遇到 async1() 调用执行 输出 "async1 start"
   3.遇到 await async2()，await 就立刻让出线程,阻塞后面的代码 等待 async2执行结束，输出 "async2"
   4.await会阻塞后面的代码,先执行async外面的同步代码, 输出 'script start'
   如果一个 Promise 被传递给一个 await 操作符，await 将等待 Promise 正常处理完成并返回其处理结果。
   5.同步代码执行完毕后,在回到async内部，执行输出 "asnyc1 end"
   */
   ```

3. 示例

   ```js
   async function async1() {
       console.log( 'async1 start' )
       await async2()
       console.log( 'async1 end' )
   }
   async function async2() {
       console.log( 'async2' )
   }
   console.log( 'script start' )
   setTimeout( function () {
       console.log( 'setTimeout' )
   }, 0 )
   async1();
   new Promise( function ( resolve ) {
       console.log( 'promise1' )
       resolve();
   } ).then( function () {
       console.log( 'promise2' )
   } )
   console.log( 'script end' )
   
   /*输出结果
   script start
   async1 start
   async2
   promise1
   script end
   promise2
   async1 end
   setTimeout
   */
   /*结果分析
   1.await async2()调用输出 "async2",之后，
   2.继续执行最外层宏任务队列，接着执行最外层微任务队列，
   3.然后在执行await async2()之后的逻辑
   */
   ```

   

4. 示例

   ```js
   setTimeout(()=>{
       console.log("setTimeout1");
       Promise.resolve().then(data => {
           console.log(222);
       });
   });
   setTimeout(()=>{
       console.log("setTimeout2");
   });
   Promise.resolve().then(data=>{
       console.log(111);
   });
   
   /*输出结果
       111
       setTimeout1
       222
       setTimeout2
   */
   ```

5. 示例

   ```js
   console.log('script start');
   
   setTimeout(function () {
       console.log('setTimeout---0');
   }, 0);
   
   setTimeout(function () {
       console.log('setTimeout---200');
       setTimeout(function () {
           console.log('inner-setTimeout---0');
       });
       Promise.resolve().then(function () {
           console.log('promise5');
       });
   }, 200);
   
   Promise.resolve().then(function () {
       console.log('promise1');
   }).then(function () {
       console.log('promise2');
   });
   Promise.resolve().then(function () {
       console.log('promise3');
   });
   console.log('script end');
   
   /*输出结果
   script start
   script end
   promise1
   promise3
   promise2
   setTimeout---0
   setTimeout---200
   promise5
   inner-setTimeout---0
   */
   
   /*结果分析
   
   1.js进入主线程进入第一个宏任务，遇到console 输出 "script start"
   2.遇到第一个 setTimeout 放入宏任务 task queue，等待执行
   3.遇到第二个 setTimeout 放入宏任务 task queue，等待执行
   4.遇到第一个 Promise.resolve().then 放入微任务 task queue，等待执行
   5.遇到第二个 Promise.resolve().then 放入微任务 task queue，等待执行
   6.遇到 conmsole 输出 "script end",第一个宏任务执行结束
   
   7.检查执行微任务 4,5 执行 输出 "promise1 promise3 promise2" 第一个微任务结束
   	a.由于Promise.resolve().then()返回的还是一个promise对象，所以第二个then也是一个微任务，在调用第二个微任务是将其添加到微任务 task queue 中等待执行
   	
   8.宏任务执行 第一个 setTimeout 输出 "setTimeout---0"
   9.第二个 setTimeout 等待 200 毫秒 执行，
   10.遇到  console.log 输出 "setTimeout---200"
   11.遇到 setTiemeout 放入宏任务 task queue，等待执行
   12.遇到 Promise.resolve().then 放入微任务 task queue，等待执行
   13.第二个 setTimeout 内部第一个宏任务 执行结束 查看微任务并执行
   14.微任务 输出 "promise5"，微任务执行结束 查看宏任务并执行
   15.宏任务 输出 "inner-setTimeout---0",所有任务执行结束
   */
   ```

   



