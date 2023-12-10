# 开始使用

https://v2.cn.vuejs.org/

## 网页调试工具 vue-tools

https://devtools.vuejs.org/guide/installation.html

## 一般页面使用 vue $el

```html
<script src="./vue.js"></script>
<script src="./vue.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
开发环境
<script src="https://cdn.jsdelivr.net/npm/vue@2"></script>
生产环境 ...

<div id="app">
	<!-- 插值语法 -->
	{{ message }}
</div>

<script>
	var app = new Vue({
		el: "#app", // 绑定元素
		data: {
			message: "Hello Vue!", //数据
		},
	})

	app.$el == document.getElementById("app") // true
</script>
```

## node 环境使用 单页面 vue

```js
//引入Vue
import Vue from "vue"
//引入根组件 App
import App from "./App.vue"

//创建vm
new Vue({
	el: "#app", //绑定元素
	render: h => h(App), // 根组件
})

new Vue({
	render: h => h(App),
}).$mount("#app") // 绑定元素
```

# template 可调用数据和方法

## 基础数据 data $data

```javascript
{
  // 在 vm 内可以通过 this 直接使用
  data(){ // data == $data true
    return {
      message:"",
    }
  },
  // 也可以是对象,但在但在node环境报错
}

// 原理数据代理

// 通过 Object.defineProperty()把 data 对象中所有属性添加到 vm 上。
// 为每一个添加到 vm 上的属性，都指定一个 getter/setter。
// 在 getter/setter 内部去操作（读/写）data 中对应的属性。
```

## 计算属性 computed

```javascript
{
  computed:{
    key1: {
      get(){... return value1},
      set(value1){ ... } //如果计算属性要被修改，那必须写 set 函数去响应修改，且 set 中要引起计算时依赖的数据发生改变。
    },
    key2: function(){  //只读不改，即只有get()，可以简写
      return value2;
    }
  }
}
```

计算属性是基于它们的响应式依赖进行缓存的, 只要基于的值不变,多次使用而不用再次计算;

## 侦听属性 watch $watch

```javascript
{
  watch: {
      //监视属性
    key1: {  // key1 为data里声明的属性
      //immediate 初始化时让handler调用一下，默认false
      immediate: true,
      //deep 深度监视
      //	(1).Vue中的watch默认不监测对象内部值的改变（一层）
      //  (2).配置deep:true可以监测对象内部值改变（多层）。
      //备注：
      //  (1).Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以！
      //	(2).使用watch时根据数据的具体结构，决定是否采用深度监视。
      deep:true,
      //handler 操作属性函数，
        // 参数： 1.newValue：改变后；  2.oldValue：改变前
        // 没有返回值
      handler(newValue, oldValue){......}
    },
  },
  created(){
    /**
     *  参数1,监听对象,表达式字符串或函数,当表达式或函数返回值变动时触发
     *  参数2,回调函数
     *  参数3,配置对象{immediate: true,deep:true,}
     *
     * 返回值: 函数,用来取消监听
     */
    var unwatch = this.$watch() // 监听对象
    // 之后取消观察
    unwatch()
  }

}

```

## 函数方法 methods

```javascript
{
  methods:{
    func1(){},
  }
}
```

## 响应式原理--数据挟持

1. 原理 `Object.defineProperty`,使用 getter 和 setter 监听修改
2. 缺陷,
   1. 无法对添加\删除属性响应,解决: `this.$set()|$delete()`和`Vue.set()|delete()`操作属性的删除和添加
   2. 无法对数组索引修改响应,解决:重写了修改数组的方法`push()|pop()|shift()|unshift()|splice()|sort()|reverse()`

```js
//源数据
let person = {
	name: "张三",
	age: 18,
}

//模拟Vue2中实现响应式
//#region
let p = {}
Object.defineProperty(p, "name", {
	configurable: true,
	get() {
		//有人读取name时调用
		return person.name
	},
	set(value) {
		//有人修改name时调用
		console.log("有人修改了name属性，我发现了，我要去更新界面！")
		person.name = value
	},
})
Object.defineProperty(p, "age", {
	get() {
		//有人读取age时调用
		return person.age
	},
	set(value) {
		//有人修改age时调用
		console.log("有人修改了age属性，我发现了，我要去更新界面！")
		person.age = value
	},
})
//#endregion
```

## template 调用显示响应式数据

1. 可用数据和方法
   1. data|computed|methods|this 的属性方法
   2. 内置全局变量`Infinity,undefined,NaN,isFinite,isNaN,`
   3. 全局方法`parseFloat,parseInt,decodeURI,decodeURIComponent,encodeURI,encodeURIComponent,require`
   4. 全局工具类`Math,Number,Date,Array,Object,Boolean,String,RegExp,Map,Set,JSON,Intl`
2. 使用方法
   1. 插值语法,`{{数据}}`,放在 在 html 内容中,可以将数据渲染为文本
   2. 指令语句,例如: `v-bind:value="数据"`

# 组件

组件嵌套是以树的方式组合, 一般的,定义根组件 `APP`

## 单文件多组件(不推荐)

```javascript
// 定义全局组件
/**
 * param1, string 组件名
 * param2, object 组件配置项,与 vue 配置项相似,但需要配置 template 属性
 *  template, string,组件 html 模板
 */
Vue.component('todo-item', {
  template: `<li>这是个待办项</li>`
})
/**
 * 定义局部组件
 */
const vc = Vue.extend({
  template: ``,...
})

// 引用 VUE
var app = new Vue({
  el:'#app',
  components:{
    'v-vc':vc
  }
})
```

```html
<!-- todo-item 父组件的 作用域中使用子组件 -->
<div>
	<!-- 全局组件 -->
	<todo-item></todo-item>
	<!-- 局部组件 -->
	<v-vc></v-vc>
</div>
```

## 单文件单组件

### 使用

**1. 编写子组件**

每个组件必须只有一个根元素

```vue
<template>
	<div></div>
</template>
<script>
export default {
	// 省略 new Vue()
	name: "Child",
}
</script>
<style></style>
```

**2. 父组件使用子组件**

```vue
<template>
	<div id="app">
		<!-- 2.3. 使用子组件 -->
		<Child></Child>
	</div>
</template>
<script>
// 2.1 引入子组件
import Child from "./Child.vue"
export default {
	// 省略 new Vue()
	name: "App",
	// 2.2. 注册组组件
	components: {
		Child,
	},
}
</script>
<style></style>
```

### 内置关系

1. 一个重要的内置关系：`VueComponent.prototype.__proto__ === Vue.prototype`

2. 为什么要有这个关系：让组件实例对象（vc）可以访问到 Vue 原型上的属性、方法。

### 获取组件实例通过 ref

用在标签上： `ref=""` 使用`this.$refs[ref].方法/属性`调用该组件的属性和方法
用在创建时 options: `name:''` 使用 vue-tools 时显示的组件名

### 组件自调用

```vue
<template>
	<div>
		<v-header></v-header>
	</div>
</template>
<script>
export default {
	name: "v-header", // 定义组件名后,可以通过名自调用
}
</script>
```

## 组件通信

### props 父<->子

props 传递的属性会默认添加到根元素,可以通过`inheritAttrs: false`取消.注意 inheritAttrs: false 选项不会影响 style 和 class 的绑定。
这尤其适合配合实例的 `$attrs` property 使用，该 property 包含了传递给一个组件的 attribute 名和 attribute 值

1. 功能：让组件接收外部传过来的数据
2. 传递数据：`<Demo name="xxx"/>` 数据项不以 v-bind 的方式绑定，将只会传递字符串，而不解析表达式
3. 接收数据：
   1. 第一种方式（只接收）：`props:['name'] `
   2. 第二种方式（限制类型）：`props:{name:String}`
   3. 第三种方式（限制类型、限制必要性、指定默认值）：
      `js props:{ name:{ type:String, //类型 required:true, //必要性 default:'老王' //默认值 } } `
      > 备注：props 是只读的，Vue 底层会监测你对 props 的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制 props 的内容到 data 中一份，然后去修改 data 中的数据。
4. 不接受,属性可以通过`$attrs`获取,但是不推荐,且一旦使用`props`接收,`$attrs`中属性会消失

```js
{
  props:["属性名"],
  props:{
    property_name:property_type, // String,Number,Array,Object,
  }
  props:{
    property_name:{
      type: property_type,
      required:true | false ,
      default:默认值,
    }
  }
}
```

**组件通信**

> 用于向子组件传递数据：
> ​ 正常使用即可
> 用于向父组件传递数据：
> ​ 1.父：向子组件传递一个函数，函数的回调在父组件中 `<Child :func1='func1'></Child> ...... methods:{func1(value){处理子组件传递的参数}}`  
> ​ 2.子：`props:[func1],......,使用func1将数据以参数的形式写入func1中传递给父组件`

### 组件自定义事件 子->父

推荐你始终使用 kebab-case 的事件名

1. 一种组件间通信的方式，适用于：子组件 ===> 父组件
2. 使用场景：A 是父组件，B 是子组件，B 想给 A 传数据，那么就要在 A 中给 B 绑定自定义事件（事件的回调在 A 中）。
3. 绑定自定义事件：
   1. 第一种方式，在父组件中：`<Demo @atguigu="test"/>` 或 `<Demo v-on:atguigu="test"/>`
   2. 第二种方式，在父组件中：
      ```js
      <Demo ref="demo"/>
      ......
      mounted(){
         this.$refs.demo.$on('atguigu',this.test)
      }
      ```
   3. 若想让自定义事件只能触发一次，可以使用`once`修饰符，或`$once`方法。
4. 触发自定义事件：在子组件中,`this.$emit('atguigu',数据)`
5. 解绑自定义事件: `this.$off('atguigu')`
6. 组件上也可以绑定原生 DOM 事件，需要使用`native`修饰符。
7. 注意：通过`this.$refs.xxx.$on('atguigu',回调)`绑定自定义事件时，回调要么配置在 methods 中，要么用箭头函数，否则 this 指向会出问题！

```html
<base-input v-on:focus.native="onFocus"></base-input>
<!-- 子组件内容如下:  此时是不生效的,因为根元素label没有focus事件 -->
<label>
	{{ label }}
	<input v-bind="$attrs" v-bind:value="value" v-on:input="$emit('input', $event.target.value)" />
</label>
<!-- 为了解决这个问题，Vue 提供了一个 $listeners property，它是一个对象，里面包含了作用在这个组件上的所有监听器。 -->
```

### 全局事件总线

1. 一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。
2. 安装全局事件总线：
   ```js
   new Vue({
   	......
   	beforeCreate() {
   		Vue.prototype.$bus = this //安装全局事件总线，$bus就是当前应用的vm
   	},
       ......
   })
   ```
3. 使用事件总线：

   1. 接收数据：A 组件想接收数据，则在 A 组件中给$bus 绑定自定义事件，事件的<span style="color:red">回调留在 A 组件自身。</span>
      ```js
      methods(){
        demo(data){......}
      }
      ......
      mounted() {
        this.$bus.$on('xxxx',this.demo)
      }
      ```
   2. 提供数据：`this.$bus.$emit('xxxx',数据)`

4. 最好在 beforeDestroy 钩子中，用$off 去解绑<span style="color:red">当前组件所用到的</span>事件。

### 消息订阅与发布

1.  一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。

2.  使用步骤：

    1.  安装 pubsub：`npm i pubsub-js`

    2.  引入: `import pubsub from 'pubsub-js'`

    3.  接收数据：A 组件想接收数据，则在 A 组件中订阅消息，订阅的<span style="color:red">回调留在 A 组件自身。</span>

        ```js
        methods(){
          demo(data){......}
        }
        ......
        mounted() {
          this.pid = pubsub.subscribe('xxx',this.demo) //订阅消息
        }
        ```

    4.  提供数据：`pubsub.publish('xxx',数据)`

    5.  最好在 beforeDestroy 钩子中，用`PubSub.unsubscribe(pid)`去<span style="color:red">取消订阅。</span>

### $root $parent

在每个 `new Vue` 实例的子组件中，其根实例可以通过 `$root` property 进行访问。
和 `$root` 类似，`$parent` property 可以用来从一个子组件访问父组件的实例。它提供了一种机会，可以在后期随时触达父级组件，以替代将数据以 `prop` 的方式传入子组件的方式。
尽管存在 prop 和事件，有的时候你仍可能需要在 JavaScript 里直接访问一个子组件。为了达到这个目的，你可以通过 ref 这个 attribute 为子组件赋予一个 ID 引用,然后`$refs.ref_name`获取子组件实例

### 依赖注入 provide 与 inject

```js
<google-map>
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>

// google-map 内部,增加属性
provide: function () {
  return {
    getMap: this.getMap
  }
}
// 然后在任何后代组件里,我们都可以使用 inject 选项来接收指定的我们想要添加在这个实例上的 property
inject: ['getMap'],
// 之后即可在后代组件中使用 getMap
```

### .sync 修饰符

sync 修饰符,是父子组件双向绑定的语法糖,其实是做了两步动作：
1、声明传的数据`visible`
2、声明`@update:visible`事件

```Vue
<template>
  <el-dialog :visible.sync="visible">

  </el-dialog>
</template>

<!-- 在组件 el-dialog 内部,可以调用自定义事件 update:visible 来修改父组件传递过来的值 -->
<script>
{
  mounted(){
    this.$emit('update:visible', false); // 将父组件传过来的visible值改为false
  }
}
</script>
```

## 绑定 js 原生事件: `.native`

Vue 提供了一个 `$listeners` property，它是一个对象，里面包含了作用在这个组件上的所有监听器。
可以通过 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的子元素

## 动态组件

可以通过 Vue 的 `<component>` 元素加一个特殊的 `is` attribute 来实现动态组件

```HTML

<!-- 组件会在 `currentTabComponent` 改变时改变 -->
<component v-bind:is="currentTabComponent"></component>

在上述示例中，currentTabComponent 可以包括

已注册组件的名字，或一个组件的选项对象

<table>
  <tr is="blog-post-row"></tr>
</table>
```

## keep-alive 组件缓存

```HTML
<!-- 常用在动态组件上,失活的组件将会被缓存！-->
<!-- 当切换组件时,已缓存的组件可以直接使用而不会重新渲染 -->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>

```

## 异步组件

```js
Vue.component("async-example", function (resolve, reject) {
	setTimeout(function () {
		// 向 `resolve` 回调传递组件定义
		resolve({
			template: "<div>I am async!</div>",
		})
	}, 1000)
})
```

## 递归组件

组件是可以在它们自己的模板中调用自身的。不过它们只能通过 name 选项来做这件事.

```Vue
<template>
  <m-demo></m-demo>
</template>
<script>
export default{
  name:"m-demo"
}
</script>
```

当你使用 Vue.component 全局注册一个组件时，这个全局的 ID 会自动设置为该组件的 name 选项。

# 生命周期钩子

1. vue 初始化
2. `beforeCreate`
3. data methods computed watched
4. `created`
5. 相关的 render 函数首次被调用（虚拟 DOM）,实例已完成以下的配置： 编译模板，把 data 里面的数据和模板生成 html，完成了 el 和 data 初始化，注意此时还没有挂在 html 到页面上。
6. `beforeMount`
7. 挂在完成，也就是模板中的 HTML 渲染到 HTML 页面中，此时一般可以做一些 ajax 操作，mounted 只会执行一次。这时 `el` 被新创建的 `vm.$el` 替换了。
8. `mounted`
9. `beforeUpdate`在数据更新之前被调用，发生在虚拟 DOM 重新渲染和打补丁之前，可以在该钩子中进一步地更改状态，不会触发附加地重渲染过程
10. `updated` 在由于数据更改导致地虚拟 DOM 重新渲染和打补丁只会调用，调用时，组件 DOM 已经更新，所以可以执行依赖于 DOM 的操作，然后在大多是情况下，应该避免在此期间更改状态，因为这可能会导致更新无限循环，该钩子在服务器端渲染期间不被调用
11. `beforeDestory`在实例销毁之前调用，实例仍然完全可用，这一步还可以用 this 来获取实例，一般在这一步做一些重置的操作，比如清除掉组件中的定时器 和 监听的 dom 事件
12. `destoryed` 在实例销毁之后调用，调用后，所以的事件监听器会被移出，所有的子实例也会被销毁，该钩子在服务器端渲染期间不被调用

13. `activated` 被 keep-alive 缓存的组件激活时调用。**该钩子在服务器端渲染期间不被调用。**当组件在 `<keep-alive>` 内被切换，它的 activated 和 deactivated 这两个生命周期钩子函数将会被对应执行。
14. `deactivated` 被 keep-alive 缓存的组件失活时调用。**该钩子在服务器端渲染期间不被调用。**
15. `errCapured`

    - **类型**：`(err: Error, vm: Component, info: string) => ?boolean`
      在捕获一个来自后代组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 `false` 以阻止该错误继续向上传播。你可以在此钩子中修改组件的状态。因此在捕获错误时，在模板或渲染函数中有一个条件判断来绕过其它内容就很重要；不然该组件可能会进入一个无限的渲染循环。

    **错误传播规则**

    - 默认情况下，如果全局的 `config.errorHandler` 被定义，所有的错误仍会发送它，因此这些错误仍然会向单一的分析服务的地方进行汇报。
    - 如果一个组件的 inheritance chain (继承链)或 parent chain (父链)中存在多个 `errorCaptured` 钩子，则它们将会被相同的错误逐个唤起。
    - 如果此 `errorCaptured` 钩子自身抛出了一个错误，则这个新错误和原本被捕获的错误都会发送给全局的 `config.errorHandler`。
    - 一个 `errorCaptured` 钩子能够返回 `false` 以阻止错误继续向上传播。本质上是说“这个错误已经被搞定了且应该被忽略”。它会阻止其它任何会被这个错误唤起的 `errorCaptured` 钩子和全局的 `config.errorHandler`。

## $nextTick

1. 语法：`this.$nextTick(回调函数)`
2. 作用：在下一次 DOM 更新结束后执行其指定的回调。
3. 什么时候用：当改变数据后，要基于更新后的新 DOM 进行某些操作时，要在 nextTick 所指定的回调函数中执行。

```js
handleEdit(todo){
				if(todo.hasOwnProperty('isEdit')){
					todo.isEdit = true
				}else{
					// console.log('@')
					this.$set(todo,'isEdit',true)
				}
				this.$nextTick(function(){
					this.$refs.inputTitle.focus()
				})
```

# 指令

1. `v-once` 数据只渲染一次,数据改变后不再次渲染
2. `v-bind` 数据单向绑定,数据->界面,简写 `:value=""`
3. `v-model` 数据双向绑定,只能用在元素的 value 属性上,可以省略 value`v-model=''` 多个复选框 value 填显示,绑定数组,
   1. `<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">`
   2. `.lazy` change 事件之后同步数据
   3. `.number` 将输入转为数字
   4. `.trim` 去除字符串首尾空白字符
4. `v-html` 将文本渲染为 html,并放到标签体中,`<span v-html="rawHtml"></span>`
5. `v-if` , 根据表达式的值的 true|false 来有条件地渲染元素。在切换时元素及它的数据绑定 / 组件被销毁并重建。如果元素是 `<template>`，将提出它的内容作为条件块。当条件变化时该指令触发过渡效果。可以使用 key 属性不在复用属性
6. `v-else-if`
7. `v-else`
8. `v-for`
9. `v-on` @
10. `v-show` ,根据表达式之真假值，切换元素的 `display` CSS property。当条件变化时该指令触发过渡效果。`display: none`

从 2.6.0 开始，可以用方括号括起来的 JavaScript 表达式作为一个指令的参数

```javascript
//动态参数预期会求出一个字符串，异常情况下值为 null。这个特殊的 null 值可以被显性地用于移除绑定。任何其它非字符串类型的值都将会触发一个警告。
// 使用没有空格或引号的表达式，或用计算属性替代这种复杂表达式。

// 在 DOM 中使用模板时 (直接在一个 HTML 文件里撰写模板)，还需要避免使用大写字符来命名键名，因为浏览器会把 attribute 名全部强制转为小写：
<a v-bind:[attributeName]="url"> ... </a>
//
data(){
  return {
    attributeName:'herf'
  }
}
```

## v-model

```html
<input v-model="searchText" />
等价于：

<input v-bind:value="searchText" v-on:input="searchText = $event.target.value" />

当用在组件上时，v-model 则会这样：

<custom-input v-bind:value="searchText" v-on:input="searchText = $event"></custom-input>

为了让它正常工作，这个组件内的 <input /> 必须： 将其 value attribute 绑定到一个名叫 value 的 prop 上 在其 input 事件被触发时，将新的值通过自定义的
input 事件抛出 Vue.component('custom-input', { props: ['value'], template: `
<input v-bind:value="value" v-on:input="$emit('input', $event.target.value)" />
` })
```

## v-on

1. 使用 v-on:xxx 或 @xxx 绑定事件，其中 xxx 是事件名；
2. 事件的回调需要配置在 methods 对象中，最终会在 vm 上；
3. methods 中配置的函数，不要用箭头函数！否则 this 就不是 vm 了；
4. methods 中配置的函数，都是被 Vue 所管理的函数，this 的指向是 vm 或 组件实例对象
5. `@click="demo"` 和 `@click="demo($event)"` 效果一致，但后者可以传参

```html
<!-- $event 变量名固定,但参数顺序不固定 -->
<botton v-on:click="func($event)"></botton>
<botton @click="func"></botton>
<!--绑定事件的时候：@xxx="yyy" yyy可以写一些简单的语句-->
<botton @click="x++"></botton>
<!-- 对象语法 -->
<button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
```

为子组件绑定事件后,在子组件中可以使用`this.$emit()`触发事件

```js
<子组件 @on-click="xxx"></子组件> 可以通过$event获取事件的第一个参数,或参数作为函数的参数

在子组件中可以使用
this.$emit('on-click',参数)
调用
```

## 事件修饰符

Vue 中的事件修饰符：
1.prevent：阻止默认事件（常用）；
2.stop：阻止事件冒泡（常用）；
3.once：事件只触发一次（常用）；
4.capture：使用事件的捕获模式；
5.self：只有 event.target 是当前操作的元素时才触发事件；
6.passive：事件的默认行为立即执行，无需等待事件回调执行完毕；

> 可以连用，但有先后顺序 .prevent.stop :先取消默认事件，再阻止冒泡

不要把 .passive 和 .prevent 一起使用，因为 .prevent 将会被忽略，同时浏览器可能会向你展示一个警告。请记住，.passive 会告诉浏览器你不想阻止事件的默认行为。

```html
<div class="box1" @click.capture="showMsg(1)"></div>
```

## 键盘事件修饰符

1. 按键别名

回车 => `.enter`
删除 => `.delete` (捕获“删除”和“退格”键)
退出 => `.esc`
空格 => `.space`
换行 => `.tab` (特殊，必须配合 keydown 去使用)
上 => `.up`
下 => `.down`
左 => `.left`
右 => `.right`

> Vue 未提供别名的按键，可以使用按键原始的 key 值去绑定，但注意要转为 kebab-case（短横线命名）

系统修饰键（用法特殊）：`.ctrl`、`.alt`、`.shift`、`.meta`

(1).配合 keyup 使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发。
(2).配合 keydown 使用：正常触发事件。

> 可以指定特定的按键 @keyup.ctrl.y=‘’,释放 ctrl+y 触发事件

可以使用 keyCode 去指定具体的按键（不推荐）Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名

2. `.exact` 修饰符允许你控制由精确的系统修饰符组合触发的事件。

## 鼠标修饰符

.left
.right
.middle

```html
<input type="text" placeholder="按下回车提示输入" @keydown.huiche="showInfo" />
```

## v-for

```js
//遍历数组
v-for="(arr,index) in array" :key="index"
//遍历对象
v-for="(value, name, index) in object" :key="name"
//遍历字符串
v-for="(char,index) in str" :key="index"
//遍历指定次数
v-for="(number,index) in 5" :key="index"
//遍历五次，number:1-5  index:0-5
```

> 当和 `v-if` 一起使用时，`v-for` 的优先级比 `v-if` 更高

## 自定义指令 directives

### 局部指令

```js
new Vue({
	directives: {
		//完整的配置对象
		bindy: {
			//指令与元素成功绑定时（一上来）
			bind(element, binding) {
				element.value = binding.value
			},
			//指令所在元素被插入页面时
			inserted(element, binding) {
				element.focus()
			},
			//指令所在的模板被重新解析时
			update(element, binding) {
				element.value = binding.value
			},
		},
		//简写的配置对象，相当于存在相同函数体的bind和update
		bindx(element, binding) {
			// 使用 <span v-bindy='exp'></span>
			//没有返回值
			element.innerHTML = binding.value // element:指令所在的HTML元素  span
			// binding:object 绑定值 exp表达式的值
		},
	},
})
```

### 全局指令

```js
Vue.directive(指令名, 配置对象)
或
Vue.directive(指令名, 回调函数)
```

# 样式

## 绑定 class 样式

语法 `class="原有样式" :class="表达式"` 渲染后结果： `class="原有样式 表达式"`

```html
<!-- 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定 -->
<div class="basic" :class="mood"></div>

<!-- 绑定class样式--数组写法，适用于：要绑定的样式个数不确定、名字也不确定 -->
<div class="basic" :class="classArr"></div>
classArr:['atguigu1','atguigu2','atguigu3'],

<!-- 绑定class样式--对象写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用 -->
<div class="basic" :class="classObj"></div>
classObj:{ atguigu1:false, atguigu2:false, }
```

## 绑定 style 样式

```html
1.绑定style样式--对象写法
<div class="basic" :style="styleObj">{{name}}</div>
styleObj: { fontSize: '40px', //连字符换驼峰 color: 'red', }, 2.绑定style样式--数组写法
<div class="basic" :style="styleArr">{{name}}</div>
styleArr: [ { fontSize: '40px', color: 'blue', }, { backgroundColor: 'gray' } ] 从 2.3.0 起你可以为 style 绑定中的 property
提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 display: flex。
```

## scoped

作用：让样式在局部生效，防止冲突。

**scoped 子组件渗透**

1. `>>>`
2. `/deep/`
3. `:deep(.class_name){}`

```html
<style scoped>
	>>> .a {
	}
	/deep/ .a {
	}
	:deep(.a) {
	}
</style>
```

## lang

样式语言：css less
默认为 css

## 过渡

Vue 提供了 transition 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡

条件渲染 (使用 v-if)
条件展示 (使用 v-show)
动态组件
组件根节点

1. 作用：在插入、更新或移除 DOM 元素时，在合适的时候给元素添加样式类名。
2. 图示：![Transition Diagram](../../photo/transition.png)
3. 写法：
   1. 准备好样式：
      - 元素进入的样式：
        1. v-enter：进入的起点,元素插入之前生效
        2. v-enter-active：进入过程中
        3. v-enter-to：进入的终点
      - 元素离开的样式：
        1. v-leave：离开的起点
        2. v-leave-active：离开过程中
        3. v-leave-to：离开的终点
4. 如果你使用一个没有名字的 `<transition>`，则 `v-` 是这些类名的默认前缀。如果你使用了 `<transition name="my-transition">`，那么 `v-enter` 会替换为 `my-transition-enter`。

### css

```css
/*动画*/
.v-enter-active {
	animation: atguigu 0.5s linear;
}

.v-leave-active {
	animation: atguigu 0.5s linear reverse;
}

@keyframes atguigu {
	from {
		transform: translateX(-100%);
	}
	to {
		transform: translateX(0px);
	}
}

/*过渡*/
/* 进入的起点、离开的终点 */
.v-enter,
.v-leave-to {
	transform: translateX(-100%);
}
.v-enter-active,
.v-leave-active {
	transition: 0.5s linear;
}
/* 进入的终点、离开的起点 */
.v-enter-to,
.v-leave {
	transform: translateX(0);
}
```

### transition

1.  使用`<transition>`包裹要过度的元素，并配置 name 属性：

```vue
<transition name="hello" appear>
  <h1 v-show="isShow">你好啊！</h1>
</transition>
```

3.  备注：若有多个元素需要过度，则需要使用：`<transition-group>`，且每个元素都要指定`key`值。

```VUE
<transition-group name="hello" appear tag='ul'>
需要注意的是使用 FLIP 过渡的元素不能设置为 display: inline 。作为替代方案，可以设置为 display: inline-block 或者放置于 flex 中
  <h1 v-show="isShow" key='1'>你好啊！</h1>
  <h1 v-show="isShow" key='2'>你好啊！</h1>
  <h1 v-show="isShow" key='3'>你好啊！</h1>
</transition-group>
<style>
.hello-move {
  transition: transform 1s;
}
</style>
```

`<transition-group>` 组件还有一个特殊之处。不仅可以进入和离开动画，还可以改变定位。要使用这个新功能只需了解新增的 `v-move` class，它会在元素的改变定位的过程中应用。像之前的类名一样，可以通过 name attribute 来自定义前缀，也可以通过 move-class attribute 手动设置。

4.  属性说明

    1. `name=''` 设置后更改样式类选择器的名字，使之一一对应。 如果你使用一个没有名字的 `<transition>`，则 `v-` 是这些类名的默认前缀。如果你使用了 `<transition name="my-transition">`，那么 `v-enter` 会替换为 `my-transition-enter`。

    2. `appear `是一个布尔值，添加后进入页面就激活进入动画
    3. `duration` 定制一个显性的过渡持续时间 (以毫秒计),

       1. `:duration="1000"`
       2. `:duration="{ enter: 500, leave: 800 }"` 定制进入和移出的持续时间

    4. 过渡类名

       - `enter-class`
       - `enter-active-class`
       - `enter-to-class` (2.1.8+)
       - `leave-class`
       - `leave-active-class`
       - `leave-to-class` (2.1.8+)

       优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 [Animate.css](https://daneden.github.io/animate.css/) 结合使用十分有用

    5. 过渡模式( `<transition>` 的默认行为 - 进入和离开同时发生。)
       1. 同时生效的进入和离开的过渡不能满足所有要求，所以 Vue 提供了过渡模式
       2. `in-out`：新元素先进行过渡，完成之后当前元素过渡离开。
       3. `out-in`：当前元素先进行过渡，完成之后新元素过渡进入。
       4. `mode="out-in"`

### 第三方库

    1. https://animate.style/


## 用在组件上 class

在组件上添加 class 属性时,如

```html
<v-h class="a b c"></v-h>
<!-- v-h 根  -->
<div class="e f"></div>

<!-- 渲染最终结果 -->
class="a b c e f"
```

## css 获取 vm 属性值

```css
* {
	background-color: v-bind(color); /* color 为 vm.color */
}
```

## vm 获取 css 属性值

```vue
<style module>
.red {
	/* 可以通过 vm.$color.red 获取*/
	color: green;
}
</style>
<style module="class1">
.red {
	/* 可以通过 vm.class1.red */
	color: green;
}
</style>
```

如果是在 script 中使用，可以使用 useCssModule

```js
import { useCssModule } from "vue"
const classes1 = useCssModule("classes1")
const classes2 = useCssModule("classes2")
```

# 全局配置

```js
//取消 Vue 所有的日志与警告。
Vue.config.silent = true

//指定组件的渲染和观察期间未捕获错误的处理函数。这个处理函数被调用时，可获取错误信息和 Vue 实例。
Vue.config.errorHandler = function (err, vm, info) {
  // handle error
  // `info` 是 Vue 特定的错误信息，比如错误所在的生命周期钩子
  // 只在 2.2.0+ 可用
}

//为 Vue 的运行时警告赋予一个自定义处理函数。注意这只会在开发者环境下生效，在生产环境下它会被忽略。
Vue.config.warnHandler = function (msg, vm, trace) {
  // `trace` 是组件的继承关系追踪
}

//须使 Vue 忽略在 Vue 之外的自定义元素 (e.g. 使用了 Web Components APIs)。否则，它会假设你忘记注册全局组件或者拼错了组件名称，从而抛出一个关于 Unknown custom element 的警告。
Vue.config.ignoredElements=Array<string | RegExp>

//给 v-on 自定义键位别名。
Vue.config.keyCodes = {
  v: 86,
  f1: 112,
  // camelCase 不可用
  mediaPlayPause: 179,
  // 取而代之的是 kebab-case 且用双引号括起来
  "media-play-pause": 179,
  up: [38, 87]
}

//设置为 true 以在浏览器开发工具的性能/时间线面板中启用对组件初始化、编译、渲染和打补丁的性能追踪。只适用于开发模式和支持 performance.mark API 的浏览器上。
Vue.config.performance = true

//设置为 false 以阻止 vue 在启动时生成生产提示。
Vue.config.productionTip = false

```

# mixin 混入

1. 功能：可以把多个组件共用的配置提取成一个混入对象

2. 使用方式：

   第一步定义混合：

   ```js
   //mixin.js
   export const x = {
       data(){....},
       methods:{....}
       ....
   }
   ```

   第二步使用混入：

   ​ 全局混入：`Vue.mixin(xxx)`
   ​ 局部混入：`mixins:['xxx'] `

```js
import {x} from './mixin.js'
//全局混入,在main.js vm上方设置全局配置，在vm,app,以及所有子vc内生效
Vue.mixin(x)


//局部混入
export default {
  ...
  mixins:['x']
}
```

> 混入原则：
>
>     1. data 混入，属性合并
>     1. data 冲突，以自身data为主
>     1. 生命周期钩子冲突，先执行自身钩子方法，在执行混入的钩子

# 插件

1. 功能：用于增强 Vue

2. 本质：包含 install 方法的一个对象，install 的第一个参数是 Vue 构造函数，第二个以后的参数是插件使用者传递的数据。

```js
{
  install = function (Vue, options) {
      // 1. 添加全局过滤器
      Vue.filter(....)

      // 2. 添加全局指令
      Vue.directive(....)

      // 3. 配置全局混入(合)
      Vue.mixin(....)

      // 4. 添加实例方法
      Vue.prototype.$myMethod = function () {...}
      Vue.prototype.$myProperty = xxxx
  }
}

```

1. 使用插件：`Vue.use()`

# slot 插槽

作用：让父组件可以向子组件指定位置插入 html 结构，也是一种组件间通信的方式，适用于 父组件 ===> 子组件。

在组件内部添加 `<slot></slot>` 标签,标签所在即为插槽.

推荐使用`v-slot`属性替代` scope``slot-scope``slot `属性;跟 `v-on` 和 `v-bind` 一样，`v-slot` 也有缩写，即把参数之前的所有内容 (`v-slot:`) 替换为字符 `#`。然而，和其它指令一样，该缩写只在其有参数的时候才可用,`#default`

如果没有设置插槽,子组件可以通过 vc 上的`$slots`属性获取虚拟节点

### 默认插槽

```html
<zuJian>
	<!--  将插入组件zujian内的插槽<slot></slot>,做一个替换 -->
	<div>....</div>
</zuJian>

<!-- 组件 zujian-->
<template>
	<div>
		<slot>在这里可以写入默认信息，当有外来标签插入时，显示外来的；当没有外来传入时，使用默认的内容</slot>
	</div>
</template>
```

### 具名插槽 slot 属性

具有名字的插槽

应用场景：单组件多插槽，使用默认插槽时，多个插槽都解析；

使用： 特殊属性`slot` ; 使用 template 可以使用 `v-slot:name`

```html
<zuJian>
	<!--  1. slot -->
	<div slot="name1">....</div>
	<!-- 2. v-slot -->
	<template v-slot:name1></template>
</zuJian>

<!-- 组件 zujian-->
<template>
	<div>
		<slot name="name1">在这里可以写入默认信息，当有外来标签插入时，显示外来的；当没有外来传入时，使用默认的内容</slot>
	</div>
</template>
```

### 作用域插槽

理解：数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定。

```HTML
<zujian>
  <!-- 1. template scope-->
	<template scope="obj">
    obj是一个由插槽传入数据的对象，{value1:'',value2:'',...}
  </template>
  <!-- 2. slot-scope -->
  <template slot-scope="obj">
    obj是一个由插槽传入数据的对象，{value1:'',value2:'',...}
  </template>
  <!-- 3. v-slot -->
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</zujian>

<!--zujian -->
<slot :key1="value1" :key2="value2"></slot>
```

# 渲染函数 render

Vue 推荐在绝大多数情况下使用模板来创建你的 HTML。然而在一些场景中，你真的需要 JavaScript 的完全编程的能力。这时你可以用渲染函数，它比模板更接近编译器。

```js
Vue.component("anchored-heading", {
	render: function (createElement) {
		return createElement(
			"h" + this.level, // 标签名称
			this.$slots.default // 子节点数组
		)
	},
	props: {
		level: {
			type: Number,
			required: true,
		},
	},
})

//createElement 到底会返回什么呢？其实不是一个实际的 DOM 元素。它更准确的名字可能是 createNodeDescription，因为它所包含的信息会告诉 Vue 页面上需要渲染什么样的节点，包括及其子节点的描述信息。我们把这样的节点描述为“虚拟节点 (virtual node)”，也常简写它为“VNode”。“虚拟 DOM”是我们对由 Vue 组件树建立起来的整个 VNode 树的称呼。
```

## createElement

```js
// @returns {VNode}
createElement(
	// {String | Object | Function}
	// 一个 HTML 标签名、组件选项对象，或者
	// resolve 了上述任何一种的一个 async 函数。必填项。
	"div",

	// {Object}
	// 一个与模板中 attribute 对应的数据对象。可选。
	{
		// (详情见下一节)
	},

	// {String | Array}
	// 子级虚拟节点 (VNodes)，由 `createElement()` 构建而成，
	// 也可以使用字符串来生成“文本虚拟节点”。可选。
	[
		"先写一些文字",
		createElement("h1", "一则头条"),
		createElement(MyComponent, {
			props: {
				someProp: "foobar",
			},
		}),
	]
)
```
