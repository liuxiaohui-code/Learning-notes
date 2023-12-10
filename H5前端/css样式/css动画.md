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