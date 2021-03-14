# es5继承

## 原型链继承

````javascript
// 原型链继承
function Father() {         // 父构造函数
    this.name = 123
}

Father.prototype.say= function() {   // 父类原型挂载方法
  console.log("hello")
}

function Son() {     // 子类构造函数

}

Son.prototype = new Father() //Father的实例对象赋值给Son的原型

Son.prototype.constructor = Son  //改变子类中constructor

const son = new Son()   // 子类创建实例对象son
 
son.say()    // 子类调用父类继承的方法 输出hello

````

* 缺点： 存在属性值会共享的问题	

  ````javascript
  function Father() {      // 父构造函数
    this.name = [1, 2, 3]  // 父类中的属性
  }
  
  Father.prototype.say= function() {  // 父类原型挂载方法
    console.log("hello")
  }
   
  function Son() {       // 子类构造函数
  
  }
  
  Son.prototype = new Father()   // 实现继承
  
  Son.prototype.constructor = Son  //改变子类中constructor
  
  const son1 = new Son()  // 子类创建实例son1
  
  const son2 = new Son()  // 子类创建实例son2
  
  son2.name.push(5) // 改变子类son2继承的name属性
  
  console.log(son1.name)   // [1,2,3,5]
  console.log(son1.name)   // [1,2,3,5]
  // 在son1修改的值被son2共享了
  ````

  



## 构造函数继承

````javascript
function Father() {        // 父构造函数
  this.name = "yuweiqi",
  this.age = [1,2,3]
}

function Son() {       //子构造函数
  Father.call(this)    
}											
// 子构造函数实例化对象的时候调用父构造函数同时改变this的指向为子构造函数的实例对象


const son1 = new Son()
const son2 = new Son()

son1.age.push(5)


console.log(son1.age)    // [1, 2, 3, 4]
console.log(son2.age)    // [1, 2, 3]
````

* 缺点： 没办法拿到父构造函数原型上的方法



## 组合继承（伪经典继承）

````javascript
function Father() {
  this.name = "yuweiqi",
  this.age = 12,
  this.address = [1, 2, 3]
}

Father.prototype.say = function() {
  console.log("hello world")
}


function Son() {
  Father.call(this)   // 继承父级属性
}

Son.prototype = new Father()   // 继承父级方法

Son.prototype.constructor = Son  // 改变子级constructor指向


const son1 = new Son()


console.log(son1)
console.log(son1.name)   // yuweiqi
console.log(son1.age)    // 12
console.log(son1.address)  // [1, 2, 3]
````

* 缺点： 调用两次父类构造函数





## 寄生组合继承（经典继承）

````javascript
function Father() {
  this.name = "yuweiqi",
  this.age = 12,
  this.address = [1, 2, 3]
}

Father.prototype.say = function() {
  console.log("hello world")
}


function Son() {
  Father.call(this)
}


//object.create(proto) 返回一个指定原型的新对象 proto表示执行的新建对象的原型对象
Son.prototype = Object.create(Father.prototype)

Son.prototype.constructor = Son


const son1 = new Son()


console.log(son1)

console.log(son1.name)
console.log(son1.age)
console.log(son1.address)
````





## 圣杯模式

> 封装成函数   函数内生成一个空对象用于隔离子级与父级

````javascript
function inherit(Target, Origin) {
  function F(){}
  F.prototype = Origin.prototype
  Target.prototype = new F()
  Target.prototype.constructor = Target
}

function Father() {

}

Father.prototype.say = function() {
  console.log("hello")
}


function Son() {}

inherit(Son, Father)

const son = new Son()


son.say()
console.log(son)
````



