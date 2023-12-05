---
title: 'Vue脚手架'
date: '2023-04-26 01:15:21'
tags: ['Vue']
language: 'zh-cn'
description: ''
---
[TOC]



## 一. 初始化脚手架

文档 [Vue CLI](https://cli.vuejs.org/zh/)

### 1.具体步骤：

1. **全局安装  `npm install -g @vue/cli`**

2. **切换到创建项目的目录，使用命令创建项目vue create xxx**

3. **选择使用vue的版本**

    - **启动项目npm run serve**
    - **打包项目npm run build**
    - **暂停项目 Ctrl+C**

    > Vue脚手架隐藏了所有webpack相关的配置，若想查看具体的webpack配置，请执行vue inspect > output.js

### 2.脚手架文件结构

```markdown
.文件目录
├── node_modules 
├── public
│   ├── favicon.ico: 页签图标
│   └── index.html: 主页面
├── src
│   ├── assets: 存放静态资源
│   │   └── logo.png
│   │── component: 存放组件
│   │   └── HelloWorld.vue
│   │── App.vue: 汇总所有组件
│   └── main.js: 入口文件
├── .gitignore: git版本管制忽略的配置
├── babel.config.js: babel的配置文件
├── package.json: 应用包配置文件 
├── README.md: 应用描述文件
└── package-lock.json: 包版本控制文件
```

==App.vue==

- **用来管里components文件夹下的所有组件**

 ```vue
 <template>
 	<div>
    	 <img src="./assets/logo.png" alt="">
     <School></School>
     <Student></Student>
     </div>
 </template>
 
 <script>
     // 引入组件
     import School from './components/School.vue'
     import Student from './components/Student.vue'
 
     export default {
         name:'App',
         components:{ School, Student }
     }
 </script>
 ```



==main.js==

- main.js ---- `整个项目的入口文件`

- ./mount(‘#App’) === el:’App‘

```js
// 该文件是整个项目的入口文件

import Vue from 'vue'				// 引入Vue
import App from './App.vue'	// 引入App组件，它是所有组件的父组件

Vue.config.productionTip = false

new Vue({
	el:'#app',
  render: h => h(App),			// render函数完成了这个功能：将App组件放入容器中
})// .$mount('#app')
```

### 3.render函数

	**功能：  render函数功能：将App组件放入容器中**

```javascript
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  el:'#app',
  // render函数功能：将App组件放入容器中
  // 简写形式
  render: h => h(App),
  // 完整形式
  // render(createElement){
  //   return createElement(App)
  // }
})
```

### 4. 关于不同版本的函数

> **关于不同版本的函数**
> **`1. vue.js 与 vue.runtime.xxx.js的区别 `** 
> 		vue.js 是完整版的Vue，包含：核心功能+模板解析器
> 		vue.runtime.xxx.js 是运行版的Vue，只包含核心功能，没有模板解析器
> 		esm 就是 ES6 module
>
> 2. 因为 vue.runtime.xxx.js 没有模板解析器，所以不能使用template配置项，需要使用render函数接收到的  createElement函数去指定具体内容

### 5. vue.config.js 配置文件

> 1. ==vue inspect > output.js==可以查看到Vue脚手架的默认配置
>
> 2. 使用vue.config.js可以对脚手架进行个性化定制，和package.json同级目录，
>
>     详见 [配置参考 | Vue CLI](https://cli.vuejs.org/zh/config/#vue-config-js)

```javascript
module.exports = {
  pages: {
    index: {
      entry: 'src/index/main.js' // 入口
    }
  },
  lineOnSave: false	// 关闭语法检查
}
```

## 二. Vue-CLI： ref/props/mixin/plugin/scoped

### 1. ref属性

##### （1）ref功能：

> **ref被用来给元素或子组件注册引用信息（id的替代者）**

##### （2）ref应用及使用方式：

- 应用在`html标签`上获取的是`真实DOM`元素，应用在`组件标签`上获取的是`组件实例化对象vc`

- 使用方式 

    - 打标识：==<h1 ref="xxx"></h1>==或==<School ref="xxx"></School>==

    - 获取：==this.$refs.xxx==



**App.Vue文件**

```vue
<template>
	<div>
    	<h1 v-text="msg" ref="title"></h1>
    	<School ref="school"></School>
    	<button @click="showName">点击我输出上方属性的DOM元素</button>
    </div>
</template>

<script>
    import School from './components_02/My-School.vue'
    export default {
        name: 'App',
        components: {
            School  
        },
        data() {
            return {
                msg: '你好，Vue'
            }
        },
        methods: {
            showName(){
                //this的指向的VC组件的实例化对象
                
               console.log(this.$refs.title);  //输出h1标签的dom元素
              console.log(this.$refs.school);  //输出school标签的VueComponent实例化对象
            }
        }
    }
</script>

```

 **this.$refs结果**

![image-20230416190226757](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304170220486.png)    	**（ref输出HTML元素)this.$refs.title输出结果**

![image-20230416190314261](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304170220378.png)

**（ref输出组件标签）this.$refs.school**

![image-20230416190415433](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304170220577.png)

### 2. 配置项props

#####   (1)功能

##### 	props让组件接收外部传过来的数据： 

- 传递数据<Demo name="xxx" `:`age="18"/> 这里age前加**`:`** ，通过v-bind使得里面的18是数字（v-bind绑定的元素引号值里面的是js表达式）

- 接收数据

    -  第一种方式（只接收）props:['name', 'age'] 

    -  第二种方式（限制类型）props:{name:String, age:Number}

    -  第三种方式（限制类型、限制必要性、指定默认值） 

		​	

		```js
		props: {
          name: {
               type: String,	 // 类型
               required: true,// 必要性
             //default: 'cess'// 默认值
         }
		}
		```

##### (2)备注：

> **`props是只读的`**，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中，然后去修改data中的数据

### 3. minin3 混入

##### （1）功能： 

	**可以把多个组件共用的配置提取成一个混入对象**

##### （2）使用方式：

1. **定义混入**
```js
    const mixin = {
        data() {....},
        methods: {....}
        ....
    }
```
2. **使用混入**

	**`全局混入：`** Vue。mixin(xxx)

	**`局部混入：`** minins: [‘xxx’]



**minin.js文件**

```js
// 定义一个变量hehe存储共有的data/methods ,并将其暴露
export const hehe = {
    methods: {
        showName() {
            alert(this.Name)
        }
    }
}
```

**Studetn.vue文件**

```vue
<script>
    // 1. 先导入minin.js文件
    import {hehe} from '../minin'
    export default {
        name: "Student-Mess",
        data() {
            return {
                Name: 'yanle',
                Age: 22
            };
        },
	// 2. minins:[xxx]
        mixins: [hehe] 
    };
</script>
```

### 4. Vue插件

##### （1）功能： 

​	用于增强Vue

##### （2）本质：

​	包含一个install方法的一个对象，install的第一个参数就是Vue，第二个以后的参数是使用者传递的数据；

##### （3）定义插件：

```js
对象名.install = function (Vue,options) {	
	// 1. 添加全局过滤器
    Vue.filter(......)
    // 2. 添加全局指令
    Vue.directive(......)
   	// 3. 配置全局混入
    Vue.mixin(......)
    // 4. 添加实例方法
    Vue.prototype.$myMethod = function () {......}        
}
```

##### (4)使用插件：

```vue
Vue.use(对象名)
```

### 5. scoped样式：

##### （1）作用： 

​	让样式在局部生效，防止冲突

##### （2）写法：

==<style scoped></style>==

![image-20230426000933222](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304260009521.png)

