# js高级特性--闭包

## 前置知识

### 变量声明

* 基本数据类型

  ````javascript
  var a = 12
  var b = a
  b = 13
  ````

  ![微信截图_20210301141229](.\imgs\微信截图_20210301141229.png)

  

* 引用数据类型

  ````javascript
  var a = {n: 12}
  var b = a
  b.n = 13
  console.log(a)       // { n: 13 }
  ````

![2](.\imgs\2.png)



* 函数的声明和调用

  * 函数声明

    ````javascript
    ...
    const A = function(a) {
        ...
    }
    ...
    ````

    ![函数声明](.\imgs\函数声明.png)

    > 函数声明步骤： 1、声明作用域[[scope]]    2、声明形参     3、存储代码块

  * 函数调用

    ![函数调用](.\imgs\函数调用.png)

    > 函数调用步骤： 1、形成私有的执行上下文   2、初始化作用域链  3、形参赋值 **（浅拷贝）**  4、代码执行

### js作用域（es5）

> js中作用域分为全局作用域和局部作用域（函数作用域）

* 全局作用域

  > 任何地方都能被访问

  ````javascript
  var num = 100
  function f1() {
    console.log(num);
  }
  f1()      // 100
  ````

* 函数作用域

  > 在固定的代码片段中才能被访问

  ````java
  function f2() {
    var num = 200
  }
  f2()
  console.log(num);  //demo.html:13 Uncaught ReferenceError: num is not defined
  ````

  * **注意** 定义的变量不使用var关键词 则为全局作用域

    ````javascript
    function f2() {
      num = 200    // 定义的变量为全局作用域
    }
    f2()
    console.log(num);  // 200
    ````

    

### 作用域链

>  变量寻找过程： 作用域f1 ---》 作用域f2 ---》 全局作用域     （当前作用域找不到值就会向上一级作用域找，直至找到为止，形成作用域链）             

````javascript
var a = 1              // 变量a为全局作用域
function f1() {
  var b = 2            // 变量b为f1作用域
  function f2() {
    var c = 3          // 变量c为f2作用域
    console.log(a);
  }
  f2()
}
f1()    //  输出1

// 执行函数f2  打印变量a  在f2作用域中没有变量a  向上找f1作用域  f1作用域没有变量a 向上找全局作用域 存在变量a  正确打印
````



### 立即执行函数

````javascript
(function() {...})()     // 不需要调用  直接执行函数
````

* 作用：创建一个独立的作用域，外部无法访问，避免变量污染。例如jQuery源码

  

## 思考

> 作用域链机制可以让函数作用域内访问全局作用域的变量

* 反之如何实现在全局作用域内访问函数作用域中的变量

  ````javascript
  function f1() {
    var a = 1
    var b = 2
    function f2 (){      
      console.log(a+b);
    }
    return f2
  }
  var f3 = f1()
  f3()

  // f2函数嵌套在f1函数内部，所以f1内部的所有变量对于f2都是可访问的（作用域链机制），当f2作为返回值，我们就可以在全局作用域中访问函数作用域中的变量了。
  
  ````
  
  

## 闭包概念

* 思考中的f3函数就是闭包，闭包就是可以访问其他函数内部变量的函数

* 因为在js中只有函数内部的子函数才能访问局部变量，所以闭包可以理解为函数内部的函数

* **闭包指的是有权访问另一个函数作用域中的变量的函数**

  > 不一定要在函数中返回另一个函数的形式才叫闭包   只要在函数中用到了其他函数作用域的数据都是闭包

  ````javascript
  function f1() {
    const a = 1
    function f2() {
        console.log(a)    // f2函数中使用了f1函数中的变量  所以是闭包
    }  
    f2()
  }
  
  f1()
  ````

  

* 闭包本质就是函数内部与函数外部连接的桥梁





## 闭包的用途

1. 可以访问函数作用域中的变量(上文思考中)

2. 将变量保存在内存中

   * 没有闭包的情况下

     ````javascript
     function f1() {
       var a = 1
       a++
       console.log(a);
     }
     f1()                       // 输出为2
     f1()                       // 输出为2  因为每次调用后变量会被垃圾回收机制回收
     
     // f2函数中的a是自己的函数作用域  所以f2执行完就回收变量a
     ````

   * 存在闭包的情况下

     ````javascript
     function f1() {
       var a = 2
       return function () {
         a++
         console.log(a);
       }
     }
     var f2 = f1()
     f2()                                     // 输出为3
     f2()		                             // 输出为4
     
     // f2 函数中的a访问的是f1中的作用域 所以f2执行完并不会回收变量a
     ````




## 使用闭包注意事项

* js的垃圾回收机制： 为防止内存溢出，函数执行完毕后会销毁函数内部的变量内存空间
* **√注意**：在函数执行完后，当函数内的变量被其他执行上下文占用时，变量的内存空间不会被销毁，所以闭包要谨慎使用，会造成内存泄露，使用完闭包后即使接触，如 在上面的代码块中**f2 = null**





## 闭包经典问题

````html
 <ul id=”test”>
     <li>这是第一条</li>
     <li>这是第二条</li>
     <li>这是第三条</li>
 </ul>
 
 <script>
     var liList=document.getElementsByTagName('li');
     for(var i=0;i<liList.length;i++){
         liList[i].onclick=function(){
             console.log(i);
        }
     };
 </script>   //不管点击哪个li都是输出3   因为i贯穿整个作用域 触发点击事件时循环已经执行完i是3
````



> 解决方案1： 使用立即执行函数消除闭包

```html
 <ul id=”test”>
     <li>这是第一条</li>
     <li>这是第二条</li>
     <li>这是第三条</li>
 </ul>
 
 <script>
     var liList=document.getElementsByTagName('li');
     for(var i=0;i<liList.length;i++){
         (
           function(i) {
             liList[i].onclick=function(){
             console.log(i)
           }
             )(i)   // 立即执行函数 每个i都存在于一个独立的作用域中 实现点击li分别为 0、1、2
        }
     };
 </script>
```

> 解决方案2： 使用es6 let关键词   利用块级作用域

````javascript
 <ul id=”test”>
     <li>这是第一条</li>
     <li>这是第二条</li>
     <li>这是第三条</li>
 </ul>
 
 <script>
     var liList=document.getElementsByTagName('li');
     for(let i=0;i<liList.length;i++){
         (
           liList[i].onclick=function(){
           console.log(i)
             )(i)   // 立即执行函数 每个i都存在于一个独立的作用域中 实现点击li分别为 0、1、2
        }
     };
 </script>
````





## 练习

* 练习1 

  ````javascript
  var name = "The Window";
  var object = {
  　　name : "My Object",
  　　getNameFunc : function(){
  　　　　return function(){
  　　　　　　return this.name;
  　　　　};
  　　}
  };
  alert(object.getNameFunc()());   //  输出为  The Window
  ````

  ````javascript
  var name = "The Window";
  var object = {
  　name : "My Object",
  　getNameFunc : function(){
  　　　var that = this;
  　　　return function(){
  　　　　 return that.name;
  　　　};
  
  　　}
  
  　};
  
  　alert(object.getNameFunc()());   //  输出为 My Object
  ````

  

* 练习2

  ````javascript
  for(var i = 0; i < 5; i++) {
        setTimeout(() => {
          console.log(i);    // i为全局作用域  因为定时器是异步 所以执行的时候i=5
        }, 1000);
      }
      console.log(123) // 输出123 一秒后输出5个5 
  
  -------------------------------------------------------------------------------
  for(var i = 0; i < 5; i++) {  
        (function (i) {
          setTimeout(() => {
            console.log(i)   // 立即执行函数+闭包  立即执行函数开辟独立作用域  闭包内层定时                               器内函数应用外层立即执行函数的作用域  所以每次循环i都是独立                               的作用域
          }, 1000);
        })(i)
      }
      console.log(123)  // 输出123 一秒后输出0、1、2、3、4
          
  
  for(let i = 0; i < 5; i++) {
        setTimeout(() => {
          console.log(i)  //es6 let关键词声明变量i 具有块级作用域 
                          //每次循环的i都处于不同的块级作用域
        }, 1000);
      }
      console.log(123)
  
  
  ````






* 练习3(闭包 + 递归)

  ````javascript
  function fun(n, o) {
    console.log(o)
    return {
      fun: function(m) {
        return fun(m, n)
      }
    }
  }
  
  
  var a = fun(0)   // undefined
  a.fun(1)		 // 0
  a.fun(2)         // 0
  a.fun(3)		 // 0
  
  var b = fun(0).fun(1).fun(2).fun(3)
         // undefined  0   1   2
  
  
  
  var c = fun(0).fun(1)  // 0
  c.fun(2)               // 1
  c.fun(3)			   // 1
  ````

  



* 练习4

  ````javascript
  let a = 0
  b = 0
  function A(a) {
      A = function(b) {
          console.log(a + b++)
      }
      console.log(a++)
  }
  A(1)     // 输出为1
  A(2)     // 输出为4
  ````

  运行过程

![运行过程2](.\imgs\运行过程2.png)

