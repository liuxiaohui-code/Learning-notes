# svg 画矢量图

可缩放矢量图形（Scalable Vector Graphics），使用 XML 格式定义图像；
SVG 文件可通过以下标签嵌入 HTML 文档：`<embed>`、`<object>` 或者 `<iframe>`。

```html
<embed
  src="rect.svg"
  width="300"
  height="100"
  type="image/svg+xml"
  pluginspage="https://image.16pic.com/00/72/73/16pic_7273289_s.png?imageView2/0/format/png" />

<object data="rect.svg" width="300" height="100" type="image/svg+xml" codebase="http://www.adobe.com/svg/viewer/install/" />

<iframe src="rect.svg" width="300" height="100"></iframe>
```

制作 svg 图形:记事本写入以下代码,保存为.svg 文件

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN"
"http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg width="100%" height="100%" version="1.1"
xmlns="http://www.w3.org/2000/svg">

<rect width="300" height="100"
style="fill:rgb(0,0,255);stroke-width:1;
stroke:rgb(0,0,0)"/>
</svg>
```

## 预定义形状

1. 矩形 `<rect>`
   1. `x="0"` 定义矩形到浏览器窗口左侧的距离是 0px
   2. `y="0"` 定义矩形到浏览器窗口顶端的距离是 0px
   3. `width="100"` 矩形宽
   4. `height="100"` 矩形高
   5. `rx="0"` 圆角 x
   6. `ry="0"` 圆角 y
2. 圆 `<circle>`
   1. `cx="0"` 圆心坐标 x
   2. `cy="0"` 圆心坐标 y
   3. `r="0"` 半径
3. 椭圆 `<ellipse>`
   1. cx 属性定义圆点的 x 坐标
   2. cy 属性定义圆点的 y 坐标
   3. rx 属性定义水平半径
   4. ry 属性定义垂直半径
4. 线 `<line>`
   1. x1 属性在 x 轴定义线条的开始
   2. y1 属性在 y 轴定义线条的开始
   3. x2 属性在 x 轴定义线条的结束
   4. y2 属性在 y 轴定义线条的结束
5. 折线 `<polyline>`
   1. points
6. 多边形 `<polygon>`
   1. `points="1,1 2,2 3,8"` 属性定义多边形每个角的 x 和 y 坐标
7. 路径 `<path>`
   1. 指令;允许小写字母。大写表示绝对定位，小写表示相对定位
      1. `Mx y` 开始位置(x,y)
      2. `Lx y` 到达位置(x,y) 直线连接
      3. `Hx` 直线到达位置(x,0)
      4. `Vy` 直线到达位置(0,y)
      5. `Cx1 y1 x2 y2 x y` 从当前位置到位置(x, y)，绘制一条 cubic Bezier curve。（x1, y1) 是曲线起始位置的控制点；(x2, y2) 是曲线终止位置的控制点。
      6. `Sx2 y2 x y` 绘制从当前点到(x,y)的三次 Bézier 曲线。假设第一个控制点是相对于当前点的前一个命令的第二个控制点的反映。(如果没有先前的命令，或者先前的命令不是 C、C、S 或 S，则假定第一个控制点与当前控制点重合。) (x2,y2)是第二个控制点(即曲线末端的控制点)。
      7. `Qx1 y1 x y` 以(x1,y1)为控制点，绘制从当前点到(x,y)的二次 Bézier 曲线。
      8. `Tx y` 绘制从当前点到(x,y)的二次 Bézier 曲线。控制点被假定为控制点相对于当前点在前一个命令上的反映。(如果没有先前的命令，或者先前的命令不是 Q、Q、T 或 T，则假设控制点与当前控制点重合。)
      9. `A = elliptical Arc`
      10. `Z` 结束标志,首尾相连
   2. `d="M250 150 L150 350 L350 350 Z"`

```html

```

## style

使用属性`style`或直接作为属性值都可以;

```css
fill: rgb() |#0000|color; /*颜色填充*/
stroke-width: number; /*边框宽度*/
stoke: color; /*边框颜色*/
opacity: 0.5; /*透明度*/
```

## 滤镜

您可以在每个 SVG 元素上使用多个滤镜！
feBlend
feColorMatrix
feComponentTransfer
feComposite
feConvolveMatrix
feDiffuseLighting
feDisplacementMap
feFlood
feGaussianBlur
feImage
feMerge
feMorphology
feOffset
feSpecularLighting
feTile
feTurbulence
feDistantLight
fePointLight
feSpotLight

使用时,在 svg 的 style 属性增加`filter:url(#filter_id)`指定滤镜

```html
<defs>
  <filter id="Gaussian_Blur">
    <!--高斯滤镜-->
    <feGaussianBlur in="SourceGraphic" stdDeviation="3" />
  </filter>
</defs>
```

1. `<filter>` 标签用来定义 SVG 滤镜。`<filter>` 标签使用必需的 id 属性来定义向图形应用哪个滤镜.
   1. `id`
2. `<defs>` 标签是 definitions 的缩写，它允许对诸如滤镜等特殊元素进行定义。`<filter> `标签必须嵌套在 `<defs>` 标签内。
3. `<feGaussianBlur>` 滤镜元素
   1. `in="SourceGraphic"` 这个部分定义了由整个图像创建效果
   2. `stdDeviation` 属性可定义模糊的程度

## 渐变

1. `<linearGradient>` 定义 SVG 的线性渐变。必须嵌套在 `<defs>` 的内部
   1. 属性
      1. `id=""` 映射到图形的`style="fill:url(#id)"`
      2. `x1="0%" y1="0%"` 开始位置
      3. `x2="0%" y2="0%"` 结束位置
   2. 当 y1 和 y2 相等，而 x1 和 x2 不同时，可创建水平渐变
   3. 当 x1 和 x2 相等，而 y1 和 y2 不同时，可创建垂直渐变
   4. 当 x1 和 x2 不同，且 y1 和 y2 不同时，可创建角形渐变
2. `<stop />`渐变的颜色范围可由两种或多种颜色组成。每种颜色通过一个 `<stop>` 标签来规定。
   1. `offest="0%"`用来定义渐变的开始和结束位置
   2. `style="background-color:;stop-opacity:1"`
3. `<radialGradient>` 用来定义放射性渐变。必须嵌套在 `<defs>` 中
   1. 属性
      1. id
      2. cx="50%" cy="50%" r="50%" fx="50%" fy="50%" cx、cy 和 r 属性定义外圈，而 fx 和 fy 定义内圈

## svg 元素

- `a` 定义超链接
- `altGlyph` 允许对象性文字进行控制，来呈现特殊的字符数据（例如，音乐符号或亚洲的文字）
- `altGlyphDef` 定义一系列象性符号的替换（例如，音乐符号或者亚洲文字）
- `altGlyphItem` 定义一系列候选的象性符号的替换
- `animate` 随时间动态改变属性
- `animateColor` 规定随时间进行的颜色转换
- `animateMotion` 使元素沿着动作路径移动
- `animateTransform` 对元素进行动态的属性转换
- `circle` 定义圆
- `clipPath`
- `color`-`profile` 规定颜色配置描述
- `cursor` 定义独立于平台的光标
- `definition`-`src` 定义单独的字体定义源
- `defs` 被引用元素的容器
- `desc` 对 SVG 中的元素的纯文本描述 - 并不作为图形的一部分来显示。用户代理会将其显示为工具提示。
- `ellipse` 定义椭圆
- `feBlend` SVG 滤镜。使用不同的混合模式把两个对象合成在一起。
- `feColorMatrix` SVG 滤镜。应用 matrix 转换。
- `feComponentTransfer` SVG 滤镜。执行数据的 component-wise 重映射。
- `feComposite` SVG 滤镜。
- `feConvolveMatrix` SVG 滤镜。
- `feDiffuseLighting` SVG 滤镜。
- `feDisplacementMap` SVG 滤镜。
- `feDistantLight` SVG 滤镜。 Defines a light source
- `feFlood` SVG 滤镜。
- `feFuncA` SVG 滤镜。feComponentTransfer 的子元素。
- `feFuncB` SVG 滤镜。feComponentTransfer 的子元素。
- `feFuncG` SVG 滤镜。feComponentTransfer 的子元素。
- `feFuncR` SVG 滤镜。feComponentTransfer 的子元素。
- `feGaussianBlur` SVG 滤镜。对图像执行高斯模糊。
- `feImage` SVG 滤镜。
- `feMerge` SVG 滤镜。创建累积而上的图像。
- `feMergeNode` SVG 滤镜。feMerge 的子元素。
- `feMorphology` SVG 滤镜。 对源图形执行"`fattening`" 或者 "`thinning`"。
- `feOffset` SVG 滤镜。相对与图形的当前位置来移动图像。
- `fePointLight` SVG 滤镜。
- `feSpecularLighting` SVG 滤镜。
- `feSpotLight` SVG 滤镜。
- `feTile` SVG 滤镜。
- `feTurbulence` SVG 滤镜。
- `filter` 滤镜效果的容器。
- `font` 定义字体。
- `font-face` 描述某字体的特点。
- `font-face-format`
- `font-face-name`
- `font-face-src`
- `font-face-uri`
- `foreignObject`
- `g` 用于把相关元素进行组合的容器元素。
- `glyph` 为给定的象形符号定义图形。
- `glyphRef` 定义要使用的可能的象形符号。
- `hkern`
- `image`
- `line` 定义线条。
- `linearGradient` 定义线性渐变。
- `marker`
- `mask`
- `metadata` 规定元数据。
- `missing-glyph`
- `mpath`
- `path` 定义路径。
- `pattern`
- `polygon` 定义由一系列连接的直线组成的封闭形状。
- `polyline` 定义一系列连接的直线。
- `radialGradient` 定义放射形的渐变。
- `rect` 定义矩形。
- `script` 脚本容器。（例如 ECMAScript）
- `set` 为指定持续时间的属性设置值
- `stop`
- `style` 可使样式表直接嵌入 SVG 内容内部。
- `svg` 定义 SVG 文档片断。
- `switch`
- `symbol`
- `text`
- `textPath`
- `title` 对 SVG 中的元素的纯文本描述 - 并不作为图形的一部分来显示。用户代理会将其显示为工具提示。
- `tref`
- `tspan`
- `use`
- `view`
- `vkern`
