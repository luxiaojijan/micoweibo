##《node.js开发指南》微博网站例子  
* express@4.12.3
* ejs@2.3.1
采用express@4.x,首先需要安装express的生成器express-generator。  

	npm install -g express-generator

新版的express已经放弃命令行模式,需要安装生成器。

	express -e test  

生成一个模板为ejs的测试案例。
##express应用的结构 

* views/放模板的方法，编写的ejs的模板文件都放在这里的。
* public/这里放一些前端的静态资源,css,webfont,js,图片文件都放到这里。
* routes/这里放路由文件，路由就是用来解析请求路径，调用相应路径逻辑。
* models/这个是自建的，用来放控制模的文件。  
* app.js 这个是网站的入口文件。

##知识点整理
###1. 路由控制权转移  
需求:匹配路径时需要先做一些事情，然后调用模板渲染出页面。比如为页面设置访问权限，已经登录的用户才能登出，没有登录的用户才能使用登录注册功能。
解决：express提供路由控制权转移的方法。回调函数第三个参数next,回调函数中使用next()

```
router.get('/login',checkNotLogin); //检查用户是否已经登录，没有登录切换下一条路由规则。
router.get('/login',function(req,res){
	res.render('login',{
		title: '用户登入',
	});
});
```
###2. 路由规则的设置

首先需要在入口文件`app.js`中用`app.use('/', routes);`这一句代码把路由引到routes/文件下。  
express@4.x路由设置这边有点变化，需要先加载express,然后使用express模块的Router（）方法。
设置代码如下：
```
var express = require('express');
var router = express.Router();
router.get('路径',回调函数);
router.post('路径',回调函数);
```

###3. 模板引擎ejs的使用
ejs这个模板引擎非常好用,只有三个函数。  
* <% javascript代码 %>直接执行javascript代码。
* <%= js变量 %> 转义js变量输出
* <% include ejs模板文件名 %> 引入ejs文件

###4. express的片段试图（partials)
在express@4.x中，很多中间组件都已不能从express模块中获取。  
![截图](http://luxiaojian.qiniudn.com/屏幕快照 2015-04-30 上午12.22.03.png)  
[详情地址](https://github.com/senchalabs/connect#middleware)
所以需要现在需要使用express-partials中间组件让express来支持片段试图。

###5. 数据库mongodb的安装和连接  
* 安装mongodb数据库。采用`brew install mongodb`来在mac上安装mongodb
* mongodb的数据存在/data/db/,需要建立相应路径，改权限为主机名。
* 用mongod来启动数据库，用mongo命令进入命令行模式。
* 数据库文件参数设置： settings文件，  
```
module.exports = {
	cookieSecret: 'micoblog',
	db: 'blog',
	host: 'localhost',
};
```
* 控制中连接文件设置： db.js
```
var settings = require('../settings');
var Db = require('mongodb').Db;
var Connection = require('mongodb').Connection;
var Server = require('mongodb').Server;

module.exports = new Db(settings.db, new Server(settings.host, 27017, {}), {safe: true});

```
###6. 关于cookie  
关于数据库设置cookie这一块不是很懂，但是因为这个出过错，所以这里强调下。settings.js中cookie文件设置要和app.js入口文中设置相同。  
![](http://luxiaojian.qiniudn.com/屏幕快照 2015-04-30 上午12.50.40.jpg)

###7. 关于包模板文件中变量为定义错误  
解决：请求路径定义有问题，把路由修改正确。

###参考文档  
* [从 Express 3.x 迁移到 4.x](http://segmentfault.com/a/1190000000603327)