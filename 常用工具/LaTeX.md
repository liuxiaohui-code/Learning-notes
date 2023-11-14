# 数学公式 LaTeX

-   行内公式: $f(x)=x^2+1x+2$
-   行间公式:

$$
f(x)=x^2+1x+2
$$

上标和下标分别使用`^` 与`_` ,例如 $x^2_i$,默认情况下,上、下标符号仅仅对下一个组起作用。一个组即单个字符或者使用`{..}` 包裹起来的内容;上下标可以嵌套,也可以同时使用

# 多行

## 多行表达式

-   公式中`\begin{equation}...\end{equation}` 表示方程开始与结束
-   `\begin{split}...end{split}` 表示开始多行公式开始与结
-   `\\` 表示回车到下一行
-   `&` 表示对齐的位置
    $$
    \begin{equation}
    \begin{split}
    a&=(b+c)\times{d} \\
    &\quad +(e-f)     \\
    &=g+h             \\
    & =i
    \end{split}
    \end{equation}
    $$

## 方程组

-   公式中`\left \{ ... \right.`表示方程的左边和右边，
-   `\begin{array}...\end{array}` 表示方程组的开始与结束
-   `.`表示空格, 但`a..b`与`a.b`都会显示`ab`。如需要显示 a b，可用\增加些许空隙, `\;`与`\quad{}`增加较宽间隙, `\qqaud{}`增加更大间隙。
    $$
    \left \{
    \begin{array}{c}
    a_1x+b_1y+c_1z=d_1 \\
    a_2x+b_2y+c_2z=d_2 \\
    a_3x+b_3y+c_3z=d_3
    \end{array}
    \right.
    $$

## 分类表达式

可以使用`\\[4ex] `代替`\\` 来将分类之间的垂直间隔变大。其中`[4ex]` 中的数字代表间隔距离，`1ex` 相当于原始距离，数字越大，距离越大。

$$
f(n)=
\begin{cases}
\cfrac n2, &if\ n\ is\ even\\
3n^2 + n+1, &if\  n\ is\ odd
\end{cases}
$$

$$
L(Y,f(X)) =
\begin{cases}
0, & \text{Y = f(X)} \\[4ex]
1, & \text{Y $\neq$ f(X)}
\end{cases}
$$

## 注释方程式

在 `{align}` 中灵活组合 `\text` 和 `\tag` 语句。\tag 语句编号优先级高于自动编号。

$$
\begin{align}
   v + w & = 0  &\text{Given} \tag 1\\
   -w & = -w + 0 & \text{additive identity} \tag 2\\
   -w + 0 & = -w + (v + w) & \text{equations $(1)$ and $(2)$} \tag 3
\end{align}
$$

# 公式中的字母

| name    | 大写 | code      | 小写 | code       |
| ------- | ---- | --------- | ---- | ---------- |
| alpha   | A    | $A $      | α    | $\alpha $  |
| beta    | B    | $B $      | β    | $\beta $   |
| gamma   | Γ    | $\Gamma$  | γ    | $\gamma $  |
| delta   | Δ    | $\Delta$  | δ    | $\delta $  |
| epsilon | E    | $E $      | ϵ    | $\epsilon$ |
| zeta    | Z    | $Z $      | ζ    | $\zeta $   |
| eta     | H    | $H $      | η    | $\eta $    |
| theta   | Θ    | $\Theta$  | θ    | $\theta $  |
| iota    | I    | $I $      | ι    | $\iota $   |
| kappa   | K    | $K $      | κ    | $\kappa $  |
| lambda  | Λ    | $\Lambda$ | λ    | $\lambda $ |
| mu      | M    | $M $      | μ    | $\mu $     |
| nu      | N    | $N $      | ν    | $\nu $     |
| xi      | Ξ    | $\Xi $    | ξ    | $\xi $     |
| omicron | O    | $O $      | ο    | $\omicron$ |
| pi      | Π    | $\Pi $    | π    | $\pi $     |
| rho     | P    | $P $      | ρ    | $\rho $    |
| sigma   | Σ    | $\Sigma$  | σ    | $\sigma $  |
| tau     | T    | $T $      | τ    | $\tau $    |
| upsilon | υυ   | $υ $      | υ    | $\upsilon$ |
| phi     | Φ    | $\Phi $   | ϕ    | $\phi $    |
| chi     | X    | $X $      | χ    | $\chi $    |
| psi     | Ψ    | $\Psi $   | ψ    | $\psi $    |
| omega   | Ω    | $\Omega$  | ω    | $\omega $  |
| ell_p   | ℓp   | $\ell_p$  |

变量专用形式$\var-

| $ code     | 小写 | code     | 大写 | var-code      | 变量 |
| ---------- | ---- | -------- | ---- | ------------- | ---- |
| $\epsilon$ | ϵ    | $E $     | E    | $\varepsilon$ | ε    |
| $\theta $  | θ    | $\Theta$ | Θ    | $\vartheta $  | ϑ    |
| $\rho $    | ρ    | $P $     | P    | $\varrho $    | ϱ    |
| $\sigma $  | σ    | $\Sigma$ | Σ    | $\varsigma $  | ς    |
| $\phi $    | ϕ    | $\Phi $  | Φ    | $\varphi $    | φ    |

# 公式中的常见符号

## 上下标

1. $x_i^2$ 或 $x^2_i$
2. 默认情况下,上下标对一个组起作用,一个组即单个字符或`{..}`包裹起来的内容
3. 上下标可以嵌套,也可以同时使用 ${x^y}^z = {(1+e^x)}^{-2xy^w}$
4. 如果要在左右两边都有上下标,可以用$\sideset$命令

## 括号与切割符

| code    | 显示 | code    | 显示 | code    | 显示 | code    | 显示 |
| ------- | ---- | ------- | ---- | ------- | ---- | ------- | ---- |
| $\langle$ | ⟨    | $\rangle$ | ⟩    | $\lceil $ | ⌈    | $\rceil $ | ⌉    |
| $\lfloor$ | ⌊    | $\rfloor$ | ⌋    | $\lbrace$ | {    | $\rbrace$ | }    |
