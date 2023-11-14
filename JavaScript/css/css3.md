# 前言

- CSS 指的是层叠样式表 (Cascading Style Sheets)
- CSS 描述了*如何在屏幕、纸张或其他媒体上显示 HTML 元素*
- CSS _节省了大量工作_。它可以同时控制多张网页的布局
- 外部样式表存储在 *CSS 文件*中

```css
选择器 {
  属性: 值;
  ...;
}
```

# 引入

1. 外部.css 文件
   1. 在 head 部分的 `<link>` 元素内包含对外部样式表文件的引用。
   2. `.css` 文件
2. 内部,在 head 部分的 `<style>` 元素中进行定义。
3. 行内,使用元素属性`style="css属性"`

```html
<head>
  <link rel="stylesheet" type="text/css" href="cssfile_name.css" />
  <style>
    /*....css_code*/
    .class {
      backound-color: red !important;
    }
  </style>
</head>
<body>
  <p style="css属性"></p>
</body>
```

注意:

1. 如果在不同样式表中为同一选择器（元素）定义了一些属性，则将使用最后读取的样式表中的值。
2. 页面中的所有样式将按照以下规则“层叠”为新的“虚拟”样式表，其中第一优先级最高：
   1. 行内样式（在 HTML 元素中）
   2. 外部和内部样式表（在 head 部分）
   3. 浏览器默认样式
   4. `!important` 拥有最高优先级

# 选择器

1. 元素选择器:`标签名{}`,选择页面上的所有此标签
2. id 选择器:`#id{}`,选择 id 为 id 的元素,id 名称不能以数字开头
3. 自定义类名选择器:`.class_name{}`,选择元素属性 class 中具有 class_name 的属性
4. 组合选择器:
   1. `选择器1选择器2{}`,多个选择器并列,表示必须同时满足多个选择条件的元素
   2. `选择器1,选择器2{}`,满足任意选择器的元素
5. 伪类选择器:`选择器:伪类{}`,伪类用于定义元素的特殊状态.
6. 伪元素选择器:`选择器::伪元素{}`,伪元素用于设置元素指定部分的样式
   1. `::after` 在每个元素之后插入内容。
   2. `::before` 在每个元素之前插入内容。
   3. `::first-letter` 选择每个元素的首字母。 块级元素
   4. `::first-line` 块级元素,用于向文本的首行添加特殊样式。
   5. `::selection` 选择用户选择的元素部分。
   6. `content` 属性与 :before 及 :after 伪元素配合使用，来插入生成内容。
7. 通用选择器:`*{}`,匹配所有元素
8. 属性选择器:`[属性]`,选择带有该属性的所有元素
   1. `[属性=值]`,属性为值
   2. `[属性~=值]`,属性值中包含指定单词
   3. `[属性^=值]`,属性值以指定值开头
   4. `[属性$=值]`,属性值以指定值结尾
   5. `[属性*=值]`,属性值包含指定值
   6. `[属性|=值]`,`[lang|="en"]`选择 lang 属性等于 en 或以 en- 开头的所有元素。该值必须是整个单词。
9. 后代选择器:`选择器1 选择器2 ...`,中间有空格,选择器 1 后端元素中选择器 2 的元素,可以是子代、孙子代、、、、
10. 子代选择器:`选择器1 > 选择器2`,
11. 相邻兄弟选择器:
    1. `选择器1 + 选择器2`,相同父元素,紧跟选择器 1 的首个选择器 2
    2. `选择器1 ~ 选择器2`,相同父元素,紧跟选择器 1 的所有选择器 2

## 伪类:

伪类用于定义元素的特殊状态。

```css
:link    未访问的链接
:visited 已访问的链接
:hover   鼠标悬停链接
:active  已选择的链接

:first-child 伪类与指定的元素匹配：该元素是另一个元素的第一个子元素
:lang 伪类允许您为不同的语言定义特殊的规则。

所有 CSS 伪类
选择器	例子	例子描述
:link			a:link	选择所有未被访问的链接。
:visited		a:visited	选择所有已访问的链接。
:hover			a:hover	选择鼠标悬停其上的链接。
:active			a:active	选择活动的链接。

:enabled		input:enabled	选择每个已启用的 <input> 元素。
:checked		input:checked	选择每个被选中的 <input> 元素。
:disabled		input:disabled	选择每个被禁用的 <input> 元素。
:focus			input:focus		选择获得焦点的 <input> 元素。
:in-range		input:in-range	选择具有指定范围内的值的 <input> 元素。
:invalid		input:invalid	选择所有具有无效值的 <input> 元素。
:optional		input:optional	选择不带 "required" 属性的 <input> 元素。
:out-of-range	input:out-of-range	选择值在指定范围之外的 <input> 元素。
:read-only		input:read-only		选择指定了 "readonly" 属性的 <input> 元素。
:read-write		input:read-write	选择不带 "readonly" 属性的 <input> 元素。
:required		input:required		选择指定了 "required" 属性的 <input> 元素。
:valid			input:valid			选择所有具有有效值的 <input> 元素。

:empty			p:empty			选择没有子元素的每个 <p> 元素。
:first-child	p:first-child	选择作为其父的首个子元素的每个 <p> 元素。
:first-of-type	p:first-of-type	选择作为其父的首个 <p> 元素的每个 <p> 元素。
:lang(language)	p:lang(it)		选择每个 lang 属性值以 "it" 开头的 <p> 元素。
:last-child		p:last-child	选择作为其父的最后一个子元素的每个 <p> 元素。
:last-of-type	p:last-of-type	选择作为其父的最后一个 <p> 元素的每个 <p> 元素。
:nth-child(n)	p:nth-child(2)	选择作为其父的第二个子元素的每个 <p> 元素。
:nth-of-type(n)	p:nth-of-type(2)	选择作为其父的第二个 <p> 元素的每个 <p> 元素。
:only-of-type	p:only-of-type	选择作为其父的唯一 <p> 元素的每个 <p> 元素。
:only-child		p:only-child	选择作为其父的唯一子元素的 <p> 元素。
:nth-last-child(n)		p:nth-last-child(2)	选择作为父的第二个子元素的每个<p>元素，从最后一个子元素计数。
:nth-last-of-type(n)	p:nth-last-of-type(2)	选择作为父的第二个<p>元素的每个<p>元素，从最后一个子元素计数

:not(selector)	:not(p)	选择每个非 <p> 元素的元素。

:root			root	选择元素的根元素。

:target			#news:target	选择当前活动的 #news 元素（单击包含该锚名称的 URL）。



```

## content

content 属性与 :before 及 :after 伪元素配合使用，来插入内容。

```css
none			设置 content 为空值。
normal			在 :before 和 :after 伪类元素中会被视为 none，即也是空值。
counter			设定计数器，格式可以是 counter(name) 或 counter(name,style) 。产生的内容是该伪类元素指定名称的最小范围的计数；格式由style指定（默认是'decimal'——十进制数字）
attr(attribute)	将元素的 attribute 属性以字符串形式返回。。
string			设置文本内容
open-quote		设置前引号
close-quote		设置后引号
no-open-quote	移除内容的开始引号
no-close-quote	移除内容的闭合引号
url(url)		设置某种媒体（图像，声音，视频等内容）的链接地址
inherit			指定的 content 属性的值，应该从父元素继承
```

# 注释

```css
/*  */
```

# 函数

```css
attr(属性)	/**返回所选元素的属性值。*/
calc()	/**允许您执行计算来确定 CSS 属性值。*/
var(var_name, value)	/**插入自定义属性的值,value 未找到变量时的缺省值*/

a:after {
  content: " (" attr(href) ")";
} /**在 a 标签后插入标签的内容 */

/**定义全局变量 */
:root {
  /* 一般在根元素上定义全局变量,必须以两个破折号（--）开头，且区分大小写！*/
  --blue: #1e90ff;
  --white: #ffffff;
}
body {
  background-color: var(--blue);
}

* {
  height: calc(100vh - 50px);
  /*
	1. 运算符与长度之间必须用空格隔开
	2. 单位可以不一致
	3. 运算符 + - * / ;
	*/
}
```

# 长度单位

数字和单位之间不能出现空格。但是，如果值为 0，则可以省略单位。

对于某些 CSS 属性，允许使用负的长度。

长度单位有两种类型：**绝对单位**和**相对单位**。

绝对长度单位是固定的，用任何一个绝对长度表示的长度都将恰好显示为这个尺寸。

不建议在屏幕上使用绝对长度单位，因为屏幕尺寸变化很大。但是，如果已知输出介质，则可以使用它们，例如用于打印布局（print layout）。

| 单位 | 描述                       |
| :--- | :------------------------- |
| `cm` | 厘米                       |
| `mm` | 毫米                       |
| `in` | 英寸 (1in = 96px = 2.54cm) |
| `px` | 像素 (1px = 1/96th of 1in) |
| `pt` | 点 (1pt = 1/72 of 1in)     |
| `pc` | 派卡 (1pc = 12 pt)         |

像素（px）是相对于观看设备的。对于低 dpi 的设备，1px 是显示器的一个设备像素（点）。对于打印机和高分辨率屏幕，1px 表示多个设备像素。

相对长度单位规定相对于另一个长度属性的长度。相对长度单位在不同渲染介质之间缩放表现得更好。

| 单位   | 描述                                                             |
| :----- | :--------------------------------------------------------------- |
| `em`   | 相对于元素的字体大小（font-size）（2em 表示当前字体大小的 2 倍） |
| `ex`   | 相对于当前字体的 x-height(极少使用)                              |
| `ch`   | 相对于 "0"（零）的宽度                                           |
| `rem`  | 相对于根元素的字体大小（font-size）                              |
| `vw`   | 相对于视口宽度的 1%                                              |
| `vh`   | 相对于视口高度的 1%                                              |
| `vmin` | 相对于视口较小尺寸的 1％                                         |
| `vmax` | 相对于视口较大尺寸的 1％                                         |
| `%`    | 相对于父元素                                                     |

提示：em 和 rem 单元可用于创建完美的可扩展布局！
视口（Viewport）= 浏览器窗口的尺寸。如果视口为 50 厘米宽，则 1vw = 0.5 厘米。

# 颜色

**指定颜色是通过使用预定义的颜色名称，或 RGB、HEX、HSL、RGBA、HSLA 值。**

```css
*{
    color:Tomato;  /*预定义的颜色名称*/
    color: rgb(255, 99, 71);/*RGB*/
    color: #ff6347;/*HEX*/
    color: hsl(9, 100%, 64%);/*HSL*/
    color: rgba(255, 99, 71, 0.5); /*RGBA透明度0.5*/
    color: hsla(9, 100%, 64%, 0.5); /*HSLA*/
}

color: 文本颜色
background-color: 背景颜色
border:2px solid Tomato;边框颜色
```

## RGB

**rgb(red, green, blue)**

每个参数 (red、green 以及 blue) 定义了 0 到 255 之间的颜色强度。

通常为所有 3 个光源使用相等的值来定义灰色阴影：

```
rgb(0,0,0)
rgb(60,60,60)
rgb(120,120,120)
rgb(180,180,180)
rgb(240,240,240)
```

## RGBA

`rgba(red, green, blue, alpha)`

RGBA 颜色值是具有 alpha 通道的 RGB 颜色值的扩展 - 它指定了颜色的不透明度。

_alpha_ 参数是介于 0.0（完全透明）和 1.0（完全不透明）之间的数字

## HEX

在 CSS 中，可以使用以下格式的十六进制值指定颜色：

`#rrggbb`

其中 rr（红色）、gg（绿色）和 bb（蓝色）是介于 00 和 ff 之间的十六进制值（与十进制 0-255 相同）。

## HSL

在 CSS 中，可以使用色相、饱和度和明度（HSL）来指定颜色，格式如下：

`hsla(hue, saturation, lightness)`

色相（_hue_）是色轮上从 0 到 360 的度数。0 是红色，120 是绿色，240 是蓝色。

饱和度（_saturation_）是一个百分比值，0％ 表示灰色阴影，而 100％ 是全色。

亮度（_lightness_）也是百分比，0％ 是黑色，50％ 是既不明也不暗，100％是白色。

## HLSA

HSLA 颜色值是带有 Alpha 通道的 HSL 颜色值的扩展 - 它指定了颜色的不透明度。

HSLA 颜色值指定为：

**hsla(_hue_, _saturation_, _lightness_, _alpha_)**

_alpha_ 参数是介于 0.0（完全透明）和 1.0（完全不透明）之间的数字

## opacity 透明度

```css
opacity: 0.3;
```

opacity 属性指定元素的不透明度/透明度。取值范围为 0.0 - 1.0。值越低，越透明

**注意：**使用 **opacity** 属性为元素的背景添加透明度时，其所有子元素都继承相同的透明度。这可能会使完全透明的元素内的文本难以阅读。

## 渐变

CSS 渐变使您可以显示两种或多种指定颜色之间的平滑过渡。

CSS 定义了两种渐变类型：

- 线性渐变 `linear-gradient(direction, color-stop1, color-stop2, ...);`
- 重复线性渐变 `repeating-linear-gradient()`
- 径向渐变,由其中心定义 `radial-gradient()`

```css
/**
direction: to bottom|to right|to bottom right|0deg; 从上到下(默认)|从左到右|从左上角开始（到右下角）|角度 0deg 等于向上（to top）。值 90deg 等于向右（to right）。值 180deg 等于向下（to bottom）。
*/
background-image: linear-gradient(direction, color-stop1, color-stop2, ...);

/** 1/0.5=2 重复两次 0-0.25 红to黄，0.25-0.5黄to绿 */
background-image: repeating-linear-gradient(red, yellow 25%, green 50%);
/**
默认地，shape:circle|ellipse 圆|椭圆(默认)，
size 为最远角，size 参数定义渐变的大小。它可接受四个值：
  closest-side
  farthest-side
  closest-corner
  farthest-corner
position 为中心。
*/
background-image: radial-gradient(shape size at position, start-color, ..., last-color);
```

# 容器

## 背景

```css
background-color: red; /*属性指定元素的背景色*/
background-image: url("图片地址"); /*背景图像 */
background-repeat: "" |repeat-x|repeat-y|no-repeat; /**默认,水平和垂直方向上都重复|水平重复|垂直重复 */
background-attachment: fixed|scroll; /** 指定背景图像是应该滚动还是固定的 */
/** 
一个值: center top bottom  left right 15px 10% 相当于一边,另一边为50%
两个值: 一个定义 x 坐标left 或 right，另一个定义 y 坐标top 或 bottom。
三个值: 两个值是关键字值，第三个是前面值的偏移量 left top offsety|left offsetx top
四个值: x offsetx y offsety
*/
background-position: top left|长度 长度; /** 指定背景图像的位置: top bottom center left right 九宫格*/
background: #ffffff url("tree.png") no-repeat fixed right top;
/**CSS 允许您通过 background-image 属性为一个元素添加多幅背景图像。图像会彼此堆叠，其中的第一幅图像最靠近观看者 */
background: url(flower.gif) right bottom no-repeat, url(paper.gif) left top repeat;
background-size: length length|contain|cover; /**背景尺寸|尽可能大使适合内容|缩放使超过内容区域 */
background-origin: border-box|padding-box|content-box; /**属性指定背景图像的位置,从边框|内边|内容左上角开始绘制 */
background-clip: border-box|padding-box|content-box; /**指定背景的绘制区域 */
/**多个背景图片混合模式 */
background-blend-mode: normal|multiply|screen|overlay|darken|lighten|color-dodge|saturation|color|luminosity;
```

## 轮廓 outline

轮廓是在元素周围绘制的一条线，在边框之外，以凸显元素。

```css
/**
dotted 		- 定义点状的轮廓。
dashed 		- 定义虚线的轮廓。
solid 		- 定义实线的轮廓。
double 		- 定义双线的轮廓。
groove 		- 定义 3D 凹槽轮廓。
ridge 		- 定义 3D 凸槽轮廓。
inset 		- 定义 3D 凹边轮廓。
outset 		- 定义 3D 凸边轮廓。
none 		- 定义无轮廓。
hidden 		- 定义隐藏的轮廓。
*/
outline-style: solid; /**样式 */
outline-color: black; /**颜色 */
outline-width: thin|mediun|thick|length;
outline-offset: length; /**轮廓与边框之间添加透明空间 */
outline: width style color;
```

## 边框 border

CSS border 属性允许您指定元素边框的样式、宽度和颜色。

```css
border: 1px solid red; /* 宽度 类型 颜色 */
border-style: dotted|dashed|solid|double|groove|ridge|inset|outset|none|hidden; /**点线|虚线|实线|双边|3d坡口|3d脊线| */
border-width: 长度|thin|medium|thick;
border-color: 颜色|transparent;
border-radius: 长度|x半轴 / y半轴; /**圆角,椭圆 */
border-image: 图片 切割方式 处理; /** 将图像等切为9各部分,中间不要,其余左边框使用 */
border-image-source: url(图片路径);
border-image-slice: number|%|fill; /** 规定图像的上、右、下、左侧边缘的向内偏移||保留边框图像的中间部分 */
border-image-width: number|%|auto;
border-image-outset: length|number; /**属性规定边框图像超过边框盒的量 ,代表对应的 border-width 的倍数*/
border-image-repeat: stretch|repeat|round; /** 重复|拉伸|铺满*/
border-spacing: length; /**边框空格 */
border-collapse: collapse|separate; /**边框合并,常用于表格 */
/**可以设置多个值,2: 上下 左右  4:上 右 下 左 */
border-top-style: dotted;
border-right-style: solid;
border-bottom-style: dotted;
border-left-style: solid;
border-top: ;
border-right: ;
border-bottom: ;
border-left: ;
```

## 外边距 margin

CSS **margin** 属性用于在任何定义的边框之外，为元素周围创建空间。

```css
margin-top;
margin-right;
margin-bottom;
margin-left;
margin: top right bottom left;
/**
所有外边距属性都可以设置以下值：
	auto - 浏览器来计算外边距
	length - 以 px、pt、cm 等单位指定外边距
	% - 指定以包含元素宽度的百分比计的外边距
	inherit - 指定应从父元素继承外边距
*/
```

**外边距合并指的是，当两个垂直外边距相遇时，它们将形成一个外边距。**

**合并后的外边距的高度等于两个发生合并的外边距的高度中的较大者。**

1. 当一个元素出现在另一个元素上面时，第一个元素的下外边距与第二个元素的上外边距会发生合并；
2. 当一个元素包含在另一个元素中时（假设没有内边距或边框把外边距分隔开），它们的上和/或下外边距也会发生合并。
3. 尽管看上去有些奇怪，但是外边距甚至可以与自身发生合并。假设有一个空元素，它有外边距，但是没有边框或填充。在这种情况下，上外边距与下外边距就碰到了一起，它们会发生合并；如果这个外边距遇到另一个元素的外边距，它还会发生合并

这就是一系列的段落元素占用空间非常小的原因，因为它们的所有外边距都合并到一起，形成了一个小的外边距。

## 内边距 padding

CSS **padding** 属性用于在任何定义的边界内的元素内容周围生成空间。

```css
padding-top
padding-right
padding-bottom
padding-left
padding: top right bottom left;
/**
所有内边距属性都可以设置以下值：

	length - 以 px、pt、cm 等单位指定内边距
	% - 指定以包含元素宽度的百分比计的内边距
	inherit - 指定应从父元素继承内边距
提示：不允许负值。
*/

```

## 高/宽

### height/width

**height** 和 **width** 属性用于设置元素的高度和宽度。

**height 和 width 属性不包括内边距、边框或外边距。它设置的是元素内边距、边框以及外边距内的区域的高度或宽度。**

```css
height 和 width 属性可设置如下值：

auto - 默认。浏览器计算高度和宽度。
length - 以 px、cm 等定义高度/宽度。
% - 以包含块的百分比定义高度/宽度。
initial - 将高度/宽度设置为默认值。
inherit - 从其父值继承高度/宽度。
```

### max/min

```css
max-height	设置元素的最大高度。
max-width	设置元素的最大宽度。
min-height	设置元素的最小高度。
min-width	设置元素的最小宽度。
```

max-width 属性用于设置元素的最大宽度。

可以用长度值（例如 px、cm 等）或包含块的百分比（％）来指定 max-width（最大宽度），也可以将其设置为 none（默认值。意味着没有最大宽度）。

当浏览器窗口小于元素的宽度（500px）时，会发生之前那个 <div> 的问题。然后，浏览器会将水平滚动条添加到页面。

在这种情况下，使用 max-width 能够改善浏览器对小窗口的处理。

### resize 调整大小

resize 属性规定元素是否应（以及如何）被用户调整大小。(**通过拖动右下角改变元素的宽高**)

**注意：**Internet Explorer 不支持 resize 属性。

**注释：**如果希望此属性生效，需要设置元素的 overflow 属性，值可以是 auto、hidden 或 scroll。

```css
resize: none|both|horizontal|vertical;
```

| 值         | 描述                         |
| :--------- | :--------------------------- |
| none       | 用户无法调整元素的尺寸。     |
| both       | 用户可调整元素的高度和宽度。 |
| horizontal | 用户可调整元素的宽度。       |
| vertical   | 用户可调整元素的高度。       |

### object-fit

用于规定应如何调整 `<img>` 或 `<video>` 的大小来适应其容器。

这个属性告知内容以各种方式填充容器。例如“保留长宽比”或“展开并占用尽可能多的空间”。

`object-position` 属性与 `object-fit` 一起使用，可规定应如何在其“自己的内容框”内使用 x/y 坐标定位 `<img>` 或 `<video>`。

```css
/**
fill	默认值。调整替换内容的大小来填充元素的内容框。如有必要，对象将被拉伸或挤压。
contain	缩放被替换的内容以保持其宽高比，同时适合元素的内容框。
cover	调整被替换内容的大小，以在填充元素的整个内容框时保持其长宽比。该对象将被裁剪。
none	替换的内容不会调整大小。
scale-down	内容的尺寸与 none 或 contain 中的一个相同，取决于它们两个之间谁得到的对象尺寸会更小一些。
*/
object-fit: fill|contain|cover|scale-down|none|initial|inherit;
/**
position	规定图像或视频在其内容框中的位置。第一个值控制 x 轴，第二个值控制 y 轴。可以是字符串（left、center 或 right）或数字（以 px 或 ％ 为单位）。允许负值。
*/
object-position: position|initial|inherit;
```

## 框模型

![boxmodel](....\photo\boxmodel.gif)

对不同部分的说明：

- _内容_ - 框的内容，其中显示文本和图像。
- _内边距_ - 清除内容周围的区域。内边距是透明的。
- _边框_ - 围绕内边距和内容的边框。
- _外边距_ - 清除边界外的区域。外边距是透明的。

元素的总宽度应该这样计算： `元素总宽度 = 宽度 + 左内边距 + 右内边距 + 左边框 + 右边框 + 左外边距 + 右外边距`

元素的总高度应该这样计算：`	元素总高度 = 高度 + 上内边距 + 下内边距 + 上边框 + 下边框 + 上外边距 + 下外边距`

## 元素阴影 box-shadow

CSS `box-shadow` 属性应用阴影于元素。

```css
/**
向框添加一个或多个阴影。该属性是由逗号分隔的阴影列表，每个阴影由 2-4 个长度值、可选的颜色值以及可选的 inset 关键词来规定。省略长度的值是 0。
	h-shadow 必需。水平阴影的位置。允许负值。
	v-shadow 必需。垂直阴影的位置。允许负值。
	blur 可选。模糊距离。
	spread 可选。阴影的尺寸。
	color 可选。阴影的颜色。请参阅 CSS 颜色值。
	inset 可选。将外部阴影 (outset) 改为内部阴影。
*/
box-shadow: h-shadow v-shadow blur spread color inset;
```

# 滚动条

::-webkit-scrollbar 仅在基于 Blink 或 WebKit 的浏览器（例如，Chrome、Edge、Opera、Safari、iOS 上所有的浏览器，以及其它基于 WebKit 的浏览器）上可用。滚动条样式的标准方法可用于 scrollbar-color 和 scrollbar-width。

```css
::-webkit-scrollbar——整个滚动条。
::-webkit-scrollbar-button——滚动条上的按钮（上下箭头）。
::-webkit-scrollbar-thumb——滚动条上的滚动滑块。
::-webkit-scrollbar-track——滚动条轨道。
::-webkit-scrollbar-track-piece——滚动条没有滑块的轨道部分。
::-webkit-scrollbar-corner——当同时有垂直滚动条和水平滚动条时交汇的部分。通常是浏览器窗口的右下角。
::-webkit-resizer——出现在某些元素底角的可拖动调整大小的滑块。

/**火狐浏览器使用*/
scrollbar-color: auto| color1 color2; /**color1 轨道颜色;color2 滑块颜色 */
scrollbar-width: auto|thin|none;
/** 平滑滚动而不是直接跳转 */
scroll-behavior: auto|smooth;
```

# 布局

## display

display 属性规定是否/如何显示元素。

每个 HTML 元素都有一个默认的 display 值，具体取决于它的元素类型。大多数元素的默认 display 值为 block 或 inline。

属性

```css
display: none|block|inline;

none	/**  此元素不会被显示。 */
block	/**  此元素将显示为块级元素，此元素前后会带有换行符。 */
inline	/**  默认。此元素会被显示为内联元素，元素前后没有换行符。 */
inline-block	/** 行内块元素。（CSS2.1 新增的值）  */
list-item	/** 此元素会作为列表显示。  */
run-in	/** 此元素会根据上下文作为块级元素或内联元素显示。  */
table	/**  此元素会作为块级表格来显示（类似 <table>），表格前后带有换行符。 */
inline-table	/** 此元素会作为内联表格来显示（类似 <table>），表格前后没有换行符。  */
table-row-group	/**  此元素会作为一个或多个行的分组来显示（类似 <tbody>）。 */
table-header-group	/**  此元素会作为一个或多个行的分组来显示（类似 <thead>）。 */
table-footer-group	/**  此元素会作为一个或多个行的分组来显示（类似 <tfoot>）。 */
table-row	/** 此元素会作为一个表格行显示（类似 <tr>）。  */
table-column-group	/** 此元素会作为一个或多个列的分组来显示（类似 <colgroup>）。  */
table-column	/** 此元素会作为一个单元格列显示（类似 <col>）  */
table-cell	/**  此元素会作为一个表格单元格显示（类似 <td> 和 <th>） */
table-caption	/**  此元素会作为一个表格标题显示（类似 <caption>） */
inherit	/** 规定应该从父元素继承 display 属性的值。  */
```

**注意：**

​ 设置元素的 display 属性仅会更改*元素的显示方式*，而不会更改元素的种类。因此，带有 **display: block;** 的行内元素不允许在其中包含其他块元素。

**块级元素(block)特性：**

- 总是独占一行，表现为另起一行开始，而且其后的元素也必须另起一行显示;
- 宽度(width)、高度(height)、内边距(padding)和外边距(margin)都可控制;

**内联元素(inline)特性：**

- 和相邻的内联元素在同一行;
- 宽度(width)、高度(height)、内边距的 top/bottom(padding-top/padding-bottom)和外边距的 top/bottom(margin-top/margin-bottom)都不可改变，就是里面文字或图片的大小;

## visibility

visibility 属性规定元素是否可见。

**提示：**即使不可见的元素也会占据页面上的空间。请使用 "display" 属性来创建不占据页面空间的不可见元素。

```css
/**
visible	默认值。元素是可见的。
hidden	元素是不可见的。
collapse	当在表格元素中使用时，此值可删除一行或一列，但是它不会影响表格的布局。被行或列占据的空间会留给其他内容使用。如果此值被用在其他的元素上，会呈现为 "hidden"。
inherit	规定应该从父元素继承 visibility 属性的值。
*/
visibility: visible|hidden|collapse|inherit;
```

## 定位 position

​position 属性规定应用于元素的定位方法的类型（static、relative、fixed、absolute 或 sticky）。

​ 元素其实是使用 top、bottom、left 和 right 属性定位的。但是，除非首先设置了 position 属性，否则这些属性将不起作用。根据不同的 position 值，它们的工作方式也不同。

```css
/**
static; 静态定位的元素不受 top、bottom、left 和 right 属性的影响。不会以任何特殊方式定位；它始终根据页面的正常流进行定位

relative; 元素相对于其正常位置进行定位。设置相对定位的元素的 top、right、bottom 和 left 属性将导致其偏离其正常位置进行调整。不会对其余内容进行调整来适应元素留下的任何空间。

fixed;元素是相对于视口定位的，这意味着即使滚动页面，它也始终位于同一位置。 top、right、bottom 和 left 属性用于定位此元素。固定定位的元素不会在页面中通常应放置的位置上留出空隙。

absolute;元素相对于最近的定位祖先元素进行定位（而不是相对于视口定位，如 fixed）。然而，如果绝对定位的元素没有祖先，它将使用文档主体（body），并随页面滚动一起移动。注意：“被定位的”元素是其位置除 static 以外的任何元素。

sticky;元素根据用户的滚动位置进行定位,粘性元素根据滚动位置在相对（relative）和固定（fixed）之间切换。起先它会被相对定位，直到在视口中遇到给定的偏移位置为止 - 然后将其“粘贴”在适当的位置（比如 position:fixed）。
*/
position: static|relative|fixed|absolute|sticky;
/* 具体位置 */
top: length;
bottom: ;
left: ;
right: ;
```

## 重叠 z-index

在对元素进行定位时，它们可以与其他元素重叠。

**注意：**如果两个定位的元素重叠而未指定 **z-index**，则位于 HTML 代码中最后的元素将显示在顶部。

eg:

```css
/** 元素可以设置正或负的堆叠顺序,具有较高堆叠顺序的元素始终位于具有较低堆叠顺序的元素之前 */
z-index: 1;
```

## 剪裁 clip

clip 属性**剪裁绝对定位**元素。

```css
/** 将绝对定位的元素进行裁剪,(top,left)-(bottom,right) */
clip: rect (top, right, bottom, left) | auto;
```

## 溢出 overflow

overflow 属性指定在元素的内容太大而无法放入指定区域时是剪裁内容还是添加滚动条。仅适用于具有指定高度的块元素

```css
/**

visible - 默认。溢出没有被剪裁。内容在元素框外渲染
hidden - 溢出被剪裁，其余内容将不可见
scroll - 溢出被剪裁，同时添加滚动条以查看其余内容
auto - 与 scroll 类似，但仅在必要时添加滚动条
*/
overflow: visible|hidden|scroll|auto;
overflow-x: 指定如何处理内容的左/右边缘。;
overflow-y: 指定如何处理内容的上/下边缘。;
```

## 浮动 float 和清除 clear

CSS float 属性规定元素如何浮动。

CSS clear 属性规定哪些元素可以在清除的元素旁边以及在哪一侧浮动。使用 clear 属性的最常见用法是在元素上使用了 float 属性之后。​ 在清除浮动时，应该对清除与浮动进行匹配：如果某个元素浮动到左侧，则应清除左侧。您的浮动元素会继续浮动，但是被清除的元素将显示在其下方。

```css
float: left|right|none|inherit;
/**禁止元素浮动到该元素 */
clear: none|left|right|both|inherit;
```

## 为多个浮动框定义相同的宽高 box-sizing

您可以轻松地并排创建三个浮动框。但是，当您添加一些内容来扩大每个框的宽度（例如，内边距或边框）时，这个框会损坏。 box-sizing 属性允许我们在框的总宽度（和高度）中包括内边距和边框，确保内边距留在框内而不会破裂。

```css
/**
content-box	这是由 CSS2.1 规定的宽度高度行为。宽度和高度分别应用到元素的内容框。在宽度和高度之外绘制元素的内边距和边框。

border-box	为元素设定的宽度和高度决定了元素的边框盒。就是说，为元素指定的任何内边距和边框都将在已设定的宽度和高度内进行绘制。通过从已设定的宽度和高度分别减去边框和内边距才能得到内容的宽度和高度。
*/
box-sizing: content-box|border-box;
```

## 弹性布局 flex

设置`display: flex;`的容器将成为弹性容器,可以使用以下属性约束子元素样式:

```css
display: flex;
/**从上到下|从下到上|从左到右|从右到左,定义容器要在哪个方向上堆叠 flex 项目。 */
flex-direction: column|column-reverse|row|row-reverse;
/**不换|必要时换|换但相反顺序；属性规定是否应该对 flex 项目换行。 */
flex-wrap: nowrap|wrap|wrap-reverse;
/**用于同时设置 flex-direction 和 flex-wrap 属性的简写属性。 */
flex-flow: d w;
/**中心对齐|开头对齐（默认）|末端对齐|显示行之前、之间和之后带有空格的 flex 项目； 用于对齐 flex 项目： */
justify-content: center|flex-start|flex-end|space-around;
/**中|顶|底|填充（默认）|基线；用于垂直对齐 flex 项目 */
align-items: center|flex-start|flex-end|stretch|baseline;
/** 显示的弹性线之间有相等的间距|显示弹性线在其之前、之间和之后带有空格|拉伸弹性线以占据剩余空间（默认）|在容器中间显示弹性线|在容器开头显示弹性线|在容器的末尾显示弹性线;用于对齐弹性线*/
align-content: space-between|space-around|stretch|center|flex-start|flex-end;
```

弹性容器的直接子元素会自动成为弹性项目。用于弹性项目的属性有：

```css
/** 值必须是数字，默认值是 0。规定 flex 项目的顺序*/
order: n;
/** 属性规定某个 flex 项目相对于其余 flex 项目将增长多少*/
flex-grow: n;
/** 规定某个 flex 项目相对于其余 flex 项目将收缩多少*/
flex-shrink: n;
/** 规定 flex 项目的初始长度*/
flex-basis: n_px;
/** */
flex: flex-grow flex-shrink flex-basis;
/** 规定弹性容器内所选项目的对齐方式,将覆盖容器的 align-items 属性所设置的默认对齐方式 */
align-self: center|flex-start|flex-end|stretch|baseline;
```

## 网格布局 grid

```css
display: grid;
grid: none|grid-template-rows / grid-template-columns|grid-template-areas|grid-template-rows / [grid-auto-flow] grid-auto-columns|[grid-auto-flow] grid-auto-rows / grid-template-columns|initial|inherit;

grid-area: grid-row-start / grid-column-start / grid-row-end / grid-column-end | itemname;
grid-row-start: auto|row-line; /**属性定义项目将在哪条行线上开始。 */
grid-column-start: auto|span n|2; /** span n 横跨列数|规定从哪列开始显示项目 */
grid-row-end: auto|row-line|span n; /**属性规定项目将横跨多少行，或者项目将在在哪条行线上结束 */
grid-column-end: auto|span n|column-line; /**属性项目将横跨多少列，或者项目会在哪条列线（column-line）上结束。 */

grid-auto-columns: auto|max-content|min-content|length|fit-content() |minmax(min.max); /**设置网格容器中列的尺寸 */
grid-auto-rows: auto|max-content|min-content|length; /**为网格容器中的行设置尺寸 */
grid-auto-flow: row|column|dense|row dense|column dense; /**控制自动放置的项目如何插入网格中。行|列|填充空余| */

grid-column: grid-column-start / grid-column-end;
grid-row: grid-row-start / grid-row-end;
grid-gap: grid-row-gap grid-column-gap;
grid-column-gap: length; /**定义网格布局中列间隙的尺寸。 */
grid-row-gap: length; /**定义网格布局中行间隙的尺寸。 */
grid-template: none|grid-template-rows / grid-template-columns|grid-template-areas|initial|inherit;
grid-template-rows: none|auto|max-content|min-content|length|initial|inherit; /**规定网格布局中的行数（和高度）,值是用空格分隔的列表，其中每个值指定相应行的高度 */
grid-template-columns: none|auto|max-content|min-content|length|initial|inherit; /**属性规定网格布局中的列数（和宽度）。这些值是一个用空格分隔的列表，其中每个值指定相应列的尺寸。 */
grid-template-areas: none|itemnames; /**在网格布局中规定区域 */
```

# 文本

```css
/** 规定样式表的字体,放在css文件第一行 */
@charset "UTF-8";
/**字体颜色 */
color: red;
/** 垂直对齐元素:父元素基线|文本下标|上标| ||||将元素升高或降低指定的高度，可以是负数*/
vertical-align: baseline|sub|super|top|text-top|middle|bottom|text-bottom|length;
/**文本方向 */
direction: left-to-right|right-to-left;
/** 与 direction 属性一起使用，来设置或返回文本是否被重写，以便在同一文档中支持多种语言。*/
unicode-bidi: normal|embed|bidi-override;
/**指定文本中字符之间的间距 */
letter-spacing: length;
/**指定行之间的间距：行高 */
line-height: length|number|normal;
/**指定文本中单词之间的间距。 */
word-spacing: length|normal;
/** 强制换行,默认|强制换行 */
word-wrap: normal|break-word;
/** 自动换行 : 默认|单词内部|只能在半角空格或连字符处换行*/
word-break: normal|break-all|keep-all;
/**属性规定文本行是水平放置还是垂直放置:
horizontal-tb	让内容从左到右水平流动，从上到下垂直流动。
vertical-rl	让内容从上到下垂直流动，从右到左水平流动。
vertical-lr	让内容从上到下垂直流动，从左到右水平流动。*/
writing-mode: horizontal-tb|vertical-rl|vertical-lr;
/** 指定元素内部空白的处理方式; 忽略|保留|不换行|保留空白且换行|合并空白,保留换行符*/
white-space: normal|pre|nowrap|pre-weap|pre-line;
/**规定把标点符号放在文本整行的开头还是结尾的行框之外 */
hanging-punctuation: none|first|last|allow-end|force-end;
/**是否允许在一行文本中使用连字符创建更多的自动换行机会,不要|只在&处|要 */
hyphens: none|manual|auto;
/**设置嵌套引用（embedded quotation）的引号类型,用于标签q */
quotes: none|string string string string;
/**规定tab制表符的宽度  -moz-tab-size*/
tab-size: 16;
/**规定是否能选取元素的文本。 */
user-select: auto|none|text|all;
```

## text-

```css
/** 水平对齐: 居中|左|右|两端 */
text-align: center|left|right|justify;
/** 段落最后一行对齐方式 */
text-align-last: center|left|right|justify;
/** text-align 设置为 "justify" 时文本的对齐方式
      auto	浏览器决定齐行算法。
      none	禁用齐行。
      inter-word	增加/减少单词间的间隔。
      inter-ideograph	用表意文本来排齐内容。
      inter-cluster	只对不包含内部单词间隔的内容（比如亚洲语系）进行排齐。
      distribute	类似报纸版面，除了在东亚语系中最后一行是不齐行的。
      kashida	通过拉伸字符来排齐内容。
*/
text-justify: auto|none|inter-word|inter-ideograph|inter-cluster|distribute|kashida;
/**文本修饰: 无|下划线|上划线|删除线|闪烁 */
text-decoration: none|underline|overline|line-through|blink;
/**规定 text-decoration 的颜色。 */
text-decoration-color: color|initial|inherit;
/** 线的位置 */
text-decoration-line: none|underline|overline|line-through|initial|inherit;
/**设置文本装饰的类型（实线、波浪线、点线、虚线、双线）。 */
text-decoration-style: solid|double|dotted|dashed|wavy|initial|inherit;
/** 指定文本中的大写和小写字母。无|大写开头|大写|小写 */
text-transform: none|capitalize|uppercase|lowercase;
/** 指定文本第一行的缩进 */
text-indent: length;
/** 文本阴影,逗号分割多个阴影 */
text-shadow: 水平阴影px 垂直阴影px [模糊px] [颜色];
/** 文本溢出:剪切|省略号|给定字符省略 */
text-overflow: clip|ellipsis|string;
```

## 字体 font

在 CSS 中，有五个通用字体族：

- 衬线字体（Serif）- 在每个字母的边缘都有一个小的笔触。它们营造出一种形式感和优雅感。
- 无衬线字体（Sans-serif）- 字体线条简洁（没有小笔画）。它们营造出现代而简约的外观。
- 等宽字体（Monospace）- 这里所有字母都有相同的固定宽度。它们创造出机械式的外观。
- 草书字体（Cursive）- 模仿了人类的笔迹。
- 幻想字体（Fantasy）- 是装饰性/俏皮的字体。

所有不同的字体名称都属于这五个通用字体系列之一。

下面列出了适用于 HTML 和 CSS 的最佳 Web 安全字体：

Arial (sans-serif)
Verdana (sans-serif)
Helvetica (sans-serif)
Tahoma (sans-serif)
Trebuchet MS (sans-serif)
Times New Roman (serif)
Georgia (serif)
Garamond (serif)
Courier New (monospace)
Brush Script MT (cursive)

```css
/** 选择字体, 名称不止一个单词，则必须用引号引起来; 备用字体用,分割 */
font-family: "Times New Roman", serif;
/**斜体: 正常|斜体|倾斜 */
font-style: normal|italic|oblique;
/**粗细 */
font-weight: normal|bold|bolder|lighter|number;
/** 小型大写字母 */
font-variant: normal|small-caps;
/** 大小 */
font-size: length;
font: style variant weight size family;
```

## @font-face 引入第三方字体

```css
@font-face {
  font-family: name; /*	必需。定义字体名称。*/
  src: URL; /*	必需定义下载字体的 URL。*/
  font-stretch: normal|condensed|ultra-condensed|extra-condensed|semi-condensed|expanded|semi-expanded|extra-expanded|ultra-expanded; /**字体拉伸 */
  font-style: normal|italic|oblique; /**字体样式 */
  font-weight: bold; /**粗体 */
  unicode-range: unicode-range; /*可选。定义字体支持的 Unicode 字符范围。默认值是 "U+0-10FFFF"。*/
}
```

网络字体:

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Sofia" />
<style>
  body {
    font-family: "Sofia";
    font-size: 22px;
  }
</style>
```

本地字体:

```css
@font-face {
  font-family: myFirstFont; /**定义字体名 */
  src: url(sansation_light.woff); /** 引入字体文件 */
}
@font-face {
  font-family: myFirstFont;
  src: url(sansation_bold.woff);
  font-weight: bold; /** 使用 myFirstFont字体为粗体时,使用这个*/
}

div {
  font-family: myFirstFont; /**使用字体 */
}
```

# 列表 ul ol

CSS 列表属性使您可以：

- 为有序列表设置不同的列表项标记
- 为无序列表设置不同的列表项标记
- 将图像设置为列表项标记
- 为列表和列表项添加背景色

```css
/**
none	无标记。
disc	默认。标记是实心圆。
circle	标记是空心圆。
square	标记是实心方块。
decimal	标记是数字。
decimal-leading-zero	0开头的数字标记。(01, 02, 03, 等。)
lower-roman	小写罗马数字(i, ii, iii, iv, v, 等。)
upper-roman	大写罗马数字(I, II, III, IV, V, 等。)
lower-alpha	小写英文字母The marker is lower-alpha (a, b, c, d, e, 等。)
upper-alpha	大写英文字母The marker is upper-alpha (A, B, C, D, E, 等。)
lower-greek	小写希腊字母(alpha, beta, gamma, 等。)
lower-latin	小写拉丁字母(a, b, c, d, e, 等。)
upper-latin	大写拉丁字母(A, B, C, D, E, 等。)
hebrew	传统的希伯来编号方式
armenian	传统的亚美尼亚编号方式
georgian	传统的乔治亚编号方式(an, ban, gan, 等。)
cjk-ideographic	简单的表意数字
hiragana	标记是：a, i, u, e, o, ka, ki, 等。（日文平假名字符）
katakana	标记是：A, I, U, E, O, KA, KI, 等。（日文片假名字符）
hiragana-iroha	标记是：i, ro, ha, ni, ho, he, to, 等。（日文平假名序号）
katakana-iroha	标记是：I, RO, HA, NI, HO, HE, TO, 等。（日文片假名序号）
*/
list-style-type: ; /** 属性指定列表项标记的类型。*/
list-style-image: url|none; /**属性将图像指定为列表项标记*/
list-style-position: inside|outside; /**属性指定列表项标记（项目符号）的位置。*/
list-style: type position image; /* 属性是一种简写属性。它用于在一条声明中设置所有列表属性*/
```

# 鼠标光标 cursor

```css
/**
url 需使用的自定义光标的 URL。
注释：请在此列表的末端始终定义一种普通的光标，以防没有由 URL 定义的可用光标。
default 默认光标（通常是一个箭头）
auto 默认。浏览器设置的光标。
crosshair 光标呈现为十字线。
pointer 光标呈现为指示链接的指针（一只手）
move 此光标指示某对象可被移动。
e-resize 此光标指示矩形框的边缘可被向右（东）移动。
ne-resize 此光标指示矩形框的边缘可被向上及向右移动（北/东）。
nw-resize 此光标指示矩形框的边缘可被向上及向左移动（北/西）。
n-resize 此光标指示矩形框的边缘可被向上（北）移动。
se-resize 此光标指示矩形框的边缘可被向下及向右移动（南/东）。
sw-resize 此光标指示矩形框的边缘可被向下及向左移动（南/西）。
s-resize 此光标指示矩形框的边缘可被向下移动（南）。
w-resize 此光标指示矩形框的边缘可被向左移动（西）。
text 此光标指示文本。
wait 此光标指示程序正忙（通常是一只表或沙漏）。
help 此光标指示可用的帮助（通常是一个问号或一个气球）。
*/
cursor: default;
/**规定 input、textareas 或任何可编辑元素中的光标（插入符号）的颜色。 */
caret-color: red;
/** 是否对指针事件作出反应 */
pointer-events: auto|none;
```

# 表格

```css
caption-side: top|bottom /*	规定表格标题的位置。*/
empty-cells:hide|show;/**属性设置是否显示表格中的空单元格（仅用于“分离边框”模式）。*/
table-layout	/**设置用于表格的布局算法。*/
```

# 计数器

CSS 计数器就像“变量”。变量值可以通过 CSS 规则递增（将跟踪它们的使用次数）。常用与目录编号

```css
/** 定义一个名字为id的递增计数器,默认增量是 number,匹配的选择器从上到下计数*/
counter-increment: id number;
/** 属性设置某个选择器出现次数的计数器的值。默认为 0。*/
counter-reset: id number;
/**使用counter()函数获取计数器计数 */
counter(name)
```

eg

```css
body {
  counter-reset: section;
}

h2::before {
  counter-increment: section;
  content: "Section " counter(section) ": ";
}
```

# 多列

1. 应用场景：杂志、报纸
2. 作用:将一个容器分为多列

```css
column-count: number|auto; /** 规定元素应划分的列数*/
column-width: length; /** 为列指定建议的最佳宽度。 */
columns: 2px 3; /** 用于设置 column-width 和 column-count 的简写属性。*/
column-fill: balance|auto; /**规定如何填充列。 主流浏览器都不支持 */
column-gap: length; /**指定列之间的间隙。 */
column-rule: width style color; /** 用于设置所有 column-rule-* 属性(分割线)的简写属性。*/
column-rule-color: red; /**规定列之间规则的颜色。*/
column-rule-style: none|hidden|dotted|dashed|solid|double|groove|ridge|inset|outset; /**规定列之间的规则样式。*/
column-rule-width: 1px; /**规定列之间的规则宽度。*/
/**在分列的子元素中使用 */
column-span: 2; /**规定一个元素应该跨越多少列。*/
```

# 滤镜 filter

```css
/** filter 属性定义元素（通常是 <img>）的视觉效果（如模糊和饱和度）。
none	默认值。规定无效果。
blur(px)	对图像应用模糊效果。较大的值将产生更多的模糊。如果为指定值，则使用 0。
brightness(%)	调整图像的亮度。0％ 将使图像完全变黑。默认值是 100％，代表原始图像。值超过 100％ 将提供更明亮的结果。
contrast(%)	调整图像的对比度。0％ 将使图像完全变黑。默认值是 100％，代表原始图像。超过 100％ 的值将提供更具对比度的结果。
drop-shadow(h-shadow v-shadow blur spread color)	对图像应用阴影效果。可能的值：
  h-shadow - 必需。指定水平阴影的像素值。负值会将阴影放置在图像的左侧。
  v-shadow - 必需。指定垂直阴影的像素值。负值会将阴影放置在图像上方。
  blur -可选。这是第三个值，单位必须用像素。为阴影添加模糊效果。值越大创建的模糊就越多（阴影会变得更大更亮）。不允许负值。如果未规定值，会使用 0（阴影的边缘很锐利）。
  spread - 可选。这是第四个值，单位必须用像素。正值将导致阴影扩展并增大，负值将导致阴影缩小。如果未规定值，会使用 0（阴影与元素的大小相同）。注释：Chrome、Safari 和 Opera，也许还有其他浏览器，不支持第 4 个长度；如果添加，则不会呈现。
  color - 可选。为阴影添加颜色。如果未规定，则颜色取决于浏览器（通常为黑色）。
grayscale(%)	将图像转换为灰阶。0% (0) 是默认值，代表原始图像。100％ 将使图像完全变灰（用于黑白图像）。注释：不允许负值。
hue-rotate(deg)	在图像上应用色相旋转。该值定义色环的度数。默认值为 0deg，代表原始图像。注释：最大值是 360deg。
invert(%)	反转图像中的样本。0% (0) 是默认值，代表原始图像。100％将使图像完全反转。注释：不允许负值。
opacity(%)	设置图像的不透明度级别。opacity-level 描述了透明度级别，其中：0% 为完全透明。100% (1) 是默认值，代表原始图像（不透明）。注释：不允许负值。提示：这个滤镜类似 opacity 属性。
saturate(%)	设置图像的饱和度。0% (0) will make the image completely un-saturated.100% is default and represents the original image.Values over 100% provides super-saturated results.注释：不允许负值。
sepia(%)	将图像转换为棕褐色。0% (0) 是默认值，代表原始图像。100％ 将使图像完全变为棕褐色。注释：不允许负值。
url()	url() 函数接受规定 SVG 滤镜的 XML 文件的位置，并且可以包含指向特定滤镜元素的锚点。实例：filter: url(svg-url#element-id)
initial	将此属性设置为其默认值。参阅 initial。
inherit	从其父元素继承此属性。参阅 inherit。
*/
filter: none | blur() | brightness() | contrast() | drop-shadow() | grayscale() | hue-rotate() | invert() | opacity() | saturate() | sepia() | url();
```

# @media 设备选择

媒体查询旨在为不同的设备（显示器、平板电脑、手机等）定义不同的样式规则

CSS2 中引入了 @media 规则，它让为不同媒体类型定义不同样式规则成为可能。

CSS3 中的媒体查询扩展了 CSS2 媒体类型的概念：它们并不查找设备类型，而是关注设备的能力。

媒体查询可用于检查许多事情，例如：

- 视口的宽度和高度
- 设备的宽度和高度
- 方向（平板电脑/手机处于横向还是纵向模式）
- 分辨率

```css
/**
not：not 关键字反正整个媒体查询的含义。
only：only 关键字可防止旧版浏览器应用指定的样式，这些浏览器不支持带媒体特性的媒体查询。它对现代浏览器没有影响。
and：and 关键字将媒体特性与媒体类型或其他媒体特性组合在一起。
, 类似于或,使用逗号将另一个媒体查询添加到已存在的媒体查询中

meidatype:
  all    用于所有媒体类型设备。
  print   用于打印机。
  screen  用于计算机屏幕、平板电脑、智能手机等等。
  speech  用于大声“读出”页面的屏幕阅读器。
*/
@media not|only mediatype and (expressions) {
  CSS-Code;
}

/* 当宽度介于 600px 和 900px 之间或大于 1100px 时 - 改变 <div> 的外观 */
@media screen and (max-width: 900px) and (min-width: 600px), (min-width: 1100px) {
  div.example {
    font-size: 50px;
    padding: 50px;
    border: 8px solid black;
    background: yellow;
  }
}

/**
expression:
any-hover                    是否有任何可用的输入机制允许用户（将鼠标等）悬停在元素上？在 Media Queries Level 4 中被添加。
any-pointer                  可用的输入机制中是否有任何指针设备，如果有，它的精度如何？在 Media Queries Level 4 中被添加。
aspect-ratio                 视口（viewport）的宽高比。
color                        输出设备每个像素的比特值，常见的有 8、16、32 位。如果设备不支持输出彩色，则该值为 0。
color-gamut                  用户代理和输出设备大致程度上支持的色域。在 Media Queries Level 4 中被添加。
color-index                  输出设备的颜色查询表（color lookup table）中的条目数量。如果设备不使用颜色查询表，则该值为 0。
device-aspect-ratio          输出设备的宽高比。已在 Media Queries Level 4 中被弃用。
device-height                输出设备渲染表面（如屏幕）的高度。已在 Media Queries Level 4 中被弃用。
device-width                 输出设备渲染表面（如屏幕）的宽度。已在 Media Queries Level 4 中被弃用。
display-mode                 应用程序的显示模式，如 web app 的 manifest 中的 display 成员所指定在 Web App Manifest spec 被定义。
forced-colors                检测是用户代理否限制调色板。在 Media Queries Level 5 中被添加。
grid                         输出设备使用网格屏幕还是点阵屏幕？
height                       视口（viewport）的高度。
hover                        主输入机制是否允许用户将鼠标悬停在元素上？在 Media Queries Level 4 中被添加。
inverted-colors              浏览器或者底层操作系统是否反转了颜色。在 Media Queries Level 5 中被添加。
light-level                  当前环境光水平。在 Media Queries Level 5 中被添加。
max-aspect-ratio             显示区域的宽度和高度之间的最大比例。
max-color                    输出设备每个颜色分量的最大位数。
max-color-index              设备可以显示的最大颜色数。
max-height                   显示区域的最大高度，例如浏览器窗口。
max-monochrome               单色（灰度）设备上每种“颜色”的最大位数。
max-resolution               设备的最大分辨率，使用 dpi 或 dpcm。
max-width                    显示区域的最大宽度，例如浏览器窗口。
min-aspect-ratio             显示区域的宽度和高度之间的最小比例。
min-color                    输出设备每个颜色分量的最小位数。
min-color-index              设备可以显示的最小颜色数。
min-height                   显示区域的最小高度，例如浏览器窗口。
min-monochrome               单色（灰度）设备上每种“颜色”的最小位数。
min-resolution               设备的最低分辨率，使用 dpi 或 dpcm。
min-width                    显示区域的最小宽度，例如浏览器窗口。
monochrome                   输出设备单色帧缓冲区中每个像素的位深度。如果设备并非黑白屏幕，则该值为 0。
orientation                  视窗（viewport）的旋转方向（横屏还是竖屏模式）。
overflow-block               输出设备如何处理沿块轴溢出视口(viewport)的内容。在 Media Queries Level 4 中被添加。
overflow-inline              沿内联轴溢出视口(viewport)的内容是否可以滚动？在 Media Queries Level 4 中被添加。
pointer                      主要输入机制是一个指针设备吗？如果是，它的精度如何？在 Media Queries Level 4 中被添加。
prefers-color-scheme         探测用户倾向于选择亮色还是暗色的配色方案。在 Media Queries Level 5 中被添加。
prefers-contrast             探测用户是否有向系统要求提高或降低相近颜色之间的对比度。在 Media Queries Level 5 中被添加。
prefers-reduced-motion       用户是否希望页面上出现更少的动态效果。在 Media Queries Level 5 中被添加。
prefers-reduced-transparency 用户是否倾向于选择更低的透明度。在 Media Queries Level 5 中被添加。
resolution                   输出设备的分辨率，使用 dpi 或 dpcm。
scan                         输出设备的扫描过程（适用于电视等）。
scripting                    探测脚本（例如 JavaScript）是否可用。在 Media Queries Level 5 中被添加。
update                       输出设备更新内容的渲染结果的频率。在 Media Queries Level 4 中被添加。
width                        视窗（viewport）的宽度。
*/
```

```html
<link rel="stylesheet" media="screen and (min-width: 900px)" href="widescreen.css" />
<link rel="stylesheet" media="screen and (max-width: 600px)" href="smallscreen.css" />
....
```

# @import 导入其他样式表

```css
@import urlstring list-of-mediaqueries; /**引用其他样式表,顶行 */

@import "navigation.css";
@import url("navigation.css");
@import "printstyle.css" print;
@import "mobstyle.css" screen and (max-width: 768px);
```

# transform 对元素进行旋转、缩放、移动或倾斜。

使用百分比做参数,是相对于元素自身,而不是父元素

通过使用 CSS `transform` 属性，您可以利用以下 2D 转换方法：

- `translate(x,y)` 方法从其当前位置移动元素（根据为 X 轴和 Y 轴指定的参数）。
- `rotate(n_deg)` 方法根据给定的角度顺时针或逆时针旋转元素。使用负值将逆时针旋转元素。
- `scaleX(宽)` 方法增加或减少元素的宽度。
- `scaleY(高)` 方法增加或减少元素的高度。
- `scale(宽，高)`方法增加或减少元素的大小（根据给定的宽度和高度参数）。
- `skewX(x_deg)` 方法使元素沿 X 轴倾斜给定角度
- `skewY(y_deg)` 方法使元素沿 Y 轴倾斜给定角度。
- `skew(x_deg,y_deg)` 方法使元素沿 X 和 Y 轴倾斜给定角度。
- `matrix(scaleX(),skewY(),skewX(),scaleY(),translateX(),translateY())` 方法把所有 2D 变换方法组合为一个。

通过 CSS transform 属性，您可以使用以下 3D 转换方法：

- `matrix3d(_n_,_n_,_n_,_n_,_n_,_n_, _n_,_n_,_n_,_n_,_n_,_n_,_n_,_n_,_n_,_n_)` 定义 3D 转换，使用 16 个值的 4x4 矩阵。
- `translate3d(_x_,_y_,_z_)` 元素平移
- `translateX(_x_)` 定义 3D 转化，仅使用用于 X 轴的值。
- `translateY(_y_)` 定义 3D 转化，仅使用用于 Y 轴的值。
- `translateZ(_z_)` 定义 3D 转化，仅使用用于 Z 轴的值。
- `scale3d(_x_,_y_,_z_)` 定义 3D 缩放转换。
- `scaleX(_x_)` 定义 3D 缩放转换，通过给定一个 X 轴的值。
- `scaleY(_y_)` 定义 3D 缩放转换，通过给定一个 Y 轴的值。
- `scaleZ(_z_)` 定义 3D 缩放转换，通过给定一个 Z 轴的值。
- `rotate3d(_x_,_y_,_z_,_angle_)` 定义 3D 旋转。
- `rotateX(_angle_)` 定义沿 X 轴的 3D 旋转。
- `rotateY(_angle_)` 定义沿 Y 轴的 3D 旋转。
- `rotateZ(_angle_)` 定义沿 Z 轴的 3D 旋转。
- `perspective(_n_)` 定义 3D 转换元素的透视视图。

```css
/**设置旋转的基点位置 */
transform-origin: x-axis y-axis z-axis;
/**规定如何在 3D 空间中呈现被嵌套的元素 不|保留 */
transform-style: flat|preserve-3d;

transform: rotate(45deg);
transform-origin: 20% 40%;
```

# 过渡

CSS 过渡允许您在给定的时间内平滑地改变属性值。

如需创建过渡效果，必须明确两件事：

- 您要添加效果的 CSS 属性
- 效果的持续时间

`注意：`如果未规定持续时间部分，则过渡不会有效果，因为默认值为 0。

```css
transition: property duration timing-function delay;
/**
- ease - 规定过渡效果，先缓慢地开始，然后加速，然后缓慢地结束（默认）
- linear - 规定从开始到结束具有相同速度的过渡效果
- ease-in -规定缓慢开始的过渡效果
- ease-out - 规定缓慢结束的过渡效果
- ease-in-out - 规定开始和结束较慢的过渡效果
- cubic-bezier(n,n,n,n) - 允许您在三次贝塞尔函数中定义自己的值
*/
transition-timing-function;/**规定过渡效果的速度曲线 */
transition-delay:2s;/**属性规定过渡效果的延迟 */
transition-duration:2s;/**规定完成过渡效果需要花费的时间（以秒或毫秒计） */
transition-property: none|all|property_name;/** 规定应用过渡效果的 CSS 属性的名称 */
```

# 动画

CSS 可实现 HTML 元素的动画效果，而不使用 JavaScript 或 Flash！

- @keyframes
- animation-name
- animation-duration
- animation-delay
- animation-iteration-count
- animation-direction
- animation-timing-function
- animation-fill-mode
- animation

## @keyframes

如果您在 @keyframes 规则中指定了 CSS 样式，动画将在特定时间逐渐从当前样式更改为新样式。

要使动画生效，必须将动画绑定到某个元素。

```css
/* 以百分比来规定改变发生的时间，或者通过关键词 "from" 和 "to"，等价于 0% 和 100%。 */
@keyframes example {
  from {
    background-color: red;
  }
  to {
    background-color: yellow;
  }
}
/* 建议使用 % */
@keyframes example2 {
  0% {
    background-color: red;
  }
  25% {
    background-color: yellow;
  }
  50% {
    background-color: blue;
  }
  100% {
    background-color: green;
  }
}

/* 向此元素应用动画效果 */
div {
  width: 100px;
  height: 100px;
  background-color: red;
  animation-name: example;
  animation-duration: 4s;
}
```

## animation

animation-delay 属性规定动画开始的延迟时间。

负值也是允许的。如果使用负值，则动画将开始播放，如同已播放 N 秒。

```css
animation: name duration timing-function delay iteration-count direction;
animation-name: keyframename|none; /** 为 @keyframes 动画规定名称。*/
animation-duration: time; /** 定义动画完成一个周期所需要的时间，以秒或毫秒计。*/
animation-delay: time; /**定义动画何时开始 */
animation-iteration-count: n|infinite; /** 定义动画的播放次数 n|无限*/
animation-direction: normal|alternate; /** 定义是否应该轮流反向播放动画。 */
/**
linear	动画从头到尾的速度是相同的。	测试
ease	默认。动画以低速开始，然后加快，在结束前变慢。	测试
ease-in	动画以低速开始。	测试
ease-out	动画以低速结束。	测试
ease-in-out	动画以低速开始和结束。	测试
cubic-bezier(n,n,n,n)	在 cubic-bezier 函数中自己的值。可能的值是从 0 到 1 的数值。
*/
animation-timing-function: value; /** 规定动画的速度曲线。 */
animation-fill-mode: none | forwards | backwards | both; /** 规定动画在播放之前或之后，其动画效果是否可见。*/
animation-play-state: paused|running; /**规定动画正在运行还是暂停 */
```

# css 参考

https://www.w3school.com.cn/cssref/index.asp
