javascript-nodejs
	简介
		一句话描述
			前后端分离的基础
			前端JS库管理工具，类似于Maven
		概念
			V8引擎
				javascript的编译、运行环境
				同时是Chorme的Javascript引擎
			回调函数
			模块化
				将函数分组（分成一个一个文件），每一个文件便是一个“模块”
				模块化提高了函数的可维护性、重用性
				一个JS文件对应一个模块
				Node.js遵从CommonJS模块化规范
					require、exports、module
				Node.js 6.0（V8引擎的5.0） 以后的版本支持ES6的模块化规范
					import、export
			NPM
				npm命令
					npm -v
						查看npm版本
					npm init
						生成package.json文件
					npm install
						安装package.json里的所有插件
					npm install  jquery
						安装jquery包最新版本
					npm install jquery@3.4.1
						安装jquery包的3.4.1版本
					npm install jquery --save
						安装jquery包最新版本，并将依赖记录到package.json中
					npm install jquery --save-dev
						安装jquery包最新版本，并将依赖记录到package.json中
					npm uninstall  jquery
						卸载jquery包
					npm ls jquery
						查看jquery包版本
					npm config set registry https://registry.npm.taobao.org
						更改 npm 的下载镜像为淘宝镜像
					npm start
						运行node.js项目
					npm run [scriptName]
						运行脚本（Shell 脚本，脚本定义在package.json的scripts中）
						而package.json中的脚本又存放在node_modules/.bin/中（包括linux和windows的）
			支持的ECMAScript版本
				https://node.green/
				Node.js 6.0（V8引擎的5.0） 以后的版本基本支持所有ES6的特性
	环境配置
		安装
			https://nodejs.org/en/download/
			配置环境变量
				PATH里加上“C:\Program Files\nodejs\;”
		模块安装
			npm install <module name>
				-g 全局安装
			npm下载模块速度慢，可使用淘宝的cnpm
	CLI（命令行界面）
		交互式解释器（REPL）
			进入方法：node
			进入后可直接在控制台里输入JS代码，进行调试
	Node.js包
		结构
			./nodeJSExample
				package.json
					scripts
						自定义可执行脚本
							"scripts": {
    "dev": "node dev.js",    --执行 npm run dev 就等于 执行了node dev.js
    "pub": "node build.js"  --执行 npm run pub 就等于 执行了node build.js
  }
					dependencies 
						运行时依赖
					devDependencies 
						开发时依赖
				./node_modules
					依赖包存放目录
		流程
			新建一个Node.js包
				1.新建文件夹，文件夹名即为包名
				2.在命令行输入 npm init，生成package.json文件
				3.在package.json配置该node.js包需要依赖的其他包
				4.在命令行输入 npm install，安装依赖包到./node_modules中
					npm install --dependencies   //只安装运行依赖
					npm install --devDependencies    //只安装开发依赖
			写一个Node.js模块（CommonJS）
				新建一个模块（示例：hello.js）。内容如下：
				hello.js
					'use strict';
					为模块定义一个函数
						function helloWorld(){  console.log('hello world!');   } ;
					暴露函数
						暴露一个
							module.exports = helloWorld;
						暴露多个
							module.exports ={ helloWorld :helloWorld ,
 helloChina : helloChina }
					在其他模块加载hello模块
						var module = require('./hello'); //参数为模块所在路径
						var module = require('hello'); //参数为模块名，引擎会按照内置模块、全局模块的顺序检索
			写一个Node.js模块（ES6）
				新建一个模块（示例：hello.js）。内容如下：
				hello.js
					暴露变量、函数
						定义多个变量
							var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;
						暴露变量
							export { firstName, lastName, year };
						暴露变量并重命名
							export { firstName as fn };
						为模块定义一个函数
							function helloWorld(){  console.log('hello world!');   } ;
						暴露函数
							export { helloWorld };
						暴露函数并重命名为v1
							export { helloWorld as v1 };
						指定模块的默认输出（一个模块里只能调用一次）
							export default 变量or方法or类
								export default function() { ... }
								var a = 1;
export default a;
								export default class { ... }
						export 与 export default可同时使用
					加载其他模块
						import { firstName, lastName, year } from './profile.js';
							加载同级目录下的profile.js模块中的三个变量（相对路径）
						import { firstName, lastName, year } from '/profile.js';
							加载源目录下的profile.js模块中的三个变量（绝对路径）
						import { firstName, lastName, year } from 'profile';
							加载profile模块中的三个变量（根据配置文件查找）
						import * as circle from './circle';
							将属性整体加载到一个对象上
						import myprofile from './profile.js';
							export default 的情况下，这样加载
							等价于 Import {default as myprofile} from './profile.js';
						import myprofile,{ firstName, lastName, year } from './profile.js';
							同时输入default与其他变量
							等价于 Import {default as myprofile,firstName, lastName, year } from './profile.js';
					暴露其他模块的变量、函数（import&export）
						export { foo as myFoo } from 'my_module';
					继承其他模块
						export * from 'circle';
					动态加载模块（即在运行时加载）
						import('my_module')
							返回一个 Promise 对象
			运行一个Node.js模块
				node hello.js
	基础模块
		http
		events
		global
			唯一的全局对象
		process
			代表node.js进程
			process.env
			process.stdout
			process.exit(1)
		fs
			文件读写
	API
		http://nodejs.cn/api/