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

Son.prototype.constructor = Son  //改变子类中constructor指向

const son = new Son()   // 子类创建实例对象son
 
son.say()    // 子类调用父类继承的方法 输出hello

````

* 缺点：

  1. 单纯的使用原型链继承存在原型属性值（**尤其是引用类型属性， 因为原型链继承所有的属性继承都来自原型 所有实例共享**）共享的问题
  
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
     
     son2.name.push(5) // 改变子类son2继承的name属性   其实是改变了son2的原型上的name属性的值 又因为原型上的属性为所有的实例对象共享的 所有在这里会同时改变了son1的name属性
     
     console.log(son1.name)   // [1,2,3,5]
     console.log(son2.name)   // [1,2,3,5]
   // 在son1修改的值被son2共享了
     ````
  
     
  
  2. 使用原型链继承 在创建子类实例时 无法向父类传参
  
     ![原型链继承缺点2](.\imgs\原型链继承缺点2.png)
  
     
  
     ​		![原型链继承缺点22](.\imgs\原型链继承缺点22.png)

## 构造函数继承

````javascript
function Father() {        // 父构造函数
  this.name = "yuweiqi",
  this.age = [1,2,3]
}

function Son() {       //子构造函数
  Father.call(this)    
}											
// 子构造函数实例化对象的时候调用父构造函数同时改变this的指向为子构造函数的实例对象 这样保证了 1.继承的属性全部都是实例独有的 不存在继承属性共享相互影响的问题， 2.创建子类实例可以同时向父类传递参数 弥补了原型链继承的两大致命缺点


const son1 = new Son()
const son2 = new Son()

son1.age.push(5)


console.log(son1.age)    // [1, 2, 3, 4]
console.log(son2.age)    // [1, 2, 3]
````

* 缺点： 
  1. 只能继承父类的实例属性和方法 没办法拿到父类构造函数原型上的属性和方法



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

* 缺点： 

  1. 调用两次父类构造函数， 导致了创建的实例和何其原型上存在相同的属性（name） （虽然实例上的属性会覆盖原型上的属性 不存在继承属性共享的问题）

  ![组合继承缺点123](.\imgs\组合继承缺点123.png)

  ![组合继承缺点](.\imgs\组合继承缺点.png)





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


//object.create(proto) 返回一个指定原型的新对象 proto表示执行的新建对象的原型对象 这样就不存在实例对象属性和原型属性重复的问题了
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
  function F(){}   // 使用一个空函数F作为中间层 对比es6的Obejct.create()
  F.prototype = Origin.prototype
  Target.prototype = new F()
  Target.prototype.constructor = Target
  Target.prototype.uber = Origin.prototype // uber表明了继承自谁
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



