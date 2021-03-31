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

  

## Symbol

> es6新增的原始数据类型  类似于字符串  唯一的

* 创建一个Symbol

  ````javascript
  const s1 = Symbol("yuweiqi") // 传的参数只是一个表示符  理解为注释
  
  const s2 = Symbol("yuweiqi")
  
  console.log(s1 === s2);   // false
  ````

* Symbol.for(xxx)

  > 先检查给定描述符的Symbol是否存在 存在就返回这个Symbol 不存在就创建一个这个Symbol

  ````javascript
  
  const s1 = Symbol.for("yyy")
  
  const s2 = Symbol.for("yyy")
  
  
  console.log(s1 === s2);        // true
  ````

  

* 给对象添加唯一的属性

  > 用于确保对象中的属性是唯一的  不会覆盖对象中的原有属性

  ````javascript
  
  const person = {}
  
  person[Symbol("uname")] = "ywq"
  
  person[Symbol("age")] = 18
  
  
  console.log(person);
  ````

  ````javascript
  const obj = {
    [Symbol("username")]: "yuweiqi",  //symbol作为属性名 用【】因为是表达式
    [Symbol("age")]: 20
  }
  ````

* 获取对象中的所有symbol属性名

  > *Object.getOwnPropertySymbols(person)*

  ````javascript
  const obj = {
    [Symbol("username")]: "yuweiqi",  //symbol作为属性名 用【】因为是表达式
    [Symbol("age")]: 20
  }
  
  console.log(Object.getOwnPropertySymbols(obj)) 
  //[Symbol(uname), Symbol(age)]
  ````

  

## 迭代器

> 1. 迭代器（iterator）是一种接口（对象中的一个属性）任何数据接口只要部署了iterator接口，就可以完成遍历操作
> 2. iterator主要提供for...of来遍历
> 3. iterator是迭代数据中的Symbol.iterator属性  是一个函数

### 迭代器概念

* 原生具备iterator接口的数据

  * Array
  * Arguments（伪数组）
  * Set
  * Map
  * String
  * TypedArray
  * NodeList（伪数组）




### iterator工作原理

1. 创建一个指针对象，指向当前数据结构的起始位置

2. 第一次调用对象的next方法，指针自动指向数据结构的第一个成员

3. 接下来不断的调用next方法 指针一直往后移动，直至指向最后一个成员



````javascript
const arr = ["唐僧", "孙悟空", "猪八戒"]


console.log(arr);

// 对象中的Symbol.iterator属性
const it = arr[Symbol.iterator]()

// 调用对象的next方法  返回包含value和done属性的对象
console.log(it.next());  // {value: "唐僧", done: false}
console.log(it.next());  // {value: "孙悟空", done: false}
console.log(it.next());  // {value: "猪八戒", done: false}
console.log(it.next());  // {value: undefined, done: true}

````



### 自定义迭代器

````javascript
const obj = {
  uname: "zhangsan",
  list: ["zs", "ls", "ww"],
  [Symbol.iterator]() {
    let index = 0
    let _this = this
    return {
      next() {
        if(index < _this.list.length) {
          const result = { value: _this.list[index], done: false }
          index++
          return result
        } else {
          return { value: undefined, done: true }
        }
        
      }
    }
  }
}


for(let i of obj) {
  console.log(i);
}

````





## 集合 set

* 创建集合

  ````javascript
  let s1 = new Set()    // 创建一个空的集合
  
  let s2 = new Set([1, 2, 3])  
  
  
  console.log(s2);
  ````

### 集合添加元素

> add()

````javascript
let s1 = new Set()    // 创建空集合

let s2 = new Set([1, 2, 3])


s2.add(5)


console.log(s2);  // Set(4) {1, 2, 3, 5}
````



​		

### 集合删除元素

````javascript
let s1 = new Set()

let s2 = new Set([1, 2, 3])

s2.delete(1)


console.log(s2);   // Set(2) {2, 3}
````

### 集合检测元素

````javascript

let s1 = new Set()

let s2 = new Set([1, 2, 3])


console.log(s2.has(2));  // 检测2是否s2中  返回布尔值   true
````



### 清空集合

````javascript
let s1 = new Set()

let s2 = new Set([1, 2, 3])


s2.clear()


console.log(s2);   // Set(0) {}
````



## map

> 类似于对象  但是键的对象不限于字符串

* 创建一个空的map

  ````javascript
  const p1 = new Map()
  
  
  console.log(p1);
  ````

### map添加元素

> set(属性名， 属性值)

````javascript
const p1 = new Map()

p1.set("uname", "zs")

console.log(p1);
````

````javascript
const p1 = new Map()


const n = function() {
  let num = 1
}


p1.set(n, "123")


console.log(p1);
````



### map删除元素

> delete(元素名)

````javascript
const p1 = new Map()


const n = function() {
  let num = 1
}


p1.set(n, "123")

p1.delete(n)

console.log(p1);
````

### map获取元素

> get(元素名)

````javascript
const p1 = new Map()


const n = function() {
  let num = 1
}


p1.set(n, "123")

console.log(p1.get(n));
````



