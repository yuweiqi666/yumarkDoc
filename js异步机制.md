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

> 改变promise状态 + 指定回调函数（先后问题）两个操作

* 抽象表达：promise是进行异步编程的新的解决方案，解决旧方案回调地狱的问题
  * 旧的解决方案： 异步嵌套，形成回调地狱

* 语法具体表达
  1. 语法上说： Promise是一个构造函数
  2. 功能上说： promise对象用来封装一个异步操作并可以获取其结果

#### promise的状态改变

* pending（开始实例化promise对象）    resolved      rejected

  > 两种状态改变，且状态只能改变一次  

  * pending -> resolved        成功
  * pending -> rejected         失败

* 流程状态

  ![promise状态](E:\桌面\笔记\imgs\promise状态.jpg)

#### promise的基本使用

> 1. Promise构造函数传的回调函数（executor执行器函数）是同步回调函数（里面具体的任务可以是同步也可以是异步）
> 2. promise.then(xxxxx)   / promise.catch(xxxxxx)    .then()是同步（指定回调函数）    传的回调函数是异步回调函数

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

     

### 如何使用promise

#### Promise.resolve()

* 快速创建一个成功的promise对象  **可以传一个promise对象作用一个参数**

````javascript
const p1 = Promise.resolve(3)  // 快速创建一个成功的promise对象

p1.then(res => {
  console.log(res)   // 3
})

````



#### Promise.reject()

* 快速创建一个失败的promise对象

````javascript
const p2 = Promise.reject(2)  // 快速创建一个失败的promise对象

p2.catch(res => {
  console.log(res)   // 2
})
````



#### Promise.all()

> 1. 多个peomise对象以数组的形式作为参数
> 2. 其中只要有一个为失败  就会变为rejected状态  触发catch回调   返回这个失败的值
> 3. 当所有的peomise都成功 变为resolved状态   触发then回调   返回所有成功的值（数组的形式）
>
> 

````javascript
// 失败的情况
const p1 = Promise.resolve(1)

const p2 = Promise.reject(3)

const p3 = Promise.reject(2)


const pAll = Promise.all([p1, p2, p3])


pAll.then(res => {
  console.log(res)
})
.catch(err => {
  console.log(err)    //  失败触发catch回调 获取第一个失败的值 3
})
````



````javascript
// 成功的情况
const p1 = Promise.resolve(1)

const p2 = Promise.resolve(3)

const p3 = Promise.resolve(2)


const pAll = Promise.all([p1, p2, p3])


pAll.then(res => {
  console.log(res)   // 所有的promise都是成功  触发then回调 获取所有的成功的值（数组形式）
})
.catch(err => {
  console.log(err)
})
````



#### Promise.race()

> 1. 多个peomise对象以数组的形式作为参数
> 2. 以第一个完成任务的peomise对象来决定获取的值

````javascript
const p1 = Promise.resolve(1)

const p2 = Promise.resolve(3)

const p3 = Promise.resolve(2)


const pAll = Promise.race([p1, p2, p3])


pAll.then(res => {
  console.log(res)   // 第一个完成的是p1，p1为成功的peomise对象  触发then回调函数  获取值为1
})
.catch(err => {
  console.log(err)
})
````



### promise核心知识点

* 如何改变promise的状态

  * 调用resolve(value)    pending -> resolved

    ````javascript
    Promise.resolve(2).then(res => {
      console.log(res)   // 2
    })
    ````

    

  * 调用reject(reason)     pending -> rejected

    ````javascript
    Promise.reject(3).catch(err => {
      console.log(err)   // 3
    })
    ````

    

  * 抛出异常 throw  xxxx   （人为抛出任意值异常，但一般抛error对象。也适用于js内部抛异常）  pending -> rejected

    ````javascript
    const p = new Promise((resolve, reject) => {
      throw 5
    })
    
    p.catch(err => {
      console.log(err)
    })
    ````

    ````javascript
    
    const p = new Promise((resolve, reject) => {
      throw new Error()
    })
    
    p.catch(err => {
      console.log(err)
    })
    ````
  
    


* 一个promise对象同时指定多个回调（都会触发）

  ````javascript
  const p = new Promise((resolve, reject) => {
    resolve(123)
  })
  
  p.then(res => {
    console.log(res)    // 123
  })
  
  p.then(res => {
    console.log(res)    //  123
  })
  ````

  

* 改变promise状态和指定回调函数谁先谁后的问题

  * 先指定回调函数  在改变peomise状态

    ````javascript
    
    const p = new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(12) // 定时器三秒后执行  三秒后完成异步任务 触发resolve() 改变promise状态
      }, 3000);
    })
    
    p.then(res => {
      console.log(res)   
    })
    // .then()为立即执行（里面的回调是异步的， 在promise状态改变后触发） 在peomise状态改变之前指定回调函数
    ````

    

  * 先改变promise状态  在指定回调函数

    ````javascript
    const p = new Promise((resolve, reject) => {
      resolve(1)           // promise中为同步任务   所以按顺序先改变了peomise状态
    })
    
    p.then(res => {
      console.log(res)   // 后指定了回调函数  立即触发回调函数
    })
    
    ````




* promise中的return相关问题

  > promise对象触发回调，再返回peomise对象 

  *   **不写return 默认return undefined**

  ````javascript
  const p = Promise.resolve(3)   // 成功的promise
  
  p.then(res => {
    console.log(res)      // p状态为成功  触发成功回调  输出3  然后返回 
    // return undefined  默认返回undefined
  }, err => {
    console.log(err)
  }).then(res => {       // return undefined 再次触发成功的回调     输出为undefined
  console.log(res), err => {
    console.log(err)
  }})
  ````

  * 触发promise回调函数后  人为写return

  ````javascript
  // 返回非promise的任意值  新promise状态为resolved
  const p = new Promise((resolve, reject) => {
    reject(123)             // 失败的peomise回调
  })
  
  p.then(res => {
    console.log(res)
  }, err=> {            // 触发失败的回调函数     输出123
    console.log(err)
    return 3           // 人为返回3
  }).then(res => { 
    console.log(res)    // 再次触发成功的回调函数   输出3  
  }, err => {
    console.log(err)
  })
  ````

  ````javascript
  // 返回抛出异常   新promise状态变为 rejected
  const p = new Promise((resolve, reject) => {
    reject(123)             // 失败的peomise回调
  })
  
  p.then(res => {
    console.log(res)
  }, err=> {            // 触发失败的回调函数     输出123
    console.log(err)
    throw new Error()           // 人为返回error对象
  }).then(res => { 
    console.log(res)    
  }, err => {            // 再次触发失败的回调函数   输出error对象 
    console.log(err)
  })
  ````

  ````javascript
  // 返回正常的promise对象   根据具体情况判断resolved rejected状态
  const p = new Promise((resolve, reject) => {
    reject(123)             // 失败的peomise回调
  })
  
  p.then(res => {
    console.log(res)
  }, err=> {            // 触发失败的回调函数     输出123
    console.log(err)
    return Promise.resolve(1)          // 人为返回成功的promise对象
  }).then(res => { 
    console.log(res)    // 再次触发成功的回调函数   输出1
  }, err => {            
    console.log(err)
  })
  ````

  

* peomise串联多个任务（链式调用）

  ````javascript
  const p = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(1)     // 异步任务  成功的promise对象
    }, 1000);
  })
  
  p.then(res => {
    console.log(res)   // 初始成功的回调函数
    return 2         // 返回非promise任意值
  }).then(res => {
    console.log(res)     // 触发下一个成功的回调函数
    return new Promise((resolve, reject) => {   // 异步任务  返回成功的promise对象
      setTimeout(() => {
        resolve(3)
      }, 1000);
    })
  }).then(res => {     //  触发最后一个成功的回调函数
    console.log(res)
  })
  
  
  // 输出细节   等待1秒  输出1 、 2  再等待一秒  输出3
  ````

  

  

* 异常穿透

  > 逐级往下找失败的回调函数

  ````javascript
  const p = new Promise((resolve, reject) => {
    reject(123)      // 失败的promise对象
  })
  p.then(res => {
    console.log(res)
  }, // err => {throw err}  // 没写失败的回调函数就相当于写这样抛一个异常 一直往下找失败的回调
        )  
  .then(res => {
    console.log(res)
  }， // err => {throw err})        // 没写失败的回调函数 就相当于写这样抛一个异常
       ).then(res => {
    console.log(res)
  }// err => {throw err})              // 没写失败的回调函数 就相当于写这样抛一个异常
             ).catch(err => {
    console.log(err)            // 最终被最后面的失败回调函数触发
  })
  ````

  

  

* 中断promise链

  ````javascript
  const p = new Promise((resolve, reject) => {
    resolve(1)    // 成功的promise对象
  })
  
  p.then(res => {
    console.log(res);     // 触发成功的回调函数
    return Promise.resolve(2)  // 返回的成功的promise对象
  }).then(res =>{       // 再次触发成功的回调函数
    return new Promise(() => {})   // 返回pending状态的回调函数   （状态没有改变）
  }).then(res => {   //所以后面虽然指定了回调函数  但是不会触发执行   中断了promise链
    console.log(res);
  })
  
  
  // 最终的输出为  1  2
  ````

  