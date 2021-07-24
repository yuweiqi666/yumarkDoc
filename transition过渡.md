

# transition过渡

## 概念

* transition是css3新增的属性，用于设置元素属性变化时的过渡效果

## 属性说明

### transition-property

> 1. 用于设置使用过渡效果的属性
> 2. 如果是所有的属性，使用 **all**

### transition-duration

> 用于设置过渡效果需要的时间（毫秒）

### transition-timing-function

> 用于设置过渡的速率（过渡动画）

#### ease(默认值)

> 启动阶段加速，然后就减速 最终停止

![ease](.\imgs\ease.png)

#### linear

> 匀速

![linear](.\imgs\linear.png)

#### ease-in

> 加速

![ease-in](.\imgs\ease-in.png)

#### ease-out

> 减速

![ease-out](.\imgs\ease-out.png)

#### ease-in-out	

> 先加速， 在减速

![微信截图_20210722151806](.\imgs\微信截图_20210722151806.png)

### transition-delay

> 设置过渡效果的延迟时间（默认为0）

### 组合写法



> transition: 属性名称    耗时    运动速率   延迟时间

````scss
// 效果： 鼠标移动到div上面  div宽度由100px 变化为200px 同时给这个变化设置过渡效果
div {
    width: 100px;
    height: 100px;
    // 表示给width属性设置过渡效果 过渡效果为2s  过渡速率为linear匀速  延时过渡时间为1s
    transition: width 2s linear 1s
    &:hover {
        width： 200px
    }
}

````





## 触发方式

### 伪类触发

> 通过
>
> 1. :hover  (鼠标移动到元素上面触发)  
>
>    ![伪类触发transition](.\imgs\伪类触发transition.png)
>
> 2. :focus (input表单获得焦点触发)
>
> 3. :checked (checkbox等选中触发)
>
> 4. 等等



### 媒体查询触发

```scss
.box {
    width: 100px;
    height: 100px;
    background-color: pink;
    transition: width 5s;
  }

  @media screen and (min-width: 800px) {
    .box {
      width: 200px;
    }
  }
```







### js触发

> 需求： 点击div元素  盒子宽度由100px 变为 200px

![transition过渡动画3](.\imgs\transition过渡动画3.png)

![js触发transition1](.\imgs\js触发transition1.png)

![js触发transition2](.\imgs\js触发transition2.png)





