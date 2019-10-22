---
title: js类型转换
date: 2019-08-29 11:16:31
categories:
  - 前端
  - Js
tags: js
photos:
  - 'https://yunpengGit.github.io/code/mock-img/fengbaobao/baoer1.jpg'
---

#### js类型转换分为 强制类型转换 和隐式类型转换。

1. Number()、parseInt()、parseFloat()、toString()、String()、Boolean() 强制类型转换
2. 逻辑运算符(&&、||、!)、运算符(+、-、*、/)、关系操作符(>、<、<=、>=)、相等运算符(==)或if/while 的条件、可能会进行隐式转换。

**类型强制转换**

> 1.Number() 将任意类型的参数转换为数值类型**简写 前面加 +**

规则：

1. 布尔值，true和false转换为1和0

2. 数字，返回自身

3. null，返回 0

4. undefined，返回 NaN

5. 字符串，规则：

   * 字符串只包含数字(或者是 0X / 0x 开头的十六进制的数字字符串，允许包含正负号)，转换为十进制数
   * 字符串中包含有效的浮点格式，将其转换为浮点数值
   * 空字符串，将其转换为0
   * 不是以上格式的字符串，均返回 `NaN`

6. Symbol，抛出错误

7. 对象，则调用对象的 `valueOf()` 方法，然后依据前面的规则转换返回的值。如果转换的// `NaN` ，则调用对象的 `toString()` 方法，再次依照前面的规则转换返回的字符串值

   ```javascript
   Number('0X12') //18
   +'0X12'
   Number(-0x12)//-18
   +-0x12
   Number('-0x12')//NaN
   +'-0x12'
   Number('0123'); //123
   Number(''); //0
   Number(['']); //0
   Number([]); //0
   Number(null); //0
   Number(undefined)//NaN
   Number({}); //NaN
   Number('1a'); //NaN
   Number(Symbol());// Cannot convert a Symbol value to a number at Number
   ```

> 2.parseInt (param, radix)

parseInt ( param, radix)将一个字符串 string 转换为 radix 进制的整数， radix 为介于2-36之间的数(为空时默认为10)。

第一个参数是字符串类型:

1. 忽略字符串前面的空格，直至找到第一个非空字符，如果是空字符串，返回 NaN

2. 第一个字符不是数字或数字符号（ 0X / 0x 开头的十六进制的数字）或者正负号，返回 NaN

3. 第一个字符是数字/正负号，则继续解析直至字符串解析完毕或者遇到一个非数字符号为止

   ```javascript
   parseInt('') //NaN
   parseInt(' 123s1') //123
   parseInt(' 0X12') //18
   parseInt(' -s') //NaN
   ```

第一个参数传入的 Number 类型:

1. 数字如果是 0 开头，则将其当作八进制来解析(如果是一个八进制数)；如果以 0x 开头，则将其当作十六进制来解析

   ```JavaScript
   parseInt(012) //10
   parseInt(0x12) //18
   ```

第一个参数是 null 或者是 undefined，或者是一个对象类型：

1. 返回NaN

   ```javascript
   parseInt(null) //NaN
   parseInt(undefined) //NaN
   parseInt({}) //NaN
   parseInt([]) //NaN
   parseInt(['']) //NaN
   ```

第一个参数是数组：

1. 取数组的第一个元素，按照上面规则解析

   ```javascript
   parseInt(['']) => parseInt('') //NaN
   ```

第一个参数是Symbol类型：

1. 报错

   ```javascript
   parseInt(Symbol()) //Cannot convert a Symbol value to a string at parseInt
   ```

> parseFloat()

规则和`parseInt`基本相同，接受一个Number类型或字符串，是字符串中，只有第一个小数点是有效的。

> toString()      

规则：

1. Number类型，输出数字字符串

2.  null 或者是 undefined，抛错

3. 数组，那么将数组展开输出。空数组，返回`''`

4. 对象，返回 `[object Object]`

5. Date, 返回日期的文字表示法

6. 函数，输出对应的字符串

7. Symbol，输出Symbol字符串

   ```javascript
   123.toString() //'123'
   123+'' 
   null.toString() //Cannot read property 'toString' of null
   null+'' //'null'
   undefined.toString() //Cannot read property 'toString' of undefined
   undefined+''//'undefined'
   [1,2,3].toString() //'1,2,3'
   [1,2,3]+''
   [].toString() //''
   []+''
   
   {}.toString() //Uncaught SyntaxError: Unexpected token .
   {}+'' //0
   let obj={} obj.toString() //"[object Object]"
   obj+'' //"[object Object]"
   
   let date = new Date(); date.toString() //"Thu Aug 29 2019 14:37:38 GMT+0800 (中国标准时间)"
   var fn=function (){} 
   fn.toString() //"function (){}"
   Symbol(123).toString() //"Symbol(123)"
   var str=Symbol(123) str.toString() //"Symbol(123)"
   ```

> String()  **简写方式 拼接 + "" **

`String()` 的转换规则与 `toString()` 基本一致，最大的一点不同在于 `null` 和 `undefined`，使用 String 进行转换，null 和 undefined对应的是字符串 `'null'` 和 `'undefined'`

> Boolean **简写方式 !!**

除了 undefined、 null、 false、 ''、 0(包括 +0，-0)、 NaN 是false，其它都是true.

```js
var x = Boolean(expression);     // 推荐
var x = !!(expression);          // 推荐
!![] //true
!!{} //true
!!['']//true
!!''  //false
!!null //false
!!undefined //false
!!NaN //false
!!0 //false
```

______

**隐式类型转换**

> && 、|| 、 ! 、 if/while 的条件判断

将数据转换成 Boolean 类型，转换规则同 Boolean 强制类型转换

> 运算符: + - * /

`+` 号操作符，数字相加，字符串拼接。

1. `+` 号两边数字，加法运算，两边字符串，字符串拼接

2. 操作数是对象、数组、数字、布尔值、转换规则：

   ```js
   String({}) //"[object Object]"
   
   String([]) //""
   []+''      //""
   String(null) //"null"
   null+''    //'null'
   String(undefined) //"undefined"
   undefined  //'undefined'
   var a={},b=[],c=null,d=undefined,e='';
   a.toString()  //"[object Object]"
   b.toString()  //""
   e.toString()  //""
   
   console.log(null+10); //10
   console.log(null+true); //1
   console.log(null+[1,2,3]) //null1,2,3
   console.log(null+{}) //null[object Object]
   
   console.log(undefined+10); //NaN
   console.log(undefined+true); //NaN
   console.log(undefined+[1,2,3]) //null1,2,3
   console.log(undefined+{}) //null[object Object]
   ```


   * 将对象，数组数字转换为 字符串拼接

   ```js
   console.log({}+10); //"[object Object]10"
   console.log([]+10); //"10"
   console.log({}+[1,3]) //"[object Object]1,3"
   console.log(false+{})  //"false[object Object]"
   console.log(true+{})   //"true[object Object]"
   console.log(false+[1,2])  //"false1,2"
   console.log(true+[])  //"true"
   ```

   * **布尔值和 数字 (将数字转换为数字计算)**

   ```js
   console.log(false+10)   //10
   console.log(false+10)   //11
   ```

`-`、`*`、`/` 操作符是运算，操作值不是数值，则被隐式调用Number()函数进行转换。如果其中有一个转换为NaN，结果为NaN.

```js
Number({}) //NaN
Number([]) //0
Number([10]) //10
Number([1,0]) //NaN
Number(null); //0
Number(undefined)//NaN
```

> 关系操作符: ==、>、< 、<=、>=

1. 两个操作值都是数值，则进行数值比较
2. 两个操作值都是字符串，则比较字符串对应的字符编码值
3. 有一方是Symbol类型，抛出错误
4. 除了上述情况之外，都进行Number()进行类型转换，然后再进行比较。

**注：** NaN 它其他任何类型的值都不相等，包括它自己，同时它与任何类型的值比较都返回false。

```js
console.log(1 > {});//返回false.
/**
 *{}.valueOf ---> {}
 *{}.toString() ---> '[object Object]' ---> NaN
 *NaN 和 任何类型比大小，都返回 false
 */
```

> 相等操作符：`==`

1. 类型相同，无需进行类型转换。

2. 其中一个操作值是 null 或者是 undefined，那么另一个操作符必须为 null 或者 undefined 时，才返回 true，否则都返回 false.

   ```js
   null==undefined //true
   ```

3. 其中一个是 Symbol 类型，那么返回 false.

4. 两个 一个值是数字，另一个值是字符串，先将字符串转换为数学，然后使用转换后的值进行比较。 

   ```js
   0=='01' //=> 0==0 true
   //比较
   0=='0' //=> 0==0  true
   0==[]  //=> 0==0  true
   '0'==[]  //=> '0'==''  false
   ```

5. **几个特殊的对象：`''，0，'0'，[]，{}`**

   **<span style="color:skyblue"> `{}` 对象在有操作符中执行时，在 `{}` 之前有操作符，则当做 `{}` 空对象处理，如果没有则被当作一个独立的空代码块（不执行任何操作)</span>**

   ```js
   //...+...加号的运算优先级从左到右
   ''+{}  //"[object Object]"     ''字符串遇到+执行字符串拼接操作,{}当做一个空对象处理，转为字符串"[object Object]"
   {}+'' //0  {}之前没有任何操作符，被当作一个独立的空代码块（不执行任何操作),+''空字符串转为数字0
   
   {}+''=== 0  //true
   {}+''=='0' //true => 0 =='0'
   ''+{}=='0' //false =>
   '0'=={}+'' //false => '0'=="[object Object]"+'' {}之前有==操作符，当做空对象处理，+ 执行字符串拼接操作
   '0'==''+{} //false =>'0'==''+"[object Object]" {}之前有+操作符，当做空对象处理，+ 执行字符串拼接操作
   
   //...==... 运算优先级从左到右
   0=='0' //true => 0==0   +'0'=> 0
   0==[]  //true 0==0     +[]=> 0
   '0'==[] //false '0'=='' []+''=>''
   
   []+{}   // "[object Object]"
   {}+[]   // 0
   ({}+[])   // "[object Object]"
   +[]     // 0   ，此处+为正号
   +{}     // NaN   ，此处+为正号
   [].toString()    // ""
   ({}).toString() // "[object Object]"    
   {}.toString() //会出错，Uncaught SyntaxError: Unexpected token .
   0+[]    // "0"
   0+{}    // "0[object Object]"
   ```

6. 一个操作值是 boolean，那么转换成 number

7. 一个操作值为 object 且另一方为 string、number 或者 symbol，是的话就会把 object 转为原始类型再进行判断(调用 object 的valueOf() / toString()方法进行转换)

   ```js
   //== 左右的对象转换 
   //三目运算符？前面的对象转换为Boolean类型
   console.log(([])?true:false); 
   console.log(([]==false?true:false)); 
   console.log(({}==false)?true:false)
   
   下面是题目的类型转换结果：
   
   Boolean([]); //true
   Number([]); //0
   Number({}); // NaN
   Number(false); //0
   
   因此：
   
   console.log(([])?true:false);// => console.log((true)?true:false); 三目运算符？前面的表达式需要转换为Boolean类型 !!快捷转换
   console.log(1?true:false);// => console.log(true?true:false); 
   console.log(0?true:false);// => console.log(false?true:false); 
   
   console.log([]==false?true:false); // => console.log(0==0?true:false);
   console.log(({}==false)?true:false); // => console.log((NaN==0)?true:false);
   ```


> **对象如何转换成原始数据类型**

**如果有 [Symbol.toPrimitive]  接口，那么调用此接口，若返回的不是基础数据类型，抛出错误。如果没有 [Symbol.toPrimitive] 接口，那么先返回 valueOf() 的值，若返回的不是基础类型的值，再返回 toString() 的值，若返回的不是基础类型的值， 则抛出异常。**

```js
//先调用 valueOf, 后调用 toString
let obj = {
    [Symbol.toPrimitive]() {
        return 200;
    },
    valueOf() {
        return 300;
    },
    toString() {
        return 'Hello';
    }
}
//如果 valueOf 返回的不是基本数据类型，则会调用 toString， 
//如果 toString 返回的也不是基本数据类型，会抛出错误
console.log(obj + 200); //400
```

**a等于什么以下逻辑打印1**

```js
// a=?
if(a == 1 && a == 2 && a == 3) {
    console.log(1);
}
```

1. a为对象，a的取值规则 [对象如何转换成原始数据类型] 

   取值相关的三个属性方法，Symbol.toPrimitive、valueOf、toString

   * Symbol.toPrimitive示例

     ```js
     //利用闭包创建独立作用域的特性，每次返回一个值
     let a = {
         [Symbol.toPrimitive]: (function() {
                 let i = 1;
                 return function() {
                     return i++;
                 }
         })()
     }
     if(a == 1 && a == 2 && a == 3) {
         console.log(1);
     }
     
     //a设定一个属性i返回,每次取值都返回这个改变后的i
     let a = {
         i:1,
         [Symbol.toPrimitive]: function() {
                 return this.i++;
         }
     }
     if(a == 1 && a == 2 && a == 3) {
         console.log(1);
     }
     ```

   * valueOf、toString 同上

2. Object.definePropert 获取a属性时，调用get.

   ```js
   let b=1;
   Object.defineProperty(window, 'a', {
     get: function() {
       return b++;
     }
   });
   if(a == 1 && a == 2 && a == 3) {
       console.log(1);
   }
   ```

3. 数组

   ```js
   var a = [1,2,3];
   a.join = a.shift;
   if(a == 1 && a == 2 && a == 3) {
       console.log(1);
   }
   ```

   数组在类型转换时调用toString()返回一个字符串，该字符串有由数组的每一个值的toString()返回值调用join()连接而成（join默认使用逗号连接）；重写 join 方法，让其依次返回数组元素

   

