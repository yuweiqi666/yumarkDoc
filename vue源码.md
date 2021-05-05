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
> 2. 虚拟dom之间精细化比较   最后反应在真实dom上    （patch函数）

* 研究内容
  * 虚拟dom如何被渲染函数（h函数）产生
    * 手写h函数
  * diff算法原理
    * 手写diff算法
  * 虚拟dom如何通过diff变回真正的dom
  
  
  
* √ diff算法心得

  > **2， 3 的操作在实际的vue开发中基本不会遇到的**

  1. 最小量更新很厉害，key属性很重要**（key是dom节点的唯一标识，告诉diff算法更改前后是同一个dom节点）**

     ````javascript
     const vNode1 = h("ul", {}, [
       h("li", {key: "A"}, "A"),
       h("li", {key: "B"}, "B"),
       h("li", {key: "C"}, "C"),
       h("li", {key: "D"}, "D"),
     ])
     
     const vNode2 = h("ul", {}, [
       h("li", {key: "E"}, "E"),
       h("li", {key: "A"}, "A"),
       h("li", {key: "B"}, "B"),
       h("li", {key: "C"}, "C"),
       h("li", {key: "D"}, "D")
     ])
     // 如果节点不加key值 那么要在A B C D 节点前加 E  首先是在A B C D的后面加E节点， 然后将E放到第一位 A放到第二位 B放到第三位 C放到第四位  D方到第五位
     // 如果 加了key值   diff算法就能识别改变前后的A B C D为同一节点 会直接在A前面增加E节点  增加效率
     ````

  2.  只有同一个虚拟节点， 才进行精细化比较**（选择器相同且key相同）**

     ````javascript
     const vNode1 = h("ul", {}, [
       h("li", {key: "A"}, "A"),
       h("li", {key: "B"}, "B"),
       h("li", {key: "C"}, "C"),
       h("li", {key: "D"}, "D"),
     ])
     
     const vNode2 = h("ol", {}, [
       h("li", {key: "E"}, "E"),
       h("li", {key: "A"}, "A"),
       h("li", {key: "B"}, "B"),
       h("li", {key: "C"}, "C"),
       h("li", {key: "D"}, "D")
     ])
     // 父级节点从ul 变为 ol  则后面的子级节点不会再进行精细化比较 而是暴力拆除 然后重新上树
     ````

  3. 只进行同层比较，不会进行跨层比较

     ````javascript
     const vNode1 = h("ul", {}, [
       h("li", {key: "A"}, "A"),
       h("li", {key: "B"}, "B"),
       h("li", {key: "C"}, "C"),
       h("li", {key: "D"}, "D"),
     ])
     
     const vNode2 = h("ul", {}, h("section", {}, [
       h("li", {key: "E"}, "E"),
       h("li", {key: "B"}, "B"),
       h("li", {key: "C"}, "C"),
       h("li", {key: "D"}, "D")
     ]))
     // 在四个li外层多加一个section节点 那么更新前后的四个li不是在一个层级 所以不会精细化比较 而是暴力拆除老的  重新插入新的
     ````



#### snabbdom

> 著名的虚拟dom库, diff算法的鼻祖

* 环境搭建
  1. snabbdom库是dom库，不能在node环境中运行，所以需要搭建webpack和webpack-dev-server开发环境



#### 手写h函数

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
>    4. h("div", {}, [h(), h(), h()...])
>
>    5. h("div", {}, "文字")
>
>    6. h("div", {}, h())

````javascript
// h函数   返回时的vNode函数只是将传入的参数包成一个对象

export default function(sel, data, c) {
  // 检查参数的个数
  if(arguments.length !== 3) {
    throw new Error("对不起， 低配版h函数必须传三个参数")
  }
  // 检查参数的类型
  if(typeof c == "string" || typeof c == "number") {
    // 形态1  c是字符串或者是数字   h(sel, data, "文字")
    return vnode(sel, data, undefined, c, undefined)
  } else if(Array.isArray(c)) {
    let children = []
    // 形态2     c是数组       h(sel, data, [h(), h(), h()])
    // 遍历数组c  里面的元素必须是对象
    for(let i = 0; i < c.length; i++) {
      if(!(typeof c[i] == "object" && c[i].hasOwnProperty("sel"))) {
        return new Error("传入的数组参数有项不是h函数")
      } else {
        children.push(c[i])
      }
    }
    // 循环结束  children收集完毕 返回虚拟节点
    return vnode(sel, data, children, undefined, undefined)

  } else if(typeof c == "object" && c.hasOwnProperty("sel")) {
    // 形态3   c是对象       h(sel, data, h())
    let children = [c]

    return vnode(sel, data, children, undefined, undefined)
    
  } else {
    return new Error("传入的参数类型不对")
  }
   
}
````



* 虚拟节点的属性

  ````javascript
  {
      children: undefined,  // 节点的子元素
      data: {},        // 节点的属性 样式等
      elm: undefined,     //虚拟节点对应的真正的dom节点 undefined表示还没有上树
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




#### 手写diff算法（patch函数）

* patch函数执行流程

  > patch函数思路：
  >
  > 1.  patch传两个参数 oldVnode 和newVnode
  >
  > 2. 先判断oldVnode是虚拟节点还是真实dom节点
  >
  > 3. 如果是真实dom节点就通过vnode函数包装为虚拟dom节点
  >
  > 4. 在判断oldVnode 和newVnode是不是同一个虚拟节点
  >
  > 5. 如果oldVnode和newVnode不是同一个虚拟节点 如果不是同一个虚拟节点
  >
  >    1. 封装createElement函数 参数为newVnode 
  >    2. createElement内部先将newVnode通过document.createElement()创建真实dom节点
  >    3. 判断newVnode中有没有子节点 如果么没有子节点直接innerText = newVnode.text 返回真实dom
  >    4. 如果有子节点就递归调用createElement() 递归结束 appendChild()最后返回真实dom
  >    5. insertBefore插入这个返回的真实dom
  >    6. removeChild删除旧的dom
  >
  >    ````javascript
  >    // createElement函数
  >    // 真正创建节点  将vnode创建为DOM  不进行插入
  >    export default function createElement(vnode) { 
  >      // 创建dom节点
  >      const domNode = document.createElement(vnode.sel)
  >      // 有子节点还是文本
  >      if(vnode.text !== "" && (vnode.children == undefined || vnode.children.length == 0)) {
  >        // 内部是文本
  >        domNode.innerText = vnode.text;
  >      } else if(Array.isArray(vnode.children) && vnode.children.length > 0) {
  >        // 内部是子节点 要递归创建子节点
  >        vnode.children.forEach(item => {
  >          const reldom = createElement(item)
  >          domNode.appendChild(reldom)
  >        })
  >      }
  >    
  >      // 补充elm属性
  >      vnode.elm = domNode
  >      // 返回elm elm属性是一个纯dom对象
  >      return vnode.elm
  >    }
  >    -----------------------------------------------------------------------------
  >    // patch函数
  >    export default function(oldVnode, newVnode) {
  >      // 判断传的第一个参数时dom节点还是虚拟节点
  >      if(oldVnode.sel == "" || oldVnode.sel == undefined) {
  >        //传入的第一个参数是dom节点  此时需要包装成虚拟节点
  >        oldVnode = vnode(oldVnode.tagName.toLowerCase(), {}, [], undefined, oldVnode)
  >    
  >        console.log("oldVnode", oldVnode);
  >      }
  >    
  >      // 判断oldVnode和newVnode是不是同一个节点
  >      if(oldVnode.key == newVnode.key && oldVnode.sel == newVnode.sel) {
  >        // 是同一个节点
  >      } else {
  >        console.log(123);
  >        // 不是同一个节点 暴力插入新的 删除旧的
  >        var newVnodedom = createElement(newVnode)
  >         //插入新的
  >        oldVnode.elm.parentNode.insertBefore(newVnodedom, oldVnode.elm)
  >         //删除旧的
  >        oldVnode.elm.parentNode.removeChild(oldVnode.elm)
  >      }
  >    }
  >    ````
  >
  >    
  >
  > 6. 如果是同一个虚拟节点 进行精细化比较
  >
  >    

  ![patch函数_20210503110649](.\imgs\patch函数_20210503110649.png)



##### 精细化比较

* 精细化比较执行阶段

  ![精细化比较](.\imgs\精细化比较.png)

  * **精细化比较 最精彩的地方**

    * oldVnode和newVnode都有children

      ![diff优化策略](.\imgs\diff优化策略.png)

      > 四种命中方式（按顺序）：
      >
      > * 命中一种后就不在继续进行命中判断 没有命中就按顺序就进行下一种命中方式判断
      > * 循环移动指针的条件： 新前<=旧后  && 旧前 <= 旧后
      > * 旧节点先循环完毕，说明新节点中有需要插入的节点是（新前与新后之间的节点）
      > * 新节点先循环完毕，说明旧节点中有需要删除的节点是（旧前与旧后之前的节点）
      >
      > 1. 新前和旧前（命中后指针下移）
      >
      > 2. 新后与旧后（命中后指针上移）
      >
      > 3. 新后与旧前（移动新后指向的节点到旧后之后，之前旧前指针的位置标为undefined，旧前指针下移，新后指针上移）
      >
      >    ![新后与旧前](.\imgs\新后与旧前.png)
      >
      > 4. 新前与旧后（移动新前指向的节点到旧前节点之前，之前旧后指针的位置标为undefined，新前指针下移，旧后指针上移）
      >
      >    ![新前与旧后命中](.\imgs\新前与旧后命中.png)
      >
      > 5. 四种都没有命中就循环查找旧节点中有没有新前指向的节点，找到将节点移动到旧前节点之前，之前指针位置标为undefined，没有找到就在旧前节点之前新建该节点（然后新前指针下移）
      >
      >    ![四种都没有命中](.\imgs\四种都没有命中.png)

