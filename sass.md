# sass

## 基本概念

### 介绍

* sass是一种css的预处理器，引入了编程思想，增加了代码的整洁度和可读性。
* sass是css的超集，100%兼容css语法



### 文件格式

* sass有两种后缀名的文件： .sass 和 .scss
  * .sass文件：使用的类Ruby语法，空格缩进，没有花括号，要求严格（不推荐）
  * **.scss文件： sass3引入的新语法，写法和原生css基本类似（推荐）**



### 安装

> 1. 先安装Ruby环境
> 2. 在安装sass



* 安装Ruby环境

  * Ruby官网下载

* 安装sass

  ````javascript
  gem install sass  // 下载sass
  
  sass -v  // 查看sass是否安装成功
  ````

  

### 编译

> sass文件必须要进行编译 转化为css文件  才能被浏览器识别
>
> * 编译命令的相关属性：
>
>   1. --style  编译成不同样式的css文件
>
>      1. nested （嵌套式）默认的
>      2. expanded（展开式） 日常写的css的常用格式
>      3. compact （紧凑式） 一个css样式写成一行
>      4. compressed（压缩式）压缩后的css样式
>
>   2. --watch  实时自动编译
>
>      
>
> * 编译后注释相关的
>
>   1. // 当行注释
>
>      > 不会出现在编译后的css文件中
>
>   2. /*多行注释\*/
>
>      > 只会出现在编译后的expended格式的css文件中
>
>   3. /\*!我是强制注释\*/
>
>      > 会出现在任何格式编译后的css文件中
>
> 

* 单文件编译

  > 每次sass文件更新都要手动再次编译

  ````javascript
  sass style.scss style.css
  // 将style.scss文件转化为style.css
  // 每一次style.scss文件更新都要执行这个命令进行编译
  ````

* 单文件监听命令

  ````javascript
  sass --watch style.scss:style.css
  // 每次style.scss文件更新后都会自动编译css文件
  ````

* 多文件监听命令

  ````javascript
  sass --watch sass/main:dist/css
  // 监视sass目录下的main文件夹中的所有sass文件 自动编译为css文件并放入dist下的css文件夹中
  ````





## 基本语法

### 变量 

> 使用$符号来定义一个变量，实现多次重复使用

* 变量定义

  ````scss
  $box-width: 100px;
  .box1 {
    width: $box-width;
    height: $box-width;
    background-color: aqua;
    margin-bottom: 10px;
  }
  ````

* 变量拼接字符串使用#{}

  ````scss
  $left: left
  .box {
      border-#{$left}-radius: 20px        
  }
  
  -----------------------------------------
  // 编译后的css
  .box {
      border-left-radius: 20px
  }
  ````

* 变量作用域

  > scss中的变量具有块级作用域（参照es6的变量作用域  也有作用域链）

  ````scss
  .box3 {
    $red: red;
    .son {
      width: 100px;
      height: 100px;
      background-color: $red;
    }
    @include box3-size($red)
  }
  ````

* 变量计算

  > \+ - * / %
  >
  > **使用/除法时必须加括号**
  >
  > 运算时还需要考虑单位  如乘法 10px * 2  不能写成  10px * 2px

  ````scss
  .box {
      width: 400px;
      height: (100px / 2);
  }
  --------------------------------
  编译为css
  
  .box {
      width: 400px;
      height: 50px;
  }
  ````

  

### 嵌套

* 一般嵌套

  > 父子选择器的嵌套，避免代码的重复书写

  ````scss
  .box1 {
    width: $box-width;
    height: $box-width;
    background-color: aqua;
    margin-bottom: 10px;
    .son {
      width: $son-width;
      height: $son-width;
      background-color: pink;
    }
  }
  ````

* 伪类嵌套

  > 使用&符号表示父类选择器  在这边&:hover 表示 .box1:hover

  ````scss
  .box1 {
    width: $box-width;
    height: $box-width;
    background-color: aqua;
    margin-bottom: 10px;
    &:hover {
      width: $new;
    }
  }
  ````

* 属性嵌套

  > 有些 CSS 属性遵循相同的命名空间 (namespace)，比如 `font-family, font-size, font-weight` 都以 `font` 作为属性的命名空间

  ````scss
  .div {
      font: {
          family: fantasy,
          size: 20px,
          weight: blod
      }	
  }
  
  -------------------------------------------------
  // 编译后的css
  
  .div {
      font-family: fantasy,
      font-size: 20px,
      font-weight: blod
  }
  ````

  

### 继承

> 让一个选择器继承另一个选择器的所有样式
>
> 使用@extend符号

````scss
.box1 {
  width: $box-width;
  height: $box-width;
  background-color: aqua;
  margin-bottom: 10px;
}

.box2 {
  @extend .box1
}
// .box2选择器继承了.box1选择器的所有样式
--------------------------------------------
// 编译后的 css
.box1,
.box2 {
   width: $box-width;
   height: $box-width;
   background-color: aqua;
   margin-bottom: 10px;
}
````



### Mixin混合器

> 1. 相当于可以重用的代码块  将重复使用的样式封装起来，可以多次使用
> 2. 使用@mixin声明混合
> 3. 使用@include + 混合名称来使用

* 无参数的@mixin声明和使用

  ````scss
  // 使用时不需要传参数 box3-size混合相当于吧宽度300px 高度200px这个样式封装起来了 
  @mixin box3-size() {
    width: 300px;
    height: 200px;
  }
  
  .box3 {
    @include box3-size()
  }
  ````

* 有参数的@mixin声明和使用

  ````scss
  // 将背景色作为参数 传入mixin  每次使用都要传一个背景颜色 参数设置pink为默认值 不传参的时候使用默认值  传参的时候覆盖默认值
  @mixin box3-size($bgc:pink) {
    width: 300px;
    height: 200px;
    background-color: $bgc;
  }
  
  .box3 {
    @include box3-size(#333)
  }
  ````

  * 多组数值参数（参数后面加...）的声明和使用

    > **需要传多个参数的时候 多数值参数必须要写最后一个（对照js的剩余参数）**

    ````scss
    // 多数值参数 如border的属性 1px solid pink
    @mixin box4-size($border...) {
      width: 200px;
      height: 100px;
      border: $border;
    }
    
    .box4 {
      @include box4-size(1px solid pink)
    }
    ````

    

### 导入

> 使用@import在sass文件中导入其他sass文件  
>
> 将多个sass文件导入一个sass文件  最终编译就只需要编译这一个sass文件





### 控制指令

* if语句

  * @if 条件成立输出{}内的代码

    ````scss
    p {
        @if 1 + 1 == 2 {border: 1px solid #ccc }
        @if 5 < 3 { borde: 1px solid pink }
    }
    
    -----------------------
    // 编译后的css
    
    p {
        border: 1px solid #ccc
    }
    ````

  * @if 后面跟@else-if 和 @else

    ````scss
    $type: monster;
    p {
      @if $type == ocean {
        color: blue;
      } @else if $type == matador {
        color: red;
      } @else if $type == monster {
        color: green;
      } @else {
        color: black;
      }
    }
    
    --------------------------
    编译后的css
    p {
        color: green
    }
    ````

* @for

  > 限定范围内重复

  ````scss
  for $i from 1 through 3 {
      .item-#{$i} {
          width: $i * 2em
      }
  }
  
  --------------------------
  编译后的css
  .item-1 {
      width: 2em
  }
  
  .item-2 {
      width: 4em
  }
  
  .item-3 {
      width: 6em
  }
  ````

* @each

  > $var  in  <list>    $var 是任何变量      list是一连串值
  >
  > **@each将$var变量作用域于list中每一个项目然后输出结果**

  ````scss
  @each $animal in puma, sea-slug, egret, salamander {
    .#{$animal}-icon {
      background-image: url('/images/#{$animal}.png');
    }
  }
  
  -----------------------------------------------
  // 编译为css
  .puma-icon {
    background-image: url('/images/puma.png'); }
  .sea-slug-icon {
    background-image: url('/images/sea-slug.png'); }
  .egret-icon {
    background-image: url('/images/egret.png'); }
  .salamander-icon {
    background-image: url('/images/salamander.png'); }
  ````

  * 多个参数的情况下

    ````scss
    @each $animal, $color, $cursor in (puma, black, default),
                                      (sea-slug, blue, pointer),
                                      (egret, white, move) {
      .#{$animal}-icon {
        background-image: url('/images/#{$animal}.png');
        border: 2px solid $color;
        cursor: $cursor;
      }
    }
    
    --------------------------------------
    编译成css
    .puma-icon {
      background-image: url('/images/puma.png');
      border: 2px solid black;
      cursor: default; }
    .sea-slug-icon {
      background-image: url('/images/sea-slug.png');
      border: 2px solid blue;
      cursor: pointer; }
    .egret-icon {
      background-image: url('/images/egret.png');
      border: 2px solid white;
      cursor: move; }
    ````

    

### 函数

* 自定义函数

  > @function   @return

  ````scss
  $grid-width: 40px;
  $gutter-width: 10px;
  
  @function grid-width($n) {
     @return $n * $grid-width + ($n - 1) * $gutter-width;    
  }
  
  #sidebar { 
      width: grid-width(5) 
  }
  
  ---------------------------------
  编译为css
  #sidebar {
    width: 240px; 
  }
  ````

  

  