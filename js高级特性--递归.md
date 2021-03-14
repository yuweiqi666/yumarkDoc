# js高级特性--递归

## 概念

>1、递归是不断调用自身的一种函数
>
>2、递归函数内部必须存在停止递归的条件，否则栈溢出
>
>3、递归的作用类似于循环，性能差

* 例1

  ````javascript
  function f1(n) {
    if(n > 0) {
      console.log(n)
      f1(n-1)
    }
  }
  f1(3)     // 输出3 2 1
  
  // 执行顺序  f(3)  输出3  销毁》 f(2) 输出2 销毁》 f(1)输出1  销毁   
  // 执行过程中没有执行上下文堆在栈中
              
  
  ````

* 例2

  ````javascript
  function f1(n) {
    if(n > 0) {
      f1(n-1)
      console.log(n)
    }
  }
  f1(3)       // 输出1 2 3
  ````

  

  * 运行过程

    > 注意： 1.同一个函数多次被调用也会产生多个不同的执行上下文
    >
    > ​			2.函数中代码在执行完毕之前会一直存在于执行栈中

    ![微信截图_20210304211145](.\imgs\微信截图_20210304211145.png)

* 例3

  ````javascript
  (function f1(n) {
      if(n === 3) {
          return
      } else {
          f1(++n)
          console.log(n)
      }
  })(0)                    // 同理 输出 3 2 1
  ````




## 实践

### 求阶乘

````javascript
function fn(num) {
  if(num === 1) {
    return num 
  } else {
    return num* fn(--num)
  }
}
````





### 手写深拷贝

````javascript
// 传的参数为obj 默认为{}
function clone(obj = {}) {
  // 过滤参数  如果obj不是object类型的或者obj为null  直接返回
  if(typeof obj !== "object" || obj === null) {
    return obj
  }
  // 定义深拷贝返回的数据result
  let result 
  // 判断obj 是对象还是数组
  result = obj instanceof Array ? [] : {}
  // 遍历obj
  for(let k in obj) {
    if(obj.hasOwnProperty(k)) {
      // 递归obj中每一个属性并依次赋值
      result[k] = clone(obj[k])
    }
  } 
  // 返回结果
  return result
}
````



​		

### 面试题

* 一个细胞，一个小时分裂一次，生命周期三小时，求n个小时后容器内有多少细胞

  ````javascript
  function total(n) {
    // 分裂过两次为黄色
    function yellow(n) {
      if(n === 0 || n=== 1) {
        return 0
      }
      return green(n-1)
    }
    // 分裂过一次为绿色
    function green(n) {
      if(n === 0) {
        return 0
      }
      return white(n-1)
    }
    // 新细胞为白色
    function white(n) {
      if(n===0) {
        return 1
      }
      return white(n-1) + green(n-1) + yellow(n-1)
    }
  
    return yellow(n) + green(n) + white(n)
  }
  ````

  



