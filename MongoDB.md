# MongoDB数据库

## 数据库概念

### 数据库介绍

* 存储数据的仓库，独立于语言之外的软件，可以通过api去操作它
* 生活中
  * 通过仓库来存放物品，每个仓库有N个架子, 每个架子有N个物品
* 在程序中
  * 通过数据库来存放数据， 每个数据库有N个表/集合，每个表/集合有N个数据/文档

### 数据库种类

* 关系型数据库

  > 1. 遵循统一的SQL标准，语法大同小异
  > 2. 有库和表的约束等

  * Oracle、 MySql、 SQLite、 SQL Server等

* 非关系型数据库（Not Only SQL）

  > 1. 没有统一的标准
  >
  > 2. 一般以键值对形式存储
  > 3. 读取速度更快

  * Mongodb（文档）、Redis/Memcache(内存)

### 为什么使用数据库

1. 动态网站的数据都存储于数据库中
2. 可以持久存储客户端通过表单收集的用户信息
3. 可以对数据进行高效的管理

### MongoDB中的相关术语

* database: 数据库，mongodb中可以建立多个数据库

* collection：集合，一组数据的集合，可以理解为js中的数组

* document：文档，一条具体的数据， 可以理解为js中的对象

* field：字段，可以理解为js中的对象属性

  > field --> document --> collection --> database

### 目标

> 1. 存放项目数据
> 2. 实战工作中可以独立写api接口

## MongoDB安装步骤

* linux环境

  > 1. 下载mongodb（linux）安装包（通过winScp工具将安装包从window移动到linux）
  >
  > 2. 远程工具连接linux（putty等）
  >
  > 3. 移动到安装包的目录解压安装包
  >
  >    ````shell
  >    tar -zxvf mongodb-linux-x86_64-xxxxx.tgz
  >    ````
  >
  > 4. 将解压包移动到指定目录
  >
  >    ````shell
  >    mv mongodb-linux-x86_64-xxxx/ /user/local/mongodb
  >    ````
  >
  > 5. 创建数据存放目录与日志存放目录
  >
  >    ````shell
  >    mkdir -p /user/local/mongodb/data /user/local/mongodb/logs
  >    ````
  >
  > 6. 启动MongoDB服务
  >
  >    ````shell
  >    /user/local/mongodb/bin/mongod --dbpath=/user/local/mongodb/data --logpath=/user/local/mongodb/logs/mongodb.log --logappend --port=27017 --fork
  >    ````
  >
  > 7. 后期登录即可
  >
  >    ````shell
  >    /user/local/mongodb/bin/mongo
  >    ````

* window环境

  > 1. 下载mongodb（window）安装包并解压
  >
  > 2. 创建服务（dos命令窗口中移动至安装包的安装路径）                                                                                                                                                    
  >
  >    ````shell
  >    bin/mongod.exe --install --dbpath 磁盘路径 --logpath 日志路径
  >    ````
  >
  > 3. 启动服务（在mongodb的bin目录   ===》 以管理员身份运行）
  >
  >    ````shell
  >    net start mongodb
  >    net stop mongodb       
  >    ````
  >
  > 4. 登录(在mongodb的bin目录   ====》 也可以直接配置环境变量)
  >
  >    ````shell
  >    mongo
  >    ````
  >
  >    

## MongoDB基本操作

### 查看数据库

````shell
show databases
````

### 选择数据库

````shell
use 数据库名
````

> 注意：在mongodb中选择不存在的数据库时不会报错**（隐式创建）**，当这个数据库中有内容后会展示



### 查看当前选择的数据库

````shell
db
````

### 删除数据库

````shell
db.dropDatabase()
````

> 删除当前选择的数据库

### 查看集合

````shell
show collections
````

### 创建集合

````shell
db.createCollection('集合名')
````

> 注意： 后期插入数据都是**隐式创建**集合



### 删除集合

````shell
db.xxx.drop()
````



### mongodb文档增删改查

> 数据库主要用来存放项目数据， 数据库和集合的创建完成之后需要对文档（数据）进行增删改查

#### 插入文档

* 语法

  ````shell
  db.集合名.insert(json数据)
  ````

  > 集合若存在，则直接插入数据，若集合不存在，则**隐式创建**

* 练习：在test2数据库的c1集合中插入数据（名字叫yuweiqi  年龄18岁）

  ````shell
  use test2
  db.c1.insert({
  	name: "yuweiqi",
  	age: 18
  })
  // mongodb会给每一条文档加一个全球唯一的_id键
  ````

  * 可以给每条数据自定义_id   **但是强烈不推荐**

    * 只要在插入数据的时候加上自定义的_id既可覆盖

  * 插入多条数据

    ````shell
    db.c1.insert([
    	{name: "zhangsan", age: 18},
    	{name: "zhaosi", age: 19},
    	{name: "wangwu", age: 20}
    ])
    
    // 一次性插入三条数据
    ````

  * 如何快速插入多条数据

    > 因为mongodb底层使用的是js引擎实现的，所以支持部分js语法
    >
    > 因此可以写for循环

    ````shell
    for(var i = 0; i < 10; i++) {
    	db.c1.insert({name: "a"+ i, age: i})
    }
    ````

    

#### 删除文档

* 语法

  ````
  db.集合名.remove(条件, [,是否删除一条])
  
  是否删除一条： true 删除全部匹配的   false  删除一条（默认）
  ````

  

#### 修改文档

* 基础语法

  ````shell
  db.集合名.update(条件，新数据[,是否新增， 是否修改多条])
  
  是否新增： 根据条件匹配不到数据时是否插入该条数据     true为插入  false为不插入（默认）
  是否修改多条： 将匹配成功的数据都修改     true 是  false 否（默认）
  ````

* 升级语法

  ````shell  
  db.集合名.update(条件，{修改器：{键：值}})
  ````

  | 修改器  | 作用     |
  | ------- | -------- |
  | $inc    | 递增     |
  | $rename | 重命名列 |
  | $set    | 修改列值 |
  | $unset  | 删除列   |

  

  * **准备工作**

    ````shell
    for(var i = 1; i <= 10; i++) {
    	db.c2.insert({uname: "zs"+i, age: i})
    }
    在test2数据库的c2集合中插入10条数据
    ````

  * **练习1：将{uname: zs1}改为{uname: zs2}**

    ````shell
    db.c2.update({uname: "zs2"}, {$set: {uname: "zs22"}})
    ````

  * **练习2：将{uname: zs10}的年龄增加两岁或者减小两岁**

    ````  
    db.c2.update({uname: "zs10"}, {$inc: {age: 2}})  增加两岁
    db.c2.update({uname: "zs10"}, {$inc: {age: -2}}) 减小两岁
    ````

  * **练习3：插入数据： db.c2.insert({uname: "升龙教主"，age： 888， who: "男"， other：“非国人”})**

    ````
    db.c2.update({uname: "升龙教主"}, {$set: {uname: "升龙大教主"}})  修改uname的值
    db.c2.update({uname: "升龙大教主"}, {$rename: {who: "sex"}})   修改who字段的字段名
    db.c2.update({uname: "升龙大教主"}, {$inc: {age: 111}}) 		age字段的值增加111
    db.c2.update({uname: "升龙大教主"}, {$unset: {other: true}})   删除other字段
    
    
    // 一次性写多个修改器
    db.c2.update({uname: "升龙大教主"}, {
    	$set: {uname: "升龙大教主"},
    	$rename: {who: "sex"},
    	$inc: {age: 111},
    	$unset: {other: true}
    }
    ````

#### 查询文档

* 语法

  ````shell
  db.集合名.find(条件[,查询的字段])
  
  // 1.条件：
  	查询所有的数据 ---> {}或者不写
  	查询 age=6 的数据 ---> {age： 6}
  	查询age=6 同时 sex = "男"的数据 ---> {age: 6, sex: "男"}
     
     2.查询的字段（可选参数）
     	不写 ---> 查询符合条件数据的所有字段
  	{age： 1}  ---> 只显示符合条件数据的age字段
  	{age： 0}  ---> 不显示符合条件数据的age字段（其他字段都显示）
  	
  	但是不管怎么写 系统自定义的_id都会显示
  ````

  | 条件 | 含义           |
  | :--- | :------------- |
  | $gt  | 大于           |
  | $lt  | 小于           |
  | $gte | 大于等于       |
  | $lte | 小于等于       |
  | $ne  | 不等于         |
  | $in  | 包含(查询多个) |
  | $nin | 不包含         |

  

  * **练习1：查询所有的数据**

    ````shell
    db.c1.find({})
    ````

  * **练习2：查询年龄大于5岁的数据**

    ````shell
    db.c1.find({age: {$gt: 5}})
    ````

  * **练习3：查询年龄是5岁、8岁、10岁的数据**

    ````shell
    db.c1.find({age: {$in: [5, 8, 10]}})
    ````

  * **练习4：只看年龄列（_id不考虑）**

    ```shell
    db.c1.find({},{age:1})
    ```




## MongoDB实战

### 排序

* 语法

  ````
  db.集合名.find().sort(JSON数据)
  
  说明：JSON中键时候需要排序的字段，  值为1 升序
  							  值为-1 降序
  							
  							
  练习： 根据年龄升序&降序
  db.c1.find({}).sort({age: 1})   升序
  db.c1.find({}).sort({age: -1})  降序
  ````

  

### 分页

* **Limit与Skip方法**

  ````
  db.集合名.find().sort().limit(数字).skip(数字)
  
  说明： limit() 限制查询数据的条数
  	  skip()  跳过指定的数量
  	  
  练习：降序查询查询2条 
  		db.c1.find().sort({age: -1}).limit(2)
  		
  	 降序跳过2条并查询2条
  	 
  		dn.c1.find().sort({age: -1}).skip(2)
  ````

  

* 实战分页

  > 需求：数据库1-10数据，每页显示2条数据（5页）

  语法：

  ````
  db.find().skip(m).limit(n)
  
  m: (当前页 - 1) * n
  n: 每页显示条数
  ````



### 统计总数量

* 语法： count()

  ````
  db.集合名.find().count()
  ````



## MongoDB聚合查询

### 概念

* 把数据聚起来然后查询  可以对集合中的文档进行变换和组合

### 语法

````
db.集合名字.aggregate([
	{
		管道：{表达式}
	},
	....
])
````

* 常用管道

````
$group   将集合中的文档进行分组，用于统计数据
$match   用于过滤数据,只输出符合条件的数据
$sort    聚合数据进一步排序     1 升序   -1降序
$skip    跳过指定文档
$limit   限制集合数据返回的文档数
$project 增加、删除、重命名字段
$lookup  用以引入其他集合的数据（表关联查询）
...
````

* 常用表达式

  ````
  $sum: 总和       $sum: 1 相当于count  统计数量
  $avg: 平均           
  $min: 最小值
  $max: 最大值
  ````

  

* 练习

  * **统计男生、女生的总年龄**

    ````
    db.c1.aggregate([
    	{
    		$group: {
    			_id: "$sex",
    			res: {
    				$sum: "$age"
    			}
    		}
    	}
    ])
    ````

    

  * **统计男生女生的总人数**

    ````
    db.c1.aggregate([
    	{
    		$group: {
    			_id: "$sex",
    			count: {$sum: 1}
    		}
    	}
    ])
    ````

    

  * **求学生总数和平均年龄**

    ````
    db.c1.aggregate([
    	{
    		$group: {
    			_id: null,
    			count: {$sum: 1},
    			avg: {$avg: "$age"}
    		}
    	}
    ])
    ````

    

  * **查询男生、女生人数, 按人数升序**

    ````
    db.c1.aggregate([
    	{
    		$group: {
    			_id: "$sex",
    			count: {$sum: 1}
    		}
    	},
    	{
    		$sort: {
    			count: 1
    		}
    	}
    ])
    ````

  * $lookup  集合（表）关联
  
    ````
    db.order.aggregate([               //order集合与order_item集合关联
    	{
    		$lookup: {
    			from: "order_item",     // 关联副表
    			localField: "order_id", // 主表的关联字段
    			foreignField: "order_id", // 副表的关联字段
    			as: "items"     // 关联后的数据显示的字段
    		}
    	}
    ])
    ````
  
    


## MongoDB索引

### 索引概念

* 说明： 索引是一种排序好的便于快速查询的数据结构
* 作用：帮助数据库高效的查询数据



### 索引优缺点

* 优点：

  > 提高数据查询的效率， 降低数据库IO成本

* 缺点：

  > 占用磁盘空间，
  >
  > 大量的索引影响SQL语句效率，因为每次插入和修改数据都会更新索引

### 语法

* 创建索引语法

  ````
  db.集合名.createIndex(待创建索引的列[,额外选项])
  ````

* 参数：

  > 待创建的索引：{ 键:1, ....., 键：-1 }
  >
  > ​	说明: 1 升序  -1降序  例如{ age: 1 } 表示创建age索引并升序  的方式存储
  >
  > 额外选项： 设置索引的名称或者唯一索引等等

  

* 删除索引语法

  > 全部删除：db.集合名.dropIndexes() 
  >
  > 删除指定：db.集合名.dropIndex(索引名)



* 查看索引语法

  > db.集合名.getIndexes()

  

* 创建组合索引

  > db.c1.createIndex({键: 方式，键: 方式})

  

* 创建唯一索引

  > db.集合名.createIndex(待添加索引的列, {unique: l列名})

### 练习

* 给name列添加普通索引

  ````
  db.c1.createIndex({name: 1})
  ````

* 删除name索引

  ````
  db.c1.dropIndex("name")
  ````

* 给name列添加索引并且命名为yuweiqi

  ````
  db.c1.createIndex({name: 1}, {name: "yuweiqi"})
  ````

* 创建组合索引 同时给两个字段加索引

  ````
  db.c1.createIndex({name: 1, age: 1})
  ````

  

* 给name列添加唯一索引

  ````
  db.c1.createIndex({name: 1}, {unique: name})
  ````

  

### 分析索引（explain）

* 语法： 

  ````
  db.c1.find().explain('executionStats')
  
  ````

### 选择原则（如何选择合适的列做索引）

* 为常做条件、排序、分组、联合操作的字段建立索引
* 选择唯一性索引                （同值较少如性别字段）
* 选择较小的数据列， 为较长的字符串使用前缀索引



## MongoDB权限机制

### 需求

> 我们在DOS窗口直接输入命令就可以登录数据库
>
> 这个在实战中是不允许的
>
> 所以使用权限机制，开启验证模式即可

### 语法

* 创建账号

  ````
  // 登录超级管理员的前提下 给某个数据库创建用必须要先切换到这个数据库
  db.createUser({
  	"user": 账号,
  	"pwd": 密码,
  	roles: [{
  		role: 角色,
  		db: 所属数据库
  	}]
  })
  ````

* 角色

  ````
  #角色种类
  超级用户角色： root
  数据库用户角色： read  readWrite
  数据库管理角色： dbAdmin userAdmin dbOwner
  ......
  ````
  
* 删除用户

  > 使用管理员账户登录先切换到admin数据库

  ````shell
  db.system.users.remove({user: "用户名"})
  ````

* 查看所有的用户

  > 使用管理员账户登录先切换到admin数据库

  ````shell
  db.system.users.find()
  ````

  

### 开启验证模式

> 概念： 用户需要输入账号密码才能登录使用

* 操作步骤

  ````
  1.添加超级管理员
  2.退出卸载服务
  3.重新安装需要输入账号密码的服务（注意在原安装命令基础上加上--auth即可）
  4.启动服务
  ````

* 第一步：添加超级管理员

  ````
  # mongo登录进数据库
  mongo
  
  # 选择admin数据库
  use admin
  
  # 添加角色
  db.createUser({
  	"user"："账号"，
  	“pwd”: "密码"，
  	roles: [{
  		role: "角色"，
  		db: "所属数据库"
  	}]
  })
  ````

  > 注意： 前面版本的数据库是看不到adifmin数据库的  直接选中就可以了
  >
  > ![mongodb账号权限](.\imgs\mongodb账号权限.png)

  

* 第二步：退出卸载服务

  ````
  bin/mongod --remove
  ````

  > DOS窗口必须用管理员身份运行

  

* 第三步： 安装验证模式的mongodb

  ````
  #在mongo的bin目录下（或者配置环境变量）
  mongod --install --dbpath D:\MongoDB\Server\4.4\data --logpath D:\MongoDB\Server\4.4\log\mongod2.log(注意日志名字不能和之前安装的重复) --auth
  ````

  > 测试安装成功： 1、mongo登录没有warning警告  
  >
  > ​							2、show adtabases 默认看不到数据库了



### 通过超级管理员账号登录

* 语法：

  ````
  # 第一种
  mongo 服务器ip地址:端口(27017)/数据库名 -u 用户名 -p 密码
  
  admin  admin123
  
  
  #第二种
  先登录 -》 再选择数据库 -》 输入 db.auth(用户名,密码)
  ````

 ### 指定数据库增加用户

1. 使用超管用户登录
2. 切换到指定的数据库
3. 添加用户 **（正常的增删改查操作只需要给用户readWrite权限就可以了）**

### 指定数据库删除用户

1. 使用超管用户登录

2. 切换到指定的数据库

3. 删除指定用户数据库操作

   ````shell
   
   ````



## MongoDB备份还原

### 备份数据库

* 语法

  ````
  导出数据语法： mongodump -h -port -u -p -d -o
  说明： -h       host 服务器IP地址（一般不写，默认本机）
  	  -port         端口（默认27017 一般不写）
  	  -u       user  账号
  	  -p       pwd   密码
  	  -d       database  数据库（数据库不写则导出全局）
  	  -o       open    备份到指定目录
  ````



### 还原数据库

* 语法： 

  ````
  还原数据库语法： mongorestore -h -port -u -p -d --drop 备份数据目录
  
  --drop  先删除数据  再导入  不写则覆盖
  ````

  





## nodejs操作数据库

### mongoose模块

1. 安装mongoose模块 

````javascript
npm install mongoose    // 安装mongoose模块

net start mongodb  //开启数据库
net stop mongodb    //关闭数据库


````

2. node连接mongoodb

   ````javascript
   mongoose.connect("mongodb://用户名:密码@host:port/数据库名称", {相关配置项}).then(res => {
       console.log("数据库连接成功")
   })
   .catch(err => {
       console.log(err)
   })
   ````




### 创建集合

* 创建集合的步骤

  * 1. 对集合设定规则(创建模型)

       ````javascript
       const courseSchema = new mongoose.Schema({
           name: String,
           author: String,
           isPublish: boolean
       })
       ````

    2. 创建集合

       ````javascript
       const Course = mongoose.model('Course', courseSchema);
       ````


### 创建文档

* 创建文档的两种方法

  * 第一种方法

    ````javascript
    const course = new Course({
       name: "nodejs",
       author: "于伟奇",
       isPUblish: false
    })
    
    course.save()
    ````

  * 第二种方法

    ````javascript
    Course.create({    
        name: "nodejs",
        author: "于伟奇"，
        isPublish: true
    }, (err, doc) => {
        if(err === null) {
            console.log(doc)
        }
    })
    
    //Course.create()返回的是Promise对象
    // promise写法
    Course.create({
        name: "nodejs",
        author: "于伟奇",
        isPublish: false
    }).then(res => {
        console.log(res)
    })
    .catch(err => { 
        console.log(err)
    })
    ````

    

### 数据导入导出数据库

* 准备工作

  2. 在mongodb安装路径中找到mongoimport.exe/ mongoexport.exe可执行文件
  3. 将路径添加到系统path环境变量中
  
* 使用

  ````shell
  # 导入json数据
  mongoimport -h IP（默认本机）--port 端口（默认27017） -u 用户名 -p 密码  -d xxxxx -c xxxx --file xxxxx
  // -d 数据库名
  // -c 导入集合名
  // --file 导入文件的位置
  
  # 导出数据
  mongoexport -h IP(默认本机) --port 端口（默认27017） -u 用户名 -p 密码 -d xxxx   -c xxxx  -o 导出的文件名 
  ````

### 查询文档

#### 根据条件查询文档

* 指定字段值查询

````javascript
Course.find().then(res => {
    console.log(res)
})
// Course 文档名称
//返回Course集合中所有的文档（返回格式为数组的形式）


Course.find({_id: '123456'}).then(res => {
    console.log(res)
})
//返回Course集合中符合条件的文档  例：返回_id为'123456'的文档


Course.findOne().then(res => {
    console.log(res)
})

//返回一个文档   若条件为空则返回Course集合中第一个文档
````

* 指定字段名查询

  ````javascript
  Course.find("name email -_id").then(res => {
      console.log(res)
  })
  
  // 查询字段名为name、email和字段名不为_id的文档   查询多个字段名则中间用空格隔开,不想要的字段前面加-  如： -_id 字段名不为_id  
  ````

  

* $gt 大于  $lt  小于

  ````javascript
  User.find({age: {$gt: 20, $lt: 50}}).then(res => {
      console.log(res)
  })
  // 查询User文档中age字段大于20 小于50的文档
  ````

  

* $in

  ````javascript
  User.find({hobbies: {$in: "敲代码"}}).then(res => {
      console.log(res)
  })
  // 查询User文档中hobbies字段
  ````

  

#### 查询文档排序

````javascript
Course.find().sort('age').then(res => {
    console.log(res)
})

//查询文档同时根据age字段升序排列
//    -age   降序排列

````



#### 限制个数查询

````javascript
Course.find().skip(2).limit(5).then(res => {
    console.log(res)
})

// 跳过两个数据并且限制只能查询5条数据
````



### 删除文档

* 删除单个文档

  ````javascript
  User.findOneAndDelete({id: "123456"}).then(res => {
      console.log(res)
  })
  // 删除字段id值为'123456'的文档,如果有多个也只会删除匹配的第一个
  ````

* 删除多个文档

  ````javascript
  User.deleteMany({}).then(res => {
      console.log(res)
  })
  
  //  删除User集合中的所有文档   返回{ n: 5, ok: 1 } 表示删除5个文档  删除成功
  ````

### 更新文档

* 更新单个文档

  ````javascript
  // User指的是集合名
  User.updateOne({name: "张三"}, {name: "李狗蛋"}).then(res => {
      console.log(res)
  })
  // 更新User集合中name字段’张三‘改为'李狗蛋'(只更新一个)
  ````

* 更新多个文档

  ````javascript
  User.updateMany({name: "张三"}， {name: "李狗蛋"}).then(res => {
      console.log(res)
  })
  
  // 更新User集合中name字段“张三”改为”李狗蛋“（更新所有的）
  ````



### mongoose验证

> 在创建集合规则时， 设置当前字段的验证规则，验证失败则插入失败

````javascript
require: true  //必传字段

require： [true, '插入失败时显示']
````

> 示例

````javascript
const postSchema = new mongoose.Schema({
    name: {                    // name为必传字段
        type: String,
        require: true
    }
})
````



* 针对字符串的验证

  * maxlength: xxx      传入字段的最大长度              maxlength: [xx, xxxxxxx]   xxxxxxx为验证失败时提示

  * minlength：xxx     传入字段的最小长度             minlength:[xx, xxxxxxxx]   xxxxxxx为验证失败时提示

  * trim: true/false       是否允许字符串前后有空格

    

* 针对Number型

  * max：xxxx          最大值
  * min： xxxx          最小值




* 默认值

  * default: xxxxx

    ````javascript
    publishDate: {
        type: Date,
        default: Date.now        //  publishDate字段类型为Date类型，不传值则默认为当前时间
    }
    ````

* enum

  * enum: xxxxx

    ````javascript
    category: {
        type: String,
        enum: ['html', 'css', 'java']
    }
    
    // category 只能为html 或 css  或 java
    ````

    

* 自定义验证

````javascript
validate: {
    validate: v => {
        return v && v.length > 4   //条件：长度大于4 
    },
    message: "xxxxxxxxx"           //失败时的错误信息
}

````

​	





### 集合关联

> * 使用id进行关联
>
> * 使用populate方法进行关联集合查询

````javascript
// User集合
const User = mongose.model("User", new mongoose.Schema({
    name: {
        type: String
    }
}))


// Post集合（关联User集合）
const Post = mongoose.model("Post", new mongoose.Schema({
    title: {
        type: String
    },
    author: {
        type: mongoose.Schema.Types.ObjectId,  //固定写法
        ref: "User"
    }
}))

//User集合中创建一个文档
User.create({
    name: "张三"
}).then(res => {
    console.log(res)
})

// Post集合中创建一个文档

Post.create({
    title: '123',
    author: 'xxxxxxxxxxx'     // xxxxxxx为User集合中具体的关联文档的_id字段
})


// 查询post集合

Post.find().populate('author').then(res => {
    console.log(res)
})



````





## 接口开发规范（Restful API）

### 明确需求

* 说明： RESTful 是目前最流行的一种互联网软件架构（思想）

* 作用： 声明/提供可接口设计原则和约束条件（一个规范）

* 相关：

  > get 查看  post 添加  put 修改  delete 删除

* 好处： 统一开发规范， 便于团队协作开发



### 标准的RESTful架构需要做到

````
订单模块
/order      get
/order      post
/order/编号  put
/order/编号  delete
````

* 项目所有模块有统一的标准
* 看URL就知道要操作的资源是什么（也就是哪个模块）
* 看Http Method就知道操作动作是什么，如post（添加） delete（删除）
* 看Http Status Code 就知道操作结果如何， 成功（200）内部错误（500）