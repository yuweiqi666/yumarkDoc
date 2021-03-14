# express

## 基本用法

````javascript
const express = require("express")
const app = express()
app.get("/", (req, res) => {
  res.send("ok")
})
app.listen(3000)
````



## express路由传值

* 静态路由

  ````javascript
  app.get("/", (req, res) => {
    res.send("于伟奇")
  })
  
  app.get("/login", (req, res) => {
    res.send("login")
  })
  
  app.post("/login", (req, res) => {
    res.send("loginPost")
  })
  
  app.put("/login", (req, res) => {
    res.send("loginput")
  })
  
  app.delete("/login", (req, res) => {
    res.send("logindelete")
  })
  ````

* 动态路由

  ````javascript
  app.get("/register/:id", (req, res) => {
    console.log(req.params);       
    let { id } = req.params    //  req.params 返回的是一个对象  这边能用的是es6的解构赋值
    res.send(`register${id}`)      
  })
  app.listen(3000)
  ````

  > 注意： 配置动态路由的时候要注意前后顺序   可能会覆盖后面的静态路由

  

* 路由get请求传参

  ````javascript
  app.get("/register/:id", (req, res) => {
    console.log("query:", req.query);   // req,query 返回的是传的参数  对象形式   
    let { id } = req.params
    res.send(`register${id}`)
  })
  app.listen(3000)
  
  ````




## express中间件

> 中间件就是匹配路由之前或者匹配路由完成后做的一系列操作，中间件如果想往下匹配的话，那么需要next()

### 内置中间件



* 静态资源托管

  ````javascript
  app.use(express.static("public"))    // 静态资源托管在public目录下
  ````



* 静态资源托管在虚拟目录

  ````javascript
  app.use("xxx", express.static("public"))    //静态资源存放在public中  挂载在虚拟目录xxxx
  ````



### 应用级中间件(用于权限判断)

````javascript
app.use((req, res, next) => {
  console.log("中间件");      // 写在路由匹配的前面  在匹配路由之前会触发
  next()
})
````





### 路由级中间件（用的比较少）

````javascript
app.get("/login/123", (req, res, next) => {     // next()写在路由匹配中
  res.send("login")
  next()
})

app.get("/login/:id", (req, res) => {
  const params = req.params
  console.log('登录页面');         // 匹配完路由会触发
})
````



### 错误处理中间件

````javascript
app.use((req, res, next) => {   // 写在路由匹配和静态资源托管的后面 没有匹配的路由就出发这个中间件
  res.end("404")
})
````



### 第三方中间件

#### body-parser

> 第三方中间件用于获取post提交的数据

````javascript
const express = require("express")
const bodyParser = require("body-parser")

const app = express()

var jsonParser = bodyParser.json()
var urlencodedParser = bodyParser.urlencoded({ extended: false })

app.use(express.static("static"))

app.post("/dologin", urlencodedParser, (req, res) => {
  console.log(req.body);
  res.send("接收post传的值:" + req.body.username)
})

app.post("/dologin2", jsonParser, (req, res) => {
  res.send("接收json数据：" + req.body.username)
})

app.use((req, res, next) => {
  res.send("404")
})


app.listen(3000)
````



#### cookie-parser

> 存储在客户端

> 在express中使用cookie

````javascript
res.cookie(name: string, val: string, options: {
    maxAge: number;    // 过期时间   以毫秒为单位
    signed: boolean;   // 是否加密
    expires: Date;     // 过期具体日期
    httpOnly: boolean; // 只能被web server访问，防止xss攻击
    path: string;      // 指定获取cookie的路由
    domain: string;    // 二级域名共享cookie
    secure: boolean;   // 设置为true时表示只有在https访问才有效
    encode: (val: string) => string;
    sameSite: boolean | 'lax' | 'strict' | 'none';
}
           })
````



* 设置和获取cookie

  ````javascript
  const express = require("express")
  const cookieParser = require("cookie-parser")
  const app = express()
  app.use(cookieParser())
  app.get("/", (req, res) => {
    // 设置cookie
    res.cookie("username", "yyy")
    res.send("你好， express")
  })
  
  app.get("/login", (req, res) =>{
    // 获取不加密的cookie信息
    res.send("login页面____"+ req.cookies.username)
  })
  
  app.get("/product", (req, res) => {
    res.send("product页面")
  })
  
  app.get("/article", (req, res) => {
    res.send("article页面")
  })
  
  
  app.listen(3000)
  ````

* 指定路由才能获取cookie

  ````javascript
  const express = require("express")
  const cookieParser = require("cookie-parser")
  const app = express()
  app.use(cookieParser())
  
  app.get("/", (req, res) => {
    // 指定获取cookie的路由
    res.cookie("username", "yuweiqi", {path: "/login"})
    res.send("你好， express")
  })
  
  app.get("/login", (req, res) =>{
     // 获取路由
    res.send("login页面____"+ req.cookies.username)
  })
  
  app.get("/product", (req, res) => {
    res.send("product页面____"+ req.cookies.username)
  })
  
  app.get("/article", (req, res) => {
    res.send("article页面____"+req.cookies.username)
  })
  
  
  app.listen(3000)
  ````

* cookie加密

  ````javascript
  const express = require("express")
  const cookieParser = require("cookie-parser")
  const app = express()
  app.use(cookieParser("123"))   // 填入加密秘钥
  app.get("/", (req, res) => {
    res.cookie("username", "于伟奇", { signed: true })  // 设置cookie为加密
    res.send("你好， express")
  })
  
  app.get("/login", (req, res) =>{
    res.send("login页面____"+ req.signedCookies.username)  // 获取加密cookie
  })
  
  app.get("/product", (req, res) => {
    res.send("product页面____"+ req.signedCookies.username)
  })
  
  app.get("/article", (req, res) => {
    res.send("article页面____"+req.signedCookies.username)
  })
  
  
  app.listen(3000)
  ````



#### express-session

> 当浏览器访问服务器发送第一次请求时, 服务器会创建一个session对象， 生成一个类似于key，value的键值对， 然后将key（cookie）返回到浏览器（客户）端， 浏览器下次再访问时， 携带key（cookie）， 找到对应的session（value）

* 设置和获取session

  ````javascript
  const express = require("express")
  
  const session = require("express-session")
  
  
  const app = express()
  
  app.use(session({
    secret: 'keyboard cat',   // 服务端生成session签名  随意写
    name: "username",         // 设置客户端存储的cookie的name
    resave: false,            // 强制保存， 即使session并没有变化
    saveUninitialized: true,  // 强制将未初始化的session存储
    cookie: { 
      maxAge： 1000* 6       // 设置session失效时间
      rolling: true,         // 每次请求获取session时重新刷新session失效时间
      secure: false           // 设置成true 则只有https访问才能设置session
    }
  }))
  
  
  app.get("/", (req, res) => {
    if(req.session.username) {
      // 获取session
      res.send("已登录————" + req.session.username)
    } else {
      res.send("未登录")
    }
    
  })
  
  
  app.get("/login", (req, res) => {
    // 设置session 并将key存储在客户端cookie中
    req.session.username = "于伟奇"
    res.send("登录")
  })
  
  app.get("loginout", (req, res) => {
      // 销毁session
      // 1、req.session.cookie.maxAge = 0   所有的session都会被销毁
      // 2、 req.session.username = "" （常用的）
      req.session.destory()    // 销毁所有的session
      res.send("退出登录")
  })
  
  app.listen(3000)
  ````

  



#### connect-mongo

* 分布式系统中session存储在mongodb数据库中

  ````javascript
  
  const express = require("express")
  const session = require("express-session")
  const mongoStore = require("connect-mongo")(session)
  const app = express()
  app.use(session({
    secret: 'keyboard cat',   // 服务端生成session签名  随意写
    name: "username",         // 设置客户端存储的cookie的name
    resave: false,            // 强制保存， 即使session并没有变化
    saveUninitialized: true,  // 强制将未初始化的session存储
    cookie: { 
      maxAge: 1000*6,   // 设置session失效时间
      secure: false   // 设置成true 则只有https访问才能设置session
    },
    store: new mongoStore({
        // 将session存储至mongodb数据库
          url: 'mongodb://test1User:test1User123@localhost/test1'        
      })
  
      
  }))
  
  app.get("/", (req, res) => {
    if(req.session.username) {
      res.send("已登录————" + req.session.username)
    } else {
      res.send("未登录")
    }
    
  })
  
  
  app.get("/login", (req, res) => {
    req.session.username = "于伟奇"
    res.send("登录")
  })
  
  app.get("/logout", (req, res) => {
    // req.session.username = ""
    req.session.destroy()
    res.send("退出登录")
  })
  
  app.listen(3000)
  ````



## 路由模块化

> 将路由配置写到单独的文件中， 易于维护和管理

````javascript
## 在routes/login.js中

const express = require("express")

const router = express.Router()

router.get("/", (req, res) => {
  res.send("首页")
})

router.get("/login1", (req, res) => {
  res.send("登录页面")
})

router.get("/logout1", (req, res) => {
  res.send("登出")
})

module.exports = router


## 在app.js中

const express = require("express")
// 引入外部路由模块
const login = require("./routes/login")
const app = express()
// 挂载路由
app.use("/login", login)

app.listen(3000)



##   /login 返回首页
##   /login/login1 返回登录页面
##   /login/logout1  返回登出

````



​	

