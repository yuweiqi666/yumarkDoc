# nodejs开发

## 概念

* 服务端开发
  1. 实现网站的业务逻辑
  2. 数据的增删改查
* nodejs
  * nodejs是基于chrome V8 引擎的 javascript 代码运行环境
* 组成部分
  * nodejs组成部分： ECMAScript + node模块api



## node模块化开发

### 传统的js开发存在弊端

* 文件依赖不明确
* 命名冲突



### nodejs模块化开发

* nodejs规定一个js文件就是一个模块，模块内部定义的变量金额函数默认情况下外部无法得到
* 模块内部可以使用exports/module.exports导出对象成员，使用require导入对象成员



### nodejs模块的导出和导入（commonjs）

* module.exports导出模块

  ````javascript
  //在a.js中导出模块
  module.exports = {
      name: "yuweiqi"，
      age: 20
  }
  
  ````

* require 导入模块

  ````javascript
  const user = require("./a")  //  后缀.js可以省略
  console.log(a.name)      ---->  yuweiqi
  console.log(a.age)		 ----> 20
  ````




### node初始化项目

> 在目录中会生成package.json文件用来记录项目中用到的依赖

````
npm init             // 以node规范的形式初始化一个项目 或者创建一个node的包文件
````



## node系统模块

### 文件模块（fs）

* 读取文件  fs.readFile

  ````javascript
  const fs = require("fs")
  fs.readFile("a.txt", "utf-8", (err, doc) => {
    if(!err) {
      console.log(doc);
    }
  })  
  // 路径尽量使用绝对路径 因为相对路径仅仅是当前执行命令的路径
  ````

* 写入文件  fs.writeFile    （有内容会进行替换）

  ````javascript
  const fs = require("fs")
  fs.writeFile("a.txt", "我是于伟奇123", (err) => {
    if(!err) {
      console.log("写入成功");
    }
  })
  ````

* 文件中追加内容     fs.appendFile         （不会覆盖之前的内容）

  ````
  const fs = require("fs")
  
  fs.appendFile("./a.txt", "我是于伟奇456", err => {
    if(!err) {
      console.log("追加成功");
    }
  })
  ````



* 检测是文件还是目录  fs.stat

  ````javascript
  const fs = require("fs")
  
  fs.stat("./a.txt", (err, doc) => {
    if(err) {
      console.log(err);
    } else {
      console.log("是文件：",doc.isFile());
      console.log("是目录：",doc.isDirectory());
    }
  })
  ````

* 创建目录 fs.mkdir

  ````javascript
  const fs = require("fs")
  
  fs.mkdir("./css", err => {
    if(!err) {
      console.log("创建成功");
    }
  })
  ````

* 读取文件流

  ````javascript
  const fs = require("fs")
  
  const readSteam = fs.createReadStream("a.txt") // 创建写入流
  
  var n = 0
  
  readSteam.on("data", (doc) => {     // 读取事件
    n++                               
  })          
  
  readSteam.on("end", () => {          //  读取结束事件
    console.log(n);  // 打印的n表示读取次数
  })
  
  readSteam.on("error", (err) => {
    console.log(err);  // 捕获异常
  })
  ````

* 文件流的方式写入文件

  ````javascript
  var str = "我是于伟奇123\n"
  for(var i = 0; i < 10000; i++) {
    str+="我是于伟奇123\n"
  }
  
  const fs = require("fs")
  
  const writeSteam = fs.createWriteStream("a.txt")  //创建写入流
  
  writeSteam.write(str)   // 写入事件
  ````

* 管道流    ---》 从一个流中读取数据， 在写入另一个流中，用于复制大文件

  ````javascript
  const fs = require("fs")
  
  const readSteam = fs.createReadStream("a.txt")
  
  const writeSteam = fs.createWriteStream("b.txt")
  
  readSteam.pipe(writeSteam)
  
  ````

  

  



## node常用第三方模块



### path模块

> 用于拼接路径
>
> 因为不同的操作系统分隔符不统一  使用path模块可以自动识别

````
path.join("itcast", "a", "b")    ===> 路径 itcast/a/b
````

````javascript
path.join(__dirname, "a.txt")    ===>__dirname 意思为当前位置的绝对路径
````



### mime模块

> 用于识别文件格式

````javascript
mime.getType("xxxxxxxxxxx")   // 原生node进行静态资源托管的时候识别文件类型
````



## 请求参数获取

### get请求参数

> 参数为application/x-www-form-urlencoded格式       参数只能是application/x-www-form-urlencoded格式

````javascript
//请求地址 http://localhost:3000/?username=123   x-www-form-urlencoded格式

const http = require("http")

const url = require("url")

const app = http.createServer()


app.on("request", (req, res) => {
  const { query } = url.parse(req.url, true)
  console.log(`get参数: ${query.username}`);     // 获取get参数username
  res.end("ok")
})

app.listen(3000)
````





### post请求参数

> form表单提交默认为application/x-www-form-urlencoded格式   参数可以为application/x-www-form-urlencoded、application/json、multipart/form-data（主要用于文件上传）三种格式

````javascript
## post传递x-www-form-urlencoded格式的参数
const http = require("http")
const url = require("url")
const path = require("path")
const fs = require("fs")
const mime = require("mime")
const queryString = require("querystring")
const app = http.createServer()

app.on("request", (req, res) => {
  let { pathname } = url.parse(req.url, true)
  let realPath = path.join(__dirname, "public" + pathname)
  let fileType = mime.getType(realPath)
  let data = ""
  if(pathname === "/login") {
    // post参数以流的形式传输   每次传输都会触发data事件
    req.on("data", (paramsUrl) => {
      data = data + paramsUrl
    })
	// 参数传输完毕触发end事件
    req.on("end", () => {
      let { username, password } = queryString.parse(data) 
      //使用queryString模块可以将参数转化为对象的形式
      console.log("参数username：", username);
      console.log("参数password：", password);
    })
    res.end("ok")
  } else {
    fs.readFile(realPath, (err, doc) => {
      if(!err) {
        res.writeHead(200, {
          "content-type": fileType
        })
        res.end(doc)
      }
    })
  }
})


app.listen(3000)
````



````javascript
## post传递json格式的参数
const http = require("http")
const url = require("url")
const path = require("path")
const fs = require("fs")
const mime = require("mime")
const queryString = require("querystring")
const app = http.createServer()

app.on("request", (req, res) => {
  let { pathname } = url.parse(req.url, true)
  let realPath = path.join(__dirname, "public" + pathname)
  let fileType = mime.getType(realPath)
  let data = ""
  if(pathname === "/login") {
    req.on("data", (paramsUrl) => {
      data = data + paramsUrl
    })

    req.on("end", () => {
      console.log("json参数", JSON.parse(data)); 
      // 传参会默认将参数转化为字符串格式， 使用JSON.parse()将json字符串转化为json对象
    })
    res.end("ok")
  } else {
    fs.readFile(realPath, (err, doc) => {
      if(!err) {
        res.writeHead(200, {
          "content-type": fileType
        })
        res.end(doc)
      }
    })
  }
})


app.listen(3000)
````





## 原生node静态资源托管

* 原生代码

  ````javascript
  // http模块搭建web服务器
  const http = require("http")
  // fs模块读取文件
  const fs = require("fs")
  // mime模块识别读取文件的类型
  const mime = require("mime")
  // url模块分离出地址中的pathname
  const url = require("url")
  // path模块用于拼接路径
  const path = require("path")
  const app = http.createServer()
  app.on("request",(req, res) => {    // 监听请求事件
    let pathname = url.parse(req.url, true).pathname  // 获取pathname
    pathname = pathname === "/" ? "/defaule.html": pathname    // 重定向
    let realPathName = path.join(__dirname, "static"+pathname)  // 拼接成为绝对路径
    console.log(realPathName);  
    fs.readFile(realPathName, (err, doc) => {     // 根据绝对路径读取文件
      if(!err) {
        res.writeHead(200, {
          "content-type": mime.getType(realPathName) //根据路径中的文件类型设置返回信息头部
         })
        res.end(doc)
      }
    })
  })
  app.listen(3000)
  ````

* 封装静态网站服务器

  ````javascript
  # 在static.js文件中
  const mime = require("mime")
  const fs = require("fs")
  const url = require("url")
  const path = require("path")
  const static = (req, res, reqUrl) => {   
    let handlePath= url.parse(reqUrl).pathname
    handlePath = handlePath === "/" ? "/index.html": handlePath
    let realPath = path.join(__dirname, "static"+handlePath)
    const fileType = mime.getType(realPath)
    fs.readFile(realPath, (err, doc) => {
      if(!err) {
        res.writeHead(200, {
          "content-type": fileType
        })
        res.end(doc)
      }
    })
  }
  
  
  module.exports = {
    static
  }
  
  
  # 在app.js中
  
  const http = require("http")
  const { static } = require("./static")
  const app = http.createServer()
  app.on("request",(req, res) => {
    static(req, res, req.url)
  })
  app.listen(3000)
  ````

  



