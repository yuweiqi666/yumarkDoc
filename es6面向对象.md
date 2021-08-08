# es6面向对象

## 类基本语法

> 1. es5创建实例对象的方法是通过构造函数
> 2. es6通过class关键词定义类来实例化对象

### 概念

* class定义类是es6的一个语法糖，本质还是一个函数

  > 定义类可以理解为构造函数的另一种写法

  ````javascript
  class Person {
    constructor() {
      this.uname ="zhangsan",
      this.age = 18
    }
  
    sing() {
      console.log("我会唱歌");
    }
  }
  
  const p1 = new Person()  // 示例化对象
  
  console.log(p1);   // Person {uname: "zhangsan", age: 18}
  
  console.log(typeof Person);    // function
  
  console.log(Person === Person.prototype.constructor); 
  // true
  ````

  

* 类中的所有方法都定义在类的原型上

  ````javascript
  class Person {
      constructor() {
          xxx
      }
      say() {
          xxxx
      }
      sing() {
          xxxx
      }
  }
  //  constructor、 say 、sing 都是定义在Person的原型上
  
  // 等同于
  
  Person.prototype = {
      constructor(){},
    	say(){},
      sing(){}
  }
  
  ````

  

### constructor方法

> 1. constructor方法是类的默认方法
> 2. 通过new实例对象时自动调用
> 3. 类中没有显式定义时，会默认添加一个空的constructor方法

````javascript
class Person{}
// 等同于
class Person{
    constructor() {}
}
````



### 类的实例

> 通过new创建一个类的实例

````javascript
class Person{
  constructor() {
    this.name = "zhangsan",
    this.age = 12
  }
}

const p = new Person()
````



### 取值函数（getter）和存值函数（setter）

> 在class内部使用get 、set关键词来对属性进行拦截

````javascript
class Person{
  constructor() {
    this.uname = "zhangsan"
  }
  sing() {
    console.log("hello");
  }
  get prop() {
    console.log("获取prop属性");
    return "prop"
  }

  set prop(value) {
    console.log("设置属性", value);
  }
}


const p = new Person()   


console.log(p.prop);  // 获取prop属性   prop

p.prop = 123    // 设置属性    123

console.log(p.prototype.prop);   // 获取prop属性   prop

Person.prototype.prop = 555  // 设置属性    555
````





### 类中的静态方法

> 类中的方法相当于是实例对象原型的方法，在类中的方法加上static关键词，该方法就不能被实例对象调用，而是直接通过类来调用

````javascript

class Person{
  constructor() {

  }
  static go() {         // 静态方法只能通过类来调用  不在实例原型上
    console.log("hello, world");
  }
}


const p = new Person()

Person.go()  // hello, world


p.go() // p.go is not a function
````



### 类中的静态属性

````javascript
class Person {}

Person.age = 18   
// age属性没有定义在Person类中的constructor方法中 是静态属性
````





## 类继承

> 是用extends实现继承 子类继承父类的属性和方法

````javascript
class Father {
  constructor() {
    this.x = 1
    this.y = 2
  }
  say() {
    console.log("hello world");
  }
}


class Son extends Father {   // Son继承 Father的属性和方法

}


const son = new Son()   // 实例化子类


console.log(son.x);  // 1
console.log(son.y);  // 2
son.say()   // hello world

````





### super关键字

* 在子类的constructor方法中是用super关键字可以调用父类的constructor方法

  ````javascript
  // 在子类Son中使用父类Father的sum方法
  class Father {                       
    constructor(x, y) {
      this.x = x
      this.y = y
    }
  
    sum () {
      console.log(this.x + this.y);
    }
  }
  
  
  
  class Son extends Father {
    constructor(x, y) {
      super(x, y)   
      // super关键字 在子类中使用子类传的x，y作为参数调用父类的constructor方法 相当于Father.prototype.constructor.call(this)
    }
  }
  
  
  
  const son = new Son(2, 4)
  
  
  son.sum()   // 6
  
  ````





* 继承中先看子类有没有这个方法 没有再去父类找（重写）

  ````javascript
  class Father{
    say() {
      console.log("i am Father");
    }
  }
  
  
  class Son extends Father {
    say() {
      console.log("i am Son");
    }
  }
  
  
  const son = new Son()
  
  
  son.say()   //  i am Son    子类Son中有say方法  所以优先执行子类中say方法
  ````

  

* super关键字调用父类的普通函数

  ````javascript
  class Father{
    say() {
      console.log("i am Father");
    }
  }
  
  
  class Son extends Father {
    say() {
      super.say()   // 调用父类的普通函数
    }
  }
  
  
  const son = new Son()
  
  
  son.say()   // i am father
  ````

  

   

* 继承父类的加法操作  同时扩展子类的减法操作

  ````javascript
  
  class Father {
    constructor(x, y) {
      this.x = x
      this.y = y
    }
  
    sum () {
      console.log(this.x + this.y);
    }
  }
  
  
  
  class Son extends Father {
    constructor(x, y) {
      super(x, y)  
        // super调用必须在this之前  必须先调用父类构造函数 再调用子类构造函数
      this.x = x
      this.y = y
    }
  
    jianfa() {
      console.log(this.x - this.y);
    }
  }
  
  
  
  const son = new Son(5, 2)
  
  
  son.jianfa()  // 子类自生的jianfa方法     3
  
  son.sum()    //继承父类的sum方法         7
  ````

  





## 注意事项

* 类没有变量提升  所以必须先定义类  在实例化对象
* 类里面的属性和方法一定要加this使用
* 类里面this指向问题
  * constructor方法中的this指向创建的实例对象
  * 类中方法的this指向方法的调用者