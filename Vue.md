---
title: Vue
date: 2021-12-10 21:33:00
categories: 前端
tags: Vue
---

# Vue环境配置

1. **因为需要安装Vue的插件, 所以需要访问到外网, 访问外网的软件地址**.

   **地址:** https://iguge.xyz/

   ![](https://i.bmp.ovh/imgs/2021/12/0c0e55391b799ec0.png)

![](https://i.bmp.ovh/imgs/2021/12/0dbb702682d68a6c.png)

![](https://i.bmp.ovh/imgs/2021/12/58b1e57d99cc5adc.png)

2. **安装Vue工具插件**.

   **安装地址:** https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd/related

   ![](https://i.bmp.ovh/imgs/2021/12/92d8838b396f13fb.png)

3. **安装VSCode插件**

   **工作原理: 它相当于一个本机的小型服务器, 启动的时候, 会将你的页面进行一个热部署的操作, 就是你代码修改的时候, 页面会直接发生改变, 不需要反复刷新.**

![](https://i.bmp.ovh/imgs/2021/12/4f5af083b4db9b30.png)

**使用:** 

![](https://i.bmp.ovh/imgs/2021/12/957e0a3b94b032e5.png)

3. **下载Vue, 并自己引入**

![](https://i.bmp.ovh/imgs/2021/12/0a76091e23aa6f0b.png)

# Vue编程

## 1. 第一个Hello,World

```Vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初识Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 
        初识Vue:
            1. 想让Vue工作, 必须创建一个Vue实例, 且要传入一个配置对象;
            2. root容器里的代码依然符合html规范, 只不过混入了一些特殊的Vue语法;
            3. root容器里的代码称为[Vue模板]
     -->

    <!-- 准备好一个容器 -->
    <div id="root">
        <h1>Hello, {{name}}</h1>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;//阻止Vue在启动时生成生产提示.

        //创建Vue实例
        const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务, 值通常为css选择器字符串
            data:{ //data中用于存储数据, 提供el所指定的容器去使用, 值我们暂时先写成一个对象.
                name: '覃烽'

            }
        })
    </script>
</body>
</html>
```

## 2. 模板语法

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>模板语法</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>


    <!-- 准备好一个容器 -->
    <div id="root">
        <h1>插值语法</h1>
        <h1>Hello, {{name}}</h1>
        <h1>指定语法</h1>
        <a v-bind:href="school.url.toUpperCase()">点我去{{school.name}}学习1</a>
        <a :href="school.url" :x='school.hello' :y="Date.now()">点我去百度学习2</a>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;//阻止Vue在启动时生成生产提示.

        //创建Vue实例
        const x = new Vue({
            el: '#root', //el用于指定当前Vue实例为哪个容器服务, 值通常为css选择器字符串
            data:{ //data中用于存储数据, 提供el所指定的容器去使用, 值我们暂时先写成一个对象.
                name: '覃烽',
                school: {
                    name: '百度',
                    url: 'http://www.baidu.com',
                    hello: "我喜欢你, 文琦"   
                }
            }
        })
    </script>
</body>
</html>
~~~

## 3. 数据绑定

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>数据绑定</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>


    <!-- 
        Vue有两种数据绑定的方式:
            1. 单向绑定(v-bind): 数据只能从data流向页面.
            2. 双向绑定(v-model): 数据不仅能从data流向页面, 还可以从页面流向data.
                备注:
                    1. 双向绑定一般都应用在表单类元素上(如: input, select等)
                    2. v-mode:value 可以简写为v-model, 因为v-mode默认收集的就是value值.
     -->

    <!-- 准备好一个容器 -->
    <div id="root">

        <!-- 普通写法 -->
        单向数据绑定: <input type="text" v-bind:value="name">
        双向数据绑定: <input type="text" v-model:value="name">

        <!-- 简写 -->
        单向数据绑定: <input type="text" :value="name">
        双向数据绑定: <input type="text" v-model="name">

        <!-- <h1>{{name}}</h1> -->

        <!-- 如下代码是错误的, 因为v-model只能应用在表单类元素(输入类元素上) -->
        <!-- <h1 v-model:x="name">你好啊</h1> -->
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;//阻止Vue在启动时生成生产提示.

        //创建Vue实例
        const x = new Vue({
            el: '#root',
            data:{
                name: '覃烽',
            }
        })
    </script>
</body>
</html>
~~~

## 4. el与data的两种写法

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>el与data的两种写法</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 
        el与data的两种写法
        1. el两种
            (1) .new Vue时配置el属性.
            (2) .先创建Vue实例, 随后再通过v.$mount('#root')指定el的值
        2. data有两种写法
            (1).对象式
            (2).函数式
            如何选择: 目前哪种写法都可以, 以后学到组件时, data必须使用函数时, 否则会报错
        3. 一个重要的原则:
            由Vue管理的函数, 一定不要写箭头函数, 一旦写了箭头函数, this就不再是Vue实例了.
     -->

    <!-- 准备好一个容器 -->
    <div id="root">
        <h1>{{name}}</h1>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;//阻止Vue在启动时生成生产提示.

        
        //el的两种写法
        // const v = new Vue({
        //     // el: '#root',//第一种写法
        //     data:{
        //         name: '覃烽',
        //     }
        // })
        // console.log(v);
        // v.$mount('#root')//第二种写法

        //data的两种写法
        new Vue({
            el: '#root',
            //data的第一种写法: 对象式
            // data:{
            //     name: '文琦'
            // }

            //data的第二种写法: 函数式
            data(){
                console.log('@@@', this) //此处的this是Vue实例对象
                return{
                    name: "文琦"
                }
            }
        })
    </script>
</body>
</html>
~~~

## 5. MVVM模型

> 观察发现: 
>
> 1. data中所有的属性, 最后都出现在vm身上.
> 2. vm身上所有的属性 及 Vue原型上所有属性, 在Vue模板中都可以直接使用.

![](https://i.bmp.ovh/imgs/2021/12/dadd1b8cf7c27400.png)

![](https://i.bmp.ovh/imgs/2021/12/b3ec9f041a63fc3e.png)

## 6. 数据代理

> 数据代理: 通过一个对象代理另一个对象中的属性的操作(读/写)

![](https://i.bmp.ovh/imgs/2021/12/b5642c1ad8dacd58.png)

**主要通过一个defineProperty函数进行一个实现, 解释了为什么数据改变了, 页面的数据展示也会发生改变**

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>回顾Object.defineproperty方法</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>

    <!-- 准备好一个容器 -->
    <div id="root">

    </div>

    <script type="text/javascript">
        let number = 18;
        let person = {
            name: '张三',
            sex: '男'
        }
        Object.defineProperty(person, 'age', {
            //当有人读取person的age属性时, get函数(getter)就会被调用, 且返回值就是age的值
            get(){
                console.log('有人读取age属性了')
                return number
            },

            //当有人修改person的age属性时, set函数(setter)就会被调用, 且会受到修改的具体值
            set(value){
                console.log('有人修改了age属性, 且值时', value)
                number = value
            }

            //当有人修改person的age属性时, set函数(setter)就会被调用, 且会受到修改的具体值
        })
    </script>
</body>
</html>
~~~

## 7.事件的基本使用

### 事件的基本使用

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>事件的基本使用</title>
    <script type="text/javascript" src="../../js/vue.js"></script>
</head>
<body>

    <!-- 
        事件的基本使用:
            1. 使用v-on:xxx或 @xxx绑定事件, 其中xxx是时间名;
            2. 事件的回调需要配置在methods对象中, 最终会在vm上;
            3. methods中配置的函数, 不要用箭头函数! 否则this就不是vm了;
            4. methods中配置的函数, 都是被Vue所管理的函数, this的指向是vm 或 组件实例对象;
            5. @click='demo' 和 @click='demo($event)' 效果一致, 但后者可以传参.
     -->

    <div id="root">
        <h2>欢迎来到{{name}}学习</h2>

        <!-- <button v-on:click="showInfo">点击我提示信息</button> -->

        <button @click="showInfo1">点击我提示信息1</button>

        <!-- Vue传参 -->
        <button @click="showInfo2(66, $event)">点击我提示信息1</button>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;

        const vm = new Vue({
            el: '#root',
            data:{ 
                name: '覃烽'
            },
            methods:{
                showInfo1(event){
                    // console.log(this)//此处的this就是Vue实例对象
                    alert("聂文琦")
                },
                showInfo2(number, event){
                    // console.log(this)//此处的this就是Vue实例对象
                    alert("废老"+number)
                    console.log(event)
                }
            }
        })
    </script>
</body>
</html>
~~~

### 事件修饰符

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>事件修饰符</title>
    <script type="text/javascript" src="../../js/vue.js"></script>
    <style>
        *{
            margin: 20px;
        }
        .demo1{
            height: 60px;
            background-color: red;
        }
    </style>
</head>
<body>

    <!-- 
        Vue中的事件修饰符:
            1. prevent: 阻止默认事件(常用);
            2. stop组织事件冒泡(常用);
            3. once: 事件只促发一次(常用);
            4. capture: 使用时间的捕获模式;
            5. self: 只有event.target是当前操作的元素是才触发事件;
            6. passive: 事件的默认行为立即执行, 无需等待事件回调执行完毕;
     -->

    <div id="root">
        <h2>欢迎来到{{name}}学习</h2>

        <!-- prevent: 阻止默认事件(常用); -->
        <a href="http://www.baidu.com" @click.prevent="showInfo">点我提示信息</a>

        <!-- stop组织事件冒泡(常用); -->
        <div class="demo1" @click="showInfo">
            <button @click.stop="showInfo">点我提示</button>
        </div>

        <!-- once: 事件只促发一次(常用); -->
        <button @click.once="showInfo">点我提示</button>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;

        const vm = new Vue({
            el: '#root',
            data:{ 
                name: '覃烽'
            },
            methods:{
                showInfo(event){
                    alert("聂文琦")
                }
            }
        })
    </script>
</body>
</html>
~~~

### 键盘事件

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>键盘事件</title>
    <script type="text/javascript" src="../../js/vue.js"></script>
</head>
<body>

    <!-- 
        1. Vue中常用的按键别名:
            回车 => enter
            删除 => delete(捕获删除和退格键)
            退出 => esc
            空格 => space
            换行 => tab(特殊, 必须配合keydown)
            上 => up
            下 => down
            左 => left
            右 => right
        2. Vue未提供别名的案件, 可以使用按键原始的key值去绑定, 但注意要转为kebab-case (短横线命名)

        3. 系统修饰键(用法特殊): ctrl, alt, shift, meta
            (1). 配合keyup使用: 按下修饰键的同时, 再按下其他键, 随后释放其他键, 事件才被促发.
            (2). 配合keydown使用: 正常触发事件.

        4. 也可以使用keyCode去指定具体的按键(不推荐)

        5. Vue.config.keyCodes.自定义键名 = 键码, 可以去定制按键别名
     -->

    <div id="root">
        <h2>欢迎来到{{name}}学习</h2>
        <input type="text" placeholder="按下回车提示输入" @keyup.enter="showInfo">
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;

        const vm = new Vue({
            el: '#root',
            data:{ 
                name: '覃烽'
            },
            methods:{
                showInfo(e){
                    // console.log(1)
                    console.log(e)
                    console.log(e.target.value)
                }
            }
        })
    </script>
</body>
</html>
~~~

## 8.计算属性

> 需求: 根据输入框的改变, 后面姓名随着输入框改变

![](https://i.bmp.ovh/imgs/2021/12/8c795c016a77b739.png)

### 插值语法实现

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>姓名案列_插值语法实现</title>
    <script type="text/javascript" src="../../js/vue.js"></script>
</head>
<body>
    <div id="root">
        姓: <input type="text" v-model="firstName">
        名: <input type="text" v-model="lastName">
        姓名: <span>{{firstName.slice(0,3)+'-'+lastName}}</span>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;

        const vm = new Vue({
            el: '#root',
            data:{ 
                firstName: '张',
                lastName:'三'
            }
        })
    </script>
</body>
</html>
~~~

### methods实现

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>姓名案例_methods实现</title>
    <script type="text/javascript" src="../../js/vue.js"></script>
</head>
<body>
    <div id="root">
        姓: <input type="text" v-model="firstName">
        名: <input type="text" v-model="lastName">
        姓名: <spa>{{fullName()}}</span>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;

        const vm = new Vue({
            el: '#root',
            data:{ 
                firstName: '张',
                lastName:'三'
            },
            methods:{
                fullName(){
                    console.log('@--fullName')
                    return this.firstName + '-' + this.lastName;
                }
            }
        })
    </script>
</body>
</html>
~~~

