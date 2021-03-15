# js异步编程

## 准备

### 同步与异步

> 1. js是单线程，同一时间只能做一件事情
> 2. 试想如果js是多线程的，同一时间可以做多件事情，那么我给一个DOM元素绑定多个事件，同时触发，那么就会产生矛盾

* 同步任务：在js主线程执行栈中桉顺序执行
* 异步任务：当代码执行到异步任务时，满足相关条件后会将异步任务放入异步任务队列，等主线程执行完再执行异步任务队列的异步任务
  * **异步任务的形式： 1、回调函数    2、绑定事件     3、发布订阅模式**
  * **当异步任务之间有依赖关系需要按顺序执行时就会产生回调地狱的问题**

### 实例对象与函数对象

> 1. 实例对象：通过构造函数new出来的对象
> 2. 函数对象：将函数作为对象使用

* **括号左边是函数，点的左边是对象**

````javascript
function Fn() {}
const fn = new Fn()    // Fn是构造函数     fn为实例对象  简称对象
console.log(Fn.prototype)   // Fn是函数对象
Fn.bind({})             // Fn是函数对象
$("#test")  // $是函数
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

  



## Promise

### 是什么

#### 概念

* 抽象表达：promise是进行异步编程的新的解决方案，解决旧方案回调地狱的问题
  * 旧的解决方案： 异步嵌套，形成回调地狱

* 语法具体表达
  1. 语法上说： Promise是一个构造函数
  2. 功能上说： promise对象用来封装一个异步操作并可以获取其结果

#### promise的状态改变

#### promise的基本使用

> Promise构造函数传的回调函数是同步回调函数

````javascript
const p = new Promise((resolve, reject) => { 
  $.ajax({
    url: "http://localhost:3000/s",
    success: function(res) {
      resolve(res)      // 成功调用 resolve(res)
    },
    error: function(err) {
      reject(err)       // 失败调用  reject(err)
    }
  })
})

p.then(value => {            // 接收成功的数据
  console.log(value)
}).catch(reason => {       // 接收失败的数据
  console.log(reason)
})
````





### 为什么

#### 传统异步操作(纯回调函数)

1. 必须在异步任务启动前指定回调函数

   ````javascript
   createAudioFileAsync(auditSetting, successCallback, failureCallback)
                                      // 异步任务开始前指定成功的回调和失败的回调函数
   ````

   

#### promise异步操作

1. 指定回调函数的方式更加灵活

   * 在异步任务开始后完成之前指定回调函数

     ````javascript
     const p = new Promise(xxxxx) //假如异步任务耗时3秒  实例化promise后相当于异步任务开始
     p.then(res => {        // 实例完promise（异步任务开始）后指定回调函数
         console.log(res)
     })
     ````

   * 在异步任务完成之后指定回调函数

     ````javascript
     const p = new Promise(xxxxxx) // 假如异步任务耗时3秒
     setTimeout(function() {
         p.then(res => {
             console.log(res)
         })
     }, 4000)   // 4秒后指定回调函数  则在异步任务完成之后指定回调函数
     ````

     

2. 可以解决传统异步嵌套回调地狱

   ````javascript
   // 传统回调层层嵌套
   doSomething(function(res) {
       doSomething2(res, function(newRes) {
           doSomething3(newRes, function() {
               xxxxxxx
           })
       })
   })
   ````

   * promise链式调用

     ````javascript
     function p(params) {    // p函数返回一个promise对象
         xxxxxx
     }
     p(params1).then(res => {
         console.log(res)
         return p(params2)
     })
     .then(res => {
         console.log(res)
         return p(params3)
     })
     .then(res => {
         console.log(res)
     })
     .catch(err => {
         console.log(err)   // 任何一个出异常都会被捕获  （异常传透）
     })
     ````

     