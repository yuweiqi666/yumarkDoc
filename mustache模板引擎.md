# mustache模板引擎

## 数据变视图方法

### 纯DOM法

> for循环遍历数据 -> createElement -> innerHTML -> appendChild

* 非常的笨拙，没有实战意义

  ````javascript
  const data = [
    {uname: "zhangsan", age: 18, sex: "男"},
    {uname: "lisi", age: 19, sex: "女"},
    {uname: "wangwu", age: 20, sex: "男"}
  ]
  
  
  const ul = document.querySelector("ul")
  
  for(let i = 0; i < data.length; i++) {
    const li = document.createElement("li")
    // 创建DOM
    const div1 = document.createElement("div")
  
    div1.className = "div1"
    // 填充文本
    div1.innerHTML = data[i].uname + "的个人信息"
  
  
    const div2 = document.createElement("div2")
  
    const p1 = document.createElement("p")
  	
    p1.innerHTML = data[i].uname
    
    div2.appendChild(p1)
  
    const p2 = document.createElement("p")
  
    p2.innerHTML = data[i].age
  
    div2.appendChild(p2)
  
    const p3 = document.createElement("p")
  
    p3.innerHTML = data[i].sex
  
    div2.appendChild(p3)
    // 插入DOM树
    li.appendChild(div1)
  
    li.appendChild(div2)
  
    ul.appendChild(li)
  
  }
  ````

  

### 数组join法

> 曾经流行

````javascript
const data = [
  {uname: "zhangsan", age: 18, sex: "男"},
  {uname: "lisi", age: 19, sex: "女"},
  {uname: "wangwu", age: 20, sex: "男"}
]


const ul = document.querySelector("ul")

for(let i = 0; i < data.length; i++) {
  ul.innerHTML += [
    "<li>",
    "  <div>" + data[i].uname + "的个人信息</div>",
    "  <div>",
    "    <p>姓名：" + data[i].uname + "</p>",
    "    <p>年龄：" + data[i].age + "</p>",
    "    <p>性别：" + data[i].sex + "</p>",
    "  </div>",
    "</li>",
  ].join("")   // 通过数组的join方法弥补了es5字符串不能换行的问题
} 
````



### ES6反引号法

> 替代了es5的join法， 因为es6反引号可以换行

````javascript
const data = [
  {uname: "zhangsan", age: 18, sex: "男"},
  {uname: "lisi", age: 19, sex: "女"},
  {uname: "wangwu", age: 20, sex: "男"}
]


const ul = document.querySelector("ul")

for(let i = 0; i < data.length; i++) {
  ul.innerHTML += `
    <li>
      <div>${data[i].uname}的个人信息</div>
      <div>
        <p>姓名：${data[i].uname}</p>
        <p>年龄：${data[i].age}</p>
        <p>性别：${data[i].sex}</p>
      </div>
    </li>
  `
} 
````



### 模板引擎

> 解决数据变为视图最优雅的方法



## mustache使用

> 最早的模板引擎库， 为后续模板引擎的发展提供了崭新的思路
>
> **模板view还可以放在单独的<script>标签中（type设为text/template）**

````javascript
Mustache.render(view, data) // view为模板    data为数据
````



### 一般用法

> {{}}

````javascript
const container = document.querySelector(".container")

const data = {
  thing: "华为手机",
  state: "好开心"
}

const templateStr = `
  <p>今天我买了{{thing}}, 我{{state}}</p>
`

const domStr = Mustache.render(templateStr, data)


container.innerHTML = domStr

````





### 循环用法

> {{#list}}xxxxxxx{{/list}}

* 循环简单数组

  ````javascript
  const container = document.querySelector(".container")
  
  const templateStr = `
    {{#list}}
      <li>{{.}}</li>
    {{/list}}
  `
  
  
  const data = {
    list: ["苹果", "香蕉", "梨子"]
  }
  
  const domStr = Mustache.render(templateStr, data)
  
  container.innerHTML = domStr
  ````

  

* 循环复杂数组

  ````javascript
  const container = document.querySelector(".container")
  
  const data = {
    list: [
      {uname: "zhangsan", age: 18, sex: "男"},
      {uname: "lisi", age: 19, sex: "女"},
      {uname: "wangwu", age: 20, sex: "男"}
    ]
  }
  
  const templateStr = `
    <ul>
      {{ #list }}
        <li>
          <div>{{uname}}的基本信息</div>
          <div>
            <p>姓名：{{uname}}</p>
            <p>年龄：{{age}}</p>
            <p>性别：{{sex}}</p>
          </div>
        </li>
      {{ /list }}
    </ul>
  `
  
  const domStr = Mustache.render(templateStr, data)
  
  container.innerHTML = domStr
  ````

* 数组的嵌套

  ````javascript
  const container = document.querySelector(".container")
  
  const data = {
    list: [
      {uname: "张三", age: 18, hobbies: ["吃饭", "睡觉", "打豆豆"]},
      {uname: "李四", age: 20, hobbies: ["打球"]},
      {uname: "王五", age: 22, hobbies: ["玩游戏", "游泳"]}
    ]
  }
  
  const templateStr = `
    {{#list}}
      <li>
        <div>{{uname}}</div>
        {{#hobbies}}
          <p>{{.}}</p>
        {{/hobbies}}
      </li>
    {{/list}}
  
  `
  
  const domStr = Mustache.render(templateStr, data)
  
  
  container.innerHTML = domStr
  ````

  

### 元素显示与隐藏

> 使用布尔值，效果类似于vue中的v-if

````javascript
const container = document.querySelector(".container")

const templateStr = `
  {{#m}}
    <div>hello world</div>
  {{/m}}
`

const data = {
  m: false
}


const domStr = Mustache.render(templateStr, data)

container.innerHTML = domStr 
````





## mustache底层原理









