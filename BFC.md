# BFC

## 概念

> 1. Block formatting context 块级格式上下文
> 2. 形成独立的渲染区域 
> 3. 内部元素的渲染不会影响到外界
> 4. 可以看成是元素的一种属性

## 如何触发BFC

![触发BFC](.\imgs\触发BFC.png)

## BFC特性与作用

### 避免外边距重叠

* 相邻两个盒子的垂直外边距合并

  > 1. 上面一个margin为100px   下面一个margin为100px  最重两个盒子只相距100px
  > 2. 解决方案 分别给两个box设置父盒子  父盒子设置overflow hidden

  ![相邻外边距重叠](.\imgs\相邻外边距重叠.png)

  ```html
  <div class="container">
    <div class="box"></div>
  </div>
  <div class="container">
    <div class="box"></div>
  </div>
  ```

  ```css
  .box {
    width: 100px;
    height: 100px;
    background-color: pink;
    margin: 100px;
  }
  
  .container {
    overflow: hidden;
  }
  ```

  

* 嵌套两个盒子的垂直外边距合并

  > 1. 子盒子设置margin-top 100px  最终父盒子会整体向下移动100px
  > 2. 解决方案： 给父盒子设置overflow hidden

  ![嵌套外边距塌陷](.\imgs\嵌套外边距塌陷.png)

  ```html
  <div class="father">
    <div class="son"></div>
  </div>
  ```

  ````css
  .father {
    width: 300px;
    height: 300px;
    background-color: blueviolet;
    overflow: hidden;
  }
  .son{
    width: 100px;
    height: 100px;
    background-color: pink;
    margin-top: 100px;
  }
  ````

  

### 清除浮动

> 为包裹浮动元素的父盒子设置overflow hidden

```html
<div class="father">
  <div class="son">  
  </div>
</div>
```

````css
.son {
  width: 100px;
  height: 100px;
  background-color: pink;
  float: left;
}

.father {
  background-color: blueviolet;
  overflow: hidden;
}
````



## 阻止元素被浮动元素覆盖

![元素被浮动覆盖](.\imgs\元素被浮动覆盖.png)

````html
<div class="father">
  <div class="son">

  </div>
  <div class="box"></div>
</div>
````

```css
.son {
  width: 100px;
  height: 100px;
  background-color: pink;
  float: left;
}

.box {
  height: 50px;
  background-color: aqua;
  overflow: hidden;
}

.father {
  background-color: blueviolet;
  overflow: hidden;
}
```

