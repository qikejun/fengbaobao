---
title: js拷贝
date: 2019-11-11 17:04:18
categories:
  - js
  - 拷贝
tags: js
photos:
  - 'https://yunpengGit.github.io/code/mock-img/fengbaobao/baoer7.jpg'
---
# js 拷贝

### 浅拷贝

> 创建一个新对象，这个对象有原生对象属性值的一个精确拷贝。如果属性是基本类型（undefined，null，String，Number，Boolean），拷贝的就是基本类型的值，如果属性是引用类型（ Object、Array、Function、Date 等对象类型 ），拷贝的是内存地址（指针）

#### Array

```js
var arr1 = [1, 2], arr2 = arr1.slice();
console.log(arr1); //[1, 2]
console.log(arr2); //[1, 2]

arr2[0] = 3; //修改arr2
console.log(arr1); //[1, 2]
console.log(arr2); //[3, 2]


var arr1 = [1, 2, [3, 4]], arr2 = arr1.slice();
console.log(arr1); //[1, 2, [3, 4]]
console.log(arr2); //[1, 2, [3, 4]]

arr2[2][1] = 5; 
console.log(arr1); //[1, 2, [3, 5]]
console.log(arr2); //[1, 2, [3, 5]]

```

**concat**、**Array.from()**  也是浅拷贝

#### Object

```js
let target = {};
let source = { a: { b: 2 } };
Object.assign(target, source);
console.log(target); // { a: { b: 10 } }; 指针未变，实际内容已改变（浅拷贝）只拷贝了指针
source.a.b = 10;
console.log(source); // { a: { b: 10 } };
console.log(target); // { a: { b: 10 } };
```

#####  **Object.assign**  注意：

1. 不会拷贝对象继承的属性

2. 不可枚举的属性

3. 属性的数据属性/访问器属性

4. 可以拷贝Symbol类型

   ```js
   let obj1 = {
       a:{
           b:1
       },
       sym:Symbol(1)
   };
   Object.defineProperty(obj1,'c',{
       value:'不可枚举属性',
       enumerable:false
   });
   let obj2 = {};
   Object.assign(obj2,obj1)
   obj1.a.b = 2;
   console.log('obj1',obj1); 
   //{a: {b: 2}, sym: Symbol(1), c: "不可枚举属性"}
   console.log('obj2',obj2); 
   //{a: {b: 2}, sym: Symbol(1)}
   ```

    Symbol类型可以拷贝，不可枚举的属性被忽略,改变了obj1.a.b的值，obj2.a.b的值也会跟着改变

   ```js
   let obj = {a:1,b:{c:1}}
   let obj2 = {...obj};
   obj.a=2;
   console.log(obj); //{a:2,b:{c:1}}
   console.log(obj2); //{a:1,b:{c:1}}
   
   obj.b.c = 2;
   console.log(obj); //{a:2,b:{c:2}}
   console.log(obj2); //{a:1,b:{c:2}}
   ```

##### 浅拷贝实现

```js
var shallowClone = function(target) {
        if (typeof target === 'object' && target !== null) {
            var cloneTarget = Array.isArray(target) ? []: {};
            for (var prop in target) {
                if (target.hasOwnProperty(prop)) {
                    cloneTarget[prop] = target[prop];
                }
            }
            return cloneTarget;
        } else {
            return target;
        }
    }
```

### 深拷贝

#####  **JSON.parse(JSON.stringify(obj)) **

````js
var obj1 = {
    x: 1, 
    y: {
        m: 1
    }
};
var obj2 = JSON.parse(JSON.stringify(obj1));
console.log(obj1) //{x: 1, y: {m: 1}}
console.log(obj2) //{x: 1, y: {m: 1}}

obj2.y.m = 2; //修改obj2.y.m
console.log(obj1) //{x: 1, y: {m: 1}}
console.log(obj2) //{x: 2, y: {m: 2}}
````

- 转换值如果有 toJSON() 方法，该方法定义什么值将被序列化。

- 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。

- 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值。

- **`undefined`、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 `null`（出现在数组中时）。函数、undefined 被单独转换时，会返回 undefined，**如`JSON.stringify(function(){})` or `JSON.stringify(undefined)`.

- 对包含循环引用的对象（对象之间相互引用，形成无限循环）执行此方法，会抛出错误。

- 所有以 symbol 为属性键的属性都会被完全忽略掉，即便 `replacer` 参数中强制指定包含了它们。

- Date 日期调用了 toJSON() 将其转换为了 string 字符串（同Date.toISOString()），因此会被当做字符串处理。

- NaN 和 Infinity 格式的数值及 null 都会被当做 null。

- 其他类型的对象，包括 Map/Set/WeakMap/WeakSet，仅会序列化可枚举的属性。

  ```js
  var obj1 = {
      x: 1,
      y: undefined,
      z: function add(z1, z2) {
          return z1 + z2
      },
      a: Symbol("foo")
  };
  var obj2 = JSON.parse(JSON.stringify(obj1));
  console.log(obj1) //{x: 1, y: undefined, z: ƒ, a: Symbol(foo)}
  console.log(JSON.stringify(obj1)); //{"x":1}
  console.log(obj2) //{x: 1}
  ```

  实现深拷贝

  * 简易深拷贝

  ```js
  var deepClone = function(target) {
          if (typeof target === 'object' && target !== null) {
              var cloneTarget = Array.isArray(target) ? []: {};
              for (var prop in target) {
                  if(typeof obj[key] ==='object'){ 
                    	cloneTarget[key] = deepClone(obj[key]) //值是对象就再次调用函数
                 }else{
                     if (target.hasOwnProperty(prop)) {
                         cloneTarget[prop] = target[prop];
                     }
                 }
              }
              return cloneTarget;
          } else {
              return target;
          }
      }
  ```

  

  ```js
  const isComplexDataType = obj => (typeof obj === 'object' || typeof obj === 'function') && (obj !== null)
  
  const deepClone = function (obj, hash = new WeakMap()) {
  
      if (obj.constructor === Date) return new Date(obj);   //日期对象就返回一个新的日期对象
      if (obj.constructor === RegExp) return new RegExp(obj);  //正则对象就返回一个新的正则对象
  
      //如果成环了,参数obj = obj.loop = 最初的obj 会在WeakMap中找到第一次放入的obj提前返回第一次放入WeakMap的cloneObj
      if (hash.has(obj)) return hash.get(obj)
  
      let allDesc = Object.getOwnPropertyDescriptors(obj);     //遍历传入参数所有键的特性
      let cloneObj = Object.create(Object.getPrototypeOf(obj), allDesc); //继承原型链
  
      hash.set(obj, cloneObj)
  
      for (let key of Reflect.ownKeys(obj)) {   //Reflect.ownKeys(obj)可以拷贝不可枚举属性和符号类型
          // 如果值是引用类型(非函数)则递归调用deepClone
          cloneObj[key] =
              (isComplexDataType(obj[key]) && typeof obj[key] !== 'function') ?
                  deepClone(obj[key], hash) : obj[key];
      }
      return cloneObj;
  };
  
  let obj = {
      num: 0,
      str: '',
      boolean: true,
      unf: undefined,
      nul: null,
      obj: {
          name: '我是一个对象',
          id: 1
      },
      arr: [0, 1, 2],
      func: function () {
          console.log('我是一个函数')
      },
      date: new Date(0),
      reg: new RegExp('/我是一个正则/ig'),
      [Symbol('1')]: 1,
  };
  
  Object.defineProperty(obj, 'innumerable', {
      enumerable: false,
      value: '不可枚举属性'
  });
  
  obj = Object.create(obj, Object.getOwnPropertyDescriptors(obj))
  
  obj.loop = obj
  
  let cloneObj = deepClone(obj);
  
  console.log('obj', obj);
  console.log('cloneObj', cloneObj);
  
  for (let key of Object.keys(cloneObj)) {
      if (typeof cloneObj[key] === 'object' || typeof cloneObj[key] === 'function') {
          console.log(`${key}相同吗？ `, cloneObj[key] === obj[key])
      }
  }
  ```