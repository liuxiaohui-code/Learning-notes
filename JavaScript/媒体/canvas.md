# canvas 制作动画或绘画

Canvas API 提供了一个通过 JavaScript 和 HTML 的`<canvas>`元素来绘制图形的方式。
它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面。

`<canvas>` 标签只有两个属性 `width`和`height`,当没有设置宽度和高度的时候，canvas 会初始化宽度为 300 像素和高度为 150 像素。该元素可以使用 CSS 来定义大小，但在绘制时图像会伸缩以适应它的框架尺寸：如果 CSS 的尺寸与初始画布的比例不一致，它会出现扭曲。

```html
<canvas>浏览器不支持canvas元素时,将显示此句话</canvas>
```

## 渲染上下文

它公开了一个或多个渲染上下文，其可以用来绘制和处理要展示的内容

```js
var canvas = document.getElementById("tutorial")
// getContext 用来获得渲染上下文和它的绘画功能
//    2d 绘制 2d 图形 ,获得 CanvasRenderingContext2D 实例
var ctx = canvas.getContext("2d")
```

## 2d 图形绘制

1. 获取 CanvasRenderingContext2D 实例 `var ctx = canvas.getContext('2d')`
2. 调用 CanvasRenderingContext2D api 绘制图形

[api 地址](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D)

### 绘制矩形

```js
clearRect(x, y, width, height) //在一个矩形区域内设置所有像素都是透明的 (rgba(0,0,0,0))。这个矩形范围的左上角在 (x, y)，宽度和高度分别由 width 和height确定。

fillRect(x, y, width, height) //绘制一个填充了内容的矩形，这个矩形的开始点（左上点）在(x, y) ，它的宽度和高度分别由width 和 height 确定，填充样式由当前的fillStyle 决定。

strokeRect(x, y, width, height) //绘制矩形边框，其起点为(x, y) ，其大小由宽度和高度指定。
```

### 绘制文本

```js

fillText(text, x, y, [maxWidth]);//在 (x, y) 位置填充文本的方法。如果选项的第四个参数提供了最大宽度，文本会进行缩放以适应最大宽度。

strokeText(text, x, y [, maxWidth]);//在给定的 (x, y) 位置绘制文本的方法。如果提供了表示最大值的第四个参数，文本将会缩放适应宽度。

//使用当前的 font, textAlign, textBaseline 和 direction 值对文本进行渲染。(ctx.font)

measureText(text);//返回一个关于被测量文本TextMetrics 对象包含的信息（例如它的宽度）。
```

### 线型

下面的方法和属性控制如何绘制线。

```js
// 设置线条的宽度
lineWidth = 1
// 指定如何绘制每一条线段末端的属性,默认: butt线段末端以方形结束。round线段末端以圆形结束。square线段末端以方形结束，但是增加了一个宽度和线段相同，高度是线段厚度一半的矩形区域。
lineCap = "butt"
/* 
指定两条线连接点形态,方向一致不显示,拐点
miter 默认值.通过延伸相连部分的外边缘，使其相交于一点，形成一个额外的菱形区域。可以通过 miterLimit 属性看到效果。
round 通过填充一个额外的，圆心在相连部分末端的扇形，绘制拐角的形状。圆角的半径是线段的宽度。
bevel 在相连部分的末端填充一个额外的以三角形为底的区域， 每个部分都有各自独立的矩形拐角。
*/
lineJoin = "bevel"
/*
设置斜接面限制比例的属性
只有当 lineJoin 显示为 "miter" 时，miterLimit 才有效。边角的角度越小，斜接长度就会越大。为了避免斜接长度过长，我们可以使用 miterLimit 属性。如果斜接长度超过 miterLimit 的值，边角会以 lineJoin 的 " bevel " 类型来显示
*/
miterLimit = 10.0
/*
是 Canvas 2D API 获取/设置当前线段样式的方法。
segments: 数组[1,2,3,4],设置线段为一条虚线,实线段1-虚线段2-实线段3-虚线段4-...不停循环
*/
getLineDash()
setLineDash(segments)
/*
设置虚线偏移量的属性
*/
lineDashOffset = 0.0
```

### 文本样式

```js
/**
 * 字体设置。默认值 10px sans-serif。
 * 符合 CSS font 语法的DOMString 字符串。默认字体是 10px sans-serif。
 */
CanvasRenderingContext2D.font = "10px sans-serif"

/**
 * 文本对齐设置。允许的值：
 * start (默认),  文本对齐界线开始的地方（左对齐指本地从左向右，右对齐指本地从右向左）。
 * end,  文本左对齐。
 * left,  文本右对齐。
 * right, 文本居中对齐。
 * center.
 */
CanvasRenderingContext2D.textAlign = "start"

/**
 * 基线对齐设置。允许的值： top, hanging, middle, alphabetic (默认),ideographic, bottom.
 */
CanvasRenderingContext2D.textBaseline

/**
 * 文本的方向。 允许的值： ltr, rtl, inherit (默认).
 */
CanvasRenderingContext2D.direction
```

### 填充和描边样式

```js
填充设计用于图形内部的颜色和样式，描边设计用于图形的边线。

CanvasRenderingContext2D.fillStyle//图形内部的颜色和样式。默认 #000 (黑色).

CanvasRenderingContext2D.strokeStyle//图形边线的颜色和样式。默认 #000 (黑色).
```

### 渐变和图案

```js
CanvasRenderingContext2D.createLinearGradient() //创建一个沿着参数坐标指定的线的线性渐变。
CanvasRenderingContext2D.createRadialGradient() //创建一个沿着参数坐标指定的线的放射性性渐变。
CanvasRenderingContext2D.createPattern() //使用指定的图片 (a CanvasImageSource) 创建图案。通过 repetition 变量指定的方向上重复源图片。此方法返回 CanvasPattern对象。
```

### 阴影

```js
CanvasRenderingContext2D.shadowBlur //描述模糊效果。默认 0
CanvasRenderingContext2D.shadowColor //阴影的颜色。默认 fully-transparent black.
CanvasRenderingContext2D.shadowOffsetX //阴影水平方向的偏移量。默认 0.
CanvasRenderingContext2D.shadowOffsetY //阴影垂直方向的偏移量。默认 0.
```

### 路径

```js
CanvasRenderingContext2D.beginPath()//清空子路径列表开始一个新的路径。当你想创建一个新的路径时，调用此方法。
CanvasRenderingContext2D.closePath()//使笔点返回到当前子路径的起始点。它尝试从当前点到起始点绘制一条直线。如果图形已经是封闭的或者只有一个点，那么此方法不会做任何操作。
CanvasRenderingContext2D.moveTo()//将一个新的子路径的起始点移动到 (x，y) 坐标。
CanvasRenderingContext2D.lineTo()//使用直线连接子路径的最后的点到 x,y 坐标。
CanvasRenderingContext2D.bezierCurveTo()//添加一个 3 次贝赛尔曲线路径。该方法需要三个点。第一、第二个点是控制点，第三个点是结束点。起始点是当前路径的最后一个点，绘制贝赛尔曲线前，可以通过调用 moveTo() 进行修改。
CanvasRenderingContext2D.quadraticCurveTo()//添加一个 2 次贝赛尔曲线路径。
CanvasRenderingContext2D.arc()//绘制一段圆弧路径，圆弧路径的圆心在 (x, y) 位置，半径为 r，根据anticlockwise （默认为顺时针）指定的方向从 startAngle 开始绘制，到 endAngle 结束。
CanvasRenderingContext2D.arcTo()//根据控制点和半径绘制圆弧路径，使用当前的描点 (前一个 moveTo 或 lineTo 等函数的止点)。根据当前描点与给定的控制点 1 连接的直线，和控制点 1 与控制点 2 连接的直线，作为使用指定半径的圆的切线，画出两条切线之间的弧线路径。
CanvasRenderingContext2D.ellipse() 实验性//添加一个椭圆路径，椭圆的圆心在（x,y）位置，半径分别是radiusX 和 radiusY，按照anticlockwise （默认顺时针）指定的方向，从 startAngle 开始绘制，到 endAngle 结束。
CanvasRenderingContext2D.rect()//创建一个矩形路径，矩形的起点位置是 (x, y)，尺寸为 width 和 height。
```

### 绘制路径

```js
CanvasRenderingContext2D.fill() //使用当前的样式填充子路径。
CanvasRenderingContext2D.stroke() //使用当前的样式描边子路径。
CanvasRenderingContext2D.drawFocusIfNeeded() //如果给定的元素获取了焦点，那么此方法会在当前的路径绘制一个焦点。
CanvasRenderingContext2D.scrollPathIntoView() //将当前或给定的路径滚动到窗口。
CanvasRenderingContext2D.clip() //从当前路径创建一个剪切路径。在 clip() 调用之后，绘制的所有信息只会出现在剪切路径内部。例如：参见 Canvas 教程中的 剪切路径 。
CanvasRenderingContext2D.isPointInPath() //判断当前路径是否包含检测点。
CanvasRenderingContext2D.isPointInStroke() //判断检测点是否在路径的描边线上。
```

### 变换

```js
CanvasRenderingContext2D.currentTransform//当前的变换矩阵 (SVGMatrix 对象)。
CanvasRenderingContext2D.rotate()//在变换矩阵中增加旋转，角度变量表示一个顺时针旋转角度并且用弧度表示。
CanvasRenderingContext2D.scale()//根据 x 水平方向和 y 垂直方向，为 canvas 单位添加缩放变换。
CanvasRenderingContext2D.translate()//通过在网格中移动 canvas 和 canvas 原点 x 水平方向、原点 y 垂直方向，添加平移变换
CanvasRenderingContext2D.transform()//使用方法参数描述的矩阵多次叠加当前的变换矩阵。
CanvasRenderingContext2D.setTransform()//重新设置当前的变换为单位矩阵，并使用同样的变量调用 transform() 方法。
CanvasRenderingContext2D.resetTransform() 实验性//使用单位矩阵重新设置当前的变换。
```

### 合成

```js
CanvasRenderingContext2D.globalAlpha //在合成到 canvas 之前，设置图形和图像透明度的值。默认 1.0 (不透明)。
CanvasRenderingContext2D.globalCompositeOperation //通过 globalAlpha 应用，设置如何在已经存在的位图上绘制图形和图像。
```

### 绘制图像

```js
CanvasRenderingContext2D.drawImage() //绘制指定的图片。该方法有多种格式，提供了很大的使用灵活性。
```

### 像素控制

```js
CanvasRenderingContext2D.createImageData() //使用指定的尺寸，创建一个新的、空白的 ImageData 对象。所有的像素在新对象中都是透明的。
CanvasRenderingContext2D.getImageData() //返回一个 ImageData 对象，用来描述 canvas 区域隐含的像素数据，这个区域通过矩形表示，起始点为*(sx, sy)、宽为sw、高为sh*。
CanvasRenderingContext2D.putImageData() //将数据从已有的 ImageData 绘制到位图上。如果提供了脏矩形，只能绘制矩形的像素。
```

### 图像平滑

```js
CanvasRenderingContext2D.imageSmoothingEnabled 实验性//图像平滑的方式；如果禁用，缩放时，图像不会被平滑处理。
```

### canvas 状态

```js
CanvasRenderingContext2D.save() //使用栈保存当前的绘画样式状态，你可以使用 restore() 恢复任何改变。
CanvasRenderingContext2D.restore() //恢复到最近的绘制样式状态，此状态是通过 save() 保存到”状态栈“中最新的元素。
CanvasRenderingContext2D.canvas //对 HTMLCanvasElement 只读的反向引用。如果和 <canvas> 元素没有联系，可能为null。
```

### 点击区域

```js
CanvasRenderingContext2D.addHitRegion() 实验性//给 canvas 添加点击区域。
CanvasRenderingContext2D.removeHitRegion() 实验性//从 canvas 中删除指定 id 的点击区域。
CanvasRenderingContext2D.clearHitRegions() 实验性//从 canvas 中删除所有的点击区域。
```

# 动画渲染函数 requestAnimationFrame

是一个专门的循环函数，旨在浏览器中高效运行动画。它基本上是现代版本的`setInterval()` —— 它在浏览器重新加载显示内容之前执行指定的代码块，从而允许动画以适当的帧速率运行，不管其运行的环境如何。

它是针对`setInterval()` 遇到的问题创建的，比如 `setInterval()`并不是针对设备优化的帧率运行，有时会丢帧。还有即使该选项卡不是活动的选项卡或动画滚出页面等问题 。

```js
//setInterval()的现代版本;在浏览器下一次重新绘制显示之前执行指定的代码块，从而允许动画在适当的帧率下运行，而不管它在什么环境中运行.
//callback 下一次重绘之前更新动画帧所调用的函数(即上面所说的回调函数)。该回调函数会被传入DOMHighResTimeStamp参数，该参数与performance.now()的返回值相同，它表示requestAnimationFrame() 开始去执行回调函数的时刻。
//返回值  一个 long 整数，请求 ID ，是回调列表中唯一的标识。是个非零值，没别的意义。你可以传这个值给 window.cancelAnimationFrame() 以取消回调函数。
function requestAnimationFrame(callback)
```

例

```js
function draw() {
	// Drawing code goes here
	requestAnimationFrame(draw)
}
let cancel = window.requestAnimationFrame(draw)
window.cancelAnimationFrame(cancel)
```
