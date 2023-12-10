# 概念

[速查手册](https://jquery.cuishifeng.cn/)
[官网](https://jquery.com/)

jQuery 是一个 JavaScript 函数库。jQuery 是一个轻量级的"写的少，做的多"的 JavaScript 库。

jQuery 库包含以下功能：

- HTML 元素选取
- HTML 元素操作
- CSS 操作
- HTML 事件函数
- JavaScript 特效和动画
- HTML DOM 遍历和修改
- AJAX
- Utilities
  > 提示： 除此之外，jQuery 还提供了大量的插件。

# 安装

## 版本

咱们课程中选择使用 1.11.1 的版本。

## 下载

jQuery 有两个版本：生成环境使用的和开发测试环境使用的。
Production version - 用于实际的网站中，已被精简和压缩。
Development version - 用于测试和开发（未压缩，是可读的代码）
以上两个版本都可以从 jquery.com 中下载。
大家也可以直接使用我们提供好的下载内容。点击这里下载

# 使用

## 引入

jQuery 库是一个 JavaScript 文件，我们可以直接在 HTML 页面中通过 script 标签引用它，跟引用自己的外部 JavaScript 脚本文件一样的语法。

```html
<head>
  <script src="jquery-1.11.1.js"></script>
</head>
```

jQuery 版本有很多，分为 1.x 2.x 3.x

1.x 版本：能够兼容 IE678 浏览器
2.x 版本：不兼容 IE678 浏览器
1.x 和 2.x 版本 jquery 都不再更新版本了，现在只更新 3.x 版本。
3.x 版本：不兼容 IE678，更加的精简（在国内不流行，因为国内使用 jQuery 的主要目的就是兼容 IE678）
国内多数网站还在使用 1.x 的版本

## 基础语法

```js
$(selector).action()
/*
说明：美元符号定义 jQuery
选择符（selector）"查询"和"查找" HTML 元素
jQuery 的 action() 执行对元素的操作
*/
```

## 入口函数

文档就绪事件，实际就是文件加载事件。在 DOM 加载完成后才可以对 DOM 进行操作。

```js
$(document).ready(function () {
  // jQuery代码
})

//简写
$(function () {
  // jQuery代码
})
```

jQuery 的 ready 方法与 JavaScript 中的 onload 相似，但是也有区别：
`window.onload `
执行次数: 只能执行一次，如果执行第二次，第一次的执行会被覆盖
执行时机:必须等待网易全部加载挖完毕（包括图片等），然后再执行包裹的代码
`$(document).ready()`
执行次数:可用执行多次，不会覆盖之前的执行
执行时机:只需要等待网页中的 DOM 结果加载完毕就可以执行包裹的代码

# 选择器

## 基本

`#id`
`element`
`.class`
`*`
`selector1,selector2,selectorN`

## 层级

`ancestor descendant`
`parent > child`
`prev + next`
`prev ~ siblings`

## 基本筛选器

`:first`
`:not(selector)`
`:even`
`:odd`
`:eq(index)`
`:gt(index)`
`:lang1.9+`
`:last`
`:lt(index)`
`:header`
`:animated`
`:focus`
`:root1.9+`
`:target1.9+`

## 内容

`:contains(text)`
`:empty`
`:has(selector)`
`:parent`

## 可见性

`:hidden`
`:visible`

## 属性

`[attribute]`
`[attribute=value]`
`[attribute!=value]`
`[attribute^=value]`
`[attribute$=value]`
`[attribute*=value]`
`[attrSel1][attrSel2][attrSelN]`

## 子元素

`:first-child`
`:first-of-type `1.9+
`:last-child`
`:last-of-type `1.9+
`:nth-child`
`:nth-last-child() `1.9+
`:nth-last-of-type()`1.9+
`:nth-of-type()`1.9+
`:only-child`
`:only-of-type`1.9+

## 表单

`:input`
`:text`
`:password`
`:radio`
`:checkbox`
`:submit`
`:image`
`:reset`
`:button`
`:file`

## 表单对象属性

`:enabled`
`:disabled`
`:checked`
`:selected`

## 混淆选择器

`$.escapeSelector(selector)`3.0+

# 核心

## jQuery 核心函数

`jQuery([sel,[context]])`
`jQuery(html,[ownerDoc])`1.8\*
`jQuery(callback)`
`jQuery.holdReady(hold)`3.2-
`jQuery.readyException( error )`3.1+

## jQuery 对象访问

`each(callback)`
`size()`1.8-
`length`
`selector`
`context`
`get([index])`
`index([selector|element])`

## 数据缓存

`data([key],[value])`
`removeData([name|list])`1.7\*
`$.data(ele,[key],[val])`1.8-

## 队列控制

`queue(e,[q])`
`dequeue([queueName])`
`clearQueue([queueName])`

## 插件机制

`jQuery.fn.extend(object)`
`jQuery.extend(object)`

## 多库共存

`jQuery.noConflict([ex])`

# ajax

## ajax 请求

`$.ajax(url,[settings])`
`$.get(url,[data],[fn],[type])`
`$.getJSON(url,[data],[fn])`
`$.getScript(url,[callback])`
`$.post(url,[data],[fn],[type])`

## ajax 事件

`ajaxComplete(callback)`
`ajaxError(callback)`
`ajaxSend(callback)`
`ajaxStart(callback)`
`ajaxStop(callback)`
`ajaxSuccess(callback)`

## 其它

`load(url,[data],[callback])`
`$.ajaxPrefilter([type],fn)`
`$.ajaxSetup([options])`
`serialize()`
`serializeArray()`

# 属性

## 属性

`attr(name|pro|key,val|fn)`
`removeAttr(name)`
`prop(n|p|k,v|f)`
`removeProp(name)`

## CSS 类

`addClass(class|fn)`
`removeClass([class|fn])`
`toggleClass(class|fn[,sw])`

## HTML 代码/文本/值

`html([val|fn])`
`text([val|fn])`
`val([val|fn|arr])`

# CSS

## CSS

`css(name|pro|[,val|fn])`1.9\*
`jQuery.cssHooks`

## 位置

`offset([coordinates])`
`position()`
`scrollTop([val])`
`scrollLeft([val])`

## 尺寸

`height([val|fn])`
`width([val|fn])`
`innerHeight()`
`innerWidth()`
`outerHeight([options])`
`outerWidth([options])`

# 文档处理

## 内部插入

`append(content|fn)`
`appendTo(content)`
`prepend(content|fn)`
`prependTo(content)`

## 外部插入

`after(content|fn)`
`before(content|fn)`
`insertAfter(content)`
`insertBefore(content)`

## 包裹

`wrap(html|ele|fn)`
`unwrap()`
`wrapAll(html|ele)`
`wrapInner(html|ele|fn)`

## 替换

`replaceWith(content|fn)`
`replaceAll(selector)`

## 删除

`empty()`
`remove([expr])`
`detach([expr])`

## 复制

`clone([Even[,deepEven]])`

# 事件

## 页面载入

`ready(fn)`

## 事件处理

`on(eve,[sel],[data],fn)`1.7+
`off(eve,[sel],[fn])`1.7+
`bind(type,[data],fn)`3.0-
`one(type,[data],fn)`
`trigger(type,[data])`
`triggerHandler(type, [data])`
`unbind(t,[d|f])`3.0-

## 事件委派

`live(type,[data],fn)`1.7-
`die(type,[fn])`1.7-
`delegate(s,[t],[d],fn)`3.0-
`undelegate([s,[t],fn])`3.0-

## 事件切换

`hover([over,]out)`
`toggle([spe],[eas],[fn])`1.9\*

## 事件

`blur([[data],fn])`
`change([[data],fn])`
`click([[data],fn])`
`dblclick([[data],fn])`
`error([[data],fn])`1.8-
`focus([[data],fn])`
`focusin([data],fn)`
`focusout([data],fn)`
`keydown([[data],fn])`
`keypress([[data],fn])`
`keyup([[data],fn])`
`mousedown([[data],fn])`
`mouseenter([[data],fn])`
`mouseleave([[data],fn])`
`mousemove([[data],fn])`
`mouseout([[data],fn])`
`mouseover([[data],fn])`
`mouseup([[data],fn])`
`resize([[data],fn])`
`scroll([[data],fn])`
`select([[data],fn])`
`submit([[data],fn])`
`unload([[data],fn])`1.8-

# 效果

## 基本

`show([s,[e],[fn]])`
`hide([s,[e],[fn]])`
`toggle([s],[e],[fn])`

## 滑动

`slideDown([s],[e],[fn])`
`slideUp([s,[e],[fn]])`
`slideToggle([s],[e],[fn])`

## 淡入淡出

`fadeIn([s],[e],[fn])`
`fadeOut([s],[e],[fn])`
`fadeTo([[s],o,[e],[fn]])`
`fadeToggle([s,[e],[fn]])`

## 自定义

`animate(p,[s],[e],[fn])`1.8*
`stop([c],[j])`1.7*
`delay(d,[q])`
`finish([queue])`1.9+

## 设置

jQuery.fx.off
jQuery.fx.interval3.0-

# 工具

## 浏览器及特性检测

`$.support`1.9-
`$.browser`1.9-
`$.browser.version`
`$.boxModel`

## 数组和对象操作

`$.each(object,[callback])`
`$.extend([d],tgt,obj1,[objN])`
`$.grep(array,fn,[invert])`
`$.sub()`1.9-
`$.when(deferreds)`
`$.makeArray(obj)`
`$.map(arr|obj,callback)`
`$.inArray(val,arr,[from])`
`$.toArray()`
`$.merge(first,second)`
`$.unique(array)`3.0-
`$.uniqueSort(array)`3.0+
`$.parseJSON(json)`3.0-
`$.parseXML(data)`

## 函数操作

`$.noop`
`$.proxy(function,context)`

## 测试操作

`$.contains(c,c)`
`$.type(obj)`
`$.isArray(obj)`3.2-
`$.isFunction(obj)`3.3-
`$.isEmptyObject(obj)`
`$.isPlainObject(obj)`
`$.isWindow(obj)`3.3-
`$.isNumeric(value)`1.7+

## 字符串操作

`$.trim(str)`

## URL

`$.param(obj,[traditional])`

## 插件编写

`$.error(message)`
`$.fn.jquery`

# 筛选

## 过滤

`eq(index|-index)`
`first()`
`last()`
`hasClass(class)`
`filter(expr|obj|ele|fn)`
`is(expr|obj|ele|fn)`
`map(callback)`
`has(expr|ele)`
`not(expr|ele|fn)`
`slice(start,[end])`

## 查找

`children([expr])`
`closest(e|o|e)`1.7\*
`find(e|o|e)`
`next([expr])`
`nextAll([expr])`
`nextUntil([e|e][,f])`
`offsetParent()`
`parent([expr])`
`parents([expr])`
`parentsUntil([e|e][,f])`
`prev([expr])`
`prevAll([expr])`
`prevUntil([e|e][,f])`
`siblings([expr])`

## 串联

`add(e|e|h|o[,c])`1.9\*
`andSelf()`1.8-
`addBack()`1.9+
`contents()`
`end()`

## 事件对象

`eve.currentTarget`
`eve.data`
`eve.delegateTarget`1.7+
`eve.isDefaultPrevented()`
`eve.isImmediatePropag...()`
`eve.isPropagationStopped()`
`eve.namespace`
`eve.pageX`
`eve.pageY`
`eve.preventDefault()`
`eve.relatedTarget`
`eve.result`
`eve.stopImmediatePro...()`
`eve.stopPropagation()`
`eve.target`
`eve.timeStamp`
`eve.type`
`eve.which`

## 延迟对象

`def.done(d,[d])`
`def.fail(failCallbacks)`
`def.isRejected()`1.7-
`def.isResolved()`1.7-
`def.reject(args)`
`def.rejectWith(c,[a])`
`def.resolve(args)`
`def.resolveWith(c,[a])`
`def.then(d[,f][,p])`1.8\*
`def.promise([ty],[ta])`
`def.pipe([d],[f],[p])`1.8-
`def.always(al,[al])`
`def.notify(args)`1.7+
`def.notifyWith(c,[a])`1.7+
`def.progress(proCal)`1.7+
`def.state()`1.7+

## 回调函数

`cal.add(callbacks)`1.7+
`cal.disable()`1.7+
`cal.empty()`1.7+
`cal.fire(arguments)`1.7+
`cal.fired()`1.7+
`cal.fireWith([c] [,a])`1.7+
`cal.has(callback)`1.7+
`cal.lock()`1.7+
`cal.locked()`1.7+
`cal.remove(callbacks)`1.7+
`$.callbacks(flags)`1.7+

# 其它

## 正则表达式

## HTML5 速查表

## 源码下载
