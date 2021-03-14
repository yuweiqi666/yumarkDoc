# apiDoc接口文档开发工具

> nodejs中的一个模块 可以快速生成接口文档

## 安装步骤

1. 先下载模块，后期通过命令基于注释生成文档

   ````javascript
   npm install apidoc -g
   ````

2. 在项目根目录创建apidoc.json文件

   ````javascript
   {
       "name": "example",
       "version": "0.1.0",
       "description": "apiDoc basic example",
       "title": "Custom apiDoc browser title",
       "url": "https://api.github.com/v1"
   }
   ````

3. 写接口注释

   ````javascript
   /**
    * @api {get} /user/:id Request User information
    * @apiName GetUser
    * @apiGroup User
    *
    * @apiParam {Number} id Users unique ID.
    *
    * @apiSuccess {String} firstname Firstname of the User.
    * @apiSuccess {String} lastname  Lastname of the User.
    */
   ````

   

4. 生成接口文档

   ````javascript
   apidoc -i 接口注释目录 -o 接口文档存放目录 
   ````

   ````javascript
   apidoc -i .\routers -o ./doc
   ````

   