### Object.defineProperty

````javascript
Object.defineProperty(obj, prop, descriptor)

// obj: 需要定义属性的对象名
// prop: 需要定义的属性
// descriptor: 属性的描述符
````

#### 属性描述符

* value  设置属性值

  ````javascript
  var car = {}
  Object.defineProperty(car, "price", {
    value: 1000
  })
  console.log(car);  // { price: 1000 }
  // car对象设置price属性为1000
  ````

* writable 是否可被重新赋值  **默认为false**

  ````javascript
  var car = {}
      Object.defineProperty(car, "price", {
        value: 1000,
        writable: true  // 属性可以被重新赋值
      })
      car.price = 2000
      console.log(car); // { price: 2000 }
  ````

* enumerable 是否可以枚举  **默认为false**

  ````javascript
  var car = {
        name: "123"
  }
  Object.defineProperty(car, "price", {
    value: 1000,
    writable: true,
    enumerable: true   // 属性可以被遍历出来
  })
  car.price = 2000
  // es6遍历对象  ["name", "price"]
  console.log(Object.keys(car)); 
  
  
  //es5遍历对象
  // var keys = []      
  //for(key in car) {
  //   keys.push(key)
  // }
  // console.log(keys);
  ````

* configurable   属性是否可以被删除 、属性描述是够可以重新被定义（writable, configurable, enumerable）      **默认false**

  ````javascript
  var car = {
    name: "123"
  }
  Object.defineProperty(car, "price", {
    value: 1000,
    writable: true,
    enumerable: true,
    configurable: false // false 则属性特性不能被修改
  })
      
  Object.defineProperty(car, "price", {
    writable: false,
    enumerable: false          // 修改属性特性 直接报错
  }) 
  console.log(car);
  ````

#### 访问器描述符

> **当使用了getter或setter方法，不允许使用writable和value这两个属性**

* configurable
* enumerable

* get()   获取属性是执行的函数

  ````javascript
  var car = {}
  var count = 1000
  Object.defineProperty(car, "price", {
    get() {
      console.log("属性被读取了");
      return count
    }
  })
  console.log(car.price)     // 属性被读取了  
  						   // 1000
  									
  ````

  

* set()   设置属性时执行的函数

  ````javascript
  var car = {}
  var count = 1000   // 单独定义一个变量是防止溢出  无法使用value和writable
  Object.defineProperty(car, "price", {
    get() {
      console.log("属性被读取了");
      return count
    },
    set(newVal) {
      console.log("属性被修改了");
      count = newVal                 // car.price = 2000
    }                                // 属性被修改了  
  						   		   // car.price
      							   // 2000
  })
  
  
    
  
  
  // var person = {               
  //     a: 1													
  // }
  // Object.defineProperty(person, 'a', {
  // get() {
  //   return this.a
  // },
  // set(val) {
  //   this.a = val
  // }
  // })
  
  
  //person.a → get.call(person) → this.a → person.a → get.call(person) → this.a......      //这样会内存溢出
  ````

#### 定义对象比较

> ```javascript
> Object.getOwnPropertyDescriptor(apple, 'name')  // 获取对象的属性描述符
> ```

* 传统对象添加属性

  ````javascript
  var car1 = {}
  car1.price = 1000
  console.log(Object.getOwnPropertyDescriptor(car1, "price"));
  
  // {value: 1000, writable: true, enumerable: true, configurable: true}
  ````

* 描述符添加属性

  ````javascript
  var car2 = {}
  Object.defineProperty(car2, "price", {
    value: 2000
  })
  console.log(Object.getOwnPropertyDescriptor(car2, "price"));
  
  // {value: 2000, writable: false, enumerable: false, configurable: false}
  ````

  



