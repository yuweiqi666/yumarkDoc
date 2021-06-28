# vue项目部署打包

## 项目发布

### 部署到Github Pages

* 项目完成后将其发布到GitHub Pages

  1. 使用 `npm run build` 对项目进行打包 打包完成后形成一个dist文件夹

     ![dist文件夹](.\imgs\dist文件夹.png)

  2. 将项目推到github的远程仓库

     > 注意： vue-cli默认设置了git上传忽略dist目录

  3. 使用`git subtree push --prefix dist origin gh-pages`将`dist`目录推送到远程的`gh-pages`（名称不能改变）分支，若远程没有`gh-pagse`分支则会新建`gh-pagse`分支然后再推送

     ![dist目录推到远程分支](.\imgs\dist目录推到远程分支.png)

  4. 登录github 可以发现仓库中多了一个gh-pages分支

     ![github gh-pages分支](.\imgs\github gh-pages分支.png)

  5. 点击`setting -> pages `可以看到项目已经部署成功

     ![部署成功](.\imgs\部署成功.png)

* 部署完成后自定义域名

  1. 获取当前Github Page 的 ip 地址

     * 打开`cmd` 使用 `ping xxx.github.io`获取

       ![获取ip地址](.\imgs\获取ip地址.png)

     

  2. 首先先去阿里云购买域名

  3. 域名实名认证

  4. 进行域名解析

     * 找到域名列表点击解析

       ![域名解析](.\imgs\域名解析.png)

     * 进入解析设置后点击添加记录

       ![添加记录](.\imgs\添加记录.png)

     * 填写相关配置

       ![添加记录确定](.\imgs\添加记录确定.png)

     * 点击确定 等待10分钟左右 看到如图显示表示解析成功

       ![解析完成](.\imgs\解析完成.png)

  5. 进行Github Pages的自定义域名

     * ![自定义域名](.\imgs\自定义域名.png)

     

* 部署过程中遇到的坑

  1. 项目部署到Github Page后打开网址页面空白

     * 解决方案

       `在项目的vue.config.json文件中添加publicPath`

       ````
       module.exports = {
           publicPath: './'
       }
       ````

       `把路由里的history模式注释掉，因为是部署到 github pages，使用这个模式去掉了 #，服务器没有配置，页面就会请求不到啦`

       ````
       export default new Router({
            //mode: 'history'
       })
       ````

       `重新打包将dist目录推到gh-pages分支 再次打开网址`

  2. 项目部署到Github Page后打开页面接口报错 没有数据

     ![项目接口报错](.\imgs\项目接口报错.png)

     > 原因：github pages 地址为https  项目的接口地址为http  所以访问就会报错

     * 本项目的解决方案

       `使用github pages自定义域名 设置一个http的地址（具体参考上面的自定义域名设置）`

## 项目打包

> Dcloud方案 将写好的H5移动端项目打包成app

* 下载	Hbuilder X （注册登录）

* 点击新建项目

  ![Hbuilder新建项目](.\imgs\Hbuilder新建项目.png)

* 选择项目类型为5+ app

  ![选择项目类型](.\imgs\选择项目类型.png)

* 建完项目后目录中打开`unpackage-> manifest.json`中的`源码视图`

  ![manijson](.\imgs\manijson.png)

* 修改`manifest.json`中的`launch_path`修改为自己H5项目上线后的项目地址

  ![修改打包的项目线上地址](.\imgs\修改打包的项目线上地址.png)

* 将配置的源码拷贝到H5项目(新建manifest.json文件)的`public`目录中

  ![H5项目中添加配置](.\imgs\H5项目中添加配置.png)

* 在hubilder项目中导入H5中的public目录

  

![导入项目](.\imgs\导入项目.png)

* 导入成功后进行打包（选择原生app-云打包）

  ![项目打包](.\imgs\项目打包.png)

* 等待出现如图表示打包成功

  ![打包成功](.\imgs\打包成功.png)

