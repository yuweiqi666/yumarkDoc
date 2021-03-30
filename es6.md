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

### 数组解构赋值

> 1. 变量取值由位置决定，按照对应的位置对变量赋值

* 两边结构相同就可以进行解构赋值

  ````javascript
  const arr = [1, 2, 3, 4]
  
  let [a, b, c, d] = arr    // 数组的解构赋值
  
  console.log(a);   // 1 
  console.log(b);   // 2
  console.log(c);   // 3
  console.log(d);   // 4
  ````

  ````javascript
  
  let [, , a] = [1, 2, 3]
  
  console.log(a);    // 3
  ````

  ````javascript
  let [a, arr] = [1, [2, 3]]
  
  console.log(arr);     // [2, 3]
  ````

  ````javascript
  let [a, [,b]] = [1, [2, 3]]
  
  console.log(b);   // 3
  ````

  

* 解构不成功的变为undefined

  ````javascript
  let [a, b, c] = [1, 2]
  console.log(a);  // 1
  console.log(b);  // 2
  console.log(c);  // undefined
  ````

* 可以部分解构

  ````javascript
  let [a, b] = [1, 2, 3]
  
  
  console.log(a);   // 1 
  console.log(b);   // 2
  ````

* 可以设置默认值  且只有赋值为undefined才会有默认值

  ````javascript
  let [a = 1, b = 2] = [5, undefined]
  
  console.log(a);   // 5    只有赋值为undefined才会触发默认值
  console.log(b);   // 2
  ````

  

### 对象解构赋值（用的比较多）

> 1. 变量必须与属性同名（区别于数组按对应位置给变量赋值）

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

* 区分模式与变量

````javascript
let {p: {a}} = {p: {a: 2}}

console.log(a); // 2

console.log(p); // app.js:146 Uncaught ReferenceError: p is not defined


// 此时的p不是作为变量  p是模式
````

````javascript
let {p, p: {a}} = {p: {a: 2}}

console.log(a);  // 2

console.log(p); // {a: 2}

// 此时的p和a都是变量
````





* 对象解构赋值变量可以取别名

````javascript
let {a: m} = {a: 1}

console.log(m);   // 1   m为变量a的别名    其实和上面的模式与变量区别可以一起理解
````



* 对象解构指定默认值

````javascript
let {a= 1} = {a: undefined}


console.log(a)   // 1     赋值为undefined取默认值1
````



## 模板字符串

> 用``表示  

* 字符串拼接变量

````javascript
let a = 'zhangsan'


let b = `我是${a}`


console.log(b);
````

* 内容中可以直接使用换行符

```javascript
let a = `<ul>
<li>123</li>
</ul>`

console.log(a);    
```



## 对象扩展

* 对象属性方法的简写

  ````javascript
  let uname = "123"
  
  let say = function() {
    console.log(123);
  }
  
  const obj = {  
    uname,      // 相当于uname:uanme
    say         // 相当于say:say
  }
  ````

  ````javascript
  const obj = {
    uname,
    say() {           //  对象中方法的简写
      console.log('hello');
    }
  }
  
  
  
  // 相当于   say: function() {xxxxx}
  ````

  

* 对象中的属性名可以用表达式(用[])

  ````javascript
  
  const obj = {
    a: 1,
    ["m" + "n"]: 2
  }
  
  
  console.log(obj.mn);   // 2
  
  console.log(obj['mn']);  // 2
  ````

  



## 函数扩展

* 箭头函数

  * 详情见箭头函数章节

    

* es6允许给函数参数默认值

  ````javascript
  const add = (a, b, c = 5) => a + b + c
  
  
  let result = add(1, 2)
  
  console.log(result);    // 8    c没有赋值就传默认值
  ````

* 参数默认值和解构赋值结合使用

  ````javascript
  const obj = {
    uname: "zhangsan",
    age: undefined,
    hobbies: ['ball', 'sleep']
  }
  
  
  function f({uname, age = 20, hobbies}) {
    console.log(uname);      // zhangsan
    console.log(age);        // 默认值20  应为obj中的age属性为undefined
    console.log(hobbies);    // ['ball', 'sleep']
  }
  
  
  f(obj)
  ````

  

## 剩余参数（rest参数）

> 函数传参有多个参数时，剩余参数必须写在最后一个

* 将多余的参数转变为数组（es5 的arguments是转化的伪数组）

  ````javascript
  function f(a, b, ...args) {
    console.log(args);
  }
  
  
  f(1, 2, 3, 4, 5)     // [3, 4, 5]
  ````

  





## 扩展运算符

> 区别：扩展运算符用在实参        剩余参数用于形参   

* 将数组转化为逗号分隔的参数序列

  ```javascript
  function f() {
    console.log(arguments);
  }
  
  
  const n = [1, 2, 3]
  
  f(...n)   // 相当于  f(1, 2, 3)   输出为伪数组  [1, 2, 3]
  ```

  

* 对数组进行浅拷贝

  ````javascript
  const arr1 = [1, 2, 3]
  
  
  
  const arr2 = [...arr1]
  
  
  console.log(arr2);    // [1, 2, 3]
  ````



* 合并数组  (es5  用的concat方法)

  ````javascript
  const arr1 = [1, 2, 3]
  
  const arr2 = [4, 5, 6]
  
  
  const arr3 = [...arr1, ...arr2]
  
   
  console.log(arr3); // [1, 2, 3, 4, 5, 6]
  ````

* 伪数组转化为真数组

  ````javascript
  const divs = document.querySelectorAll('div') // divs是一个伪数组
  
  console.log([...divs]);   // [...divs]是真数组
  ````

  

