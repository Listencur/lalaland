---
title: 'Vue组件化编程'
date: '2023-04-17 02:09:21'
tags: ['Vue']
language: 'zh-cn'
description: ''
---


[TOC]



# Vue组件

### 一. 组件的理解：

#### 1. 图示：

<img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304120140661.png" alt="image-20230412014022155" style="zoom: 33%;" />

 

<img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304120141414.png" alt="image-20230412014133124" style="zoom: 33%;" />

#### 2. 组件的概念：

> **`组件————实现应用中局部功能代码和资源的集合`**

#### 3. 模块，组件，模块化和组件化：

**`模块：`**

- 理解：向外提供特定功能的 js 程序，一般就是一个 js 文件 
- 为什么：js 文件很多很复杂
- 作用：复用、简化 js 的编写，提高 js 运行效率

**`组件:`**

- 定义：用来实现局部功能的代码和资源的集合（html/css/js/image…）
- 为什么：一个界面的功能很复杂
- 作用：复用编码，简化项目编码，提高运行效率

**`模块化`**

- 当应用中的 js 都以模块来编写的，那这个应用就是一个模块化的应用


**`组件化：`**

- 当应用中的功能都是多组件的方式来编写的，那这个应用就是一个组件化的应用

-----

### 二. 非单文件组件：

#### 1.非单文件组件和单文件组件：

- ==非单文件组件==：一个文件中包含有 n 个组件

    

- ==单文件组件==：一个文件中只包含有 1 个组件

#### 2.Vue中使用组件的三大步骤 

##### **（1）**定义组件

-  使用`Vue.extend(options)`创建，其中options和new Vue(options)时传入的options几乎一样，但也有点区别

-  el不要写，因为最终所有的组件都要经过一个vm的管理，由vm中的el才决定服务哪个容器

-  data必须写成函数(`data(){}`)，避免组件被复用时，数据存在引用关系(*后续多次复用，改变其中一个组件的属性值，不会影响另一个组件*)

##### **（2）**注册组件

-  局部注册：new Vue()的时候options传入components选项

-  全局注册：Vue.component('组件名',组件)

###### **（3）**使用组件

- 编写组件标签如 <school></school> 

```html
<body>
    <div id="root">
        <!-- 3. 调用组件 -->
        <student></student>
        <hr>
        <school></school>
        <hr>
        <hello></hello>
    </div>
    <div id="root2">
        <hello></hello>
    </div>
</body>

<script>
 // 1. 创建student组件
    const student = Vue.extend({
        template: `
            <div>
                <h3>学生名称：{{studentName}}</h3>
				<h3>学生地址：{{studentAge}}</h3>
            </div>
        `,
        data() {
            return {
                studentName: '严乐',
                studentAge: 22
            }
        }
    })
 // 1. 创建scool组件
    const school = Vue.extend({
        template: `
            <div>
                <h3>学校名称：{{schoolName}}</h3>
				<h3>学校地址：{{address}}</h3>
            </div>
        `,
        data() {
            return {
                schoolName: '西安工业大学',
                address: '学府中央2号'
            }
        }
    })
 // 1. 创建hello组件
    const hello = Vue.extend({
        template: `
            <div>
                <h2>{{name}}</h2>
            </div>
        `,
        data() {
            return {
                name: '哈哈哈'
            }
        }
    })
 // 2.注册组件（全局注册）
    Vue.component('hello', hello)
    
    new Vue({
        el: '#root',
 // 2. 注册组件(局部注册)
        components: {
            student,    // student:student  （组件名：变量）组件名和变量名一样直接写一个
            school      // school:school
        }
    })

    new Vue ({
        el: '#root2'
    })
```

#### 3. 组件的注意事项：

###### （1）关于组件名

- 一个单词组成
    - 第一种写法（首字母小写）：school
    - 第二种写法（首字母大写）：School
- 多个单词组成
    - 第一种写法（kebab-case 命名）：my-school
    - 第二种写法（CamelCase 命名）：MySchool（需要Vue脚手架支持）
- 备注
    - 组件名尽可能回避HTML中已有的元素名称，例如：h2、H2都不行
    - 可以使用name配置项指定组件在开发者工具中呈现的名字

###### （2）关于组件标签 

- 第一种写法：<school></school>

- 第二种写法：<school/>（需要Vue脚手架支持）

- 备注：不使用脚手架时，<school/>会导致后续组件不能渲染

- 一个简写方式：

    `const school = Vue.extend{options}` 可简写为 `const school = {options}`

    因为父组件components引入的时候会自动创建

### 三. 组件嵌套

#### 1. 最基本的组件嵌套：

```html
<body>
    <div id="root">
        <school></school>
    </div>


</body>
<script>
    // 创建student组件
    const student = Vue.extend({
        template: `
            <div>
                <h3>学校名称：{{name}}</h3>
				<h3>学校地址：{{age}}</h3>
            </div>
        `,
        data() {
            return {
                name: '严乐',
                age: 29
            }
        }
    })
    // 创建school组件
    const school = Vue.extend({
        template: `
            <div>
                <h3>学校名称：{{schoolName}}</h3>
				<h3>学校地址：{{address}}</h3>
                <student></student>
            </div>
        `,
        data() {
            return {
                schoolName: '西安工业大学',
                address: '学府中路2号'
            }
        },
        components: {
            student
        }
    })

    new Vue({
        el: '#root',
        components: {
            school,
        }
    })
</script>
```

![image-20230412134819124](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304121348399.png)

<img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304121408004.png" alt="img" style="zoom:33%;" />

#### 2. app管理子组件

**root只管理app组件，而app组件来管理它的其余子组件**

```html
<body>
    <div id="root"> </div>
</body>
<script>
    // 创建student组件
    const student = Vue.extend({
        template: `
            <div>
                <h3>姓名：{{name}}</h3>
				<h3>年龄：{{age}}</h3>
            </div>
        `,
        data() {
            return {
                name: '严乐',
                age: 29
            }
        }
    })
    // 创建school组件
    const school = Vue.extend({
        template: `
            <div>
                <h3>学校名称：{{schoolName}}</h3>
				<h3>学校地址：{{address}}</h3>
                <student></student>
            </div>
        `,
        data() {
            return {
                schoolName: '西安工业大学',
                address: '学府中路2号'
            }
        },
        components: {
            student
        }
    })

    // 创建hello组件
    const hello = Vue.extend({
        template: `
                <h3>{{ name }}</h3>
        `,
        data() {
            return {
                name: 'hello,Vue'
            }
        }
    })

    // 创建app组件，用于管理其他组件
    const app = Vue.extend({
        template: `
            <div>
                <hello></hello>
                <school></school>
            </div>   
        `,
        components: {
            school,
            hello
        }
    })
    new Vue({
        el: '#root',
        template: `<app></app>`,
        components: {
            app
        }
    })
</script>
```

<img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304121409536.png" alt="image-20230412140953492" style="zoom: 50%;" /><img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304121410226.png" alt="image-20230412141040097" style="zoom: 50%;" />

#### 四. VueComponent （Vue组件）

##### 1. 关于VueComponent： 

1. **school`组件`本质是一个名为`VueComponent的构造函数`，不是程序员生成，而是调用Vue.extend()，自动生成**

    

2. **编写组件标签 <school></school> 或者 <school/> ,Vue解析时会创建school组件的实例对象(即VueComponent的实例化对象)，即Vue会自动执行new VueComponent(options)**

    

3. **每次调用Vue.extend,返回的都是一个`全新`的VueComponent，即<u>不同的组件是不同的对象</u>**

    

4. **`关于this的指向`：**

    - ==**在组件配置中：**==

        - **data函数，methods函数，watch函数，computed函数的this指向均是`VueComponent实例化对象`**

            

    - ==**new Vue(options)中**==
        - **data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是`Vue实例对象`**

5. **组件的实例对象及Vue的实例对象：**

    -  **VueComponent实例对象：`vc`**

    -  **Vue实例对象: `vm`**

        

<hr/>

#### 五. 重要的内置关系：

##### 1. JS原型链的回顾：

![image-20230412151349949](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304121513239.png)

##### 2. **一个重要的内置关系：**

> **`VueComponent.prototye.__proto__ === Vue.prototype`**

> **为什么需要这个关系：**
>
> ​	**`让组件的实例对象vc可以访问到Vue原型`**

![image.png](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304121738066.png)

#### 六. 单文件的组件

##### 1. Export和import模块化知识回顾：

[]: E:\A前端学习笔记\javasript高级\5-ES6\ES6笔记\09-ES6模块化.md	"ES6模块化 export暴露和import引入"



模块化暴露方式三种：

- export 分别暴露；
- export{}统一暴露；
- export default school   默认暴露



==**创建School组件（School.vue）**==

```vue
<template>
	<div id="Demo">
   	 	<h2>学校名称： {{ schoolName }}</h2>
   	 	<h2>学校地址： {{ schoolAddress }}</h2>
   	    <button @click="showName">点我提示学校名</button>
    </div>
</template>

<script>
    export default {
        name: 'School',
        data() {
            return {
                schoolName: '西安工业大学',
                schoolAddress: '学府中路2号'    
            }
        },
        methods: {
            showName() {
                alert(this.name)
            }

        }
    }
</script>

<style>
    #demo {
        background-color:pink;
    }
</style>
```

==**创建Studetn组件（Student.vue）**==

```vue
<template>
    <div>
        <h2>学生姓名： {{ name }}</h2>
        <h2>学生年龄： {{ age }}</h2>
    </div>
</template>

<script>
export default {
    data(){
        return {
            name: 'yanle',
            age: 22
        }
    }
}
</script>
```

==**创建App组件（App.vue）**==

> **App组件的作用：用来管理其他组件**

```vue
<template>
    <div>
        <School></School>
        <Student></Student>
    </div>
</template>

<script>
    import School from './School.vue'
    import Student from './Student.vue'

    export default {
        name: 'App',
        components: {
            School, 	// School: School
            Student 	// Student: Student
        }
    }
</script>
```

​    

==创建main.js== 

> **main.js为`入口文件`**		用来存放new Vue及 App组件的导入

```javascript
import App from './App.vue'

new Vue ({
    el: '#root',
    template: `App`,
    components: {
        App
    }
})
```

==index.html==

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>单文件组件练习</title>
</head>
<body>
    <div id="root"></div>
    <!-- body中还要引入main.js 和 Vue.js文件 -->
    <script src="./main.js"></script>
    <script src="/assets/js/vue.js"></script>
</body>
</html>
```

