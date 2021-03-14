# call()、 apply()、bind()

## 作用

* 改变this的指向

  ````javascript
  var a = 111
  const son = {
    a:1,
    b: function(name, age) {
      console.log(this.a, `我是${name}, 我今年${age}岁`)
    }
  }
  
  const son2 = {
    a: 123
  }
  
  
  son.b.call(son2, "于伟奇", "20")      
  son.b.apply(son2, ["于伟奇", "20"])    
  son.b.bind(son2,"于伟奇", "20")() 
  
  //123 "我是于伟奇, 我今年20岁"
  //123 "我是于伟奇, 我今年20岁"
  //123 "我是于伟奇, 我今年20岁
  ````

## 原理

> 1. 将要调用的函数挂载为改变指向的对象的属性
> 2. 调用对象中的方法属性
> 3. 删除对象中的属性



## 区别

|      | call               | apply              | bind                 |
| ---- | ------------------ | ------------------ | -------------------- |
| 作用 | 改变this的指向     | 改变this的指向     | 改变this的指向       |
| 参数 | 参数为a,b,c形式    | 参数为数组形式     | 参数为a,b,c形式      |
| 用法 | 改变this指向并调用 | 改变this指向并调用 | 只改变this指向不调用 |



## 实现源码

### call()

````javascript
Function.prototype.myCall = function(targetThis, ...params) {
	targetThis.f = this         // 将需要调用的函数this 挂载到需要指向的targetThis对象中
    targetThis.f(...params)     // 在targetThis对象中调用函数
    delete targetThis.f         // 删除targetThis对象中挂载的属性
}

const son1 = {
  name: "我是son1",
  say: function() {
    console.log(this.name, arguments)
  }
}

const son2 = {
  name: "我是son2"
}


son1.say.myCall(son2, 1, 2)
````



### apply()

````javascript
Function.prototype.myApply = function(targetThis, args= []) {
  if(!(args instanceof Array)) {   // 如果传的不是参数不是数组形式的话报错
    throw("参数必须为数组形式哦")        
  }
  targetThis.f = this
  targetThis.f(...args)
  delete targetThis.f
}


const son1 = {
  name: 1,
  say: function() {
    console.log(this.name, arguments)
  }
}

const son2 = {
  name: 2
}

son1.say.myApply(son2, "sa", "asas")
````



### bind()

````javascript
Function.prototype.myBind = function(targetThis, ...params1) {
  targetThis.f = this
  return function(...params2) {
    let newParams = [...params1,...params2]
    targetThis.f(...newParams)
    delete targetThis.f
  }
}


const son1 = {
  name: 1,
  say: function() {
    console.log(this.name, arguments)
  }
}

const son2 = {
  name: 2
}

son1.say.myBind(son2,1,2)(3)

````

