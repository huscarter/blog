---
title: es5和es6的区别
date: 2024-01-09 20:18:44
categories: web
tags:
     - es
description: ECMAScript5即ES5，是ECMAScript的第五次修订，于2009年完成标准化。ECMAScript6即ES6，是ECMAScript的第六次修订，于2015年完成，相对于ES5更加简洁。
---
### 变量的申明
1. 块级作用域，关键字let,常量const
let声明变量和const声明常量，两个都有块级作用域，ES5中是没有块级作用域的，并且var有变量提升，在let中，使用的变量一定要进行声明。let命令，声明变量，用法和var差不多，但是let是为JavaScript新增了块级作用域，ES5中是没有块级作用域的，并且var有变量提升的概念，但是在let中，使用的变量一定要进行声明；const声明一个只读的常量。一旦声明，常量的值就不能改变。const和let一样只在当前代码块有效，且不能重复声明，作用域不会被提升，声明的常量必须被赋值。

var、let、const之间的区别：

　　　　var声明变量可以重复声明，而let不可以重复声明var是不受限于块级的，而let是受限于块级
　　　　var会与window相映射（会挂一个属性），而let不与window相映射
　　　　var可以在声明的上面访问变量，而let有暂存死区，在声明的上面访问变量会报错
　　　　const声明之后必须赋值，否则会报错
　　　　const定义不可变的量，改变了就会报错
　　　　const和let一样不会与window相映射、支持块级作用域、在声明的上面访问变量会报错
2.箭头函数
　　ES6中的函数定义不再使用关键字function()，而是利用了()=>来进行定义。

　　　箭头函数和普通函数的区别：

1.外形不同
　　箭头函数使用箭头定义，普通函数中没有
2.箭头函数全都是匿名函数
　　普通函数可以有匿名函数，也可以有具名函数
3.箭头函数不能用于构造函数
　　普通函数可以用于构造函数，以此创建对象实例
4.箭头函数中this的指向不同
　　在普通函数中this总是指向调用它的对象，如果用构造函数，它指向创建的对象实例。
　　箭头函数本身不创建this,也就是说箭头函数本书没有this，但是它在声明时捕获别人的this供自己使用（this一旦被捕获，将不再变化）。即箭头函数中的this是捕获而来的。
　　箭头函数中的this是在它声明时捕获它所处作用域中的this。
5.箭头函数不具有arguments对象
　　每一个普通函数调用后都具有一个arguments对象，用来存储实际传递的参数。但是箭头函数并没有此对象
6.其他区别：
（1）.箭头函数不能Generator函数
（2）.箭头函数不具有prototype原型对象。
（3）.箭头函数不具有super。
（4）.箭头函数不具有new.target。

　　使用箭头函数应该注意什么？

（1）用了箭头函数，this就不是指向window，而是父级（指向是可变的）
（2）不能够使用arguments对象
（3）不能用作构造函数，这就是说不能够使用new命令，否则会抛出一个错误
（4）不可以使用yield命令，因此箭头函数不能用作 Generator 函数

3.字符串模板
　　模板字符串模板字符串是增强版的字符串，用反引号（`）标识，可以当作普通字符串使用，也可以用来定义多行字符串。

复制代码
let name = 'world';
let myName = 'Jerry' ;
let str = '你好，${name} 我是 ${myName }'
str = str.replace(/\$\{([^}]*)\}/g,function(){
     return eval(arguments[1]);
   })
console.log(str);//你好，world 我是 Jerry
复制代码
 4.赋值解构
　　解构赋值ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值

5.for of循环for...of循环可以遍历数组、Set和Map结构、某些类似数组的对象、对象，以及字符串
1.Set和map的区别

应用场景Set用于数据重组，Map用于数据储存Set：　
（1）成员不能重复
（2）只有键值没有键名，类似数组
（3）可以遍历，方法有add, delete,has
Map:
（1）本质上是健值对的集合，类似集合
（2）可以遍历，可以跟各种数据格式转换

ps:

Set方法可以很简洁的完成数组去重。

let arr = [9,12,9,16,10,13];
let item = [...new Set(arr)];
console.log(item); //  [9, 12, 16, 10, 13]
For循环当中：

复制代码
let arr = [9,16,88,10,15];
let sum = 0;
for(let i=0;i<arr.length;i++){
    sum += arr[i];
}
//138
复制代码
相当于

复制代码
let arr = [9,16,88,10,15];
let sum = 0;
for(item of arr){
    sum += item;
}
138
复制代码
forEach、for in、for of三者区别：
　　forEach更多的用来遍历数组
　　for in 一般常用来遍历对象或json
　　for of数组对象都可以遍历，遍历对象需要通过和Object.keys()
　　for in循环出的是key，for of循环出的是value

6.Symbol
　　Symbol是一种基本类型。Symbol 通过调用symbol函数产生，它接收一个可选的名字参数，该函数返回的symbol是唯一的。

7.系统库的引用及导出
　　ES5中需要先使用require导入，成为对象，再去进行真正引用
　　ES6可以使用import方法来直接实现系统库的引用，不需要额外制作一个类库对象

　　ES5中，要导出一个类给别的模块使用，一般通过module.exports来实现，引用时，依然通过require方法来获取。
　　ES6中，使用export default实现同样的功能，但要使用import方法来实现导入。

8.set数据结构　　Set数据结构，类似数组。所有的数据都是唯一的，没有重复的值。它本身是一个构造函数
9.Proxies
　　使用代理（Proxy）监听对象的操作，然后可以做一些相应事情

10.promise
　　Promise是异步编程的一种解决方案，比传统的解决方案（回调函数和事件）更合理、强大

11.async、await
　　使用 async/await, 搭配promise,可以通过编写形似同步的代码来处理异步流程, 提高代码的简洁性和可读性async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成。

