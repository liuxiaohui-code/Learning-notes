# YAML

它的基本语法规则如下：

- 大小写敏感
- 使用缩进表示层级关系
- 缩进时不允许使用 Tab 键，只允许使用空格。
- 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
- `#` 表示注释，从这个字符一直到行尾，都会被解析器忽略。
- `---` 文件分隔符,单文件多配置实现
- json 的超集

根据 [YAML 规范](https://yaml.org/spec/1.2/spec.html)，有两种集合类型和很多标量类型。

- List （sequence）
- Map
- 标量值是单个值，（与集合相反）

也就是说，你可能会遇到 Lists 的 Maps 和 Maps 的 Lists，等等。不过不用担心，你只要掌握了这两种结构也就可以了，其他更加复杂的我们暂不讨论。

# Maps

首先我们来看看 Maps，我们都知道 Map 是字典，就是一个`key:value`的键值对，Maps 可以让我们更加方便的去书写配置信息，例如：

```yaml
--- # 文件分割符
apiVersion: v1
kind: Pod
metadata:
  name: kube100-site
  labels:
    app: web
```

或许你对上面的 JSON 文件更熟悉，但是你不得不承认 YAML 文件的语义化程度更高吧？

# Lists

Lists 就是列表，说白了就是数组，在 YAML 文件中我们可以这样定义：

```yaml
args
- Cat
- Dog
- Fish
```

当然，list 的子项也可以是 Maps，Maps 的子项也可以是 list 如下所示：

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: kube100-site
  labels:
    app: web
spec:
  containers:
    - name: front-end
      image: nginx
      ports:
        - containerPort: 80
    - name: flaskapp-demo
      image: jcdemo/flaskapp
      ports:
        - containerPort: 5000
```

比如这个 YAML 文件，我们定义了一个叫 containers 的 List 对象，每个子项都由 name、image、ports 组成，每个 ports 都有一个 key 为 containerPort 的 Map 组成，

# 数字和布尔

- 如果整型或浮点型数字没有引号，通常被视为数字类型；但是如果被引号引起来，会被当做字符串
- 布尔函数也是如此
- 空字符串是 `null` （不是 `nil`）。

# 字符串：

## 单行样式必须在一行：

- 裸字没有引号，也没有转义，因此，必须小心使用字符。
- 双引号字符串可以使用`\`转义指定字符。比如，`"\"Hello\", she said"`。可以使用`\n`转义换行。
- 单引号字符串是字面意义的字符串，不用`\`转义，只有单引号`''`需要转义，转成单个`'`。

## 多行样式：

```yaml
coffee: |
  # Commented first line
  Latte
  Cappuccino
  Espresso
```

上述会被当作`coffee`的字符串值，等同于`Latte\nCappuccino\nEspresso\n`。

## 控制多行字符串中的空格:

在上述示例中，使用了 `|` 来表示多行字符串。但是注意字符串后面有一个尾随的`\n`。如果需要 YAML 处理器去掉末尾的换行符，在`|` 后面添加`-`

```yaml
coffee: |-
  Latte
  Cappuccino
  Espresso
```

现在 `coffee`的值变成了： `Latte\nCappuccino\nEspresso` (没有末尾的`\n`)。

其他时候，可能希望保留尾随空格。可以使用 `|+`符号

现在`coffee`的值是 `Latte\nCappuccino\nEspresso\n\n\n`。

文本块中的缩进会被保留，也会保留行跳出的结果

## 折叠多行字符串

有事需要在 YAML 中表示多行字符串，但是被解释时被当做单行字符串。这被称为"折叠"。要声明一个折叠块，使用 `>` 代替 `|`：

```yaml
coffee: >
  Latte
  Cappuccino
  Espresso

# 上面coffee的值是： Latte Cappuccino Espresso\n
# >- 来替换或取消所有的新行
```

# 在一个文件中嵌入多个文档

可以将多个 YAML 文档放在单个文件中。 文档前使用 `---`，文档后使用 `...`

很多情况下，可以省略`---`或者`...`。

# YAML 是 JSON 的超集

由于 YAML 是一个 JSON 的超集，任何合法的 JSON 文档 _都应该_ 是合法的 YAML。

而且两种可以混合（要小心）

## YAML 锚点

```
定义： &锚点名 引用值
运用： *锚点名
```

锚点在一些场景中很有用，但另一方面，锚点可能会引起细微的错误：第一次使用 YAML 时，将展开引用，然后将其丢弃。

# YAML 节点标签

```yaml
age: !!str 21
port: !!int "80"
```

`!!str`告诉解释器`age`是一个字符串，

即使`port`被引号括起来，也会被视为 int

# 验证

> 可以使用http://www.yamllint.com/去检验 YAML 文件的合法性。
