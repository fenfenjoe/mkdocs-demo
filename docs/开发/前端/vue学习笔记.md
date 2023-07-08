# vue学习笔记

## FAQ

### this是什么？vue是如何将变量挂载到this上的？


### watch()和computed()的区别？
https://blog.csdn.net/weixin_44063225/article/details/125635350

## 开发环境搭建


## 参考资料 




#### 教程&技术栈

javascript-VUE

#####【VUE项目创建】vue-cli
【简介】  
VUE提供的开发工具
【安装】  
npm install -g @vue/cli (全局安装到node.js)
安装后便可使用vue的指令
【创建项目】  
方法1：vue create hello-world（vue-cli3）
方法2：vue init webpack hello-world（vue-cli2）
【运行项目】  
cd hello-world
npm install
npm run dev
访问Localhost:8080，弹出vue界面
【界面化管理项目】  


##### 【模块化】
	CommonJS规范
	ES6规范
	webpack


##### 【项目管理、包管理】node.js
node.js项目 与 maven项目 相似，可以管理JS项目的生命周期（构建、打包）、项目管理插件依赖



##### 【路由】vue-router  

初始化路由
```
new Router({
// mode: 'history', //后端支持可开
scrollBehavior: () => ({y: 0}),
routes: [
{path: '/login', component: () => import('@/views/login/index'), hidden: true},
{path: '/reg', component: () => import('@/views/login/reg'), hidden: true}]
})
```
routers中的router对象解析
	path：监听的路径
	component：用哪个组件进行渲染
	hidden
	redirect：重定向到哪个路径
	children：子页面

一个router对象对应一个页面（假设有登录页、主页、注册页，则需要3个router对象）


跳转原理
根据url匹配router对象
匹配到后，用router对象对应的component组件对入口组件（App.vue）的<router-view>进行渲染
假如router对象还有子对象，则使用子对象的component组件（.vue）对router对象的component组件中的<router-view>进行渲染


在组件中添加跳转的超链接
<router-link to="/about">Go to About</router-link>


调用router
```
this.$router
```

在js中通过router重定向
```
this.$router.push('/dashboard')
```

路由守卫
全局前置钩子（每次路由改变都执行一次）
```
	router.beforeEach((to, from, next) => { ... }))
		// to: Route: 即将要进入的目标 路由对象
		// from: Route: 当前导航正要离开的路由
		// next: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。
```

##### 【数据存储及管理】vuex
	
【模块】
state（存放全局变量）
mutations（存放修改全局变量的同步函数）
actions（存放异步函数）
modules
getters（Get方法）
【用例】
1. 访问State中数据
```
方法1：
this.$store.state.变量名
方法2：
import 'mapState' from 'vuex'
computed(){
...mapState([state里面的全局变量...])
}
```
2. 修改State中的数据
```
方法1：
this.$store.state.变量名='test'（不推荐用这种方式）
方法2：
在mutatons中定义函数修改 add(store , myvalue) {  state.变量名=myvalue ;}
this.$store.commit('add' , 'test')
```
3. 访问mutations里面的函数
```
方法1：this.$store.commit('add' , 'test') //访问add函数，并将'test'作为传参
方法2：
import 'mapMutations' from 'vuex'
methods(){
...mapMutations([mutations里面的函数名...])
}
this.add('test')
```
4. 访问actions中定义的函数
```
方法1：
actions {
	add(context){
		setTimeout( () => {
			context.commit('add') //调用mutations中的add函数
		},1000)
	}
}
this.$store.dispatch('add')
方法2：
import 'mapActions' from 'vuex'
methods(){
...mapActions([actions里面的函数名...])
}
this.add('test')
```


#####【HTTP请求】axios
	
#####【UI组件库】ElementUI




#### vue项目结构

```
/src
	App.vue				定义入口组件（即类App），该组件负责渲染index.html中id=app的DIV
	main.js				相当于main()方法，是整个项目的JS入口
	/compoments
		***.vue
	/assets				一般放图片(.jpg .png...)
	/router
		index.js		根据用户访问的URL，用不同的组件去渲染<router-view/>
	/store				vuex，专门存放全局变量
	/styles				css文件
	/build				放构建脚本
	/config
		index.js		定义基础配置信息：ip、端口等
	/static				放静态资源
	package.json		等于maven的pom.xml，定义了项目的所有依赖
	index.html			首页，项目唯一的入口，会有一个<div id="app"></div>

```
	
#### vue语法
	Vue组件
		普通Vue组件(.vue)

##### Template（视图与模板部分）

1. 数据与DOM元素绑定：{{  }} 或者 v-html属性

```
<div v-html="message"></div>
将message的值与div的value绑定

<p>{{ message }}</p>
	将message的值与p的value绑定
```

2. 数据与value属性绑定：v-model

```
<input type="checkbox" v-model="use" id="r1">
	将use的值与input的“value属性”绑定，一般与input、textarea绑定
```

3. 数据与其他属性绑定:   :参数 或者 v-bind:参数
```
<a v-bind:href="url">菜鸟教程</a>
	将url的值与a标签的href属性值绑定
<a :href="url"></a>
		@事件 / v-on:事件
```

4. 为元素添加监听：v-on:click  
```
<a v-on:click="doSomething">
	往a标签中添加click事件的一个监听（dosomething）
<a @click="doSomething"></a>
v-on:submit.prevent
	<form v-on:submit.stop.prevent="onSubmit"></form>
		stop、prevent为事件修饰符，可以串联使用
stop:阻止单击事件冒泡
prevent:提交事件不再重载页面
once:事件只能点一次
```
5. :rules

6. 条件判断：v-if
```
<p v-if="seen">现在你看到我了</p>
	如果seen为true，则显示p标签的内容
```

7. 循环判断：v-for
```
<todo-item v-for="item in sites" v-bind:todo="item"></todo-item>
	模板（Template）
		<template v-if="ok"><div>如果ok则显示</div></template>
	组件
		<router-link to="/foo">Go to Foo</router-link>
		会被转换为超链接的一个组件，to填写url
```

8. 子组件-获取父组件传入的value：</slot>
```
<!-- 父组件。传入的value为Something bad happened.-->
<template>
	<AlertBox>
	Something bad happened.
	</AlertBox>
</template>

<!--子组件。<slot/> 会转换成父组件传入的value，即那句Something bad happened. -->
<template>
  <div class="alert-box">
    <strong>This is an Error for Demo Purposes</strong>
    <slot />
  </div>
</template>

```

9. 动态切换组件
```
<!-- 当点击按钮后，会将currentTab的值改变；currentTab的值改变，又会使 component切换为不同的组件 -->
<template>
	<button
       v-for="tab in tabs"
       :key="tab"
       :class="['tab-button', { active: currentTab === tab }]"
       @click="currentTab = tab"
     >
      {{ tab }}
    </button>
	<component :is="currentTab"></component>
</template>

<script>
	import Home from './Home.vue'
	import Posts from './Posts.vue'
	import Archive from './Archive.vue' 
	export default {
		components: {
			Home,
			Posts,
			Archive
		},
		data() {
			return {
			currentTab: 'Home',
			tabs: ['Home', 'Posts', 'Archive']
			}
		}
	}
</script>
```


		<router-view></router-view>
路由匹配到的组件会显示到这里
		<MyComponent></MyComponent>
调用一个叫MyComponent的自定义组件（在Script中定义）
	值传递
		属性传值


##### Script（脚本部分，javascript）
【总览】  
```
<script>

import ButtonA from './ButtonA.vue' //引入外部组件

export default {
	//定义本地变量
	data(){
		return{
			"attribute1":"hello world!",
			"attribute2":"John",
		}
	},
	//定义本地方法
	methods:{
		methodA(){

		},
		methodB(){

		}
	},
	//钩子函数
	created(){

	},
	//数据变化监听器(函数名要与本地变量名一样)
	//变量值发生改变时，函数便会执行
	watch(){
		"attribute1"(newVal,oldVal){

		}
	},
	computed(){

	},
	//外部组件
	components:{
		ButtonA
	},
	//子组件-接收来自父组件的入参
	props:[
		//传入参数的属性（String Number Boolean Array Object Date Function Symbol）
		propA:Number,
		//传入参数的可选属性
		propB:[Number,String],
		//必传
		propC:{
			type:String,
			required:true
		},
		//默认值
		propD:{
			type:String,
			default:"helloworld!"
		},
		//校验器，若不通过会在控制台提示开发者
		propE:{
			 validator(value) {
				// The value must match one of these strings
				return ['success', 'warning', 'danger'].includes(value)
			}
		}
	],
	//子组件-向父组件抛出的事件（随便命名）
	//子组件发出事件（不带参数）： <button @click="$emit('increaseBy')"> Increase by 1</button>
	//子组件发出事件（带参数）：<button @click="$emit('increaseBy', 1)"> Increase by 1</button>
	//父组件则通过@来监听事件
	//方法1：<MyComponent @searchData="callback" />  也可以写成 <MyComponent @search-data="callback" />
	emits:{
		searchData:(){

		},
	}



}

</script>

```

【生命周期钩子函数】
beforeCreate()
	组件被加载前调用
created()
	Vue 实例已经初始化后调用（属性、观察者、事件、数据属性均可访问）
	但未与DOM绑定，无法操作DOM
beforeMount()
	DOM 上挂载实例之前的那一刻
mounted()
beforeUpdate()
updated()
beforeDestroy()
destroyed()
		
vue如何将组件（.vue）渲染到html上面的
	


		main.js（项目入口）中初始化Vue
var vm = new Vue({参数1:{} , 参数2:{} })
	参数
		el
DOM元素的ID
	el: '#app'
		data
变量声明
	data: {
    name: 'Google',
    url: 'http://www.google.com'
  }
在视图中调用
	<div id ="app">{{ name }}</div>
		methods
定义函数
	methods: {
            details: function() {
                return  this.site + " - 学的不仅是技术，更是梦想！";
            }
        }
在视图中调用
	<div id ="app">{{ details() }}</div>
		computed
		watch
为变量绑定监听函数
	watch : {
        kilometers:function(val) {
            this.kilometers = val;
            this.meters = this.kilometers * 1000
        },
        meters : function (val) {
            this.kilometers = val/ 1000;
            this.meters = val;
        }
    }
		compoments
定义一些子组件
		router
		Vue调用
为变量绑定监听函数
	vm.$watch('属性名',function(){ // 监听函数})
注册一个组件
	方法一：Vue.component(tagName, options)
		options
template
	需要子组件转换成的HTML模板
		template: '<span>{{ message }}</span>
props
	从父组件传进来的入参，供template中绑定
		情况一：传入值
props: ['message']
<child message="hello!"></child>
		情况二：传入变量
props: ['message']
<child v-bind:message="parentMsg"></child>
	为入参添加校验函数
		propF: {
      validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
	为入参添加默认值
		propD: {
      type: Number,
      default: 100
    }
	入参设置为必填
		propC: {
      type: String,
      required: true
    }
	方法二：const MyComponent = { template : ... , props : ... }


##### Style（样式部分）

