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



## webpack基础

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

![webpack配置](.\imgs\webpack配置.png)

* 在项目根目录中新建webpack.config.js

  > 配置完成后运行 `npx webpack` 就可以完成打包 
  >
  > 运行`npx webpack`时 会读取webpack.config.js中配置文件 确定入口和出口

  ````javascript
  // 填写配置文件
  const path = require('path')
  
  module.exports = { 
    mode: 'development'
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

  * **mode**

    > 指定模式 可以是development或者是production 区别是一个不压缩打包后的js代码 一个压缩打包后的js代码  不设置mode 则默认是production

  * **entry**

    > 指定webpack打包的入口文件

    ````
    entry: './index.js' 是  entry: { main: './index.js' } 的简写 所以main.js是打包完成的默认出口文件（看打包的输出内容）
    ````

    * 当我们需要打包多个文件时

      ````javascript
      entry: {
      	main: './index.js',
      	sub: './index.js'
      }
      // 第一次打包入口文件为index.js  打包的id值（键值）是main
      // 第二次打包入口文件为index.js  打包出口文件的id值（键值）是sub（自定义的）
      ````

      

  * **output**

    > 指定webpack打包的出口文件和路径

    * 打包多个文件时出口文件的配置

      > **注意**：打包多个文件时出口文件名称不能写死，写死名称相当于打包多份js文件出口到一个文件中  这样写显然不合理

      ````javascript
      output: {
      	filename: '[name].js',   // [name]相当于是配置entry的main和sub
      	path: .....
      }
      ````
      
    * chunkFilename配置

      > 非入口chunk文件的名称， 如在入口文件中引入的第三方模块单独打包的文件的名称

  * **devtool**

    > 专门用于配置SourceMap

    ![source-map](.\imgs\source-map.png)

    * eval

      ````javascript
      eval('console.log(123)') ==》 输出123  // 默认显示的执行位置为 vm(虚拟机)
      eval('console.log(123) //#sourceURL=./foo/bar.js') // 此时显示的执行位置为./foo/bar.js（实际还是执行在vm中 指定的只是一个标识）
      ````

      > 1. eval 是用于执行字符串中js代码的函数
      > 2. 可以通过`//#sourceURL`指定一个js代码执行的文件路径

      *  构建最快的一种  每个模块代码都使用evel() 执行**（同时指定每个模块的sourceURL 即源文件的位置）** 所以只能定位文件位置  不能定位具体文件中的 行或者列  

    * sourceMap

      > 一种映射关系  比如js文件中有哪一行的代码写错了 如果不配置sourceMap
      >
      > 那么浏览器中只会显示这个错误在打包后的js文件中的位置 
      >
      > 配置了SourceMap后 ，他能知道打包后的js文件中的错误位置对应的打包之前js文件（源代码）中的错误位置

      * inline: 打包生成的[name].js.map文件以base64的形式存在于打包后的js文件
      * cheap: 错误只提示行 不提示列（速度提升） 且定位的是打包后的代码
      * module: 不仅提示业务代码 还提示第三方模块（loader）中的错误  定位的是没有loader处理（如babel-loader/vue-loader等）的最原始源代码
      * eval: 每个模块都使用eval() 执行 并且将source-map转化为dataUrl添加到eval()中 定位到源代码的行和列(会将生成的souce map文件以base64的形式存在于打包后的js文件)

    * 最佳实践 

      * 在development中

        ````javascript
        devtool: 'cheap-module-eval-source-map'
        ````

      * 在production中 （或者直接none / nosources-source-map 防止暴露源代码）

        ````javascript
        devtool: 'cheap-module-source-map'
        ````

  * **WebpackDevServer**

    > **痛点**： 我们每次修改了源代码之后都要输命令进行手动打包才能更新看到效果
    >
    > **需求**： 所以我们希望在每次我们修改源代码的时候 webpack都可以帮我们自动进行打包

    * 第一种方法：`在package.json中配置打包命令为webpack --watch`

      > **原理：**监听源代码变化 每次变化都重新打包
      >
      > **缺点：**虽然实现了自动打包 但是每次都需要手动刷新才能看到效果

      ![webpack--watch](.\imgs\webpack--watch.png)

    * 第二种方法： `配置devServer`

      > **优点：**1. 更改源代码自动打包并且自动刷新
      >
      >             2. 开启了一个服务器并且将打包后的静态文件放到了这个服务器（可以发送ajax）
      >
      > **注意：** 使用webpack-dev-server打包后的文件不会再存放在本地目录中，而是存放在内存中（为了提升打包的速度）

      1. 下载`webpack-dev-server`

         ````shell
         npm install webpack-dev-server -D
         ````

      2. 在`webpack.config.js`中进行需要的配置

         > **修正：**contentBase表示的时devServer启动服务器的静态资源访问路径 （默认访问的是webpack打包的output路径dist），如果还需要设置其他的静态资源访问路径(一般都是没有参与打包的图片 ico 等 ) 就需要指定contentBase

         ![webpack contentbase](.\imgs\webpack contentbase.png)

         ![public de](.\imgs\public de.png)

         > 如 webpack指定了public为额外的静态资源路径 那么启动服务器后可以直接通过  http://localhost:8080/demo3.jpg  访问图片 ico同理	

         * devServer配置proxy代理

           >背景：前端请求第三方接口可能存在跨域问题（不是所有的接口都支持cors） 所以需要将请求代理到同源地址

           ![devserver配置代理](.\imgs\devserver配置代理.png)

      

      

      1. 在`package.json`中添加脚本命令

         ![devServer添加脚本命令](.\imgs\devServer添加脚本命令.png)

      2. 在命令行执行命令

         ````shell
         npm run serve
         ````

         ![运行devServer](.\imgs\运行devServer.png)

    * **HotModuleReplacement（热模块替换）**

      > **痛点：**虽然使用了devServer可以进行自动打包和自动刷新页面 但是我们每次修改了代码， 整个页面都会初始化 ，我们希望的是只有我们代码修改的地方进行更新
    >
      > **不使用HMR：**每次修改代码 就会刷新整个页面 保存的状态都会丢失
    >
      > **使用HMR:**  每次修改代码  只会更新修改代码的指定模块位置 同时需要自定义修改指定代码的回调函数  

      1. 在webpack.config.js中配置**HotModuleReplacement**插件

         ![引入webpack](.\imgs\引入webpack.png)

         ![引入webpack](.\imgs\配置webpack2.png)

      2. 在devServer中配置

         > **配置的`hotOnly：true`的意思是 发生编译报错 就不进行热更新**

         ![devServer中的配置](.\imgs\devServer中的配置.png)

      3. 手写文件配置（但是处理css-loader已经帮我们配置好了， 所以我们不需要手写）

      4. 命令行`npm run serve`打包

         

      * 案例1：css文件的热模块替换

        > 点击新增按钮就会新增一个item div  在index.js中引入css文件 设置新增奇数个item div会设置背景颜色 使用热模块替换后修改css文件 页面只会刷新修改的部分
        >
        > **注意：**理论上任何文件的热模块替换都需要手动配置， css-loader已经底层帮我们实现了css文件的变化监听， 所以不需要我们手写（区别与js文件）

      ````css
        body div:nth-child(odd) {
        background-color: blue; 
          // devServer打包后修改背景色 页面只会局部刷新背景颜色更改的位置
        }
      ````

    ```javascript
    import './index.css'
    
    var btn = document.createElement('button')
    
    btn.innerHTML = '新增'
    
    document.body.append(btn)
    
    btn.onclick = function() {
          var div = document.createElement('div')
      div.innerHTML = 'item'
          document.body.append(div)
    }
    ```

    ​    

      * 案例2：js文件的热模块替换

        > js文件 还需要额外配置  手动监听js文件的变化
        >
        > 下图配置含义： 如果开启了热更新， 需要手动监听number.js的变化，如果变化了就重新执行函数（同时进行了删除旧的函数结果的操作） 
        
          >**注意**: 因为不同的js文件处理的逻辑/格式不同  所以并没有一个通用的js热替换方案  需要我们自己定制   
        
        ![js模块热更新](.\imgs\js模块热更新.png)

      * 案例3：图片文件的热模块替换
      
        > 图片文件修改后 重新对image中的src属性进行赋值
      
        ![图片HMR](.\imgs\图片HMR.png)
      
        ![图片HMR2](.\imgs\图片HMR2.png)
      
        

  * **plugins**

    > 配置的插件（用于在webpack打包的某一时刻做某些事情 目的：**提高效率**）

  * **module**

    > 配置的loader（用于打包非js文件）

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
Chunk Name： js文件的id值的名字/键值
Chunk Name中的main的含义： 我们在配置入口文件时 entry: './src/index.js' 相当于是
						entry: {
							main: './src/index.js' 的简写
						}


````



### loader

> webpack不能识别非js文件  所以如果要将非js文件进行打包 我们需要使用相应的loader
>
> **注意：**当我们使用有些loader时（看文档） 配置项options很多的时候, 我们可以将options这些配置项单独放到一个配置文件中（如`postcss-loader`对应配置文件为`postcss.config.js`， `babel-loader`对应配置文件为`babel.config.js`）

#### file-loader

> **拷贝源代码中的图片文件到output目录  注意 需要在webpack.config.js中使用publicPath指定图片文件路径**
>
> 专门用于打包图片文件（字体文件等）
>
> **优点：适合打包大体积图片**
>
> **缺点：增加请求次数**

#### url-loader

> **直接将源代码中的图片文件转化为data url的形式  图片使用base64格式  打包不需要再单独拷贝文件到output目录 而是直接解析转为base64的图片**
>
> **优点： 可以减少请求次数**
>
> **缺点：大体积图片使用 会加长打包时间**

* 图片打包的最佳实践：

  * 打包时判断图片的大小，对于大体积的图片使用**file-loader**拷贝图片 对于小体积的图片使用url-loader转化为base64

    > ​	注意： 如果 设置小于10kb的图片才使用url-loader  大于10kb 会自动使用file-loader打包图片 所以需要同时下载file-loader

    ![url-loader](.\imgs\url-loader.png)

#### css-loader

> 分析几个css文件之间的关系 最后合成到一个css文件

* 常见配置

  * importLoaders（作用于 `@import` 的资源之前）

    ![css-loader配置](.\imgs\css-loader配置.png)

    > 如果在index.js（入口js文件）中引入index.scss(scss文件) 在index.scss中又引入了common.scss文件  这种情况就需要配置importLoaders表示了在css-loader之前还有几个loader要执行，如果不配置的话 在打包index.scss时遇到在scss文件中使用@import引入了common.scss就直接使用css-loader进行处理了

  * modules

    ![css-loader配置](.\imgs\css-loader配置2.png)

    > 配置modules实现css模块化  
    >
    > 如果不配置的话 那么在index.js中引入的样式文件 作用于全局

    * 在js文件中对应的写法

      ![css-loader modules js](.\imgs\css-loader modules js.png)

#### style-loader

> 将css挂载到html的head部分（style内联样式） css-loader style-loader需要配合使用

#### sass-loader

> 需要同时安装node-sass

#### postcss-loader



#### babel-loader

> 将高版本的js转化为浏览器可以识别的低版本js

* 安装

  ````javascript
  npm install --save-dev babel-loader @babel/core
  ````

* 在webpack.config.js中配置

  ````javascript
  {
    module: {
      rules: [
        {
          test: /\.m?js$/,
          exclude: /node_modules/,
          use: {
            loader: "babel-loader",
            options: {
              presets: ['@babel/preset-env']
            }
          }
        }
      ]
    }
  }
  ````

* 使用**@babel/polyfill**插件

  > **痛点**：babel默认只能将一些语法（syntax）进行转化  如果需要将api进行转化的话 需要借助插件
  >
  > **场景：**一般用于编写业务代码时 
  >
  > **缺点：**会污染全局

  ````shell
  npm install --save @babel/polyfill  必须安装运行依赖
  ````

  ````javascript
  // 注意下载完@babel/polyfill 需要在webpack的入口文件中首先引入
  import "@babel/polyfill";
  ````

  

* @babel/preset-env中的一些常用的配置（前提是配置了**@babel/polyfill**）![babel配置](.\imgs\babel配置.png)

  1. `useBuiltIns`

     > 配置需要转化的内容  默认将所有的高级语法都转化， `useage`表示只转化代码中用到的 可以减少打包后代码的体积

  2. `targets`

     > 表示转化的代码需要兼容的浏览器版本 （只兼容配置的版本 减少代码打包后的体积）

* babel一些其他的插件

  * **@babel/plugin-transform-runtime**

    > **场景：**一般用于编写类库或者是写插件 （与**@babel/polyfill**效果类似）
    >
    > 但是**不会污染全局**（因为底层采用了闭包实现）
    >
    > [参考博客](https://blog.csdn.net/m0_37613019/article/details/108226550)

    ````shell
    npm install --save-dev @babel/plugin-transform-runtime
    ````

    ````shell
    npm install --save @babel/runtime
    ````

    ![plugin-transform-runtime](.\imgs\plugin-transform-runtime.png)

    ````shell
    npm install --save @babel/runtime-corejs2   
    // corejs 默认为false 不会进行转化处理  设置为2需要额外使用`@babel/runtime-corejs2`
    ````

    ````shell
    npm install --save @babel/runtime-corejs2
    ````

    

### plugin

> 在webpack运行到某一时刻的时候帮我们做一些事情（类似于vue的生命周期）

#### html-webpack-plugin

> webpack打包完成后自动生成一个html文件 同时通过script将打包后的js文件引入

#### clean-webpack-plugin

> 打包前将旧的打包文件删除

#### HotModuleReplacementPlugin

> 用于热更新   需要配合devServer配置使用



### Tree Shaking

> `Tree Shaking`只支持ES Module的引入 

> **摇树** （**一个模块就是一棵树, 模块中导出的内容就是树上的树叶。** 打包的时候将没引入的内容（树叶）全部摇晃掉）： 从其他模块中引入内容（**一个模块可能会导出很多的内容 但是我们引入的时候可能只引入部分内容**）的情况下， 进行打包时只打包引入的内容 未引入的内容不进行打包（摇晃掉） 

* **在没有配置`Tree Shaking`的情况下**

  * 在math.js文件中

    > 导出了 add minus 两个函数

    ![math](.\imgs\math.png)

  * 在入口文件index.js中只导入add函数  然后调用add函数

    ![导入add函数](.\imgs\导入add函数.png)

  * 最终打包发现add 和 minus两个函数都被打包了 （**实际上minus并没有用到**）

    ![打包后的文件](.\imgs\打包后的文件.png)

* **在webpack中配置`Tree Shaking`**

  > 1. 在`mode: development`默认没有配置`Tree Shaking`
  > 2. 在`mode: production`默认自动配置了`Tree Shaking` 只需要在`package.json`配置`sideEffects`指定没有副作用的文件
  
  * 在`webpack.config.js`中配置
  
    * **usedExports：**打包结果中只导出外部用到的成员 （标记没有用到的导出成员）
    * **minimize：**压缩代码  将导出的没有用到的成员代码移除 （将没有用到的导出成员移除）
    * 注意： 单纯的配置**tree sharking**只能移除没有用到的导出成员代码 不能实现移除整个没有用到的模块 因为webpack会认为模块内的非导出成员代码会有副作用（移除整个没有用到的模块就需要配置**sideEffects**）
  
    ![tree sharking](.\imgs\tree sharking.png)

* 在webpack中配置**sideEffects**

  > 1. 配置sideEffects（副作用）的含义：**指定有副作用的文件  这样指定有副作用的文件 在打包过程中会被tree sharking忽略 ，而其他没有指定有副作用的文件（webpack即认定没有副作用）如果没有被用到就整个模块不会被打包**
  >
  > 2. 因为有一些模块是整个直接导入， 并没有具体导入什么内容
  >
  >    如：配置profill时需要导入**@babel/polyfill**直接`import '@babel/polyfill'`即可
  >
  >    导入css文件时  `import './index.css'` 即可
  >
  >    这时候就需要配置`sideEffects: ['@babel/polyfill', '*.css']`（不配置那么这些模块就不会被打包，但实际上这些模块都是我们需要的， **也就是有副作用**）

  * 使用sideEffects

    1. webpack配置文件指定开启sideEffects特性（**production 模式下可以忽略这一步因为已经默认配置了  直接看第2步**）

       ![sideEffects](.\imgs\sideEffects.png)

    2. package.json中指定有副作用的文件 （false 表示 所有的文件都没有副作用）
    
       ![tree shaking配置2](.\imgs\tree shaking配置2.png)





* tree sharking 和 sideEffects的区别
  * **Tree-shaking 只能移除没有用到的(导出)代码成员， sideEffects可以完整移除没有用到的模块（前提指定没有副作用） 。**



### 不同环境的区分打包

> webpack打包分为开发环境（`mode：development`）和生产环境(`mode: production`)

* 需求: 在不同的环境需要下， 使用不同的webpack配置进行打包

  > 1. 打开发环境的包和打生产环境的包有很多webpack配置是不一样的，如mode模式设置， 是否代码压缩，decServer, souce-map的配置等
  > 2. 但是每一次打不同环境的包就修改一次webpack.config.js的做法过于麻烦

  * 做法一：打不同环境的包使用不同的webpack配置文件

    > **缺点：**需要在两个文件中写很多重复的配置

    1. 创建`webpack.dev.js`作为打开发环境包的配置文件 创建`webpack.prod.js`作为打生产环境包的配置文件

       ![不同的环境的配置](.\imgs\不同的环境的配置.png)

    2. 配置`package.json`的script脚本命令
    
       > 1. webpack的命令中 --config 可以指定使用哪个文件作为打包的配置文件
   > 2. 配置完后相当于使用`npm run serve`就是使用`webpack.dev.js`这个配置文件打开发环境的包
       > 3. 使用`npm run build`就是使用`webpack.prod.js`这个配置文件打生产环境的包

   ![配置2](.\imgs\配置2.png)
  
  * 做法二： 打不同环境的包使用不同的webpack配置文件 同时将两个webpack配置文件中相同的配置放到一个公用的文件`webpack.common.js`中
  
    > 需要使用到`webpack-merge`模块将`webpack.common.js`分别引入不同的配置文件
  
    ![做法2](.\imgs\做法2.png)
  
    ​	
  
    * webpack.dev.js
  
      ![开发环境配置](.\imgs\开发环境配置.png)
  
    * 生产环境配置
  
      ![生产环境配置](.\imgs\生产环境配置.png)

### code spliting

> **代码分割**（针对导入的模块进行分开打包）
>
> 不进行代码分割的缺点（所有的代码都打包到一个文件中）：
>
> 1. 打包的文件很大，加载时间会长
>
> 2. 将第三方模块和业务代码打包在一起， 业务代码需要经常改变 ，每次改变都要打包一个很大的文件
>
> 3. 模块引入分为动态引入import(xxxx) `  设置打包后的名称使用在代码中加注释的方法`   和静态引入import "xxxxx"  

* 不进行代码分割的做法

  > 第三方模块和业务代码都打包到一个文件中，每次业务逻辑修改都需要重新打包这个带有第三方模块的大文件 打包速度慢

  ![不进行代码分割的做法](.\imgs\不进行代码分割的做法.png)

* 代码分割的第一种做法

  > 将第三方模块和业务代码分开打包（配置多个入口文件）， 这样即使修改了业务代码，也只会重新打包业务代码，提高了打包速度

  1. 配置多个入口文件

     ![配置多个入口文件](.\imgs\配置多个入口文件.png)

  2. 第三方模块引入写在单独文件作为一个入口

     ![第三方模块入口](.\imgs\第三方模块入口.png)

  3. 业务逻辑写在其他一个文件作为一个入口

     ![业务逻辑入口文件](.\imgs\业务逻辑入口文件.png)

* 代码分割的第二种方法（借助SplitChunksPlugin的配置）

  * SplitChunksPlugin配置参数

    > 如果不写任何配置参数， 只写个空对象， 则使用默认配置（参考官网）
  
    ![splitChunks配置](.\imgs\splitChunks配置.png)
  
    * chunks：用于配置代码分割的作用范围（同步/异步）
  
    * cacheGroups： （决定了代码最终分割到哪里）
  
      > 只要是对同步代码进行分割 就一定会走到cacheGroups这个配置
  
      * defaultVendors： 同步代码分割 匹配的组
        * test： 匹配进行代码分割的文件
        * priority： 匹配组的优先级(代码同时匹配到多个组时生效， 值越大优先级越高)
        * filename：匹配的组打包后的文件名
        * **reuseExistingChunk**： 是否重复打包， 如a文件中引入b文件  那么在打包a的时候就同时打包了b， 后面其他文件中引入b文件 需要打包b就直接复用之前打包的b文件 不会重复进行打包了
      * default：默认分组 引入的模块都会匹配到（包括入口文件）
  
    * minSize： 进行代码分割的最小尺寸（小于这个尺寸打包就不会进行代码分割）
  
    * maxSize： 代码分割打包后的文件最大尺寸， 代码分割打包后的尺寸大于最大尺寸会进行二次分割
  
    * minChunks：**在打包后的文件（chunk）中** 模块被引入多少次才进行代码分割
  
    * maxAsyncRequests：同时加载的最大模块数  超过这个数就不做代码分割
  
    * maxInitialRequests：入口文件加载的最大模块书， 超过就不做代码分割



### Lazy Loading

> 代码懒加载（针对引入模块（文件）， 使用动态引入import）：只有当满足特定条件才加载文件（模块）
>

````javascript
function getComponent() {
    			// webpackChunkName 用于自定义打包后的文件名称 详见官网import api
  return import(/*webpackChunkName: "lodash"*/'lodash').then(({ default: _ }) => {
    
    var element = document.createElement('div')
    element.innerHTML = _.join(['a', 'b'], "***")

    return element

  })
}
// 点击才执行函数  函数中动态引入lodash第三方库  所以在页面那种只有点击了才加载lodash.js文件
// 在html文件中一开始也是不引入打包后的lodash.js文件的
document.addEventListener("click", () => {
  getComponent().then(ele => {
    document.body.append(ele)
  })
  
})
````

> **相较于静态（同步）模块（后面改动业务代码时, 单独打包的第三方模块有缓存）， import动态引入模块(异步）才是提升性能的关键，所有在初始页面渲染没有必要加载的代码（如事件绑定）全部都改成动态引入能极大提升性能**（这就是为什么code spliting默认对异步加载进行代码分割）

````javascript
// 点击事件的处理函数就可以使用动态引入（代码懒加载）  因为只有点击的时候才执行里面的函数 
// 在index.js 中
document.addEventListener("click", () => {
  import(/*webpackChunkName: "handleClick"*/'./click').then( ({ default: f }) => {
    f()
  }) 
})
````

````javascript
// 在click.js中   点击触发的执行函数
function createEle() {
  var element = document.createElement('div')
  element.innerHTML = 'yuweiqi'
  document.body.append(element)
}


export default createEle

````





### Chunk

* 打包后的文件就是chunk



### prefetch/ preload

> 区别
>
> 1. prefetch会在父chunk加载完后才开始加载， preload会在父chunk加载是，并行方式开始加载
> 2. prefetch等浏览器闲置时加载， preload是立即加载
> 3. prefetch用于未来某个时刻   preload用于当下时刻

* prefetch(预取)

  > 将来某些导航下可能需要的资源

  * 加载首页， 点击登录按钮， 显示登录弹框 ， 这是登陆弹框就需要动态引入，同时设置prefetch

    ````javascript
    // webpackPrefetch: true 用于设置prefetch
    import(/* webpackPrefetch: true */ 'LoginModal');
    ````

* preload(预加载)

  > 当前导航下可能需要资源

  * 在一个页面组件中引入了一个体积巨大的第三方库， 就可以使用preload, 加载页面的同时同步加载这个大体积的第三方库

    ```javascript
    // webpackPreload: true 用于设置 preload
    import(/* webpackPreload: true */ 'ChartingLibrary');
    ```

    



### css 代码分割

> 当页面中引入css文件时， webpack默认会通过css-loader 、 style-loader将其转化为js文件，当我们打包时需要将css文件单独分离出来就需要使用css代码分割**(建议在生产环境打包时使用)**

* MiniCssExtractPlugin

  > 用于css代码分割的插件
  >
  > **注意点**：webpack中css文件都是直接导入到js文件中的，所以如果配置了TreeShaking需要单独设置css文件不使用TreeShaking（详情见TreeShaking知识点）

* 或者从代码分割的角度将css文件加入缓存组进行单独打包



### webpack与浏览器缓存

> 1. 需要先了解http缓存机制
> 2. **如果配置的打包后的文件名称是固定的**，那么每次修改了业务代码重新打包后的文件使用的还是之前的文件名， 浏览器在重新请求文件时会发现请求文件名是相同的，那么就会使用本地的缓存， 页面就不会更新

* 解决修改了业务代码但是浏览器有缓存的问题

  > 将output中打包后的文件使用动态名称，`[contenthash]`代表打包文件的hash值， 只要文件变化重新打包， hash就会变化， 浏览器请求时发现请求的是与之前不同的文件，就不会使用本地缓存，而是使用最新的请求数据（使用代码分割单独打包的第三方模块因为我们不会去改变所以hash是不会变的，浏览器的非首次请求第三方模块都是使用的本地缓存，这是一种优化手段,详见`code splitig`代码分割）

  ![解决浏览器缓存问题](.\imgs\解决浏览器缓存问题.png)







## webpack原理

### 手写loader

> loader本质上就是一个函数

* 手写loader的配置

  > loader字段需要填写（自己loader文件）完整的路径

  ![手写loader](.\imgs\手写loader.png)

* 具体的loader代码

  > 1. source: 代表匹配的文件中的内容
  >
  > 2. this.query可以获取配置的loader中的options参数
  > 3. 更多的api参考官网

  ![手写loader2](.\imgs\手写loader2.png)

  

### 手写plugin

