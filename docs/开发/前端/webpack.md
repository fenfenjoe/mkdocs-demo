javascript-webpack
	webpack
		webpack是干什么的
			webpack是一个“静态资源打包工具”，将项目中的每个模块组合成一个或多个的Bundle输出。
			webpack需要运行在nodejs之上
		什么是模块？为什么需要模块化？
			未有webpack的时候：模块一般是指css、js这些资源；
			最初html导入这些资源的方式：在html中通过style中内联css；通过script中内联javascript
			后来发现这些资源需要复用：
				将css、js写在外部文件，在html中通过link引入css文件；通过script中引入js文件
				将css、js拆分成多个小文件
			后来发现小文件太多，引用很容易出错：
				css之间、js之间可继承和互相引用（开始有模块的概念）
				自动化工具：自动合并、压缩
		JS之间互相依赖的发展过程
			无模块（JS之间不能互相依赖）
				缺点
					✦ 污染全局作用域，多人协作简直是灾难。
					✦ 手动维护script标签的顺序，多页应用更惨。
					✦ 大型项目资源难以管理，容易留下隐患。
			同步依赖方案：CommonJS
				缺点
					✦ 同步加载不适合浏览器，浏览器的请求都是异步加载。
					✦ 不能并行加载多个模块。
			异步依赖方案：AMD
				优点
					✦ 异步加载适合浏览器。
					✦ 可并行加载多个模块。
				缺点
					✦ 模块定义方式不优雅，不符合标准模块化
			静态化方案：ES6
				优点
					✦ 可静态分析，提前编译。
					✦ 面向未来的标准。
		webpack使用的模块化方案
			webpack兼容所有模块化方案！！！
			✦ 可以兼容多模块风格，无痛迁移老项目。
			✦ 一切皆模块，js/css/图片/字体都是模块。
			✦ 静态解析，按需打包，动态加载。
	概念
		入口（entry）
			定义需要打成Bundle的资源，可以有多个入口
		输出（output）
			定义Bundle的输出位置和文件名
		loader
			webpack只能识别js和json，为了将其他资源也可以打包，需要loader去转换成js
			loader支持链式结构，即loader1将转换结果传到loader2，loader2再传到loader3...
		插件(plugin)
		模式(mode)
	配置文件
		Webpack4
			webpack.base.conf.js （公有配置文件，所有环境均需要的属性写这里）
			webpack.prod.conf.js（production生产环境配置文件）
			webpack.dev.conf.js （development开发环境配置文件）
			如何选择使用哪个配置文件？
				在package.json的scripts中，通过--config选择
		Webpack5
			语法
	用例
		子主题 1
	项目结构
		./build
			build.js
				打包正式环境代码的脚本
			dev-client.js
				监听dev-server.js中webpack-hot-middleware发布的事件并作相应的处理
			dev-server.js
				检查npm与node的版本
					require('./check-versions')()
				引入相关插件和配置
					插件：
						var hotMiddleware = require('webpack-hot-middleware')
						var devMiddleware = require('webpack-dev-middleware')
						var proxyMiddleware = require('http-proxy-middleware')
						var opn = require('opn')
						var path = require('path')
						var express = require('express')
						var webpack = require('webpack')
					配置：var webpackConfig = require('./webpack.dev.conf')
				创建express服务器和webpack编译器
					var app = express()
					var compiler = webpack(webpackConfig)
				配置开发中间件（webpack-dev-middleware）和热重载中间件（webpack-hot-middleware）
				挂载代理服务和中间件
				配置静态资源
					var staticPath = path.posix.join(config.dev.assetsPublicPath, config.dev.assetsSubDirectory)
					app.use(staticPath, express.static('./static'))
				启动服务器监听特定端口（8080）
					var server = app.listen(port)
				自动打开浏览器并打开特定网址（localhost:8080）
			webpack.base.conf.js
				webpack基础配置
			webpack.dev.conf.js
				webpack开发环境配置
			webpack.prod.conf.js
				webpack生产环境配置
		./config
			dev.env.js
				开发环境配置
			index.js
				webpack配置入口
			prod.env.js
				生产环境配置
			test.env.js
				测试环境配置
		./src
			components
			store
			App.vue
			main.js
		./static
		package.json
		index.html