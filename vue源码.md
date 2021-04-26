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



#### snabbdom

> 著名的虚拟dom库, diff算法的鼻祖

* 环境搭建
  1. snabbdom库时dom库，不能在node环境中运行，所以需要搭建webpack和webpack-dev-server开发环境