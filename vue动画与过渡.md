# vue动画与过渡

## css动画过渡

### 使用场景

#### 条件渲染（v-if）

#### 条件展示（v-show）

#### 动态组件

#### \<router-view /\>页面组件

### 原理

![原理](.\imgs\原理.png)

### 自定义类名

* 默认情况下，我们要根据`transition`组件上绑定的name属性来设置过渡/动画的类名

  > 如<transition name='fade’>\</transition\>
  >
  > 那么出现的过渡/动画类名为**fade-enter-active**
  >
  > 消失的过渡/动画类名为 **fade-leave-active**

* 对过渡/动画类名的自定义的方法

  > 1. 使用场景：
  >    1. 同时使用到动画和过渡的时候
  >    2. 需要使用到类似`animate.css`这种第三方动画库的时候
  >
  > 2. **使用方法：**在`transition`上添加**enter-active-calss**和**leave-active-class**属性
  
  ![自定义类名](.\imgs\自定义类名.png)
  
  ![自定义类名2](.\imgs\自定义类名2.png)

### vue中css过渡

#### 使用方法

* 给需要进行过渡的组件/元素包裹`<transition></transition>`

  ![vue组件过渡](.\imgs\vue组件过渡.png)

* 写css过渡样式

  ![vue过渡css](.\imgs\vue过渡css.png)

### vue中css动画

#### 使用方法

* 给需要进行过渡的组件/元素包裹`<transition></transition>`

  ![vue组件过渡](.\imgs\vue组件过渡.png)

* 写css过渡样式

  ![vue动画css](.\imgs\vue动画css.png)


#### 使用`animate.css`动画库

### 页面初始化就使用动画/过渡

* 添加`appear`和`appear-active-class`两个属性

  ![页面初始化就是用过渡动画](.\imgs\页面初始化就是用过渡动画.png)

  

### 同时使用过渡和动画

> `animate__animated animate__bounce` 是使用的第三方动画
>
> `fade-enter-active`是自己定义的过渡效果

![同时使用动画和过渡](.\imgs\同时使用动画和过渡.png)

![同时使用过渡和动画2](.\imgs\同时使用过渡和动画2.png)

### 执行时间的问题

* 手动设置`type`属性来决定以谁的执行执行时间为准

  ![执行时间以谁为准](.\imgs\执行时间以谁为准.png)

* 自定义一个执行时长

  ![自定义执行时间](.\imgs\自定义执行时间.png)

* 自定义入场执行时间和出场执行时间

  ![自定义入场和出场执行时间](.\imgs\自定义入场和出场执行时间.png)





## js动画过渡

### js动画钩子

#### before-enter

> 元素动画执行之间触发

> 元素进场前设置颜色为`pink`

![jsbefore-enter](.\imgs\jsbefore-enter.png)

![jsbefore-enter2](.\imgs\jsbefore-enter2.png)

#### enter

> 元素动画执行时触发

> 元素动画执行时2s后设置颜色为`orange`

> **注意：**执行完动画效果必须手动调用done函数 表示动画执行完毕

![enter1](.\imgs\enter1.png)

![enter2](.\imgs\enter2.png)

#### after-enter

> 元素动画执行完毕时触发

![after-enter](.\imgs\after-enter.png)

![after-enter2](.\imgs\after-enter2.png)



#### before-leave

> 离开时的js钩子和进入时的js钩子用法一样

#### leave

#### after-leave

### 使用Velocity.js动画库





## 多个元素/组件的过渡动画

### 过渡模式

> 1. 通过设置`mode`过渡模式来控制显示与隐藏多个元素时先执行显示动画还是先执行隐藏动画
> 2. **默认情况下是显示隐藏的动画时同时执行** 

* out-in
  * 隐藏的动画先执行，显示的动画再执行
* in-out
  * 显示的动画先执行  隐藏的动画再执行

### 多个元素

#### 使用方法

> 两个div盒子 点击按钮时分别切换显示（通过v-if条件渲染）

> 1. 和单个元素/组件的显示隐藏动画/过渡用法一样
> 2. 但是需要设置mode （先执行显示的动画/过渡还是先执行隐藏的动画/过渡）
> 3. 如果显示隐藏的元素的标签名相同，那么需要通过设置key值来保证唯一性

![多个元素过渡](.\imgs\多个元素过渡.png)

![多个元素过渡2](.\imgs\多个元素过渡2.png)



### 多个组件

#### 使用方法

> 多个组件切换时的动画效果需要使用动态组件`<component></component>`

![过个组件动画](.\imgs\过个组件动画.png)







## vue中的列表过渡/动画

> 需要使用`<transition-group></transition-group>`包裹
>
> **原理：**相当于给列表渲染出来的每个item项都包裹了`<transition></transition>`

![列表过渡](.\imgs\列表过渡.png)



## vue中动画的封装

![动画效果的封装](.\imgs\动画效果的封装.png)

![动画封装2](.\imgs\动画封装2.png)

