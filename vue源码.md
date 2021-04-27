# vue源码

## 虚拟dom和diff算法

### 虚拟dom

> 通过js来描述真实dom的层次结构 真实dom的所有属性都在虚拟dom中有对应的属性

````javascript
// 真实dom
<div class="box">
	<p>我是标题</p>    
</div>

// 虚拟dom
{
    “sel”: "div",
    "data": {
        "class": {"box": true}
    },
    "children": [
        {
            "sel": "p",
            data: {},
            text: "我是一个标题"
        }
    ]
}
````





### diff算法

> 1. 进行精细化比对，实现最小量更新
> 2. 虚拟dom之间精细化比较   最后反应在真实dom上

* 研究内容
  * 虚拟dom如何被渲染函数（h函数）产生
    * 手写h函数
  * diff算法原理
    * 手写diff算法
  * 虚拟dom如何通过diff变回真正的dom



#### snabbdom

> 著名的虚拟dom库, diff算法的鼻祖

* 环境搭建
  1. snabbdom库是dom库，不能在node环境中运行，所以需要搭建webpack和webpack-dev-server开发环境



### h函数

> 1. 用于产生虚拟dom
>
> 2. h函数写法多样
>
>    1. h("div")
>
>    2. h("div", "文字")
>
>    3. h("div", [])
>
>       // 目前手写只实现4 5 6种形式
>
>    4. h("div", {}, [])
>
>    5. h("div", {}, "文字")
>
>    6. h("div", {}, h())

* 虚拟节点的属性

  ````javascript
  {
      children: undefined,  // 节点的子元素
      data: {},        // 节点的属性 样式等
      elm: undefined,     //  节点有没有上树
      key: undefined,     // key 唯一标识  服务于最小量更新
      sel: "div",          // 选择器
      text: "我是一个盒子"   // 文字 
  }
  ````

* 创建虚拟节点

  ````javascript
  var myVnode = h("a", { props: {href: "http://www.baidu.com"} }, "张三")
  
  console.log(myVnode)
  
  // 虚拟节点
  {
      children: undefined
  	data: {
          props: {
              href: "http://www.atguigu.com"
          }
      }
  	elm: undefined
  	key: undefined
  	sel: "a"
  	text: "张三"
  }
  ````

* **h函数嵌套使用，得到虚拟DOM树**

  ````javascript
  var myVnode3 = h("ul", {class: {"father": true}}, [
    h("li",  "香蕉"),  // 中间的对象可以省略
    h("li",  "苹果"),
    h("li",  "梨子", h("div", "嘻嘻哈哈")) // 嵌套一个子元素时数组可以省略
  ])
  
  ````

  