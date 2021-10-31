- 

```
1.
引入Vue.js
2. 创建Vue对象
    el : 指定根element(选择器)
    data : 初始化数据(页面可以访问)
3. 双向数据绑定 : v-model
4. 显示数据 : {{xxx}}
5. 理解vue的mvvm实现
```



### MVVM

![1584812604114](asserts/vue%20note/1584812604114.png)

Model-View-ViewModel的简写

- 声明式编程：按照语法格式就可以
- 中间是Vm



### 指令

- 指令一: 强制数据绑定 v-bind:
-  指令二: 绑定事件监听 v-on:

```
<h2>2. 指令一: 强制数据绑定</h2>
<a href="url">访问指定站点</a><br><a v-bind:href="url">访问指定站点2</a><br><a :href="url">访问指定站点2</a><br><h2>

3. 指令二: 绑定事件监听</h2><button v-on:click="test">点我</button><button @click="test">点我</button>
```



### 监听

1. **计算属性**
    在computed属性对象中定义计算属性的方法,
    方法有两个参数newVal, oldVal，一般只用前一个
    在页面中使用{{方法名}}来显示计算的结果
    
2. **监视属性**:
    通过通过vm对象的**$watch()或watch**配置来监视指定的属性
    当属性变化时, 回调函数自动调用, 在函数内部进行计算
    
    选项式的watch可以使用immediate：true 立即执行，而不需要等待数据变化时才执行。
    
3. 计算属性高级:
    通过**getter/setter**实现对属性数据的显示和**监视**
    **计算属性存在缓存**, 多次读取只执行一次getter计算

4. 在computed属性对象中定义的、以及计算属性中的getter、setter、都是**回调函数**，特点：a你定义的b你没有哦调用c但最终执行了

5. **因为是回调函数，所以实际调用的时候不应该加扩号！！！**

 ### vm对象

所有vm对象的方法都以$开头 



### class与style绑定

1. 理解
    在应用界面中, 某个(些)元素的样式是变化的
    class/style绑定就是专门用来实现动态样式效果的技术
    
2. class绑定  **:class='xxx'**
    xxx是字符串
    xxx是对象 ：属性为类名，值为布尔值，若**值=true，则使用该属性**。 **属性值从vm中获取**。缺点就是不能动态添加属性。
    xxx是数组：没有对象的缺点，通过pop、push改变数组元素
    
    
    
3. style绑定
    **:style=**"{ color: activeColor, fontSize: fontSize + 'px' }"
    其中activeColor/fontSize是data属性（从vm中获取）

### 列表渲染

1. v-for: in  of
2. key 用来跟踪每个节点的身份，从而重用和排序现有元素，理想中的key值:data.id
3. 数组更新检测
   a. 使用以下方法操作数组，可以检测变动
   push() pop() shift() unshift() splice() sort() reverse()
   b. filter(), concat() 和 slice() ,map(),新数组替换旧数组
   c. 不能检测以下标变动的数组
   vm.items[indexOfItem] = newValue
4. *解决* 
   (1)**Vue.set(example1.items, indexOfItem, newValue)**
   (2)splice

###条件渲染指令

1. 条件渲染指令
    v-if
    v-else
    v-show
2. 比较v-if与v-show
    如果需要频繁切换 v-show 较好

#### [变异方法 (mutation method)](https://cn.vuejs.org/v2/guide/list.html#变异方法-mutation-method)

Vue 将被侦听的数组的变异方法进行了包裹，所以它们也将会触发视图更新。这些被包裹过的方法包括：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

重写的实现就是：先改变数组，再更新界面



### 事件监听 & 修饰符

1. 绑定监听:
    v-on:xxx="fun"
    @xxx="fun"
    @xxx="fun(参数)"
    默认事件形参: event
    隐含属性对象: $event
    
2. 事件修饰符:
    **.prevent** : 阻止事件的默认行为 **event.preventDefault()**
    **.stop** : 停止事件冒泡 **event.stopPropagation()**
    .self : 事件自能由自身触发
    .once : 事件只生效一次，用完解绑
    
3. 按键修饰符
    KeyDown：用户摁下摁键时发生
    KeyPress：用户摁下摁键，并且产生一个字符时发生
    KeyUp： 用户释放某一个摁键时触发

    **.keycode** : 操作的是某个keycode值的健
    **.enter** : 操作的是enter键

```html
<div id="example">

  <h2>1. 绑定监听</h2>
  <button @click="test1">test1</button>
  <button @click="test2('abc')">test2</button>
  <button @click="test3('abcd', $event)">test3</button>

  <h2>2. 事件修饰符</h2>
  <a href="http://www.baidu.com" @click.prevent="test4">百度一下</a>
  <div style="width: 200px;height: 200px;background: red" @click="test5">
    <div style="width: 100px;height: 100px;background: blue" @click.stop="test6"></div>
  </div>

  <h2>3. 按键修饰符</h2>
  <input type="text" @keyup.13="test7">
  <input type="text" @keyup.enter="test7">

</div>
```

### 表单修饰符

v-model.trim 去除首尾的空格

v-model.lazy  失去焦点是变更状态

v-model.number 只变更为数字



#### 其他事件

onmouseenter\onmouseleave：进出当前元素时触发，不包括进出子元素

onmouseover\onmouseout ：进出子元素时触发

![1584978970077](asserts/vue%20note/1584978970077.png)



### 计算属性

使用时不需要加括号

计算属性会缓存；依赖的状态改变了，计算属性会重新计算



## 生命周期

### mounted

- 父组件的mounted是等待所有子组件都mounted之后才触发的

初始化显示之后立即调用

挂载后

用于发送ajax请求、启动定时器等异步任务



#### beforeDestoryed

销毁前调用

用做收尾工作，如清除定时器



### 过渡transition&动画animation

1. vue动画的理解
    操作css的trasition或animation
    vue会给目标元素添加/移除特定的class
2. 基本过渡动画的编码
    1). 在目标元素外包裹<transition name="xxx">
    2). 定义class样式
    1>. 指定过渡样式: transition
    2>. 指定隐藏时的样式: opacity/其它
3. 过渡的类名
    xxx-enter-active: 指定显示的transition
    xxx-leave-active: 指定隐藏的transition
    xxx-enter: 指定隐藏时的样式



### 过滤器

1. 理解过滤器
    功能: 对要显示的数据进行特定格式化后再显示
    注意: 并没有改变原本的数据, 可是产生新的对应的数据
2. 编码
    1). 定义过滤器
    Vue.filter(filterName, function(value[,arg1,arg2,...]){
      // 进行一定的数据处理
      return newValue
    })
    2). 使用过滤器
  
    <div>{{myData | filterName}}</div>
    <div>{{myData | filterName(arg)}}</div>

### 常用内置指令
  v:text : 更新元素的 textContent
  v-html : 更新元素的 innerHTML
  v-if : 如果为true, 当前标签才会输出到页面
  v-else: 如果为false, 当前标签才会输出到页面
  v-show : 通过控制display样式来控制显示/隐藏
  v-for : 遍历数组/对象
  v-on : 绑定事件监听, 一般简写为@
  v-bind : 强制绑定解析表达式, 可以省略v-bind
  v-model : 双向数据绑定
  ref : 为某个元素注册一个唯一标识, vue对象通过$refs属性访问这个元素对象



v-model.lazy 

#### 属性指令

  v-cloak : 使用它防止闪现表达式, 与css配合: [v-cloak] { display: none }

[v-cloak]是一个属性选择器，在数据加载之后，该属性会消失。利用他的消失来让预先隐藏的显示出来。

### 存储数据

​	利用**window.localStorage**存储数据。存储的结果是KV，V为文本。

- 所以存入的时候对象需要转化成字符串，`JSON.parse(localStorage.getItem(TODOS_KEY) || '[]')`

- 取出的时候字符串要转成JSON对象`localStorage.setItem(TODOS_KEY, JSON.stringify(todos))`



存储优化

```js
向local中存储数据的工具模块
1. 向外暴露一个函数(功能)
   只有一个功能需要暴露
2. 向外暴露一个对象(包含多个功能)
   有多个功能需要暴露
 */
const TODOS_KEY = 'todos_key'
export default {
  readTodos () {
    return JSON.parse(localStorage.getItem(TODOS_KEY) || '[]')
  },
  saveTodos (todos) {
    localStorage.setItem(TODOS_KEY, JSON.stringify(todos))
  }
}

```









## 插件

### 自定义插件

- 定义插件对象
- 定义install方法，传入Vue 和 options
- 具有三种功能
  - 1、添加全局方法或属性
  - 2、添加全局资源
  - 3、添加实例方法
- 向外暴露

```js
(function (window) {
  const MyPlugin = {}
  MyPlugin.install = function (Vue, options) {
    // 1. 添加全局方法或属性
    Vue.myGlobalMethod = function () {
      console.log('Vue函数对象的myGlobalMethod()')
    }

    // 2. 添加全局资源
    Vue.directive('my-directive',function (el, binding) {
      el.textContent = 'my-directive----'+binding.value
    })

    // 4. 添加实例方法
    Vue.prototype.$myMethod = function () {
      console.log('vm $myMethod()')
    }
  }
  //向外暴露
  window.MyPlugin = MyPlugin
})(window)

```

- 使用

```html
<body>

<div id="test">
  <p v-my-directive="msg"></p>
</div>

<script type="text/javascript" src="../js/vue.js"></script>
<script type="text/javascript" src="vue-myPlugin.js"></script>
<script type="text/javascript">
  // 声明使用插件(安装插件: 调用插件的install())
  Vue.use(MyPlugin) // 内部会调用插件对象的install()

  const vm = new Vue({
    el: '#test',
    data: {
      msg: 'HaHa'
    }
  })
  Vue.myGlobalMethod()
  vm.$myMethod()

  new Object()
</script>
</body>
```





### 组件开发

组件就是页面功能的局部模块，包含了结构样式行为

组件中的data必须写成函数形式

```js
data() {
    return {
        msg: 'Ni Hao Hello!'
    }
}
```

- 根组件 src/App.vue

##组件通信

### i. 父子组件传值 (props down, events up)

### ii. 属性验证

props:{name:Number}
Number,String,Boolean,Array,Object,Function,null(不限制类型)

### iii. 事件机制

a.使用 $on(eventName) 监听事件
b.使用 $emit(eventName) 触发事件

### iv. Ref

```html
<input ref="mytext"/> this.$refs.mytext

<child-comp ref="child"></child-comp>
this.$refs.child 拿到的是子组件对象，可以获取子组件的所有属性
```

### v. 事件总线

使用空的vue实例，通过`bus.$on` 和 `bus.$emit`实现通信

var bus = new Vue();

* mounted生命周期中进行监听

```js
    var bus = new Vue();
	Vue.component("publisher", {
      template:`
        <div>
          <input ref="mytext" ></input>
          <button @click="publish">发布</button>
        </div>
      `,
      methods: {
        publish(){
          console.log('事件发布')
          bus.$emit("source1",this.$refs.mytext.value);
        }
      }
    })


    Vue.component("subscriber",{
      template:`
      <div>
        订阅者：{{text}}
      </div>
      `,
      data(){
        return{
          text:''
        }
      },
      mounted () {
        bus.$on('source1',data=>this.text=data);
      }
    })
```







通过主键标签的属性可以传递数据。

声明接收数据的方式：

```js
=======数组
export default {
    name: 'List',
    props: ['comments'],
    components:{
      Item
    }
  }


=======属性及其类型
export default {
    name: 'Item',
    props:{
      comment: Object
    }
  }
  
=======属性的详细配置
export default {      
    name: 'Add',
    props:{
      addComment: {//指定了 属性名、属性值的类型、必要性
        type:Function,
        required: true
      }
    },
```

- 数据在哪个组件，更新数据的行为（方法）就应该定义在哪个组件







## 动态组件

### `<component>` 元素

`<component>` 元素动态地绑定多个组件到它的 **is** 属性，is改变时，默认销毁上一个is的组件

有的时候，在不同组件之间进行动态切换是非常有用的，比如在一个多标签的界面里：

可以通过 Vue 的 `<component>` 元素加一个特殊的 `is` attribute 来实现：

```html
<!-- 组件会在 `currentTabComponent` 改变时改变 -->
<component v-bind:is="currentTabComponent"></component>
```

在上述示例中，`currentTabComponent` 可以包括

- 已注册组件的名字，或
- 一个组件的选项对象

你可以在[这里](https://codesandbox.io/s/github/vuejs/vuejs.org/tree/master/src/v2/examples/vue-20-dynamic-components)查阅并体验完整的代码，或在[这个版本](https://codesandbox.io/s/github/vuejs/vuejs.org/tree/master/src/v2/examples/vue-20-dynamic-components-with-binding)了解绑定组件选项对象，而不是已注册组件名的示例。

请留意，这个 attribute 可以用于常规 HTML 元素，*但这些元素将被视为组件*，这意味着所有的 attribute **都会作为 DOM attribute 被绑定**。对于像 `value` 这样的 property，若想让其如预期般工作，你需要使用 [`.prop` 修饰器](https://cn.vuejs.org/v2/api/#v-bind)。





### `<keep-alive>` 

`<keep-alive>` 保留状态，避免重新渲染

[code]: https://codesandbox.io/s/github/vuejs/vuejs.org/tree/master/src/v2/examples/vue-20-dynamic-components?file=/index.html	"code"





## 插槽

a. 单个slot
b. 具名slot

不带 `name` 的 `<slot>` 出口会带有隐含的名字“**default**”。

2.6.0起，使用v-slot:[slotName] 来指定具名插槽，注意 **`v-slot` 只能添加在 `<template>` 上** (只有[一种例外情况](https://cn.vuejs.org/v2/guide/components-slots.html#独占默认插槽的缩写语法))，这一点和已经废弃的 [`slot` attribute](https://cn.vuejs.org/v2/guide/components-slots.html#废弃了的语法) 不同。

*混合父组件的内容与子组件自己的模板-->内容分发

**父组件模板的内容在父组件作用域内编译；子组件模板的内容在子组件作用域内编译。**
作用：在插槽中可以直接访问父组件的作用域，改变父组件状态



## 动画

Vue 在插入、更新或者移除 DOM 时，提供多种不同方式的应用过渡效果。

### (1)单元素/组件过渡

* css过渡
* css动画
* 结合animate.css动画库

### (2) 多个元素过渡(设置key)

*当有相同标签名的元素切换时，需要通过 key 特性设置唯一的值来标记以让 Vue 区分它们，否则 Vue 为
了效率只会替换相同标签内部的内容。
mode:in-out ; out-in

### (3)多个组件过渡



### (4)列表过渡(设置key)

`<transition-group>`不同于 transition， 它会以一个真实元素呈现：默认为一个 `<span>`。你也可以通过tag 特性更换为其他元素。



![image-20200726025738757](asserts/vue%20note/image-20200726025738757.png)

由于虚拟dom的diff算法，元素同名的时候，他会直接替换元素的内容，不同名会直接销毁创建新的，所以左边是没有动画效果的，而右边有。
解决方法：给同名的标签加个唯一的key属性值



## 过滤器

过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符号指示：

```html
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>

```

- 可以在一个组件的选项中定义本地的过滤器：使用filters属性

```js
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```

- 或者在创建 Vue 实例之前全局定义过滤器：

```js
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
  // ...
})
```

过滤器可以串联：

```
{{ message | filterA | filterB }}
```

在这个例子中，`filterA` 被定义为接收单个参数的过滤器函数，表达式 `message` 的值将作为参数传入到函数中。然后继续调用同样被定义为接收单个参数的过滤器函数 `filterB`，将 `filterA` 的结果传递到 `filterB` 中。

过滤器是 JavaScript 函数，因此可以接收参数：

```
{{ message | filterA('arg1', arg2) }}
```

这里，`filterA` 被定义为接收三个参数的过滤器函数。其中 `message` 的值作为第一个参数，普通字符串 `'arg1'` 作为第二个参数，表达式 `arg2` 的值作为第三个参数。



<img src="asserts/vue%20note/image-20200726033400693.png" alt="image-20200726033400693" style="zoom: 80%;" />





## 自定义指令


(1)自定义指令介绍 directives
(2)钩子函数

* 参数 el,binding,vnode(vnode.context)
* bind,inserted,update,componentUpdated,unbind
(3)函数简写
(4)自定义指令-轮播
*inserted 插入最后一个元素时调用(vnode.context.datalist.length-1)
*this.$nextTick()



## 单文件组件

scoped原理

![image-20200726174954606](asserts/vue%20note/image-20200726174954606.png)

## vue.config.js

(1) proxy代理
https://cli.vuejs.org/zh/config/#%E5%85%A8%E5%B1%80-cli-%E9%85%8D%E7%BD%AE


(2) alias别名配置
@ is an alias to /src
(3) vue.config.js 中配置 publicPath: './',
在类似 Cordova hybrid 应用的文件系统中（配合hash模式）
(4)关闭eslint
vue.config.js lintOnSave: false
.eslintrc 删除 '@vue/standard'	





##路由

- 若同一路径被多个路由匹配，则只匹配最前面的

<img src="asserts/vue%20note/image-20200727224920589.png" alt="image-20200727224920589" style="zoom:67%;" />

键值对、

键path 

值 后台：处理请求的回调函数  前台：组件

#### [Prop 的大小写 (camelCase vs kebab-case)](https://cn.vuejs.org/v2/guide/components-props.html#Prop-的大小写-camelCase-vs-kebab-case)

HTML 中的 attribute 名是大小写不敏感的，所以浏览器会把所有大写字符解释为小写字符。这意味着当你使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短横线分隔命名) 命名：

```
Vue.component('blog-post', {
  // 在 JavaScript 中是 camelCase 的
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
<!-- 在 HTML 中是 kebab-case 的 -->
<blog-post post-title="hello!"></blog-post>
```

重申一次，如果你使用字符串模板，那么这个限制就不存在了。





#### 注册路由器

在main.js导入并注册：使用router属性来配置

```js
import router from './router'
new Vue({
router //简写
})`

```

#### 缓存路由组件对象

1) 默认情况下, 被切换的路由组件对象会死亡释放, 再次回来时是重新创建的
2) 如果可以缓存路由组件对象, 可以提高用户体验

```html
<keep-alive>
<router-view></router-view>
</keep-alive>
```



#### 向路由组件传递参数有两种形式

- 路由路径参数
- 通过router-view标签



#### 参数方式：
1、query：路径与参数用?分割

2、params：路径后直接加参数





## Vuex

### 状态自管理应用

1) state: 驱动应用的数据源
2) view: 以声明方式将state 映射到视图
3) actions: 响应在view 上的用户输入导致的状态变化(包含n 个更新状态的方法)

![1586154574511](asserts/vue%20note/1586154574511.png)

### 多组件共享状态的问题

1) 多个视图依赖于同一状态
2) 来自不同视图的行为需要变更同一状态
3) 以前的解决办法
a. 将数据以及操作数据的行为都定义在父组件
b. 将数据以及操作数据的行为传递给需要的各个子组件(有可能需要多级传递)
4) vuex 就是用来解决这个问题的

![1586154687373](asserts/vue%20note/1586154687373.png)

#### state

1) vuex 管理的状态对象

2) 它应该是唯一的
const state = {
xxx: initValue
}

#### mutations

1) 包含多个直接更新state 的方法(回调函数)的对象
2) 谁来触发: action 中的commit('mutation 名称')
3) 只能包含同步的代码, 不能写异步代码
const mutations = {
yyy (state, {data1}) {
// 更新state 的某个属性
}
}

#### actions

1) 包含多个事件回调函数的对象
2) 通过执行: commit()来触发mutation 的调用, 间接更新state
3) 谁来触发: 组件中: $store.dispatch('action 名称', data1) // 'zzz'
4) 可以包含异步代码(定时器, ajax)
const actions = {
zzz ({commit, state}, data1) {
commit('yyy', {data1})
}
}
6.2.4. getters
1) 包含多个计算属性(get)的对象
2) 谁来读取: 组件中: $store.getters.xxx
const getters = {
mmm (state) {
return ...
}
}

#### modules

1) 包含多个module
2) 一个module 是一个store 的配置对象
3) 与一个组件(包含有共享数据)对应

### 向外暴露store 对象

export default new Vuex.Store({
state,
mutations,
actions,
getters
})

### 组件中

import {mapState, mapGetters, mapActions} from 'vuex'
export default {
computed: {
...mapState(['xxx']),
...mapGetters(['mmm']),
}
methods: mapActions(['zzz'])
}
{{xxx}} {{mmm}} @click="zzz(data)"

### 映射store

import store from './store'
new Vue({
store
})


### store 对象

1) 所有用vuex 管理的组件中都多了一个属性$store, 它就是一个store 对象
2) 属性:
state: 注册的state 对象
getters: 注册的getters 对象
3) 方法:
dispatch(actionName, data): 分发调用action



vuex结构图

![1586159446716](asserts/vue%20note/1586159446716.png)

### 注意事项：！！！

![1586197787326](asserts/vue%20note/1586197787326.png)

![1586197777274](asserts/vue%20note/1586197777274.png)





## 案例

### 写交互

- 首先绑定事件监听





# ------js------

## 数组的方法

### `splice()`

具有对数组的增删改功能

- splice(index, count) :从索引index开始删除，删除元素个数是count
- splice(index, count, val)  : 操作在上面的基础上，对索引index处增加一个元素val
  - splice(i, 0 , newVal) ：增加
  - splice(i, 1 , newVal) ：修改

### unshift(element)

往数组添加元素

## js语法

### ||&&

**1.&&**

1.1两边条件都为true时，结果才为true；
1.2如果有一个为false，结果就为false；
1.3当第一个条件为false时，就不再判断后面的条件

注意：当数值参与逻辑与运算时，结果为true，那么会返回的会是第二个为真的值；如果结果为false，返回的会是第一个为假的值。

**2.||**

2.1只要有一个条件为true时，结果就为true；
2.2当两个条件都为false时，结果才为false；
2.3当一个条件为true时，后面的条件不再判断

注意：当数值参与逻辑或运算时，结果为true，会返回第一个为真的值；如果结果为false，会返回第二个为假的值；

**3.！**

3.1当条件为false时，结果为true；反之亦然。

**返回值：**

表达式a && 表达式b :  计算表达式a（也可以是函数）的运算结果，
                      如果为 True, 执行表达式b（或函数），并返回b的结果；
                      如果为 False，返回a的结果；

表达式a || 表达式b :   计算表达式a（也可以是函数）的运算结果，
                      如果为 Fasle, 执行表达式b（或函数），并返回b的结果；
                      如果为 True，返回a的结果；

**转换规则:**

对象为true；
非零数字为true；

零为false;

非空字符串为true；
空字符串为法false;
其他为false；

例如：
var  a =  obj || " "  ;     //如果 obj 为空，a就赋值为 " " ；
var  a = check() &&  do();    //如果check()返回为真，就执行do()，并将结果赋值给 a;



## ES6

### ${}模板字符串

- 需要使用``将${exp}包起来

```js
 methods: {
      del(){
        const {comment,index, deleteComment} = this
        if (window.confirm(`确定要删除${comment.name}吗？`)){
          deleteComment(index);
        }

      }
    }
```



###解构赋值

- 对象的解构赋值，可以很方便地将现有对象的方法，赋值到某个变量。



```js
// 例:将Math对象的对数、正弦、余弦三个方法，赋值到对应的变量上
let { log, sin, cos } = Math;
// 如果**变量名与属性名不一致**，必须写成下面这样。
let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'

let { foo: foo, bar: bar } = { foo: 'aaa', bar: 'bbb' };
```



也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。

```javascript
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"
foo // error: foo is not defined

```

上面代码中，foo是匹配的**模式**，baz才是**变量**。**真正被赋值的是变量baz**，而不是模式foo。

- #### 交换变量的值

```
let x = 1;
let y = 2;

[x, y] = [y, x];
```

- #### 从函数返回多个值

```
// 返回一个数组

function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```

- #### 无序给函数传参

```
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```

- #### 提取 JSON 数据

```
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```

- #### for…of…

```
for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// 获取键名
for (let [key] of map) {
  // ...
}

// 获取键值
for (let [,value] of map) {
  // ...
}
```

- #### require 模块时候

```js
const { SourceMapConsumer, SourceNode } = require("source-map");
```



### 形参默认值

```js
Vue.filter('dateString', function (value, format='YYYY-MM-DD HH:mm:ss') {  return moment(value).format(format);})
```



# -----html

### image

[alt] :alt 属性指定图像不能正常显示（网速慢、图片链接错误）后显示的替换文本。