# js的error处理

## Error构造函数

````javascript
new Error([message[, fileName[,lineNumber]]])
````



> 通过**Error**的构造器可以创建一个错误对象。当运行时错误产生时，Error的实例对象会被抛出



## 常见的错误类型

* **以下这些常见的错误类型构造函数都是继承自Error构造函数**

### TypeError

* 数据类型错误

  ````javascript
  var a  
  console.log(a.b)  // Uncaught TypeError: Cannot read property 'b' of undefined
                    // 未捕获的错误类型   不能阅读undefined上的b属性
  ````

  ````javascript
  var a = null
  console.log(a.b)   // Uncaught TypeError: Cannot read property 'b' of null
  				   // 未捕获的错误类型   不能阅读null上的b属性
  ````

  ````javascript
  var a= 1
  console.log(a())   // Uncaught TypeError: a is not a function
    				   // 未捕获的错误类型  a 不是一个函数
  ````



### RangeError

* 范围错误

  ````javascript
  function fn() {   // 无限递归
    fn()
  }
  
  fn()      // Uncaught RangeError: Maximum call stack size exceeded
  										// 超出最大范围
  ````

  

### ReferenceError

* 引用变量不存在

  ````javascript
  console.log(a)   // Uncaught ReferenceError: a is not defined   a没有定义
  ````



### SyntaxError

* 语法错误

  ````javascript
  const a = """"   //Uncaught SyntaxError: Unexpected string     语法错误
  ````

  

## 错误对象

### name

> 错误的具体类型

### message

> 错误相关信息

### stack

> 函数调用栈记录信息





## 错误处理

### 捕获错误

````
try{
	//代码
}catch(err) {
	console.log(err)
}
````

 ````javascript
try{
  let a = null
  console.log(a.xxx)
} catch(err) {     // err是一个对象  构造函数是TypeError
  console.log(err)  
 // {message: "Cannot read property 'xxx' of null"
//    stack: "TypeError: Cannot read property 'xxx' of null↵    at                file:///E:/%E6%A1%8C%E9%9D%A2/project/promiseDemo/main.js:37:17"

//}
  console.log(err.name)     // TypeError
  console.log(err.message)  // Cannot read property 'xxx' of null
  console.log(err.stack)   // TypeError: Cannot read property 'xxx' of null
}

console.log(123);
 ````



### 抛出错误

````javascript
throw new Error()   // 抛出一个错误实例   通过Error构建函数构建出来
````

* 抛出错误后捕获错误

````javascript
function test() {
  if(Date.now() % 2 === 1) {
    console.log("偶数 处理任务")
  }else { 
    throw new Error("不对")    // 抛出错误
  }
}

try{
  test()
}catch(err) {         //  捕获错误
  console.log(err)    // Error: 不对
}
````

