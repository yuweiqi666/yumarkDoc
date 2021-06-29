# webpack学习笔记

## js以前的写法

* 纯面向过程的方式

  `在index.html中引入index.js文件 然后将所有的逻辑写在index.js中`

  > 1. 代码冗余 不易维护

  ````javascript
  // 给root根节点添加header siderBar footer模块
  
  const root = document.getElementById('root')
  
  
  const header = document.createElement('div')
  
  header.innerHTML = "header"
  
  root.append(header)
  
  
  const siderBar = document.createElement('div')
  
  siderBar.innerHTML = 'siderBar'
  
  root.append(siderBar)
  
  
  const footer = document.createElement('div')
  
  footer.innerHTML = 'footer'
  
  root.append(footer)
  ````

  

* 面向对象的写法

  `在index.html中分别引入header.js、siderBar.js、footer.js、index.js(将不同的模块当做不同的类分别写在不同的js文件中，在index.js中统一实例化这些模块 )`

  > 1. index.html中引入大量的js文件  影响加载速度
  > 2. 无法快速知道各个模块的代码（需要自己去目录找）
  > 3. js文件的引入顺序可能导致报错

  ````javascript
  // 在header.js文件中
  function Header() {
    const header = document.createElement('div')
  
    header.innerHTML = "header"
  
    root.append(header)
  }
  
  //在siderBar.js文件中
  function SiderBar() {
    const siderBar = document.createElement('div')
  
    siderBar.innerHTML = 'siderBar'
  
    root.append(siderBar)
  }
  
  // 在footer文件中
  function Footer() {
    const footer = document.createElement('div')
  
    footer.innerHTML = 'footer'
  
    root.append(footer)
  }
  
  //将这些js文件和index.js一一引入index.html中
  
  // 在index.js中
  const root = document.getElementById('root')
  
  new Header()
  
  new SiderBar()
  
  new Footer()
  ````

* 改良写法

  `直接将所有的模块通过import引入到index.js中 这样只需要在index.html中引入index.js即可，而且出现错误也可以通过import的引入路径直接找到相应的代码`

  > 注意： **原生的浏览器是不能识别import 这种ES Module这种模块引入写法的 所以在浏览器中仍然会报错**
  >
  > 解决方案： **可以使用webpack将js文件进行一层转化 然后在index.html中将webpack转化后的js文件引入**

  ````javascript
  // 在index.js文件中
  import Header from "./header.js"
  import SiderBar from "./siderBa.js"
  import Footer from "./footer.js"
  
  new Header()
  
  new SiderBar()
  
  new Footer()
  ````



## webpack

### 概念

* `module bundler`   **webpack核心定义是一个模块打包工具**

  > 所以从上面的例子中不能简单的将webpack理解为js翻译器 ，应该理解为header.js 
  >
  > siderBar.js    footer.js都是不同的模块 webapck将这些不同的模块都打包到了一起生成最终的js文件



### 环境搭建

* node环境搭建

  * node安装
  * npm init 初始化node 项目

* webpack环境搭建

  * webpack 和 webpack-cli（**只有安装了webpack-cli才能在命令行中使用webpack命令**）安装

    > 1. **非常不建议全局安装webpack** 原因：当有两个项目依赖的weback需要的版本要求不一致 那么就无法同时运行这两个项目了
    >
    > 2. 建议在项目中局部安装webpack webpack-cli
    >
    >    ````
    >    // 在项目目录中
    >    npm install webpack webpack-cli
    >    ````
    >
    > 3. 在项目目录中执行`npx webpack -v`可以查看当前项目中的webpack版本
    >
    >    > 直接只用`webpack -v`只能在全局找webpack的版本  如果本地安装不能找到



### webpack配置

> 在上面的打包中我们没有特意对webpack进行配置 直接执行npx webpack index.js打包(相当于让webpack以index.js为入口文件 其他（如出口文件main.js和出口文件位置dist）使用默认配置进行打包) 

* 在项目根目录中新建webpack.config.js

  > 配置完成后运行 `npx webpack` 就可以完成打包 
  >
  > 运行`npx webpack`时 会读取webpack.config.js中配置文件 确定入口和出口

  ````javascript
  // 填写配置文件
  const path = require('path')
  
  module.exports = {
    // 指定模式 可以是development或者是production 区别是一个不压缩打包后的js代码 一个压缩打包后的js代码  不设置mode 则默认是production
    mode: 'development'
    // 指定webpack打包的入口文件
    entry: './index.js',
    // 指定webpack打包完成后的文件存放位置
    output: {
      // 打包后的文件名
      filename: 'bundle.js',
      // 打包后的文件路径（必须是绝对路径）
      path: path.resolve(__dirname, 'bundle')
    }
  }
  ````

* 项目结构优化

  * 目录优化

    * 将`header.js content.js footer.js`这些需要浏览器不能直接运行，需要webpack进行打包的js文件统一放到src目录中

    * 将index.html放到打包的出口路径中去

      

    ![目录优化](.\imgs\目录优化.png)

  * 将执行打包的命令 npx webpack 设置到script脚本命令中

    > 为什么添加到脚本的打包命令不需要设置为`npx webpack`？
    >
    > 因为在执行script脚本命令时  命令会优先在项目目录本地寻找并执行对应的打包命令（原理和npx类似）

    ![微信截图_20210628200309](.\imgs\微信截图_20210628200309.png)

  

### webpack打包输出内容

![webpack打包输出内容](.\imgs\webpack打包输出内容.png)

```` 
Hash: 表示每一次打包的唯一哈希值
Version: 使用的webpack的版本
Time： 打包花费时间
Built at: 何时进行的打包
Asset： 打包后的js文件  
Size: 打包后的文件大小  
Chunks：打包后js文件的id值（复杂项目可能会打包后有多个js文件 chuncks还会存放其他和其有关系的js文件的id值）
Chunk Name： js文件的id值的名字
Chunk Name中的main的含义： 我们在配置入口文件时 entry: './src/index.js' 相当于是
						entry: {
							main: './src/index.js' 的简写
						}


````



### loader

> webpack不能识别非js文件  所以如果要将非js文件进行打包 我们需要使用相应的loader

#### file-loader

> 专门用于打包图片文件（字体文件等）

#### url-loader

> 用于打包图片文件 直接将图片打包为base64引入js中 当然也可以配置通过判断图片大小来选择打包成图片文件还是打包成base64

#### css-loader

> 分析几个css文件之间的关系 最后合成到一个css文件

#### style-loader

> 将css挂载到html的head部分（style内联样式） css-loader style-loader需要配合使用

#### sass-loader

> 需要同时安装node-sass

#### postcss-loader

