# js异步

## 准备

### 同步与异步

### 实例对象与函数对象

> 1. 实例对象：通过构造函数new出来的对象
> 2. 函数对象：将函数作为对象使用

* **括号左边是函数，点的左边是对象**

````javascript
function Fn() {}
const fn = new Fn()    // Fn是构造函数     fn为实例对象  简称对象
console.log(Fn.prototype)   // Fn是函数对象
Fn.bind({})             // Fn是函数对象
$("$test")  // $是函数
$.get("/test")  // $是函数对象



````





### 回调函数的种类

> 不是所有的回调函数都是异步

* 同步回调

  ````javascript
  const arr = [1, 2, 3]
  
  arr.forEach(item => {   // 这边的回调函数时同步回调函数
    console.log(item)
  })
  
  console.log(4)    // 输出为1 2 3 4
  ````

  

* 异步回调

  ````javascript
  setTimeout(() => {    // 这边的回调函数为异步回调函数
    console.log(1)    
  }, 0);
  
  console.log(2)   // 输出为2 1
  ````

  ````javascript
  var a 
  setTimeout(() => {
    a = 1
  }, 0);
  
  console.log(a)   // 输出为undefined
   
  ````

  