# js中this指向

> 1. 全局作用域下 定义的函数是window对象的方法  定义的变量是window对象的属性
> 2. 只用函数被调用了   函数中的this才有指向

## 默认绑定规则

* 在全局作用域下指向window

  ````javascript
  console.log(this)    // window
  ````



* 函数单独调用this指向window

  ````javascript
  function foo(){
    console.log(this)
  }
  
  foo()   // window  函数单独调用
  ````

  ````javascript
  const foo = {
    a:1,
    b: function() {
      console.log(this)   // this指向foo对象   应为foo调用了b   谁调用指向谁
      function test() {
        console.log(this) // this指向window  因为这边是函数单独调用
      }
      test()  // 独立调用
    }
  }
  
  foo.b()      
  ````

  ````javascript
  const foo = {
    a: 1,
    b: function() {
      console.log(this);   // this指向foo对象
      (function() {
        console.log(this) // this 指向window  立即指向函数  单独调用 指向window
      })()
    }
  }
  
  
  foo.b()  
  ````

  ````javascript
  const foo = {
    a: 1,
    b: function() {
      console.log(this)    // 指向foo对象
      function test() {
        console.log(this)   // 指向window
      } 
      return test
    }
  };
  
  
  (foo.b())()   //独立调用
  ````

  



## 隐式绑定规则

* 谁调用就指向谁

  ````javascript
  const foo = {
    a: function() {
      console.log(this)
    }
  }
  
  foo.a()   // 指向foo对象   因为是foo调用了a函数  所以this指向foo
  ````

  

  ````javascript
  const foo = {
    a: function() {
      console.log(this)
    }
  }
  
  
  const foo2 = foo.a   //  foo对象中的a属性重新复制  
  
  foo2()          //  这种情况属于函数单独调用   this指向window
  
  ````



### 父函数可以决定回调函数this的指向（高阶函数）

````javascript
const foo1 =  {
  a: 1,
  b: function() {
    console.log(this)
  }
}



function fn(f) {  
  f()
}

fn(foo1.b)  //this指向window 函数fn在执行时要进行形参赋值 f = foo1.b ==>f()
// 这种情况属于函数单独调用   
````



````javascript
const arr = [1,2,3]
arr.forEach(function() {
  console.log(this); // 默认指向window  可以看api文档  根据传参可以决定this不同的指向
})


setInterval(() => {
  console.log(this)   // 默认指向window
}, 1000);
````



## 显示绑定 call  apply  bind

> **详见call apply  bind笔记**



## new 实例化对象 this绑定（在构造函数中）

````javascript
function person(name, age) {
  this.name = name,     // this指向的是实例化对象
  this.age = age
}

const person1 = new person("zs", 18)


console.log(person1)
````







## 箭头函数中this的指向

> 取决于父环境中的this

* 箭头函数的内部this没有明确的指向， this的指向根据其父级作用域来决定

````javascript
const test = () => {
  console.log(this)
}

test()   // 父级作用域为全局作用域  所以this指向widnow
````



````javascript
function foo() {
  var test = () => {
    console.log(this)
  }

  test()
}

foo()   // 父级作用域中this指向window   因为函数foo单独调用  

const obj = {
  a:1,
  b: foo
}

obj.b()  // 父级作用域中this指向为对象obj  因为是obj调用了foo函数	

````

* 默认绑定对箭头函数无效

````javascript
function foo() {
  var test = () => {
    console.log(this)
  }

  return test
}

const obj = {
  a:1,
  b: foo
}

const obj2 = obj.b()

obj2()  // 输出为obj  （单独调用）  默认绑定对箭头函数无效
````

* 隐式绑定规则对箭头函数无效

````javascript
const test = () => {
  console.log(this)
}

const obj = {
  a: 1,
  b: test
}

obj.b()  // this指向window
````

* 显式绑定对箭头函数无效

````javascript
const obj2 = {
  a: 1
}

const test = () => {
  console.log(this)
}

test.call(obj2)   //  this指向还是window
````

