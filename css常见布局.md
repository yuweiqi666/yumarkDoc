# css常见布局

## 单列布局

````html
<div class="container">
   <div class="header"></div>
   <div class="content"></div>
   <div class="footer"></div>
</div>
````



* 上下等宽

  ![上下等宽](.\imgs\上下等宽.png)

  ````scss
  @mixin w {
    max-width: 1000px;
    margin: 0 auto;
  } 
  
  .container {
    $classes: header content footer; 
    @each $i in $classes {
      .#{$i} {
        @include w
      }
    }
    .footer {
      height: 100px;
      background-color: pink;
    }
    .header {
      @extend .footer;
    }
  
    .content {
      height: 1000px;
      background-color: aqua;
    }
  }
  ````

* 上下通栏

  ![通栏](.\imgs\通栏.png)

  ````scss
  // @mixin 传一个宽度变量
  @mixin w($width) {
    max-width: $width;
    margin: 0 auto;
  } 
  .container {
    $classes: header content footer; 
    @each $i in $classes {
      @if $i == content {
        .#{$i} {
          @include w(1000px)
        }
      } @else {
        .#{$i} {
          @include w(100%)
        }
      }
      
    }
    .footer {
      height: 100px;
      background-color: pink;
    }
    .header {
      @extend .footer;
    }
    .content {
      height: 1000px;
      background-color: aqua;
    }
  }
  ````



## 两列自适应布局（饿了吗移动端）

> 左侧内容宽度固定，右侧内容宽度根据屏幕自适应且左侧和右侧内容可以滚动

````html
<div class="app">
    <div class="top">
      头部区域
    </div>
    <div class="container">
      <div class="left">
        <p>左侧内容</p>
      </div>
      <div class="right">
        <p>右侧内容</p>
      </div>
    </div>
    <div class="bottom">底部区域</div>
  </div>
````



![两栏自适应](.\imgs\两栏自适应.png)

* 使用float + margin 实现

  > 1. 左侧内容和右侧内容外层包一个container盒子
  > 2. container盒子通过position+top+bottom固定在屏幕的固定位置
  > 3. 左侧内容浮动 宽度设置为固定值
  > 4. 右侧内容设置margin-left为左侧内容的固定宽度值
  > 5. **左侧内容和右侧内容要设置为overflow auto**（（左侧/右侧）内容长度超出盒子可以实现滚动）

  ````javascript
  .top {
    height: 150px;
    background-color: pink;
  }
  .container {
    position: absolute;
    width: 100%;
    top: 150px;
    bottom: 50px;
    background-color: pink;
    .left {
      float: left;
      height: 100%;
      width: 100px;
      background-color: orangered;
      overflow: auto;
    }
    .right {
      background-color: chartreuse;
      margin-left: 100px;
      height: 100%;
      overflow: auto;
    }
  }
  .bottom {
    position: absolute;
    width: 100%;
    height: 50px;
    bottom: 0;
    background-color: coral;
  }
  ````

* 使用flex实现

  ````scss
  .top {
    height: 150px;
    background-color: pink;
  }
  .container {
    position: absolute;
    width: 100%;
    top: 150px;
    bottom: 50px;
    background-color: pink;
    display: flex;
    .left {
      width: 100px;
      background-color: orangered;
      overflow: auto;
    }
    .right {
      flex: 1;
      background-color: chartreuse;
      overflow: auto;
    }
  }
  .bottom {
    position: absolute;
    width: 100%;
    height: 50px;
    bottom: 0;
    background-color: coral;
  }
  ````

## 三列布局

* 实现和两列布局类似 

## 圣杯布局

> 1. 一种特殊的三栏布局
> 2. 左边栏和右边栏宽度固定 中间内容区域自适应
> 3. 中间的内容区域最先渲染
> 4. **为了让内容区域最先渲染 所以先书写内容区域的html 再书写左右栏的html**（普通的三栏布局是常规的先书写左边栏 再写内容区域 最后写右边栏）
> 5. **核心： float + margin负值 + 相对定位** 

![微信截图_20210525191844](.\imgs\圣杯布局.png)

````html
<div class="content">
    // 先写内容区域  因为要优先渲染
  <div class="main column">内容区域</div>
  <div class="left column">左侧区域</div>
  <div class="right column">右侧区域</div>
</div>
````

````css
* {
  padding: 0;
  margin: 0;
}

.content {
  padding: 0 100px 0 200px;
}

.content .main {
  width: 100%;
  background-color: black;
}
.content .left {
  position: relative;
  left: -200px;
  width: 200px;
  background-color: brown;
  margin-left: -100%;
}
.content .right {
  width: 100px;
  background-color: pink;
    // 本身宽度100px  在margin-right -100px 相当于自己不占空间 设置浮动直接排列在main盒子的后面
  margin-right: -100px;
}

.column {
  float: left;
  color: #fff;
  height: 500px;
}
````



## 双飞翼布局

> 和圣杯布局原理大致相同 不同点为双飞翼布局在main盒子中又加了一个盒子，给这个盒子设置左右margin来给左右栏预留空间，圣杯布局是main盒子设置padding值结合了左右栏的相对定位来移动

![双飞翼布局](.\imgs\双飞翼布局.png)

````html
<div class="content">
  <div class="main column">
    <div class="main-wraper">双飞翼布局</div>
  </div>
  <div class="left column">左侧区域</div>
  <div class="right column">右侧区域</div>
</div>
````

````css
* {
  padding: 0;
  margin: 0;
}

.column {
  float: left;
  color: #fff;
  height: 500px;
}

.content .main {
  background-color: black;
  width: 100%;
}

.content .main .main-wraper {
  margin: 0 80px 0 100px;
}

.content .left {
  background-color: brown;
  width: 100px;
  margin-left: -100%;
}

.content .right {
  background-color: pink;
  width: 80px;
  margin-left: -80px;
}

````



## 等高布局

![初始状态](.\imgs\初始状态.png)

> 两个不设置高度的盒子在里面内容不相同的情况下实现高度相同

````html
<div class="container">
  <div class="left">
    <p>123</p>
    <p>123</p>  
  </div>
  <div class="right">
    <p>123</p>
    <p>123</p>
    <p>123</p>
    <p>123</p>
    <p>123</p>
    <p>123</p>
    <p>123</p>
    <p>123</p>
  </div>
</div>
````



* 第一种方案 使用flex布局

  > 父盒子设置为flex布局   主轴为row

  ````css
  * {
    padding: 0;
    margin: 0;
  }
  .left,
  .right {
    width: 100px;
  }
  
  .left {
    background-color: pink;
  }
  .right {
    background-color: aqua;
  }
  
  .container {
    display: flex;
    flex-direction: row;
  }
  ````

* 第二种方案

  > 1. 给高度较小的盒子设置padding-bottom 99999px 和margin-bottom -99999px相互抵消
  > 2. 父盒子设置overflow hidden  

  ````css
  * {
    padding: 0;
    margin: 0;
  }
  .left,
  .right {
    width: 100px;
    float: left;
  }
  
  .left {
    background-color: pink;
    padding-bottom: 99999px;
    margin-bottom: -99999px;
  }
  .right {
    background-color: aqua;
  }
  
  .container {
    overflow: hidden;
  }
  ````

  

## 粘连布局(Sticky Footer)

> 1. 常用于弹框提示
> 2. 当主体内容高度小于屏幕高度时 footer固定在屏幕底部
> 3. 当主体内容高度大于屏幕高度时 footer始终处于主体内容的下面



![粘性布局](.\imgs\粘性布局.png)

````html
<div class="container">
    <div class="content">
      <div class="content-wrapper">
        <p>123</p>
        <p>123</p>
      </div>
    </div>
    <div class="footer">x</div>
  </div>
````

````css
* {
  padding: 0;
  margin: 0;
}
.container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
}
.content {
  width: 100%;
  min-height: 100%;
}
.content-wrapper {
  padding-bottom: 50px;
}
.footer {
  width: 50px;
  height: 50px;
  text-align: center;
  line-height: 50px;
  margin: -50px auto 0;
}
````

