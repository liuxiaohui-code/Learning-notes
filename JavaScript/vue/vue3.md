# vue2 与 vue3 的区别

1. 对于**生命周期**来说，整体上变化不大，只是大部分生命周期钩子**名称上 + “on”**，功能上是类似的。不过有一点需要注意，Vue3 在组合式 API（Composition API，下面展开）中使用生命周期钩子时**需要先引入**，而 Vue2 在选项 API（Options API）中可以直接调用生命周期钩子
   1. beforeCreate setup(默认)
   2. created setup(默认)
   3. beforeMount ---onBeforeMount
   4. mounted ---onMounted
   5. beforeUpdate ---onBeforeUpdate
   6. updated ---onUpdated
   7. beforeDestroy---onBeforeUnmount
   8. destroyed ---onUnmounted
2. vue3 template 支持**多根节点**
3. **Vue2 是选项 API（Options API）**，一个逻辑会散乱在文件不同位置（data、props、computed、watch、生命周期钩子等），导致代码的可读性变差。当需要修改某个逻辑时，需要上下来回跳转文件位置。**Vue3 组合式 API（Composition API）**则很好地解决了这个问题，可将同一逻辑的内容写到一起，增强了代码的可读性、内聚性，其还提供了较为完美的逻辑复用性方案
4. **异步组件（Suspense）**,Vue3 提供 Suspense 组件，允许程序在等待异步组件加载完成前渲染兜底的内容，如 loading ，使用户的体验更平滑。使用它，需在模板中声明，并包括两个命名插槽：default 和 fallback。Suspense 确保加载完异步内容时显示默认插槽，并将 fallback 插槽用作加载状态。
5. Vue3 提供 **Teleport** 组件可将部分 DOM 移动到 Vue app 之外的位置。比如项目中常见的 Dialog 弹窗。
6. Vue2 **响应式原理**基础是 **Object.defineProperty**,无法监听对象或数组新增、删除的元素,通过监听方法 push 等解决;Vue3 响应式原理基础是 **Proxy**。
7. Vue3 相比于 Vue2，虚拟 DOM 上增加 **patchFlag** 字段。
   ```js
   // patchFlags 字段类型列举
   export const enum PatchFlags {
     TEXT = 1,   // 动态文本内容
     CLASS = 1 << 1,   // 动态类名
     STYLE = 1 << 2,   // 动态样式
     PROPS = 1 << 3,   // 动态属性，不包含类名和样式
     FULL_PROPS = 1 << 4,   // 具有动态 key 属性，当 key 改变，需要进行完整的 diff 比较
     HYDRATE_EVENTS = 1 << 5,   // 带有监听事件的节点
     STABLE_FRAGMENT = 1 << 6,   // 不会改变子节点顺序的 fragment
     KEYED_FRAGMENT = 1 << 7,   // 带有 key 属性的 fragment 或部分子节点
     UNKEYED_FRAGMENT = 1 << 8,   // 子节点没有 key 的fragment
     NEED_PATCH = 1 << 9,   // 只会进行非 props 的比较
     DYNAMIC_SLOTS = 1 << 10,   // 动态的插槽
     HOISTED = -1,   // 静态节点，diff阶段忽略其子节点
     BAIL = -2   // 代表 diff 应该结束
   }
   ```
8. Vue3 的 **cacheHandler** 可在第一次渲染后缓存我们的事件。相比于 Vue2 无需每次渲染都传递一个新函数。加一个 click 事件。
9. **Diff 算法优化**,结合上文与源码，patchFlag 帮助 diff 时区分静态节点，以及不同类型的动态节点。一定程度地减少节点本身及其属性的比对。
10. 打包优化 Tree-shaking：模块打包 webpack、rollup 等中的概念。移除 JavaScript 上下文中未引用的代码。主要依赖于 import 和 export 语句，用来检测代码模块是否被导出、导入，且被 JavaScript 文件使用。
11. Vue3 由 **TypeScript** 重写，相对于 Vue2 有更好的 TypeScript 支持。

# 项目创建

```s
# vite
npm init vue@3
# vue-cli@^4.5
vue create <项目名>
```

# main.js

1. 新增工厂函数 createApp

```js
//引入的不再是Vue构造函数了，引入的是一个名为createApp的工厂函数
import { createApp } from "vue"
import App from "./App.vue"

//创建应用实例对象——app(类似于之前Vue2中的vm，但app比vm更“轻”)
const app = createApp(App)

//挂载
app.mount("#app")
```

# setup 与 ref 与 reactive

1. setup
   1. 一个新的配置项,值为函数,使用传统的`export default { setup(){return{} } }`
   2. 组合式 api 定义的舞台,数据方法都写到其中
   3. 返回值有两种:
      1. 对象,对象的属性和方法可以在 template 中直接使用(常用)
      2. 渲染函数,`import {h} from 'vue';return ()=> h('h1','尚硅谷')` (了解)
   4. setup 不能用 async 修饰; 可以使用,但是需要使用 `defineAsyncComponent` 配合引入
   5. 在`beforeCreate`之前执行一次,其`this`为 undefined
   6. 参数
      1. props: 值为对象,包含:组件外部传递过来,且组件内部声明接收了的属性
      2. context: 上下文对象
         1. attrs: 值为对象,包含:组件外都传递过来,但没有在 `props` 配置中声明的属性,相当于`this.$attrs`
         2. slots: 收到的插槽内容,相当于`this.$slots`
         3. emit: 分发自定义事件的函数,相当于`this.$emit`,需要通过新增配置项`emits`声明
2. vue3 兼容 vue2 写法,但是不建议混用
   1. vue2 可以访问 vue3 中的属性,但 vue3 中 setup 函数的值为 undefined
   2. 如果有重名,按代码顺序,后者生效
3. ref 响应式修改数据,vue3 新增函数 `ref(value):RefImpl{...,value}`
   1. `import {ref} from 'vue'`
   2. `let x = ref(10) `
   3. 响应式修改`x.value = 11`,
   4. 在模板中不用`.value`,模板使用`RefImpl`对象直接使用其`.value`
   5. 响应式原理,非对象或数组:`Object.defineProperty`,对象或数组`Proxy`---求助`reactive`函数,
4. reactive 响应式修改对象类型数据,vue3 新增函数 `reactive(object):Proxy`
   1. `import {reactive} from 'vue'`
   2. `let x = ref({x:1})`
   3. 修改`x.x = 2`
   4. template 使用`x.x`
5. 响应式原理:
   1. 通过 Proxy(代理):拦截对象中任意属性的变化,属性的读|写|添加|删除
   2. 通过 Reflect(反射)对被代理对象的属性进行操作

```js
//模拟Vue3中实现响应式
//#region
//源数据
let person = {
  name: "张三",
  age: 18,
}
const p = new Proxy(person, {
  //有人读取p的某个属性时调用
  get(target, propName) {
    console.log(`有人读取了p身上的${propName}属性`)
    return Reflect.get(target, propName)
  },
  //有人修改p的某个属性、或给p追加某个属性时调用
  set(target, propName, value) {
    console.log(`有人修改了p身上的${propName}属性，我要去更新界面了！`)
    Reflect.set(target, propName, value)
  },
  //有人删除p的某个属性时调用
  deleteProperty(target, propName) {
    console.log(`有人删除了p身上的${propName}属性，我要去更新界面了！`)
    return Reflect.deleteProperty(target, propName)
  },
})
//#endregion
```

# computed

1. vue3 同样支持 vue2 中的写法,但不建议使用
2. `import {computed} from 'vue'`,
3. `let fullname = computed(function|object)`computed 是一个函数,参数可以是回调函数(简写),也可以是对象,与 vue2 相同

# watch

1. vue3 同样支持 vue2 中的写法,但不建议使用
2. `import {watch} from 'vue'`
3. `watch(prop,监视的回调:function,配置项:{immediate|deep}`
4. 同时监视多个 `watch([a,b],function)`
5. 监视 ref 普通属性,不用`.value`;是一个对象,可以使用`{deep:true}`开启深度监视
6. 监视 reactive 所定义的一个响应式数据
   1. 注意: 此处无法正确获取 oldValue
   2. 注意: 强制开启了深度监视(deep 配置无效)
   3. 注意: 监视 reactive 内部的一个属性时
      1. 普通类型: 写法 `watch(()=>person.name)`
      2. 对象或数组类型,deep 有效,`watch(()=>person.obj)`,但是 oldValue 仍然没有用

# watchEffect

1. `import {watchEffect} from 'vue'`,函数
2. 不用指明监视哪个属性,监视的回调中用到哪个属性,那就监视哪个属性
3. 与 computed 有些像,但是不注重返回值
4. 只有一个参数,是一个无参回调函数

```js
watchEffect(() => {
  const x1 = sum.value
  const x2 = person.job.j1.salary
  console.log("watchEffect所指定的回调执行了")
})
```

# 生命周期

```js
vue2            ---vue3(vue2写法相同,但是名称有变)           ---setup(需要引入)
beforeCreate    ---beforeCreate   ---setup(默认)
created         ---created        ---setup(默认)
beforeMount     ---onBeforeMount  ---on~
mounted         ---onMounted      ---on~
beforeUpdate    ---onBeforeUpdate ---on~
updated         ---onUpdated      ---on~
beforeDestroy   ---beforeUnmount  ----onBeforeUnmount
destroyed       ---unmounted      ---onUnmounted
```

# 自定义 hook

1. hook 是一个函数,把 setup 函数中使用的 Composition API 进行了封装
2. 类似于 vue2 中的 mixin
3. 新建文件夹 src/hooks,文件名一般为 useXXXX.js

```js
// usePoint.js
import { reactive, onMounted, onBeforeUnmount } from "vue"
export default function () {
  //实现鼠标“打点”相关的数据
  let point = reactive({
    x: 0,
    y: 0,
  })

  //实现鼠标“打点”相关的方法
  function savePoint(event) {
    point.x = event.pageX
    point.y = event.pageY
    console.log(event.pageX, event.pageY)
  }

  //实现鼠标“打点”相关的生命周期钩子
  onMounted(() => {
    window.addEventListener("click", savePoint)
  })

  onBeforeUnmount(() => {
    window.removeEventListener("click", savePoint)
  })

  return point
}
```

使用:

```vue
<template>
  <h2>我是Test组件</h2>
  <h2>当前点击时鼠标的坐标为：x：{{ point.x }}，y：{{ point.y }}</h2>
</template>

<script>
import usePoint from "../hooks/usePoint"
export default {
  name: "Test",
  setup() {
    const point = usePoint()
    return { point }
  },
}
</script>
```

# toRef

1. 创建一个 ref 对象,其 value 值指向另一个对象中的某个属性
2. `const name = toRef(person,'name')`
3. 应用: 要将响应式对象中的某个属性单独提供给外部使用时
4. `toRefs`与`toRef`功能一致,但是可以批量创建多个 ref 对象,语法:`toRefs(person)`

```js
import { ref, reactive, toRef, toRefs } from "vue"
export default {
  name: "Demo",
  setup() {
    //数据
    let person = reactive({
      name: "张三",
      age: 18,
      job: {
        j1: {
          salary: 20,
        },
      },
    })

    // const name1 = person.name
    // console.log('%%%',name1)

    // const name2 = toRef(person,'name')
    // console.log('####',name2)

    const x = toRefs(person)
    console.log("******", x)

    //返回一个对象（常用）
    return {
      person,
      // name:toRef(person,'name'),
      // age:toRef(person,'age'),
      // salary:toRef(person.job.j1,'salary'),
      ...toRefs(person),
    }
  },
}
```

# 其他组合式 API

## shallowReactive 和 shallowRef

1. shallowReactive 浅层响应式,只考虑对象类型的第一层响应式,
2. shallowRef 基本类型不变,不去处理对象类型的响应式

## readonly 和 shallowReadonly

1. readonly 只读,不允许修改
2. shallowReadonly 只读,不允许修改对象类型的第一层数据,深层不管

```js
let sum = ref(0)
let person = reactive({
  name: "张三",
  age: 18,
  job: {
    j1: {
      salary: 20,
    },
  },
})

person = readonly(person)
// person = shallowReadonly(person)
// sum = readonly(sum)
// sum = shallowReadonly(sum)
```

## toRaw 和 markRaw

1. toRaw 将一个由 reactive 生成的响应式对象转为普通对象,对这个普通对象的所有操作,不会引起页面更新
2. markRow 标记一个对象,使其永远不会再成为响应式对象,用于有些值不应该被设置为响应式的,比如复杂的第三方类库等;或者用于渲染具有不可变数据源的大列表时,跳过响应式转换可以提高性能

## customRef

1. customRef 创建一个自定义的 ref,并对其依赖项跟踪和更新触发进行显式控制
2. 参数
   1. 回调函数
      1. 参数
         1. track
         2. trigger
      2. 函数体
         1. get(){track();return value}
         2. set(newValue){value = newValue;trigger()}

```vue
<template>
  <input type="text" v-model="keyWord" />
  <h3>{{ keyWord }}</h3>
</template>

<script>
import { ref, customRef } from "vue"
export default {
  name: "App",
  setup() {
    //自定义一个ref——名为：myRef
    function myRef(value, delay) {
      let timer
      return customRef((track, trigger) => {
        return {
          get() {
            console.log(`有人从myRef这个容器中读取数据了，我把${value}给他了`)
            track() //通知Vue追踪value的变化（提前和get商量一下，让他认为这个value是有用的）
            return value
          },
          set(newValue) {
            console.log(`有人把myRef这个容器中数据改为了：${newValue}`)
            clearTimeout(timer) // 防止抖动
            timer = setTimeout(() => {
              value = newValue
              trigger() //通知Vue去重新解析模板
            }, delay)
          },
        }
      })
    }

    // let keyWord = ref('hello') //使用Vue提供的ref
    let keyWord = myRef("hello", 500) //使用程序员自定义的ref

    return { keyWord }
  },
}
</script>
```

## provide 和 inject

1. 实现祖孙组件间通信
2. 使用: 父组件有一个 `provide(key:string,value:any)`来提供数据,后代组件有一个 `inject(key:string):any` 来使用这些数据

## 响应式数据的判断

1. `isRef(value):boolean` 检查一个值是否为 ref 对象
2. `isReactive(value):boolean` 检查一个对象是否由 reactive 创建的响应式代理
3. `isReadonly(value):boolean` 检查一个对象是否由 readonly 方法创建的代理
4. `isProxy(value):boolean` 检查一个对象是否由 reactive 或 readonly 方法创建的代理

# 新组件

## fragment

1. 在 vue2 中组件必须有一个根标签
2. 在 vue3 中,组件可以没有根标签,内部会将多个标签包含在一个 Fragment 虚拟元素中
3. 好处:减少标签层级,减少内存占用

## Teleport

Vue3 提供 **Teleport** 组件可将部分 DOM 移动到 Vue app 之外的位置。比如项目中常见的 Dialog 弹窗。

```html
<!-- to 为 css选择器 -->
<teleport to="body"> ... </teleport>
```

## Suspense 异步组件

1. **异步组件（Suspense）**,Vue3 提供 Suspense 组件，允许程序在等待异步组件加载完成前渲染兜底的内容，如 loading ，使用户的体验更平滑。使用它，需在模板中声明，并包括两个命名插槽：`default` 和 `fallback`。Suspense 确保加载完异步内容时显示默认插槽，并将 fallback 插槽用作加载状态。
2. `const Child = defineAsyncComponent(() => import("./components/Child"))` 异步引入组件,可以使 `setup` 返回 `Promise` 对象

```vue
<template>
  <div class="app">
    <h3>我是App组件</h3>
    <Suspense>
      <template v-slot:default>
        <Child />
      </template>
      <template v-slot:fallback>
        <h3>稍等，加载中...</h3>
      </template>
    </Suspense>
  </div>
</template>

<script>
// import Child from './components/Child'//静态引入
import { defineAsyncComponent } from "vue"
const Child = defineAsyncComponent(() => import("./components/Child")) //异步引入
export default {
  name: "App",
  components: { Child },
}
</script>

<style>
.app {
  background-color: gray;
  padding: 10px;
}
</style>

<template>
  <div class="child">
    <h3>我是Child组件</h3>
    {{ sum }}
  </div>
</template>

<script>
import { ref } from "vue"
export default {
  name: "Child",
  async setup() {
    let sum = ref(0)
    let p = new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve({ sum })
      }, 3000)
    })
    return await p
  },
}
</script>

<style>
.child {
  background-color: skyblue;
  padding: 10px;
}
</style>
```

# script-setup 语法糖
1. 语法启用: `script`标签添加属性`setup`
2. 作用: 里面的代码会被编译成组件 `setup()` 函数的内容。
3. 不再有返回值, 声明的顶层的绑定 (包括`变量`，`函数声明`，以及 `import` 导入的方法|自定义组件) 都能在模板中直接使用
4. 由于组件是通过变量引用而不是基于字符串组件名注册的，在` <script setup>` 中要使用**动态组件**的时候，应该使用动态的 `:is` 来绑定
5. 递归组件:使用文件名
6. 组件冲突--重命名`import { FooBar as FooBarChild } from './components'`
7. 命名空间组件`import * as Form from './form-components'`可以使用`.`来使用:`<Form.Label>label</Form.Label>`
8. 自定义指令:全局指令可以直接使用;setup内自定义需要遵守规范:1.不需要显式注册;2.命名必须类似`vNameOfDirective`,如果指令是从别处导入的，可以通过重命名`as`来使其符合命名规范
9. 为了在声明 `props` 和 `emits` 选项时获得完整的类型推导支持，我们可以使用 `defineProps` 和 `defineEmits` API，它们将自动地在 `<script setup>` 中可用
10. `defineExpose(object)`显式暴露属性给父组件
11. 使用 `slots` 和 `attrs` 的情况应该是相对来说较为罕见的，因为可以在模板中直接通过 $slots 和 $attrs 来访问它们。在你的确需要使用它们的罕见场景中，可以分别用 `useSlots()` 和 `useAttrs()` 两个辅助函数;需要引入
12. `<script setup>` 可以和普通的 `<script>` 一起使用。
13. 使用顶层 `await`,结果代码会被编译成 `async setup()`,必须与 Suspense 内置组件组合使用
```vue
<script setup>
// 变量
const msg = 'Hello!'

// 函数
function log() {
  console.log(msg)
}
console.log("hello script setup")
</script>
```

# 其他

## 全局 API 改变

| 2.x 全局 API(Vue)        | 3.x 实例 API(app)           |
| ------------------------ | --------------------------- |
| Vue.config.xxx           | app.config.xxx              |
| Vue.config.productionTip | 移除                        |
| Vue.componet             | app.componet                |
| Vue.directive            | app.directive               |
| Vue.mixin                | app.mixin                   |
| Vue.use                  | app.use                     |
| Vue.prototype            | app.config.globalProperties |

## 其他改变

1. data 始终被声明为一个函数
2. 过度类名的更改 v-enter 名字改为 v-enter-from
3. 移除 keyCode 作为 v-on 的修饰符,同时也不再支持 config.keyCodes
4. 移除 v-on.native 修饰符
5. 移除过滤器 filter
