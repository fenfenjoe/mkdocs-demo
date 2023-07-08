# ruoyi-vue

### 参考

ruoyi-vue官方文档：[http://doc.ruoyi.vip/ruoyi-vue/](http://doc.ruoyi.vip/ruoyi-vue/)  
ELEMENT-UI文档：[https://element.eleme.cn/#/zh-CN](https://element.eleme.cn/#/zh-CN)  
VUE官方文档：[https://cn.vuejs.org/](https://cn.vuejs.org/)   
其他Ruoyi项目：[http://doc.ruoyi.vip/ruoyi/document/xmkz.html](http://doc.ruoyi.vip/ruoyi/document/xmkz.html)  

### 实战

#### 语法、框架基础知识


[如何写一个标准的js文件](https://gitee.com/fun_zil/note/blob/master/%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF/%E6%A8%A1%E5%9D%97%E5%8C%96.md#es6%E6%A8%A1%E5%9D%97%E5%8C%96)


如何写一个单页面：略  




#### 项目安装

[Windows系统安装并部署nginx](https://blog.csdn.net/weixin_44251179/article/details/129700793)  



#### 了解项目结构

- bin               // 执行脚本
- build             // 构建相关
- node_modules
- public            // 公共文件
- src               // 源代码
    - api           // 页面调用方法写在这
    - assets        // 主题 字体等静态资源（对应maven项目里的resource）
        - style     // 公用的css文件
            - index.scss 
            - element-ui.scss
    - components    // 全局公用组件
    - directive     // 全局指令
    - layout        // 布局
    - plugins       // 通用方法
    - router        // 路由
    - store         // 全局 store管理
    - utils         // 全局公用方法
    - views         // 页面
    - App.vue       // 入口页面
    - main.js       // 入口 加载组件 初始化等（对应maven项目里的SpringBootApplication.java）
    - permission.js // 权限管理
    - setting.js    // 系统配置
- babel.config.js
- package.json      // 项目信息、依赖管理（对应maven项目里的pom.xml）
- vue.config.js     // 项目配置（对应maven项目里的application.properties）

> 详细见：[http://doc.ruoyi.vip/ruoyi-vue/document/xmjs.html#%E5%89%8D%E7%AB%AF%E7%BB%93%E6%9E%84](http://doc.ruoyi.vip/ruoyi-vue/document/xmjs.html#%E5%89%8D%E7%AB%AF%E7%BB%93%E6%9E%84)

#### 创建第一个菜单

启动ruoyi后，在【菜单管理】中添加。  

#### 画界面

组件用法可参考：[https://element.eleme.cn/#/zh-CN](https://element.eleme.cn/#/zh-CN)

##### 样式设置

#### 创建一个公共组件

#### 调用后端函数

#### 页面跳转

#### 打包

##### 打包成Web项目

参考：[若依配置教程（九）若依前后端分离版部署到服务器Nginx（Windows版）](https://blog.csdn.net/qq_46073825/article/details/129174661)


##### 打包成Android APP 

##### 打包成小程序

### FAQ 

**假设有test.js，里面有以下写法。请问这种写法是什么含义？**  

```javascript
export default{
   test(){
       //...
   },
   test2(){
       //...
   }
}
```

答：这是ES6模块化的规范。在ES6中，每一个js文件都被认为是一个“模块”。模块与模块之间可以互相引用。  
“export default”里定义的函数，就是提供给其他模块调用的函数。  

假设现在有test2.js，想调用test.js的test()函数，则可以这样写：

```javascript
import testjs from '@/test'
const test_result = testjs.test()
```


**import的用法？**  

```javascript
import axios from 'axios' //导入第三方组件axios
import { getToken } from '@/utils/auth' //导入本地公共方法（src/utils/auth.js 中的getToken()函数）
import Hamburger from '@/components/Hamburger' //导入本地公共vue组件（src/components/Hamburger/index.vue）


```


**怎样实现“进入页面后立即执行某个函数”？**  
用vue的“生命周期钩子函数”。  

【什么是生命周期钩子函数】  
在一个组件的生命周期中，经历创建->渲染->销毁等过程，在这些过程完成后，都会执行对应的钩子函数。  

（1）beforeCreate（）       实例创建前触发  
（2）created（）            实例创建完成，  
（3）beforeMount（）        模板渲染前，可以访问数据，模板编译完成，虚拟DOM已经存在  
（4）mounted（）            模板渲染完成，可以拿到DOM节点和数据  
（5）beforeUpdate（）       更新前  
（6）updated（）            更新完成  
（7）activated（）          激活前  
（8）deactivated（）        激活后  
（9）beforeDestroy（）　    销毁前  
（10）destroyed（）　　     销毁后  

【如何添加生命周期钩子函数】
```javascript

export default{
  methods:{
      myfunction(){
          //...
      }
  },
  created(){  //钩子函数1
    this.myfunction();
  }
}
```

**关于vue中的this**  

全局函数中的this:指向windows对象  

对象函数中的this:指向对象本身  

构造函数中的this:指向实例对象  

Promise函数中的this:需要用 .then(()=>{}) 写法，不能用.then(function(){})


**如何发出同步请求 & 异步请求**  
异步请求：  
```javascript
import axios from 'axios'//引入axios

axios({
      method:'get',
      url:'http://localhost:8080/test',
      data:{
        name:'joe'
      }
    });
```

同步请求：
```javascript

```



**从哪里开始阅读前端代码？**  

/src/views/login.vue  
对应登录界面



————————————————————————————————————
/src/views/system/dict/index.vue  
对应“左侧菜单栏-字典管理”界面  

* el-form:新增一个表单
* el-form-item:新增一个表单项
  * el-input:文本框
  * el-select:单选框
  * el-date-picker:时间选择框
  * el-button:按钮
* el-table:新增一个表格
————————————————————————————————————