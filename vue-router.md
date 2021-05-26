# vue-router

## 安装使用（基于vue-cli）

* 安装

  ````javascript
  npm install vue-router
  ````

* 根目录新建router文件夹

  ````javascript
  // 引入vue
  import Vue from "vue"
  
  // 引入路由
  import VueRouter from "vue-router"
  // 引入组件
  import login from "../views/login.vue"
  
  Vue.use(VueRouter)
  
  // 配置路由
  const routes = [
    {
      path: "/login",
      name: "login",
      component: login
    }
  ]
  
  const router = new VueRouter({
    routes
  })
  
  // 导出
  export default router
  ````

  ````javascript
  // 在main主文件中
  // 引入路由
  import router from "./router/index"
  
  new Vue({
    // 挂载路由
    router,
    render: h => h(App),
  }).$mount('#app')
  ````

  

## 动态路由

> 1. 路由传参（参数在路径中）
> 2. 配置路由时使用动态路径如 “/home/:msg”(msg其实就是传的参数)
> 3. 通过this.$route.params.msg接收动态路由参数

````javascript
// 路由配置
{
    path: "/home/:msg",
    name: "home",
    component: home
  }
// 路由跳转
this.$router.push({
    name: "home",
    params: {
      msg: this.propData
    }
  })

// 路由接收参数
this.message = this.$route.params.msg
console.log("this.message", this.message);
````



## 编程式导航（路由传参）

### this.$router.push()

````javascript
// 命名的路由 动态路由传参 跳转到/user/:userId  params中必须是userId 代表传的参数
router.push({ name: 'user', params: { userId: '123' }})

// 带查询参数，query传参 变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})

// 当路由跳转提供了完整的path 则params会被忽略
const userId = '123'  //跳转/user/123 这时userId只是个变量  可以随便取名
router.push({ path: `/user/${userId}` })   
````



### this.$router.replace()

> 和this.$router很像  唯一不同的是不会向history中添加新记录



### this.$router.go()

> 参数为一个整数  表示在history中前进或者后退多少步



### props路由传参

> 使用$route传参数使其与路由高度耦合 限制灵活性

* props传的是对象  （传静态参数）

  ````javascript
  // 路由配置时
  {
     // 动态参数
     path: "/home/:msg",
     name: "home",
     component: home,
     // 静态参数
     props: {
       username: "张三",
       age: 18,
       www: false
     }
   }
  
  // 在组件中使用props参数
  props: ["username", "age", "www"] // props传的是对象  不是msg
  ````

* props传的是布尔值 （传动态参数）

  ````javascript
  // 路由配置时
  {
     path: "/home/:msg",
     name: "home",
     component: home,
     props: true
   }
  
  // 在组件中使用props参数
  props: ["msg"]   // 相当于可以让params参数通过props的形式传进组件
  ````

* props传的是函数（动态参数和静态参数相结合）

  ````javascript
  // 路由配置时
  {
     path: "/home/:msg",
     name: "home",
     component: home,
     props: router => ({username: "张三", age: 20, msg: router.params.msg})
   }
  
  // 在组件中使用props参数
  props: ["msg"]   // 相当于可以让params参数通过props的形式传进组件
  ````

  

## 路由导航守卫

### 全局守卫

````javascript
router.beforeEach((to, from, next()) => {
	if(to.path == "/login") return next()
	const tokenStr = window.sessionStorage.getItem("myToken")
    if(!tokenStr) return next("/login")
	next()
})
````



