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





## 数据响应式原理

* 流程

  ![数据响应式](.\imgs\数据响应式.png)

### object.defineProperty()

> vue2.x 实现数据响应式的核心 详解见笔记

````javascript
var obj = {}
var val = 123 // 设置变量
Object.defineProperty(obj, "a", {
    // 可以被枚举
    enumerable: true,
    // 可以被配置  比如delete
    configurable: true,
    get() {
      console.log("属性被读取了");
      return val
    },
    set(newVal) {
      console.log("属性被修改了");
      if(newVal === val) {
        return 
      } 
      val = newVal
    } 
  })
````



### defineReactive()

> 1. defineProperty设置get和set拦截时 必须要另外定义一个变量 用于周转，所以 我们可以封装defineReactive提供闭包环境
> 2. 递归调用observe函数  因为对象中的属性可能是多层嵌套结构，每一层属性都需要是响应式的
> 3. 属性设置新值newValue时也需要调用observe函数  设置新值可能出现嵌套的属性

````javascript
import observe from "./observe"
export default function defineReactive(obj, key, val) {
  if(arguments.length == 2) {
    val = obj[key]
  } 


  // 递归调用observe obj属性有多层嵌套时需要每一层都变成响应式
  let childOb = observe(val)


  Object.defineProperty(obj, key, {
    // 可以被枚举
    enumerable: true,
    // 可以被配置  比如delete
    configurable: true,
    get() {
      console.log(key + "属性被读取了");
      return val
    },
    set(newVal) { 
      console.log(key + "属性被修改了");
      if(newVal === val) {
        return 
      } 
      val = newVal
      // 设置了新值  新值也要被observe   因为新值也可能是引用数据类型 有嵌套
      childOb = observe(newVal)
    } 
  })
}
````



### Observer类

> 用于将一个正常的obj对象转化为每一层级属性都可以被侦测到的obj

````javascript
class Observer {
  constructor(value) {
    // 给value对象自定义__ob__属性  属性值为实例对象本省
    def(value, "__ob__", this, false)
    // 如果是数组  就蛮干 将数组的原型指向引入的arrayMethods
    if(Array.isArray(value)) {
      Object.setPrototypeOf(value, arrayMethods)
      // 让这个数组变得observe
      this.observeArray(value)
    } else {
      this.walk(value)
    }
  }
  // 遍历 将value对象zhong每一个属性都变为响应式的
  walk(value) {
    for(let i in value) {
      // defineReactive用于将对象的某个属性变为响应式
      defineReactive(value, i)
    }
  }
  // 数组的特殊遍历
  observeArray(value) {
    for(let i = 0; i < value.length; i++) {
      // 调用observe 因为数组中也有可能是对象元素  需要被监测  如果是一般数据类型  不会被监测
      observe(value[i])
    }
  }
}
````



#### walk函数

> 遍历obj对象的每个属性  调用defineReactive函数将其变为响应式

````javascript
// 遍历 将value对象zhong每一个属性都变为响应式的
  walk(value) {
    for(let i in value) {
      // defineReactive用于将对象的某个属性变为响应式
      defineReactive(value, i)
    }
  }
````

#### observeArray函数

> 遍历array数组的每个元素  调用observe 变为响应式  因为数组的元素可能是对象

````javascript
// 数组的特殊遍历
  observeArray(value) {
    for(let i = 0; i < value.length; i++) {
      // 调用observe 因为数组中也有可能是对象元素  需要被监测  如果是一般数据类型  不会被监测
      observe(value[i])
    }
  }
````



### observe函数

> 判断参数是够为一个对象  是对象就要实例化Observer实例  不是一个对象就直接返回

````javascript
 function observe(value) {
  // 如果不是value不是对象 就什么都不做
  if(typeof value !== "object") {
    return
  }

  // 定义ob
  var ob
  if(typeof value.__ob__ !== "undefined") {
    ob = value.__ob__
  } else {
    ob = new Observer(value)
  }
  return ob;
}

````



### 数组的响应式

> 改写了数组的七个方法
>
> 1. push
> 2. pop
> 3. shift
> 4. unshift
> 5. splice
> 6. sort
> 7. reserve

> 改写步骤
>
> 1. 先以Array.prototype为原型创建对象arrayPrototype
> 2. 遍历七个方法   给arrayPrototype设置这个7个方法属性，同时备份原来的方法
> 3. 在arrayPrototype对象的这7个方法中分别要调用原来的数组方法（注意this的指向）
> 4. **unshift splice push**为数组新增元素  新增的数组元素也要是响应式的

````javascript
import { def } from "./utils"
// 得到Array.prototype
const arrayPrototype = Array.prototype

// 以Array.prototype为原型创建对象
export const arrayMethods = Object.create(arrayPrototype)

const methodsNeedChange = [
  "push",
  "pop",
  "shift",
  "unshift",
  "splice",
  "sort",
  "reserve"
]

methodsNeedChange.forEach(methodName => {
  // 备份原来的方法
  const original = arrayPrototype[methodName]
  // 定义新的方法
  def(arrayMethods, methodName, function() {
    // 把数组身上的__ob__取出来   __ob__ 就是Observer实例对
    const ob = this.__ob__
    // push unshift splice三种方法能够插入新项 插入的新项也要变成响应式
    let inserted = []

    let args = arguments

    switch (methodName) {
      case "push":
      case "unshift":
        inserted = args
        break;
      case "splice":
        inserted = args.slice[2]
        break;
    }

    // 判断有没有插入的新项
    if(inserted) {
      ob.observeArray(inserted)
    }

    console.log("数组被改变了");
    // 恢复数组原来的方法  注意this的指向
    const result = original.apply(this, arguments)


    return result

  }, false)
  
})
 
````



### 依赖收集

> **在getter中收集依赖  在setter中触发依赖**

#### Dep类

> 响应式对象的每一个对象（数组）属性的\_\_ob\_\_属性内都存在一个Dep的实例

#### Watcher类



 



## AST抽象语法树

> 1. 服务于模板编译 一种语法翻译成另一种语法
> 2. vue模板语法  --> html语法  

* **抽象语法树本质是一个对象**

  

#### 抽象语法树和虚拟节点

````html
// html
<div>
    <h3>你好</h3>
    <ul>
        <li>A</li>
        <li>B</li>
        <li>C</li>
    </ul>	
<div>
````

````javascript
// 对应的AST抽象语法树
{
    tag: "div",
    children: [
        {	
            tag: "h3",
            children: [
                {text: "你好", type: 3}
            ]
        },
        {
            tag: "ul",
            children: [
                {	
                    tag: "li",
                    children: [
                        {text: "A", type: 3}
                    ]
                },
                {	
                    tag: "li",
                    children: [
                        {text: "B", type: 3}
                    ]
                },
                {	
                    tag: "li",
                    children: [
                        {text: "C", type: 3}
                    ]
                }
            ]
        }
    ]
}
````



> 1. 抽象语法树的最终产物是h函数
> 2. h函数创建虚拟节点  从而进行diff算法 展示成真实的节点

![AST抽象语法树](.\imgs\AST抽象语法树.png)

#### 算法储备

##### 指针思想

> 指针就是一个下标位置

* 寻找字符串aaabbbbcccccccdd中连续重复次数最多的字符

  > 1. 设置两个指针 startPos为0    endPos为1
  > 2. while循环判断两个指针指向值是否相同
  > 3. 相同就将endPos++
  > 4. 不同就记录startPos和endPos之间重复字符的长度 同时将startPos指向endPos的位置
  > 5. 循环结束的条件是startPos指向的是undefined

  ````javascript
  var str = 'aaabbbbcccccccdd'
  
      var startPos = 0
      
      var endPos = 1
  
      var result = []
  
      while (str[startPos]) {
        if(str[endPos] === str[startPos]) {
          console.log(123);
        } else {
          let len = str.substring(startPos, endPos)
  
          console.log("len", len);
  
          result.push(len.length)
  
          startPos = endPos
        }
  
        endPos++
      }
  
  
      console.log("result", Math.max(...result));
  ````



##### 递归深入

* 斐波那契数列 （当前项等于前两项之和）输出前十项

  > 1，1，3，5，8，13，21，34

  ````javascript
  function fib(n) {
    // 第一项和第二项固定为1
     return n === 0 || n === 1 ? 1: fib(n-1) + fib(n-2)
   }
  // 存在大量的计算
  
  for(let i = 0; i < 9; i++) {
      console.log(fib(i))
  }
  ````
  
  ````javascript
  // 优化   加入缓存
  // 定义缓存对象 
var cache = {}
  
  function fib(n) {
  // 判断缓存中有没有这个属性 有的话就不用递归了 直接用
  if(cache.hasOwnProperty(n)) {
     return cache[n] 
  }
  
  let v = n == 0 || n == 1 ? 1 : fib(n - 1) + fib(n - 2);
     // 将结果放进缓存
     cache[n] = v;
     return v 
  }
  
  console.log(fib(4));
  ````
  
  

* 试将高维数组[1, 2, 3, [4, 5]] 形式转换

  ````javascript
  // [1, 2, 3, [4, 5]]
  var arr = [1, 2, 3, [4, 5]]
      
  // 转换函数
  function convert(arr) {
    // 准备一个结果数组
    var result = []
    for(var i = 0; i < arr.length; i++) {
      if(typeof arr[i] === "number") {
        result.push({
          value: arr[i]
        })
      } else if(Array.isArray(arr[i])) {
        result.push({
          children: convert(arr[i])
        })
      }
    }
  
  
    return result
  }
  
  console.log(convert(arr));
  ````

  

##### 栈

> 1. 栈（stack） 先进后出  
> 2. 栈中插入元素叫**进栈/入栈/压栈**  栈中删除元素叫**出栈/退栈**
> 3. 栈底封死 栈顶进行插入和删除操作
> 4. **在js中栈可以用数组模拟，限制只能使用pop()和push()  **



* 将3[abc]变为abcabcabc
  将3[2[a]2[b]]变为aabbaabbaabb
  将2[1[a]3[b]2[3[c]4[d]]]变为abbbcccddddcccddddabbbcccddddcccdddd

  > 1. 遍历字符串
  > 2. 遇到数字压入numStack栈
  > 3. 遇到'['将空字符串压入strStack栈
  > 4. 遇到字母拼接到strStack栈的最后一项
  > 5. 遇到']'将strStack的最后一项重复numStrack的最后一项值，拼接到strStack最后第二项 ，numStrack和strStrack都弹栈

  ````javascript
  function smartRepeat(str) {
    let numStack = []
    let strStack = []
    let result = ""
    for(let i of str) {
      console.log("i", typeof i);
      if(!Number.isNaN(+i)) {
        numStack.push(i)
      }else if(i === "[") {
        strStack.push("")
      }else if(i === "]") {
        if(strStack.length === 1) {
          strStack[strStack.length - 1] += strStack[strStack.length - 1].repeat(numStack[numStack.length - 1])
            } else {
              strStack[strStack.length - 2] += strStack[strStack.length - 1].repeat(numStack[numStack.length - 1])
              strStack.pop()
            }
            numStack.pop()
          }else {
            strStack[strStack.length - 1] += i
          }
        }
  
  
        console.log("numStack", numStack);
        console.log("strStack", strStack);
  
  
        result = strStack.pop()
        return result
      }
  
      console.log(smartRepeat('2[1[a]3[b]2[3[c]4[d]]]'));
  ````

  



#### 手写AST抽象语法树

* 思路

  > 1. 指针思想 index = 0   while循环模板字符串 index指向模板字符串的最后一个字符时停止循环
  > 2. 每次循环都要重新赋值rest剩余部分（通过substring）
  > 3. 然后正则匹配rest头部各种可能情况
  >    1. 头部为开始标签如<div> 就将标签名字压入stack1栈中，同时将对象{tag: 标签名字, children: []}压入stack2栈中
  >    2. 头部为标签中的文本时将文本push到stack2的最后一个对象元素的children数组属性中
  >    3. 头部为结束标签如</div> stack1弹栈  stack2也弹栈 同时stack2弹栈的那一项push进入它前一项的children数组属性中去（要考虑stack2中是不是只有一项）