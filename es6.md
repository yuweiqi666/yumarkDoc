# es6

## 声明变量

### let

1. let声明变量不能重复声明

2. let声明变量 具有块级作用域

3. let声明变量没有变量提升

4. **let声明的变量不影响作用域链**

   ````javascript
   let num = 10
   
   if(true) {
     console.log(num);    // 输出为10
   } 
   ````

5. **暂时性死区**

   > 在块级作用域中声明的变量就和该块级作用域绑定  变量不会在通过作用域链向上一层查找

   ````javascript
   var num = 10
   
   if(true) {
     console.log(num);  // 输出num不会再通过作用连向上一层查找num变量
     let num = 20     //声明num变量同时与if块级作用域绑定 
   }
   // app.js:20 Uncaught ReferenceError: Cannot access 'num' before initialization
      
   ````

* let经典问题

  ````javascript
  ## 给每一个div添加点击事件
  ## 错误写法
  const divs = document.querySelectorAll("div")
  for(var i = 0 ; i < divs.length; i++) {         
     divs[i].addEventListener("click", () => {
        divs[i].style.background = "pink"
     })
  }
  // i是全局变量   异步触发dom事件回调函数的时候在回调函数中没有i变量 根据作用域链向外层全局作用域寻找i变量  此时同步for循环已经执行完   i=3  所以dom事件触发失败
  
  
  ## 正确写法1
  const divs = document.querySelectorAll("div")
  for(let i = 0 ; i < divs.length; i++) {         
     divs[i].addEventListener("click", () => {
        divs[i].style.background = "pink"
     })
  }
  // i 为块级作用域中的变量  每次for循环声明的i变量都具有不同的块级作用域 触发dom事件回调函数的时候i找的也是自己的块级作用域中的i变量
  
  ## 正确写法2（立即执行函数+闭包）
  const divs = document.querySelectorAll("div")
  for(var i = 0; i< divs.length; i++) {
     (function(i) {
       divs[i].addEventListener("click", function() {
         this.style.backgroundColor = "pink"
       })
     })(i)
   }
  
  ````

  

### const

* 用于声明常量（声明的常量内存地址不能更改）

  ````javascript
  ## 简单数据类型
  const a = 1
     
  a = 2     // const声明的变量不能修改内存地址  重新赋值修改了a的内存地址
  
  console.log(a)
  
  //app.js:27 Uncaught TypeError: Assignment to constant variable.
  ````

  ````javascript
  ## 引用数据类型
  const obj1 = {
    a: 1
  }
  
  obj1.a = 2 // 虽然修改了obj对象中的属性值  但是obj内存地址没有被改变 所以不报错
  
  console.log(obj1);
  ````

  

  

* 必须赋初始值

  ````javascript
  const a 
  
  console.log(a);
  
  // Uncaught SyntaxError: Missing initializer in const declaration
  ````

  

* 具有块级作用域

  ````javascript
  if(true) {
    const a = 1
  }
  console.log(a); 
  
  // app.js:52 Uncaught ReferenceError: a is not defined  
  ````

  

* 没有变量提升





### 比较

| var              | let              | const                |
| ---------------- | ---------------- | -------------------- |
| 函数作用域       | 块级作用域       | 块级作用域           |
| 变量提升         | 没有变量提升     | 没有变量提升         |
| 声明变量可以修改 | 声明变量可以修改 | 声明的变量不可以修改 |
|                  |                  | 声明必须赋值         |



## 解构赋值

> 按照一定的模式从数组和对象中提取值，对变量进行赋值

* 数组中的解构赋值

  > 1. 按照对应的位置对变量赋值
  > 2. 可以单独结构数组中的某一个元素

  ````javascript
  const arr = [1, 2, 3, 4]
  
  let [a, b, c, d] = arr    // 数组的解构赋值
  
  console.log(a);   // 1 
  console.log(b);   // 2
  console.log(c);   // 3
  console.log(d);   // 4
  
  ````

  

* 对象的解构赋值（用的比较多）

  > 1. 变量名与属性名必须一致（可以给变量名取别名   如 a: b   b就是变量a的别名）
  > 2. 可以单独结构某一个属性 

  ````javascript
  
  
  const obj = {
    uname: "yuweiqi",
    age: 18,
    sing: function() {
      console.log("我会唱歌");
    }
  }
  
  
  let {uname, age, sing} = obj //也可以单独结构赋值某个属性（不一定要全部属性）
  
  
  console.log(username);    // yuweiqi
  
  console.log(age);         // 18
  
  console.log(sing);         // function() {console.log("我会唱歌")}
  
  ````

  

