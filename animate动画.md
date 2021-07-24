# animation动画

## animation和transition对比

* transition
  * 只有开始和结束两个状态
  * 必须要一定的触发条件（如伪类 js 媒体查询）
* animation
  * 可以定义具体每一帧的状态
  * 自动触发

## 基础

### 使用方法

1. 通过`@keyframes`定义一个动画（关键帧）

   ````scss
   // 定义div盒子平移 (div为绝对定位) div盒子从0px水平移动到100px的位置
   @keyframes move {
       0% {
           left: 0;
       }
       100% {
           left: 100px;
       }
   }
   ````

2. 通过`animation`属性使用动画

   ````scss
   animation: move 2s ease;
   ````



### animation属性

#### animation-name

> 使用的动画（关键帧）名称

#### animation-duration

> 一个动画周期耗时（毫秒）

#### animation-delay

> 动画的延迟执行时间（毫秒）常用于元素需要绑定多个动画有先后执行顺序的

#### animation-timing-function

> 动画速率

* ease

  > 快加速 慢减速

* ease-in

  > 加速

* ease-out

  > 减速

* ease-in-out

  > 先加速 在加速

* linear

  > 匀速

* cubic-bezier(n,n,n,n) 贝塞尔曲线 自定义速率

  > https://cubic-bezier.com/#.39,.16,1,.97
  
* step(n, start/end) 设置步长  逐帧播放 

  * n 表示将动画分成几个阶段执行
  * start  表示第一帧是第一步动画结束的状态
  * end(默认值)  表示第一帧是第一步动画开始的状态

#### animation-iteration-count

> 动画的循环播放次数

* n  定义具体的次数
* infinite 定义循环无限次



#### animation-direction

> 动画是否循环交替反向播放

* normal  动画正常播放
* reverse  动画反向播放
* alternate 动画奇数次正向播放  偶数次反向播放
* alternate-reverse 动画奇数次反向播放  偶数次正向播放



#### animation-fill-mode

> 动画结束后的状态

* none（默认值） 动画执行之前和执行之后不会应用任何动画内样式到元素上
* forwards 动画结束后将动画最后一帧的样式引用到元素上
* backwards 动画结束后将动画第一帧的样式引用到元素上

#### animation-play-state

> 指定动画是否正在运行或已暂停

* pushed 指定暂停动画
* running 指定运行动画(默认)

> 案例：鼠标移动到元素上 动画才开始运行 鼠标离开元素，动画就停止运行  
>
> 1. 相当于给元素设置动画时指定暂停动画  :hover指定运行动画

#### 属性的缩写

````scss
animation: name duration timing-function delay iteration-count direction fill-mode play-state;
````

````scss
// 具体
animation:myAnim 1s linear 1s infinite alternate both running;
````







## 实践

### 曲线运动

> 原理：在元素上分别定义多个动画

#### 平抛运动

> 水平上定义匀速移动动画  垂直上定义加速移动动画

```scss
// moveX 定义的是在水平上的匀速运动动画
@keyframes moveX {
  0% {
    left: 0;
  }
  100% {
    left: 500px;
  }
}

//moveY 定义的是在垂直上的加速运动动画 （同时元素缩小）

@keyframes moveY {
  0% {
    top: 0;
  }
  100% {
    top: 500px;
    transform: scale(0);
  }
}



// 然后在运动的元素上同时运用着两个动画
.box {
  position: absolute;
  width: 20px;
  height: 20px;
  border-radius: 20px;
  background-color: pink;
  animation: moveX 2s linear infinite, moveY 2s ease-in infinite;
}
```

#### 抛物线

````scss
// 第一阶段 向上抛Y轴匀速运动 
@keyframes moveY {
  0% {
    top: 200px;
  }
  100%{
    top: 50px;
  }
}

// 第一阶段 向上抛 x轴加速运动
@keyframes moveX {
   0% {
     left: 0;
   }
   100% {
     left: 150px;
   }
}

// 第二阶段向下落 Y轴加速运动
@keyframes moveY2 {
  0% {
    top: 50px;
  }
  100% {
    top: 500px;
  }
}

// 第二阶段向下落x轴匀速运动
@keyframes moveX2 {
  0% {
    left: 150PX;
  }
  100% {
    left: 500px;
  }
}
````



