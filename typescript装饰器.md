# typescript装饰器



# 概念

* 装饰器是一种特殊的类型声明

* 附加到类/方法/属性/参数上

* 装饰器本质就是一个函数 在ts解释执行时调用一次

  ```typescript
const ADecorator: ClassDecorator = function (target: Function) {
	console.log('i am decorator');
}
  
@ADecorator
class A {}
  ```

> ts运行时就会调用装饰器`ADecorator`

  <img src='.\imgs\decorator4.png'>



## 装饰器工厂

* 当我们需要对装饰器进行定制，可以定义一个工厂函数，传入一些参数，最终返回装饰器

```typescript
const ADecoratorFac: (data: string) => ClassDecorator = function (data: string) {
  return function (target: Function) {
     console.log('i am decorator', data);
  }
}

@ADecoratorFac('test')
class A {}
```



> `ADecoratorFac` 是一个工厂函数，返回一个装饰器。工厂函数传入参数data用于定制装饰器

  <img src='.\imgs\decorator6.png'>





## 装饰器叠加

* 可以同时使用多个装饰器

```typescript
const A1Decorator: ClassDecorator = function (target: Function) {
  console.log('A1Decorator装饰器');
}  

const A2Decorator = function (target: Function) {
  console.log('A2Decorator装饰器');
}

@A1Decorator
@A2Decorator
class A {}
```

  ![微信截图_20230407144717](.\imgs\decorator1.png)







# 环境搭建

> **装饰器是一项实验性特性，必须在命令行或`tsconfig.json`里启用`experimentalDecorators`编译器选项**

<img src='.\imgs\decorator2.png'>



![image-20230407110905895](.\imgs\decorator3.png)







# 装饰器种类

## 类装饰器

### 介绍

* 类装饰器用于装饰类 写在类名上方

* 类装饰器 方法参数`target`为这个装饰器类

* 可以用于扩展被装饰类的方法或属性（一般扩展的是通用的方法或者属性）



 ```typescript
const playDecorator: ClassDecorator = function (target: Function) {
  target.prototype.playMusic = function () {
    console.log('播放音乐');
  }
}


@playDecorator
class PlayerClass {}

const player = new PlayerClass();

(player as any).playMusic()  // 播放音乐
 ```



### 案例

* 响应消息统一处理

```typescript
const messageDecorator: ClassDecorator = function (target: Function) {
  target.prototype.message = function (content: string) {
    console.log(content);
  }
}

  

@messageDecorator
class LoginController {
  login () {
    console.log('登录成功');
    (this as any).message('返回登录成功响应')
   }
}

const loginController = new LoginController()

loginController.login()
// 登陆成功
// 返回登录成功响应
```





## 方法装饰器

### 介绍

* 方法装饰器用于装饰类中的方法，写在方法的上方

* 方法装饰器的种类

* 实例方法装饰器

  * 参数

    1. target：实例的原型对象

    2. propertyKey：方法名称

    3. descriptor：方法描述（参考`Object.defineProperty`）

       

* 静态方法装饰器
  * 参数
    1. target: 装饰的类对应的构造函数
    2. propertyKey: 方法名称
    3. descriptor：方法描述



```typescript
const getNameDecorator: MethodDecorator = function (target: Object, propertyKey: string | symbol, descriptor: PropertyDescriptor) {
  console.log('i am method decorator');
}

class Test {
  @getNameDecorator
  getName () {
    console.log('i am zhangsan');
  }
}
```

  

### 案例

* 实现文字高亮

  ```typescript
  const highLightDecorator: MethodDecorator = function (target: Object, propertyKey: string | symbol, descriptor: PropertyDescriptor) {
    const method = descriptor.value
    descriptor.value = function () {
      console.log(`<div style='color: red;'>${method()}</div>`);
    }
  }
  
    
  
  class Color {
    @highLightDecorator
    show () {
      return 'i am zhangsan'
    }
  }
  
  const color = new Color()
  color.show()
  ```




* 实现指定延迟时间执行（工厂函数）

  ```typescript
  const sleepDecorator: (time: number) => MethodDecorator = function (time) {
    return function (target: Object, propertyKey: string | symbol, descriptor: PropertyDescriptor) {
      const method = descriptor.value
        descriptor.value = function () {
          setTimeout(() => {
            method()
          }, time)
        }
    }
  }
  
  class Test {
   // sleepDecorator装饰器实现延迟5000ms执行response 
    @sleepDecorator(5000)
    response () {
      console.log('响应数据');
    }
  }
  
  const test = new Test()
  test.response()
  ```




* 全局异常处理

  ```typescript
  const errorDecorator: MethodDecorator = function (target: Object, propertyKey: string | symbol, descriptor: PropertyDescriptor) {
    const method = descriptor.value
    descriptor.value = function () {
      try {
        method()
      } catch (error) {
        console.log(error);
      }
    }
  }
  
  class Test {
    @errorDecorator
    response () {
      throw new Error('err result')
    }
  }
  
  const test = new Test() 
  test.response()
  ```

  



* 实现数据请求

  ```typescript
  const fetchData: (url: string) => MethodDecorator = function (url: string) {
    return function (target: Object, propertyKey: string | symbol, descriptor: PropertyDescriptor) {
      const method = descriptor.value
      new Promise((resolve, reject) => {
      // 根据url通过ajax请求数据
      setTimeout(() => {
          resolve('返回用户数据')
        }, 2000) 
      })
        .then(method)
    }
  }
  
  class UserController {
    @fetchData('http://xxxxxxxxx')
    getUser (data: any) {
      console.log(data); 
    }
  }
  
  const userController = new UserController()
  ```
  



## 属性装饰器

### 介绍

* 属性装饰器作用于类中的属性，写在属性的上方

* 属性装饰器的种类

* 静态属性装饰器
  * 参数
    1. target：装饰的类对应的构造函数
    2. name：属性名

* 实例属性装饰器
  * 参数
    1. target：实例的原型对象
    2. name：属性名



 ```typescript
const propDecorator: PropertyDecorator = function (target: Object, propertyKey: string | symbol) {
  console.log('target', target);
  console.log('propertyKey', propertyKey);
}

  

class Test {
  @propDecorator
  uname: string = 'zhangsan'
}
 ```



### 案例







## 参数装饰器

### 介绍

* 参数装饰器作用于类中方法的参数，写在方法参数的前面

* 参数装饰器的种类

* 静态方法参数装饰器

  * 参数

    1. target：当前修饰参数的方法所在类对应的构造函数
    2. propertyKey：参数名称
    3. parameterIndex：当前装饰的参数位置/索引

    

* 实例方法参数装饰器
  * 参数
    1. target：实例的原型对象
    2. propertyKey：参数名称
    3. parameterIndex：当前装饰的参数位置/索引

```typescript
const paramsDecorator: ParameterDecorator = function (target: Object, propertyKey: string | symbol, parameterIndex: number) {
  console.log('target', target);
  console.log('propertyKey', propertyKey);
  console.log('parameterIndex', parameterIndex);
}
  
class Test {
  uname: string = 'zhangsan'
  getName(@paramsDecorator data: string) {
    console.log('data', data);
  }
}
```





### 案例





## 元数据

### 介绍



### 案例







# 装饰器的执行顺序