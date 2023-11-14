# cookie

[js-cookie](https://github.com/js-cookie/js-cookie)

dotenv **是一个 NPM 包，其作用为将`.env`文件解析为 json 对象，并对其中的 key-value 对通过`process.env`将其赋值为环境变量。之后便可通过`process.env[key]`，eg: `process.env.DB_HOST`获取环境变量。**

# ajax

[axios](https://github.com/axios/axios)

# office

## pdf.js

[pdf.js](https://github.com/mozilla/pdf.js.git)

兼容 IE 的版本`2.0.943   # npm i pdfjs-dist@2.0.943`

## excel

### sheetjs

[源码地址](https://github.com/SheetJS/sheetjs)
[文档](https://docs.sheetjs.com/docs/)

### exceljs

[文档](https://github.com/exceljs/exceljs/blob/master/README_zh.md)
[源码地址](https://github.com/exceljs/exceljs)

### luckysheet

[luckysheet]() 在线填报,暂不支持导入\导出\打印
[luckyExcel]() luckysheet 的插件,用于支持 excel 导入

# 兼容

## babel

# jsonp

https://juejin.cn/post/6992130637167591432

https://www.npmjs.com/package/vue-jsonp

```vue
npm install vue-jsonp -S
<script>
Vue.use(VueJsonp)
/*现在你可以在Vue的组件中使用 this.$jsonp 发送 jsonp 请求了，因为Vue.use(VueJsonp)里把 $jsonp 赋给 vue 原型了：Vue.prototype.$jsonp = jsonp
例如请求百度地图接口：*/
this.$jsonp("https://apis.map.qq.com/ws/place/v1/suggestion", {
	region: "杭州",
	callbackQuery: "cb",
	callbackName: "jsonp_func",
}).then(res => {
	// ...
})
</script>

sed -i 's/5e3/3/g' node_modules\vue-jsonp\dist\index.esm.js
```

# json

## 表格与 json 互转

[Vue JSON Schema Form](https://vue-json-schema-form.lljj.me/zh/guide/#%E6%9A%B4%E9%9C%B2%E6%96%B9%E6%B3%95)

# 加解密

## blueimp-md5(32 位 MD5)

[MD5](https://www.npmjs.com/package/blueimp-md5)

npm install blueimp-md5

```js
import * as md5 from "blueimp-md5"
var hash = md5("value") // "2063c1608d6e0baf80249c42e2be5804"
```

## sha256

`npm install --save sha256`
https://www.npmjs.com/package/sha256
https://github.com/cryptocoinjs/sha256

# 代码检查

[eslint]() 代码检查与格式化
[prettier]() 代码格式化

[ESLint 之与 Prettier 配合使用](https://juejin.cn/post/6924568874700505102#heading-12)

# 图表

## echarts

# canvas

- EaselJS 使制作游戏、创作类艺术和其他侧重图形项目更容易的开源 canvas 库
- Fabric.js 具有 SVG 解析功能的开源 canvas 库
- heatmap.js 基于 canvas 的热点图的开源库
- JavaScript InfoVis Toolkit 创建交互式的 2D Canvas 数据可视化
- Konva.js 用于桌面端和移动端应用的 2D canvas 库
- p5.js 包含给艺术家、设计师、教育者、初学者使用的完整的 canvas 绘制功能
- Paper.js 运行于 HTML5 Canvas 上的开源矢量图形脚本框架
- Phaser 用于基于 Canvas 和 WebGL 的浏览器尤其的快速、自由、有趣的开源框架
- Processing.js 用于处理可视化语言
- Pts.js 在 canvas 和 SVG 中进行创意性代码写作和可视化的库
- Rekapi 关键帧动画库
- Scrawl-canvas 用来创建和编辑 2D 图形的开源库
- ZIM 框架为 canvas 上的代码创意性提供方便性、组件和可控性，包括可用性和数百个色彩缤纷的教程

# 拖动

## vuedraggable

[源码地址](https://github.com/SortableJS/Vue.Draggable)

```html
npm i vuedraggable@2.23.2 // 适用于 vue2
<!-- 
        draggable:
    
        属性:
            value: 绑定的数组
            list: 绑定数组,与 value 用法相似,但不能同时使用
            handle=".handle" 使列表元素中符合选择器的元素成为拖动的手柄，只有按住拖动手柄才能使列表元素进行拖动；
            filter: selector 格式为简单 css 选择器的字符串，定义哪些列表元素不能进行拖放，可设置为多个选择器，中间用“,”分隔
            draggable：selector 格式为简单 css 选择器的字符串，定义哪些列表元素可以进行拖放
            chosenClass：selector 格式为简单 css 选择器的字符串，当选中列表元素时会给该元素增加一个 class；
            forceFallback：boolean 如果设置为 true 时，将不使用原生的 html5 的拖放，可以修改一些拖放中元素的样式等；
            fallbackClass：string 当 forceFallback 设置为 true 时，拖放过程中鼠标附着元素的样式；
            scroll：boolean 默认为 true，当排序的容器是个可滚动的区域，拖放可以引起区域滚动。
            :group="{ name: 'people', pull: 'clone', put: false }"
              pull：pull 用来定义从这个列表容器移动出去的设置，true/false/'clone'/function
                true:列表容器内的列表元素可以被移出；
                false：列表容器内的列表元素不可以被移出；
                clone：列表元素移出，移动的为该元素的副本；
                function：用来进行 pull 的函数判断，可以进行复杂逻辑，在函数中 return false/true 来判断是否移出；
            ghost-class=".ghost" 影子样式
            :sort="false" 拖拽排序
            tag="div"  用来指定 draggable 组件渲染出来的 html 标签
            :clone="(original) => { return original;}" 函数,
            :move="null" 函数,两个参数,evt,event;evt可以获取移动的元素; 返回false,不可拖动,返回true,可拖动
            :componentData="{ //该props用于将附加信息传递给 tag props声明的子组件。
              on: { 
                change: this.handleChange,
                input: this.inputChanged
              },
              attrs:{
                wrap: true
              },
              props: {
                value: this.activeNames
              }
            }"
         事件:
            start, add, remove, update, end, choose, unchoose, sort, filter, clone
            @start="func(e)" 拖动前执行
            @end="func(e)" 拖动结束执行
            @change({removed,added,moved}) 
              removed,moved 触发顺序: start->change->end 
              added :只触发change
        1. 两个 group.name 相同的 draggable 组件可以互相拖动, 绑定的数组发生变化

        v-bind 绑定 draggable 组件的配置项，可以看看具体讲解：

group：string or object
string：命名，用处是为了设置可以拖放容器时使用
object: {name, pull, put}
name: 同 string 的方法
pull：pull 用来定义从这个列表容器移动出去的设置，true/false/'clone'/function
true:列表容器内的列表元素可以被移出；
false：列表容器内的列表元素不可以被移出；
clone：列表元素移出，移动的为该元素的副本；
function：用来进行 pull 的函数判断，可以进行复杂逻辑，在函数中 return false/true 来判断是否移出；
put：put 用来定义往这个列表容器放置列表元素的的设置，true/false/['foo','bar']/function
true:列表容器可以从其他列表容器内放入列表元素；
false：与 true 相反；
['foo','bar']：这个可以是一个字符串或者是字符串的数组，代表的是 group 配置项里定义的 name 值；
function：用来进行 put 的函数判断，可以进行复杂逻辑，在函数中 return false/true 来判断是否放入
animation: number 单位：ms，定义动画的时间；
disabled: boolean 定义此 sortable 对象是否可用，为 true 时 sortable 对象不能拖放排序等功能，为 false 时为可以进行排序，相当于一个开关；
ghostClass：selector 格式为简单 css 选择器的字符串，当拖动列表元素时会生成一个副本作为影子元素来模拟被拖动元素排序的情况，此配置项就是来给这个影子元素添加一个 class，我们可以通过这种方式来给影子元素进行编辑样式；
sort: boolean 定义是否列表元素是否可以在列表容器内进行拖拽排序；
delay: number 定义鼠标选中列表元素可以开始拖动的延迟时间；
handle: selector 格式为简单 css 选择器的字符串，使列表元素中符合选择器的元素成为拖动的手柄，只有按住拖动手柄才能使列表元素进行拖动；
filter: selector 格式为简单 css 选择器的字符串，定义哪些列表元素不能进行拖放，可设置为多个选择器，中间用“,”分隔
draggable：selector 格式为简单 css 选择器的字符串，定义哪些列表元素可以进行拖放
chosenClass：selector 格式为简单 css 选择器的字符串，当选中列表元素时会给该元素增加一个 class；
forceFallback：boolean 如果设置为 true 时，将不使用原生的 html5 的拖放，可以修改一些拖放中元素的样式等；
fallbackClass：string 当 forceFallback 设置为 true 时，拖放过程中鼠标附着元素的样式；
scroll：boolean 默认为 true，当排序的容器是个可滚动的区域，拖放可以引起区域滚动。
-->
<template>
	<div>
		<draggable class="flex-demo" v-model="myArray" group="people" @start="drag = true" @end="drag = false">
			<div class="drag-div1" v-for="element in myArray" :key="element.id">{{ element.name }}</div>
			<button slot="footer" @click="addPeople">Add2</button>
			<button slot="header" @click="addPeople">Add1</button>
		</draggable>
		<draggable class="flex-demo" v-model="myArray1" group="people" @start="drag = true" @end="drag = false">
			<div class="drag-div1" v-for="element in myArray1" :key="element.id">{{ element.name }}</div>
		</draggable>
	</div>
</template>

<script>
	import draggable from "vuedraggable"
	export default {
		name: "DraggableTest",
		components: {
			draggable,
		},
		data() {
			return {
				myArray: [
					{ id: 0, name: 0 },
					{ id: 1, name: 1 },
					{ id: 2, name: 2 },
					{ id: 3, name: 3 },
				],
				myArray1: [
					{ id: 0, name: 0 },
					{ id: 1, name: 1 },
					{ id: 2, name: 2 },
					{ id: 3, name: 3 },
				],
			}
		},
		methods: {
			addPeople() {},
		},
	}
</script>

<style scoped>
	.flex-demo {
		display: flex;
	}
	.drag-div1 {
		border: 5px solid red;
		padding: 2px;
		margin: 2px;
		border-radius: 5px;
		width: 30px;
		height: 20px;
	}
</style>
```

# markdown

[vditor](https://github.com/Vanessa219/vditor) 在线及时编辑

# 打印

[vue-print-nb]() 底层实现便是我们常见的 window.print 方法
