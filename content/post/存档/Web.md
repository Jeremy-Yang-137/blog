---
date: '2025-12-01T00:00:10+08:00'
draft: true
title: 'Web'
---

# 个人博客（html+css+JS）

大一上学期的作品，采用原始的html+css+JavaScript编写，具有主页、文章列表和文章内容三种界面。采用了手绘的风格，提供5种主题可供切换以及中/英文切换。采用比较原始的html方式管理文章。使用VS Code的Live Server插件直接在浏览器中打开运行。

# 个人博客（Django）

大一下学期为了参加星火杯的作品，前端基于原来的个人博客，用Django框架作为后端。采用model+view的方式（model用于与数据库对接，自定义存储的数据结构；view是路由响应不同的url请求调用的处理函数）。支持文章按学期筛选/多账号切换/文章以markdown及解析。html以模板（template）的形式存储，中间有占位符，在渲染的时候填充；css与JavaScript和图片一样以静态的形式存储。

Django：有一个主app，可以创建多个app

重要文件：

- manage.py：一般不编辑，管理项目时会在命令行调用
- admim.py：可以在前端可视化操作数据
- model.py：定义存储的数据结构
- urls.py：将不同的url请求分流到views函数或别的app的urls.py
- views.py：定义视图（处理函数），可以渲染html模板返回

还加入了“画廊”功能：分开绘制背景、照片和相框，通过window.requestAnimationFrame(draw)来实现向左滚动效果。可以从本地上传并滚动展示一个文件夹内的所有照片。

# Java Spring+Vue前后端结合

跟着B站上一个教程做的，员工管理系统。

前端：用vite+vue搭建的前端应用，通过将主应用的App.vue挂载到<div id=”app”>上。使用element-plus作为UI库。App.vue只有一个RouterView，实际的路由写在router/index.js里。使用路由守卫router.beforeEach实现根据不同路由修改标签页名称。

后端：是一个Java Spring Boot应用程序，新建了7个类和1个接口：

- Employee（员工个人信息）
- EmployeeControl（操作员工，将涵盖post、put、delete、get的前端请求映射到对应的处理函数。函数内通过一个- EmployeeService的实例进行各种操作）
- EmployeeService（EmployeeControl中调用这里对应的处理函数，函数内通过一个employeeMapper的实例进行各种操作）
- EmployeeMapper（接口）（EmployeeService中调用这里对应的处理函数，resources/mapper/EmployeeMapper.xml中定义了与这里的函数对应的SQL语句）
- Result（描述请求结果）
- WebController（测试用的，和EmployeeControl一样使用@RestController注解，接受规定的路由直接返回内容）
- CustomException（extends RuntimeException，自定义的错误类型，含有code和msg字段）
- GlobalExceptionHandler（捕获Exception和CustomException，返回自定义的Result类实例，通过@ResponseBody注解返回json）

# MediaWiki

多人协作的百科应用，Wikipedia就是基于它构建的。需要Apache+PHP+mySQL（或其它支持的数据库）

安装步骤：

- 安装xampp（集成了Apache、PHP和mySQL）
- Github下载MediaWiki解压到xampp/htdocs里
- 用composer安装需要的php扩展到wiki文件夹里
- Github下载主题vector，解压放到wiki/skins里
- 访问http://localhost/wiki/mw-config/ 开始安装
- 安装完后，将自动下载的LocalSettings.php放到wiki文件夹内就可以访问主页（http://localhost/wiki/index.php/首页）

后续优化：

- 可以安装wikieditor插件，不过要在php.ini里启用几个功能
- 可以用自己的logo
- 可以开启APCu对象缓存

并不适合拿来写个人博客。多用户协作、用户权限等级、历史记录等功能都用不上，而且加载速度比较慢。后面转为用wordpress（后面又转为hexo--本网站）。

