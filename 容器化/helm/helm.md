# helm

# 目录结构

```sh
mychart/
  - Chart.yaml #文件包含了该chart的描述。你可以从模板中访问它。
  - values.yaml  #文件也导入到了模板。这个文件包含了chart的 默认值 。这些值会在用户执行helm install 或 helm upgrade时被覆盖。
  - charts/  #可以 包含其他的chart(称之为 子chart)
  - templates/  #目录包括了模板文件。当Helm评估chart时，会通过模板渲染引擎将所有文件发送到templates/目录中。 然后收集模板的结果并发送给Kubernetes。
  -	LICENSE             # 可选: 包含chart许可证的纯文本文件
  - README.md           # 可选: 可读的README文件
  - values.schema.json  # 可选: 一个使用JSON结构的values.yaml文件
  - crds/               # 自定义资源的定义
  
  mychart/templates/
  	- NOTES.txt: chart的"帮助文本"。这会在你的用户执行helm install时展示给他们。
	- deployment.yaml: 创建Kubernetes 工作负载的基本清单
 	- service.yaml: 为你的工作负载创建一个 service终端基本清单。
	- _helpers.tpl: 放置可以通过chart复用的模板辅助对象
```

# helm命令

```sh
$ helm create helmname  # 创建helm模板
$ helm get manifest helmname #查看生成的完整的YAML
$ helm package  # 生成一个包
$ helm dependency update # 就会使用你的依赖文件下载所有你指定的chart到你的charts/目录
$ helm install --debug --dry-run bcb ./bcb
```

# helm内置对象

### Release

```sh
Release	#  该对象描述了版本发布本身。包含了以下对象：
	- Release.Name	# release名称
	- Release.Namespace	# 版本中包含的命名空间(如果manifest没有覆盖的话)
	- Release.IsUpgrade	#  如果当前操作是升级或回滚的话，需要将该值设置为true
	- Release.IsInstall	#  如果当前操作是安装的话，需要将该值设置为true
	- Release.Revision	#  此次修订的版本号。安装时是1，每次升级或回滚都会自增
	- Release.Service	#  该service用来渲染当前模板。Helm里一般是Helm
```

### Values

```sh
#  Values是从values.yaml文件和用户提供的文件传进模板的。Values默认为空
```

**value.yaml**

该对象提供了对传递到chart的值的访问方法， 其内容源包括了多个位置：

- chart中的`values.yaml`文件
- 如果是子chart，就是父chart中的`values.yaml`文件
- 使用`-f`参数(`helm install -f myvals.yaml ./mychart`)传递到 `helm install` 或 `helm upgrade`的values文件
- 使用`--set` (比如`helm install --set foo=bar ./mychart`)传递的单个参数
- 从默认的value中删除key，可以将key设置为`null`

> 顺序：默认使用`values.yaml`，可以被父chart的`values.yaml`覆盖，继而被用户提供values文件覆盖， 最后会被`--set`参数覆盖。

Helm中的有些文件无法包含多个文档。比如，如果`values.yaml`文件提供了多个文档，只会使用第一个。

`values.yaml` 可能包含JSON数据，Helm将`.json`后缀文件视为不合法的文件。

### Chart

```sh
Chart	#  Chart.yaml文件内容。 Chart.yaml里的任意数据在这里都可以可访问的。比如 {{ .Chart.Name }}-{{ .Chart.Version }} 会打印出 mychart-0.1.0
	# Chart 指南 中列出了可用字段
```

**Chart.yaml**

`Chart.yaml`文件是chart必需的。包含了以下字段：

```yaml
apiVersion: chart API 版本 （必需） # 当前 v2,支持之前v1
name: chart名称 （必需）
version: 语义化2 版本（必需）
kubeVersion: 兼容Kubernetes版本的语义化版本（可选）eg: >= 	1.13.0 < 1.14.0 || >= 1.14.1 < 1.15.0
	闭合间隔的连字符范围， 1.1 - 2.3.4 等价于 >= 1.1 <= 2.3.4
	通配符 x， X 和 *， 1.2.x 等价于 >= 1.2.0 < 1.3.0
	波浪符号~范围 （允许改变补丁版本）， ~1.2.3 等价于 >= 1.2.3 	< 1.3.0
	插入符号^范围 （允许改变次版本）， ^1.2.3 等价于 >= 1.2.3 < 		2.0.0
description: 一句话对这个项目的描述（可选）
type: chart类型 （可选） #application(默认) 和 library
keywords:
  - 关于项目的一组关键字（可选）
home: 项目home页面的URL （可选）
sources:
  - 项目源码的URL列表（可选）
dependencies: # chart 必要条件列表 （可选）
  - name: chart名称 (nginx)
    version: chart版本 ("1.2.3")
    repository: （可选）仓库URL ("https://example.com/charts") 或别名 ("@repo-name")
    condition: （可选） 解析为布尔值的yaml路径，用于启用/禁用chart (e.g. subchart1.enabled )
    tags: # （可选）
      - 用于一次启用/禁用 一组chart的tag
    import-values: # （可选）
      - ImportValue 保存源值到导入父键的映射。每项可以是字符串或者一对子/父列表项
    alias: （可选） chart中使用的别名。当你要多次添加相同的chart时会很有用
maintainers: # （可选）
  - name: 维护者名字 （每个维护者都需要）
    email: 维护者邮箱 （每个维护者可选）
    url: 维护者URL （每个维护者可选）
icon: 用做icon的SVG或PNG图片URL （可选）
appVersion: 包含的应用版本（可选）。不需要是语义化，建议使用引号
deprecated: 不被推荐的chart （可选，布尔值）
annotations:
  example: 按名称输入的批注列表 （可选）.
```

> 从 [v3.3.2](https://github.com/helm/helm/releases/tag/v3.3.2)，不再允许额外的字段。推荐的方法是在 `annotations` 中添加自定义元数据。

### Files

```sh
Files	#  在chart中提供访问所有的非特殊文件。当你不能使用它访问模板时，你可以访问其他文件。 请查看这个 文件访问部分了解更多信息
	- Files.Get 	# 通过文件名获取文件的方法。 （.Files.Getconfig.ini）
	- Files.GetBytes 	# 用字节数组代替字符串获取文件内容的方法。 对图片之类的文件很有用
	- Files.Glob "**.yaml"	# 用给定的shell glob模式匹配文件名返回文件列表的方法
	- Files.Lines 	# 逐行读取文件内容的方法。迭代文件中每一行时很有用
	- Files.AsSecrets 	# 使用Base 64编码字符串返回文件体的方法
	- Files.AsConfig	#  使用YAML格式返回文件体的方法
```

### Capabilities

```sh
Capabilities	#  提供关于Kubernetes集群支持功能的信息
	- Capabilities.APIVersions 	# 是一个版本集合
	- Capabilities.APIVersions.Has $version 	# 说明集群中的版本 (e.g., batch/v1) 或是资源 (e.g., apps/v1/Deployment) 是否可用
	- Capabilities.KubeVersion 	# 和 
	- Capabilities.KubeVersion.Version 	# 是Kubernetes的版本号
	- Capabilities.KubeVersion.Major 	# Kubernetes的主版本
	- Capabilities.KubeVersion.Minor 	# Kubernetes的次版本
	- Capabilities.HelmVersion 	# 包含Helm版本详细信息的对象，和 helm version 的输出一致
	- Capabilities.HelmVersion.Version 	# 是当前Helm版本的语义格式
	- Capabilities.HelmVersion.GitCommit 	# Helm的git sha1值
	- Capabilities.HelmVersion.GitTreeState 	# 是Helm git树的状态
	- Capabilities.HelmVersion.GoVersion 	# 是使用的Go编译器版本
Template	#  包含了已经被执行的当前模板信息
	- Template.Name	# 当前模板的命名空间文件路径 (e.g. mychart/templates/mytemplate.yaml)
	- Template.BasePath	# 当前chart模板目录的路径 (e.g. mychart/templates)
```

> 内置的值都是以大写字母开始。 这是符合Go的命名惯例。当你创建自己的名称时，可以按照团队约定自由设置。 就像很多你在 [Artifact Hub](https://artifacthub.io/packages/search?kind=0) 中看到的chart，其团队选择使用首字母小写将本地名称与内置对象区分开，本指南中我们遵循该惯例。

> 

## 

```
functionName arg1 arg2...

# 模板命令要括在 {{ 和 }} 之间。用点(.)分隔每个命名空间的元素
```

管道符

```
|
倒置命令是模板中的常见做法。可以经常看到 .val | quote 而不是 quote .val。两种操作都是可以的。
```

函数

```sh
--go 模板语言  https://pkg.go.dev/text/template?utm_source=godoc#section-directories

--spring模板库  https://masterminds.github.io/sprig/
quote # 引用字符串
upper  # 大写
repeat n # 复写
default DEFAULT_VALUE GIVEN_VALUE # 在模板中指定一个默认值，以防这个值被忽略 整型: 0 字符串: "" 列表: [] 字典: {} 布尔: false 以及所有的nil (或 null)
lookup # 查找 apiVersion, kind, namespace, name -> 资源或者资源列表  
# kubectl get pod mypod -n mynamespace	
# lookup "v1" "Pod" "mynamespace" "mypod"
# {{ range $index, $service := (lookup "v1" "Service" "mynamespace" "").items }}
#    {{/* do something with each service */}}
# {{ end }}
运算符(eq, ne, lt, gt, and, or等等) 

https://helm.sh/zh/docs/chart_template_guide/function_list/#file-path-functions

Helm 包括了需要逻辑和流控制函数，包括 and, coalesce, default, empty, eq, fail, ge, gt, le, lt, ne, not, and or。

Helm 包含了一下字符串函数： abbrev, abbrevboth, camelcase, cat, contains, hasPrefix, hasSuffix, indent, initials, kebabcase, lower, nindent, nospace, plural, print, printf, println, quote, randAlpha, randAlphaNum, randAscii, randNumeric, repeat, replace, shuffle, snakecase, squote, substr, swapcase, title, trim, trimAll, trimPrefix, trimSuffix, trunc, untitle, upper, wrap, 和 wrapWith

Helm 包含以下可以在模板中使用的Date Functions函数： ago, date, dateInZone, dateModify(mustDateModify), duration, durationRound, htmlDate, htmlDateInZone, now, toDate(mustToDate), and unixEpoch。

Helm 提供了以下函数支持使用字典： deepCopy(mustDeepCopy), dict, get, hasKey, keys, merge (mustMerge), mergeOverwrite (mustMergeOverwrite), omit, pick, pluck, set, unset,和 values。

Helm有以下编码和解码函数：
b64enc/b64dec: 编码或解码 Base64
b32enc/b32dec: 编码或解码 Base32

Helm模板函数没有访问文件系统的权限，提供了遵循文件路径规范的函数。包括 base, clean, dir, ext, 和 isAbs 。

Helm 通过 kind functions 和 type functions 提供了一组函数。 deepEqual 也可以用来比较值。

有些版本结构易于分析和比较。Helm提供了适用于 SemVer 2 版本的函数。包括 semver和 semverCompare。

Helm 包含 urlParse, urlJoin, 和 urlquery 函数可以用做处理URL。

Helm 可以生成UUID v4 通用唯一ID。uuidv4

Helm 包含了用于 Kubernetes的函数，包括 .Capabilities.APIVersions.Has, Files, 和 lookup。
```

# 模板函数

## 逻辑和流控制

Helm 包括了需要逻辑和流控制函数，包括 [and](https://helm.sh/zh/docs/chart_template_guide/function_list/#and), [coalesce](https://helm.sh/zh/docs/chart_template_guide/function_list/#coalesce), [default](https://helm.sh/zh/docs/chart_template_guide/function_list/#default), [empty](https://helm.sh/zh/docs/chart_template_guide/function_list/#empty), [eq](https://helm.sh/zh/docs/chart_template_guide/function_list/#eq), [fail](https://helm.sh/zh/docs/chart_template_guide/function_list/#fail), [ge](https://helm.sh/zh/docs/chart_template_guide/function_list/#ge), [gt](https://helm.sh/zh/docs/chart_template_guide/function_list/#gt), [le](https://helm.sh/zh/docs/chart_template_guide/function_list/#le), [lt](https://helm.sh/zh/docs/chart_template_guide/function_list/#lt), [ne](https://helm.sh/zh/docs/chart_template_guide/function_list/#ne), [not](https://helm.sh/zh/docs/chart_template_guide/function_list/#not), and [or](https://helm.sh/zh/docs/chart_template_guide/function_list/#or)。

### and

返回两个参数的and布尔值。

```yaml
and .Arg1 .Arg2
```

### or

返回两个参数的or布尔值。会返回第一个非空参数或最后一个参数。

```yaml
or .Arg1 .Arg2
```

### not

返回参数的布尔求反。

```yaml
not .Arg
```

### eq

返回参数的布尔等式（比如， Arg1 == Arg2）。

```yaml
eq .Arg1 .Arg2
```

### ne

返回参数的布尔非等式（比如 Arg1 != Arg2）。

```yaml
ne .Arg1 .Arg2
```

### lt

如果第一参数小于第二参数，返回布尔真。否则返回假（比如， Arg1 < Arg2）。

```yaml
lt .Arg1 .Arg2
```

### le

如果第一参数小于等于第二参数，返回布尔真，否则返回假（比如， Arg1 <= Arg2）。

```yaml
le .Arg1 .Arg2
```

### gt

如果第一参数大于第二参数，返回布尔真，否则返回假（比如， Arg1 > Arg2）。

```yaml
gt .Arg1 .Arg2
```

### ge

如果第一参数大于等于第二参数，返回布尔真，否则返回假。（比如， Arg1 >= Arg2）。

```yaml
ge .Arg1 .Arg2
```

### default

使用`default`设置一个简单的默认值。

```yaml
default "foo" .Bar
```

上述示例中，如果`.Bar`是非空值，则使用它，否则会返回`foo`。

"空"定义取决于以下类型：

- 整型: 0
- 字符串: ""
- 列表: `[]`
- 字典: `{}`
- 布尔: `false`
- 以及所有的`nil` (或 null)

对于结构体，没有空的定义，所以结构体从来不会返回默认值。

### empty

如果给定的值被认为是空的，则`empty`函数返回`true`，否则返回`false`。空值列举在`default`部分。

```yaml
empty .Foo
```

注意在Go模板条件中，空值是为你计算出来的。这样你很少需要 `if empty .Foo` ，仅使用 `if .Foo` 即可。

### fail

无条件地返回带有指定文本的空 `string` 或者 `error`。这在其他条件已经确定而模板渲染应该失败的情况下很有用。

```yaml
fail "Please accept the end user license agreement"
```

### coalesce

`coalesce`函数获取一个列表并返回第一个非空值。

```yaml
coalesce 0 1 2
```

上述会返回`1`。

此函数用于扫描多个变量或值：

```yaml
coalesce .name .parent.name "Matt"
```

上述示例会优先检查 `.name` 是否为空。如果不是，就返回值。如果 *是* 空, 继续检查`.parent.name`。 最终，如果 `.name` 和 `.parent.name` 都是空，就会返回 `Matt`。

### ternary

`ternary`函数获取两个值和一个test值。如果test值是true，则返回第一个值。如果test值是空，则返回第二个值。 这和C或其他编程语言中的的ternary运算符类似。

#### true test value

```yaml
ternary "foo" "bar" true
```

或者

```yaml
true | ternary "foo" "bar"
```

上述返回 `"foo"`。

#### false test value

```yaml
ternary "foo" "bar" false
```

或者

```yaml
false | ternary "foo" "bar"
```

上述返回 `"bar"`.

## 字符串函数

Helm 包含了一下字符串函数： [abbrev](https://helm.sh/zh/docs/chart_template_guide/function_list/#abbrev), [abbrevboth](https://helm.sh/zh/docs/chart_template_guide/function_list/#abbrevboth), [camelcase](https://helm.sh/zh/docs/chart_template_guide/function_list/#camelcase), [cat](https://helm.sh/zh/docs/chart_template_guide/function_list/#cat), [contains](https://helm.sh/zh/docs/chart_template_guide/function_list/#contains), [hasPrefix](https://helm.sh/zh/docs/chart_template_guide/function_list/#hasprefix-and-hassuffix), [hasSuffix](https://helm.sh/zh/docs/chart_template_guide/function_list/#hasprefix-and-hassuffix), [indent](https://helm.sh/zh/docs/chart_template_guide/function_list/#indent), [initials](https://helm.sh/zh/docs/chart_template_guide/function_list/#initials), [kebabcase](https://helm.sh/zh/docs/chart_template_guide/function_list/#kebabcase), [lower](https://helm.sh/zh/docs/chart_template_guide/function_list/#lower), [nindent](https://helm.sh/zh/docs/chart_template_guide/function_list/#nindent), [nospace](https://helm.sh/zh/docs/chart_template_guide/function_list/#nospace), [plural](https://helm.sh/zh/docs/chart_template_guide/function_list/#plural), [print](https://helm.sh/zh/docs/chart_template_guide/function_list/#print), [printf](https://helm.sh/zh/docs/chart_template_guide/function_list/#printf), [println](https://helm.sh/zh/docs/chart_template_guide/function_list/#println), [quote](https://helm.sh/zh/docs/chart_template_guide/function_list/#quote-and-squote), [randAlpha](https://helm.sh/zh/docs/chart_template_guide/function_list/#randalphanum-randalpha-randnumeric-and-randascii), [randAlphaNum](https://helm.sh/zh/docs/chart_template_guide/function_list/#randalphanum-randalpha-randnumeric-and-randascii), [randAscii](https://helm.sh/zh/docs/chart_template_guide/function_list/#randalphanum-randalpha-randnumeric-and-randascii), [randNumeric](https://helm.sh/zh/docs/chart_template_guide/function_list/#randalphanum-randalpha-randnumeric-and-randascii), [repeat](https://helm.sh/zh/docs/chart_template_guide/function_list/#repeat), [replace](https://helm.sh/zh/docs/chart_template_guide/function_list/#replace), [shuffle](https://helm.sh/zh/docs/chart_template_guide/function_list/#shuffle), [snakecase](https://helm.sh/zh/docs/chart_template_guide/function_list/#snakecase), [squote](https://helm.sh/zh/docs/chart_template_guide/function_list/#quote-and-squote), [substr](https://helm.sh/zh/docs/chart_template_guide/function_list/#substr), [swapcase](https://helm.sh/zh/docs/chart_template_guide/function_list/#swapcase), [title](https://helm.sh/zh/docs/chart_template_guide/function_list/#title), [trim](https://helm.sh/zh/docs/chart_template_guide/function_list/#trim), [trimAll](https://helm.sh/zh/docs/chart_template_guide/function_list/#trimall), [trimPrefix](https://helm.sh/zh/docs/chart_template_guide/function_list/#trimprefix), [trimSuffix](https://helm.sh/zh/docs/chart_template_guide/function_list/#trimsuffix), [trunc](https://helm.sh/zh/docs/chart_template_guide/function_list/#trunc), [untitle](https://helm.sh/zh/docs/chart_template_guide/function_list/#untitle), [upper](https://helm.sh/zh/docs/chart_template_guide/function_list/#upper), [wrap](https://helm.sh/zh/docs/chart_template_guide/function_list/#wrap), 和 [wrapWith](https://helm.sh/zh/docs/chart_template_guide/function_list/#wrapwith)

### print

返回各部分组合的字符串。

```yaml
print "Matt has " .Dogs " dogs"
```

如果可能，非字符串类型会被转换成字符串。

注意，当相邻两个参数不是字符串时会在它们之间添加一个空格。

### println

和 [print](https://helm.sh/zh/docs/chart_template_guide/function_list/#print)效果一样，但会在末尾新添加一行。

### printf

返回参数按顺序传递的格式化字符串。

```yaml
printf "%s has %d dogs." .Name .NumberDogs
```

占位符取决于传入的参数类型。这包括：

一般用途：

- `%v`默认格式的值
  - 当打印字典时，加号参数(`%+v`)可以添加字段名称
- `%%` 字符百分号，不使用值

布尔值：

- `%t` true或false

整型：

- `%b` 二进制
- `%c` the character represented by the corresponding Unicode code point
- `%d` 十进制
- `%o` 8进制
- `%O` 带0o前缀的8进制
- `%q` 安全转义的单引号字符
- `%x` 16进制，使用小写字符a-f
- `%X` 16进制，使用小写字符A-F
- `%U` Unicode格式： U+1234; 和"U+%04X"相同

浮点数和复杂成分

- `%b` 指数二次幂的无小数科学计数法，比如 -123456p-78
- `%e` 科学计数法，比如： -1.234456e+78
- `%E` 科学计数法，比如： -1.234456E+78
- `%f` 无指数的小数，比如： 123.456
- `%F` 与%f同义
- `%g` %e的大指数，否则是%f
- `%G` %E的大指数，否则是%F
- `%x` 十六进制计数法(和两个指数的十进制幂)，比如： -0x1.23abcp+20
- `%X` 大写的十六进制计数法，比如： -0X1.23ABCP+20

字符串和字节切片：

- `%s` 未解析的二进制字符串或切片
- `%q` 安全转义的双引号字符串
- `%x` 十六进制，小写，每个字节两个字符
- `%X` 十六进制，大写，每个字节两个字符

切片：

- `%p` 16进制的第0个元素的地址，以0x开头

### trim

`trim`行数移除字符串两边的空格：

```yaml
trim "   hello    "
```

上述结果为： `hello`

### trimAll

从字符串中移除给定的字符：

```yaml
trimAll "$" "$5.00"
```

上述结果为：`5.00` (作为一个字符串)。

### trimPrefix

从字符串中移除前缀：

```yaml
trimPrefix "-" "-hello"
```

上述结果为：`hello`

### trimSuffix

从字符串中移除后缀：

```yaml
trimSuffix "-" "hello-"
```

上述结果为： `hello`

### lower

将整个字符串转换成小写：

```yaml
lower "HELLO"
```

上述结果为： `hello`

### upper

将整个字符串转换成大写：

```yaml
upper "hello"
```

上述结果为： `HELLO`

### title

首字母转换成大写：

```yaml
title "hello world"
```

上述结果为： `Hello World`

### untitle

移除首字母大写：`untitle "Hello World"` 会得到 `hello world`.

### repeat

重复字符串多次：

```yaml
repeat 3 "hello"
```

上述结果为： `hellohellohello`

### substr

获取字符串的子串，有三个参数：

- start (int)
- end (int)
- string (string)

```yaml
substr 0 5 "hello world"
```

上述结果为： `hello`

### nospace

去掉字符串中的所有空格：

```yaml
nospace "hello w o r l d"
```

上述结果为： `helloworld`

### trunc

截断字符串。

```yaml
trunc 5 "hello world"
```

上述结果为： `hello`.

```yaml
trunc -5 "hello world"
```

上述结果为： `world`.

### abbrev

用省略号截断字符串 (`...`)

参数：

- 最大长度
- 字符串

```yaml
abbrev 5 "hello world"
```

上述结果为： `he...`， 因为将省略号算进了长度中。

### abbrevboth

两边都省略

```yaml
abbrevboth 5 10 "1234 5678 9123"
```

上述结果为： `...5678...`

It takes:

- 左侧偏移值
- 最大长度
- 字符串

### initials

截取给定字符串每个单词的首字母，并组合在一起。

```yaml
initials "First Try"
```

上述结果为： `FT`

### randAlphaNum, 

### randAlpha, 

### randNumeric, 

### randAscii

这四个字符串生成加密安全的(使用 `crypto/rand`)的随机字符串，但是字符集合不同：

- `randAlphaNum` 使用 `0-9a-zA-Z`
- `randAlpha` 使用 `a-zA-Z`
- `randNumeric` 使用 `0-9`
- `randAscii` 使用所有的可打印ASCII字符

每个函数都需要一个参数：字符串的整型长度

```yaml
randNumeric 3
```

上述会生成三个数字的字符串。

### wrap

以给定列数给文字换行。

```yaml
wrap 80 $someText
```

上述结果会以`$someText`在80列处换行。

### wrapWith

`wrapWith` 和 `wrap` 类似，但可以以指定字符串换行。(`wrap` 使用的是 `\n`)

```yaml
wrapWith 5 "\t" "Hello World"
```

上述结果为： `hello world` (其中空格是ASCII tab字符)

### contains

测试字符串是否包含在另一个字符串中：

```yaml
contains "cat" "catch"
```

上述结果为： `true` 因为 `catch` 包含了 `cat`.

### hasPrefix and hasSuffix

`hasPrefix` 和 `hasSuffix` 函数测试字符串是否有给定的前缀或后缀：

```yaml
hasPrefix "cat" "catch"
```

上述结果为： `true` 因为 `catch` 有 `cat`.

### quote and squote

该函数将字符串用双引号(`quote`) 或者单引号(`squote`)括起来。

### cat

`cat` 函数将多个字符串合并成一个，用空格分隔：

```yaml
cat "hello" "beautiful" "world"
```

上述结果为： `hello beautiful world`

### indent

`indent` 以指定长度缩进给定字符串所在行，在对齐多行字符串时很有用：

```yaml
indent 4 $lots_of_text
```

上述结果会将每行缩进4个空格。

### nindent

`nindent` 函数和indent函数一样，但可以在字符串开头添加新行。

```yaml
nindent 4 $lots_of_text
```

上述结果会在字符串所在行缩进4个字符，并且在开头新添加一行。

### replace

执行简单的字符串替换。

需要三个参数

- 待替换字符串
- 要替换字符串
- 源字符串

```yaml
"I Am Henry VIII" | replace " " "-"
```

上述结果为： `I-Am-Henry-VIII`

### plural

字符串复数化。

```yaml
len $fish | plural "one anchovy" "many anchovies"
```

如上，如果字符串长度为1，则第一个参数会被打印(`one anchovy`）。否则，会打印第二个参数(`many anchovies`）。

参数包括：

- 单数字符串
- 复数字符串
- 整型长度

注意： Helm 现在不支持多语言复杂的复数规则。`0`被认为是复数的因为英文中作为(`zero anchovies`) 对待。

### snakecase

将驼峰写法转换成蛇形写法。

```yaml
snakecase "FirstName"
```

上述结果为： `first_name`.

### camelcase

将字符串从蛇形写法转换成驼峰写法。

```yaml
camelcase "http_server"
```

上述结果为： `HttpServer`。

### kebabcase

将驼峰写法转换成烤串写法。

```yaml
kebabcase "FirstName"
```

上述结果为： `first-name`.

### swapcase

基于单词的算法切换字符串的大小写。

转换算法：

- 大写字符变成小写字母
- 首字母变成小写字母
- 空格后或开头的小写字母转换成大写字母
- 其他小写字母转换成大写字母
- 通过unicode.IsSpace(char)定义空格

```yaml
swapcase "This Is A.Test"
```

上述结果为： `tHIS iS a.tEST`.

### shuffle

对字符串进行洗牌。

```yaml
shuffle "hello"
```

上述`hello`的随机字符串可能会是`oelhl`。

## 类型转换

Helm提供了以下类型转换函数：

- `atoi`: 字符串转换成整型。
- `float64`: 转换成 `float64`。
- `int`: 按系统整型宽度转换成`int`。
- `int64`: 转换成 `int64`。
- `toDecimal`: 将unix八进制转换成`int64`。
- `toString`: 转换成字符串。
- `toStrings`: 将列表、切片或数组转换成字符串列表。
- `toJson` (`mustToJson`): 将列表、切片、数组、字典或对象转换成JSON。
- `toPrettyJson` (`mustToPrettyJson`): 将列表、切片、数组、字典或对象转换成格式化JSON。
- `toRawJson` (`mustToRawJson`): 将列表、切片、数组、字典或对象转换成HTML字符未转义的JSON。

只有`atoi`需要输入一个特定的类型。其他的会尝试将任何类型转换成目标类型。比如，`int64`可以把浮点数转换成整型，也可以把字符串转换成整型。

### toStrings

给定一个类列表集合，输出字符串切片。

```yaml
list 1 2 3 | toStrings
```

上述会将`1`转成`"1"`，`2`转成`"2"`，等等，然后将其作为列表返回。

### toDecimal

给定一个unix八进制权限，转换成十进制。

```yaml
"0777" | toDecimal
```

上述回将 `0777` 转换成 `511` 并返回int64的值。

### toJson, mustToJson

`toJson`函数将内容编码为JSON字符串。如果内容无法被转换成JSON会返回空字符串。`mustToJson`会返回错误以防无法编码成JSON。

```yaml
toJson .Item
```

上述结果为： `.Item`的JSON字符串表示。

### toPrettyJson, mustToPrettyJson

`toPrettyJson`函数将内容编码为好看的（缩进的）JSON字符串。

```yaml
toPrettyJson .Item
```

上述结果为： `.Item`的已缩进的JSON字符串表示。

### toRawJson, mustToRawJson

`toRawJson` 函数将内容编码成包含非转义HTML字符的JSON字符串。

```yaml
toRawJson .Item
```

上述结果为： `.Item`的非转义的JSON字符串表示。

## 正则表达式

Helm 包含以下正则表达式函数 [regexFind(mustRegexFind)](https://helm.sh/zh/docs/chart_template_guide/function_list/#regexfindall-mustregexfindall), [regexFindAll(mustRegexFindAll)](https://helm.sh/zh/docs/chart_template_guide/function_list/#regexfind-mustregexfind), [regexMatch (mustRegexMatch)](https://helm.sh/zh/docs/chart_template_guide/function_list/#regexmatch-mustregexmatch), [regexReplaceAll (mustRegexReplaceAll)](https://helm.sh/zh/docs/chart_template_guide/function_list/#regexreplaceall-mustregexreplaceall), [regexReplaceAllLiteral(mustRegexReplaceAllLiteral)](https://helm.sh/zh/docs/chart_template_guide/function_list/#regexreplaceallliteral-mustregexreplaceallliteral), [regexSplit (mustRegexSplit)](https://helm.sh/zh/docs/chart_template_guide/function_list/#regexsplit-mustregexsplit)。

### regexMatch, mustRegexMatch

如果输入字符串包含可匹配正则表达式任意字符串，则返回true。

```yaml
regexMatch "^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$" "test@acme.com"
```

上述结果为： `true`

`regexMatch`有问题时会出错， `mustRegexMatch`有问题时会向模板引擎返回错误。

### regexFindAll, mustRegexFindAll

返回输入字符串匹配正则表达式的所有切片。最后一个参数表示要返回的子字符串的数量，-1表示返回所有。

```yaml
regexFindAll "[2,4,6,8]" "123456789" -1
```

上述结果为： `[2 4 6 8]`

`regexFindAll` 有问题时会出错， `mustRegexFindAll` 有问题时会向模板引擎返回错误。

### regexFind, mustRegexFind

返回输入字符串的第一个 (最左边的) 正则匹配。

```yaml
regexFind "[a-zA-Z][1-9]" "abcd1234"
```

上述结果为： `d1`

`regexFind` 有问题时会出错， `mustRegexFind` 有问题时会向模板引擎返回错误。

### regexReplaceAll, mustRegexReplaceAll

返回输入字符串的拷贝，用替换字符串替换Regexp的匹配项。在替换字符串里面， $ 标志被解释为扩展，因此对于实例来说 $1 表示第一个子匹配的文本

```yaml
regexReplaceAll "a(x*)b" "-ab-axxb-" "${1}W"
```

上述结果为： `-W-xxW-`

`regexReplaceAll` 有问题时会出错， `mustRegexReplaceAll` 有问题时会向模板引擎返回错误。

### regexReplaceAllLiteral, mustRegexReplaceAllLiteral

返回输入字符串的拷贝，用替换字符串替换Regexp的匹配项。匹配字符串直接替换而不是扩展。

```yaml
regexReplaceAllLiteral "a(x*)b" "-ab-axxb-" "${1}"
```

上述结果为： `-${1}-${1}-`

`regexReplaceAllLiteral` 有问题时会出错，`mustRegexReplaceAllLiteral` 有问题时会向模板引擎返回错误。

### regexSplit, mustRegexSplit

将输入字符串切成有表达式分隔的子字符串，并返回表达式匹配项之间的切片。最后一个参数`n`确定要返回的子字符串数量，`-1`表示返回所有匹配。

```yaml
regexSplit "z+" "pizza" -1
```

上述结果为： `[pi a]`

`regexSplit` 有问题时会出错，`mustRegexSplit` 有问题时会向模板引擎返回错误。

## 加密

Cryptographic and Security Functions

Helm提供了一些高级的加密函数。包括了 [adler32sum](https://helm.sh/zh/docs/chart_template_guide/function_list/#adler32sum), [buildCustomCert](https://helm.sh/zh/docs/chart_template_guide/function_list/#buildcustomcert), [decryptAES](https://helm.sh/zh/docs/chart_template_guide/function_list/#decryptaes), [derivePassword](https://helm.sh/zh/docs/chart_template_guide/function_list/#derivepassword), [encryptAES](https://helm.sh/zh/docs/chart_template_guide/function_list/#encryptaes), [genCA](https://helm.sh/zh/docs/chart_template_guide/function_list/#genca), [genPrivateKey](https://helm.sh/zh/docs/chart_template_guide/function_list/#genprivatekey), [genSelfSignedCert](https://helm.sh/zh/docs/chart_template_guide/function_list/#genselfsignedcert), [genSignedCert](https://helm.sh/zh/docs/chart_template_guide/function_list/#gensignedcert), [htpasswd](https://helm.sh/zh/docs/chart_template_guide/function_list/#htpasswd), [sha1sum](https://helm.sh/zh/docs/chart_template_guide/function_list/#sha1sum)， 以及 [sha256sum](https://helm.sh/zh/docs/chart_template_guide/function_list/#sha256sum)。

### sha1sum

`sha1sum`函数接收一个字符串，并计算它的SHA1摘要。

```yaml
sha1sum "Hello world!"
```

### sha256sum

`sha256sum` 函数接收一个字符串，并计算它的SHA256摘要。

```yaml
sha256sum "Hello world!"
```

上述语句会以“ASCII包装”格式计算SHA 256 校验和，并安全打印出来。

### adler32sum

`adler32sum`函数接收一个字符串，并计算它的Adler-32校验和。

```yaml
adler32sum "Hello world!"
```

### htpasswd

`htpasswd` 函数使用`username` 和 `password` 生成一个密码的`bcrypt`哈希值。该结果可用于 [Apache HTTP Server](https://httpd.apache.org/docs/2.4/misc/password_encryptions.html#basic) 的基础认证。

```yaml
htpasswd "myUser" "myPassword"
```

注意，将密码直接存储在模板中并不安全。

### derivePassword

`derivePassword` 函数可用于基于某些共享的“主密码”约束得到特定密码。这方面的算法有 [详细说明](https://masterpassword.app/masterpassword-algorithm.pdf)。

```yaml
derivePassword 1 "long" "password" "user" "example.com"
```

注意，将这部分直接存储在模板中并不安全。

### genPrivateKey

`genPrivateKey` 函数生成一个编码成PEM块的新私钥。

第一个参数会采用以下某个值：

- `ecdsa`: 生成椭圆曲线 DSA key (P256)
- `dsa`: 生成 DSA key (L2048N256)
- `rsa`: 生成 RSA 4096 key

### buildCustomCert

`buildCustomCert` 函数允许自定义证书。

会采用以下字符串参数：

- base64 编码PEM格式证书
- base64 编码PEM格式私钥

返回包含以下属性的整数对象：

- `Cert`:PEM编码证书
- `Key`: PEM编码私钥

示例：

```yaml
$ca := buildCustomCert "base64-encoded-ca-crt" "base64-encoded-ca-key"
```

注意返回的对象可以使用这个CA传递给`genSignedCert`函数进行签名。

### genCA

`genCA` 函数生成一个新的，自签名的x509 证书机构。

会采用以下参数：

- 主体通用名 (cn)
- 证书有效期（天）

会返回一个包含以下属性的对象：

- `Cert`: PEM编码证书
- `Key`: PEM编码私钥

示例：

```yaml
$ca := genCA "foo-ca" 365
```

注意返回的对象可以使用这个CA传递给`genSignedCert`函数进行签名。

### genSelfSignedCert

The `genSelfSignedCert` 函数生成一个新的，自签名的x509 证书。

会采用下列参数：

- 主体通用名 (cn)
- 可选IP列表；可以为空
- 可选备用DNS名称列表；可以为空
- 证书有效期（天）

会返回一个包含以下属性的对象：

- `Cert`: PEM编码证书
- `Key`: PEM编码私钥

示例：

```yaml
$cert := genSelfSignedCert "foo.com" (list "10.0.0.1" "10.0.0.2") (list "bar.com" "bat.com") 365
```

### genSignedCert

`genSignedCert` 通过指定的CA签名生成一个新的， x509证书

会采用以下参数：

- 主体通用名 (cn)
- 可选IP列表；可以为空
- 可选备用DNS名称列表；可以为空
- 证书有效期（天）
- CA (查看 `genCA`)

示例：

```yaml
$ca := genCA "foo-ca" 365
$cert := genSignedCert "foo.com" (list "10.0.0.1" "10.0.0.2") (list "bar.com" "bat.com") 365 $ca
```

### encryptAES

`encryptAES` 函数使用AES-256 CBC 加密文本并返回一个base64编码字符串。

```yaml
encryptAES "secretkey" "plaintext"
```

### decryptAES

`decryptAES`函数接收一个AES-256 CBC编码的字符串并返回解密文本。

```yaml
"30tEfhuJSVRhpG97XCuWgz2okj7L8vQ1s6V9zVUPeDQ=" | decryptAES "secretkey"
```

## 日期

Date Functions

Helm 包含以下可以在模板中使用的函数： [ago](https://helm.sh/zh/docs/chart_template_guide/function_list/#ago), [date](https://helm.sh/zh/docs/chart_template_guide/function_list/#date), [dateInZone](https://helm.sh/zh/docs/chart_template_guide/function_list/#dateinzone), [dateModify(mustDateModify)](https://helm.sh/zh/docs/chart_template_guide/function_list/#datemodify-mustdatemodify), [duration](https://helm.sh/zh/docs/chart_template_guide/function_list/#duration), [durationRound](https://helm.sh/zh/docs/chart_template_guide/function_list/#durationround), [htmlDate](https://helm.sh/zh/docs/chart_template_guide/function_list/#htmldate), [htmlDateInZone](https://helm.sh/zh/docs/chart_template_guide/function_list/#htmldateinzone), [now](https://helm.sh/zh/docs/chart_template_guide/function_list/#now), [toDate(mustToDate)](https://helm.sh/zh/docs/chart_template_guide/function_list/#todate-musttodate), and [unixEpoch](https://helm.sh/zh/docs/chart_template_guide/function_list/#unixepoch)。

### now

当前日期/时间。和其他日期函数一起使用。

### ago

`ago` 函数返回距time.Now的以秒为单位的间隔时间。

```yaml
ago .CreatedAt"
```

返回`time.Duration`的字符串格式

```yaml
2h34m7s
```

### date

`date`函数格式化日期

日期格式化为YEAR-MONTH-DAY：

```yaml
now | date "2006-01-02"
```

日期格式化在Go中有 [一些不同](https://pauladamsmith.com/blog/2011/05/go_time.html)。

简言之，以此为基准日期：

```yaml
Mon Jan 2 15:04:05 MST 2006
```

将其写成你想要的格式，上面的例子中，`2006-01-02` 是同一个日期，却是我们需要的格式。

### dateInZone

和 `date` 一样，但是和时区一起。

```yaml
dateInZone "2006-01-02" (now) "UTC"
```

### duration

将给定的秒数格式化为`time.Duration`。

这会返回 1m35s。

```yaml
duration 95
```

### durationRound

将给定时间舍入到最重要的单位。当`time.Time`计算为一个自某个时刻以来的时间，字符串和`time.Duration`被解析为一个时间段。

这会返回2h

```yaml
durationRound "2h10m5s"
```

这会返回3mo

```yaml
durationRound "2400h10m5s"
```

### unixEpoch

返回`time.Time`的unix时间戳。

```yaml
now | unixEpoch
```

### dateModify, mustDateModify

`dateModify` 给定一个修改日期并返回时间戳。

从当前时间减去一个小时三十分钟：

```yaml
now | date_modify "-1.5h"
```

如果修改格式错误， `dateModify`会返回日期未定义。而`mustDateModify`会返回错误。

### htmlDate

`htmlDate`函数用于格式化插入到HTML日期选择器输入字段的日期。

```yaml
now | htmlDate
```

### htmlDateInZone

和htmlDate一样，但多了个时区。

```yaml
htmlDateInZone (now) "UTC"
```

### toDate, mustToDate

`toDate` 将字符串转换成日期。第一个参数是日期格式，第二个参数是日期字符串。 如果字符串无法转换就会返回0值。`mustToDate`以防无法转换会返回错误。

这在你将日期字符串转换成其他格式时很有用（使用pipe）。下面的例子会将"2017-12-31" 转换成 "31/12/2017"。

```yaml
toDate "2006-01-02" "2017-12-31" | date "02/01/2006"
```

## 字典

Dictionaries and Dict Functions

Helm 提供了一个key/value存储类型称为`dict`（"dictionary"的简称，Python中也有）。`dict`是无序类型。

字典的key **必须是字符串**。但值可以是任意类型，甚至是另一个`dict` 或 `list`。

不像`list`， `dict`不是不可变的。`set`和`unset`函数会修改字典的内容。

Helm 提供了以下函数支持使用字典： [deepCopy(mustDeepCopy)](https://helm.sh/zh/docs/chart_template_guide/function_list/#deepcopy-mustdeepcopy), [dict](https://helm.sh/zh/docs/chart_template_guide/function_list/#dict), [get](https://helm.sh/zh/docs/chart_template_guide/function_list/#get), [hasKey](https://helm.sh/zh/docs/chart_template_guide/function_list/#haskey), [keys](https://helm.sh/zh/docs/chart_template_guide/function_list/#keys), [merge (mustMerge)](https://helm.sh/zh/docs/chart_template_guide/function_list/#merge-mustmerge), [mergeOverwrite (mustMergeOverwrite)](https://helm.sh/zh/docs/chart_template_guide/function_list/#mergeoverwrite-mustmergeoverwrite), [omit](https://helm.sh/zh/docs/chart_template_guide/function_list/#omit), [pick](https://helm.sh/zh/docs/chart_template_guide/function_list/#pick), [pluck](https://helm.sh/zh/docs/chart_template_guide/function_list/#pluck), [set](https://helm.sh/zh/docs/chart_template_guide/function_list/#set), [unset](https://helm.sh/zh/docs/chart_template_guide/function_list/#unset),和 [values](https://helm.sh/zh/docs/chart_template_guide/function_list/#values)。

### dict

通过调用`dict`函数并传递一个键值对列表创建字典。

下面是创建三个键值对的字典：

```yaml
$myDict := dict "name1" "value1" "name2" "value2" "name3" "value 3"
```

### get

给定一个映射和一个键，从映射中获取值。

```yaml
get $myDict "name1"
```

上述结果为： `"value1"`

注意如果没有找到，会简单返回`""`。不会生成error。

### set

使用`set`给字典添加一个键值对。

```yaml
$_ := set $myDict "name4" "value4"
```

注意`set` *返回字典* (Go模板函数的一个要求)，因此你可能需要像上面那样使用使用`$_`赋值来获取值。

### unset

给定一个映射和key，从映射中删除这个key。

```yaml
$_ := unset $myDict "name4"
```

和`set`一样，需要返回字典。

注意，如果key没有找到，这个操作会简单返回，不会生成错误。

### hasKey

`hasKey`函数会在给定字典中包含了给定key时返回`true`。

```yaml
hasKey $myDict "name1"
```

如果key没找到，会返回`false`。

### pluck

`pluck` 函数给定一个键和多个映射，并获得所有匹配项的列表：

```yaml
pluck "name1" $myDict $myOtherDict
```

上述会返回的`list`包含了每个找到的值(`[value1 otherValue1]`)。

如果key在映射中 *没有找到* ，列表中的映射就不会有内容（并且返回列表的长度也会小于调用`pluck`的字典）。

如果key是 *存在的*， 但是值是空值，会插入一个值。

Helm模板中的一个常见用法是使用`pluck... | first` 从字典集合中获取第一个匹配的键。

### merge, mustMerge

将两个或多个字典合并为一个， 目标字典优先：

```yaml
$newdict := merge $dest $source1 $source2
```

这是个深度合并操作，但不是深度拷贝操作。合并的嵌套对象是两个字典上的同一实例。如果想深度合并的同时进行深度拷贝， 合并的时候同时使用`deepCopy`函数，比如：

```yaml
deepCopy $source | merge $dest
```

`mustMerge` 会返回错误，以防出现不成功的合并。

### mergeOverwrite, mustMergeOverwrite

合并两个或多个字典，优先按照 **从右到左**，在目标字典中有效地覆盖值：

给定的：

```yaml
dst:
  default: default
  overwrite: me
  key: true

src:
  overwrite: overwritten
  key: false
```

会生成：

```yaml
newdict:
  default: default
  overwrite: overwritten
  key: false
$newdict := mergeOverwrite $dest $source1 $source2
```

这是一个深度合并操作但不是深度拷贝操作。两个字典上嵌入的对象被合并到了同一个实例中。如果你想在合并的同时进行深度拷贝， 使用`deepCopy`函数，比如：

```yaml
deepCopy $source | mergeOverwrite $dest
```

`mustMergeOverwrite` 会返回错误，以防出现不成功的合并。

### keys

`keys`函数会返回一个或多个`dict`类型中所有的key的`list`。由于字典是 *无序的*，key不会有可预料的顺序。 可以使用`sortAlpha`存储。

```yaml
keys $myDict | sortAlpha
```

当提供了多个词典时，key会被串联起来。使用`uniq`函数和`sortAlpha`获取一个唯一有序的键列表。

```yaml
keys $myDict $myOtherDict | uniq | sortAlpha
```

### pick

`pick`函数只从字典中选择给定的键，并创建一个新的`dict`。

```yaml
$new := pick $myDict "name1" "name2"
```

上述结果为： `{name1: value1, name2: value2}`

### omit

`omit`函数类似于`pick`，除它之外返回一个新的`dict`，所有的key *不* 匹配给定的key。

```yaml
$new := omit $myDict "name1" "name3"
```

上述结果为： `{name2: value2}`

### values

`values`函数类似于`keys`，返回一个新的`list`包含源字典中所有的value(只支持一个字典)。

```yaml
$vals := values $myDict
```

上述结果为： `list["value1", "value2", "value 3"]`。注意 `values`不能保证结果的顺序；如果你需要顺序， 请使用`sortAlpha`。

### deepCopy, mustDeepCopy

`deepCopy` 和 `mustDeepCopy` 函数给定一个值并深度拷贝这个值。包括字典和其他结构体。 `deepCopy`有问题时会出错， 而`mustDeepCopy`会返回一个错误给模板系统。

```yaml
dict "a" 1 "b" 2 | deepCopy
```

### 字典的内部说明

`dict` 在Go里是作为`map[string]interface{}`执行的。Go开发者可以传`map[string]interface{}`值给上下文， 将其作为 `dict` 提供给模板。

## Encoding functions

Helm有以下编码和解码函数：

- `b64enc`/`b64dec`: 编码或解码 Base64
- `b32enc`/`b32dec`: 编码或解码 Base32

## 列表

Lists and List Functions

Helm 提供了一个简单的`list`类型，包含任意顺序的列表。类似于数组或切片，但列表是被设计用于不可变数据类型。

创建一个整型列表：

```yaml
$myList := list 1 2 3 4 5
```

上述会生成一个列表 `[1 2 3 4 5]`。

Helm 提供了以下列表函数： [append(mustAppend)](https://helm.sh/zh/docs/chart_template_guide/function_list/#append-mustappend), [compact (mustCompact)](https://helm.sh/zh/docs/chart_template_guide/function_list/#compact-mustcompact), [concat](https://helm.sh/zh/docs/chart_template_guide/function_list/#concat), [first(mustFirst)](https://helm.sh/zh/docs/chart_template_guide/function_list/#first-mustfirst), [has (mustHas)](https://helm.sh/zh/docs/chart_template_guide/function_list/#has-musthas), [initial (mustInitial)](https://helm.sh/zh/docs/chart_template_guide/function_list/#initial-mustinitial), [last (mustLast)](https://helm.sh/zh/docs/chart_template_guide/function_list/#last-mustlast), [prepend (mustPrepend)](https://helm.sh/zh/docs/chart_template_guide/function_list/#prepend-mustprepend), [rest (mustRest)](https://helm.sh/zh/docs/chart_template_guide/function_list/#rest-mustrest), [reverse (mustReverse)](https://helm.sh/zh/docs/chart_template_guide/function_list/#reverse-mustreverse), [seq](https://helm.sh/zh/docs/chart_template_guide/function_list/#seq), [slice (mustSlice)](https://helm.sh/zh/docs/chart_template_guide/function_list/#slice-mustslice), [uniq (mustUniq)](https://helm.sh/zh/docs/chart_template_guide/function_list/#uniq-mustuniq), [until](https://helm.sh/zh/docs/chart_template_guide/function_list/#until), [untilStep](https://helm.sh/zh/docs/chart_template_guide/function_list/#untilstep), 和 [without (mustWithout)](https://helm.sh/zh/docs/chart_template_guide/function_list/#without-mustwithout)。

### first, mustFirst

获取列表中的第一项，使用 `first`。

```
first $myList` 返回 `1
```

`first` 有问题时会出错，`mustFirst` 有问题时会向模板引擎返回错误。

### rest, mustRest

获取列表的尾部内容(除了第一项外的所有内容)，使用`rest`。

```
rest $myList` returns `[2 3 4 5]
```

`rest`有问题时会出错，`mustRest` 有问题时会向模板引擎返回错误。

### last, mustLast

使用`last`获取列表的最后一项：

`last $myList` 返回 `5`。这大致类似于反转列表然后调用`first`。

### initial, mustInitial

通过返回所有元素 *但* 除了最后一个元素来赞赏`last`。 `initial $myList` 返回 `[1 2 3 4]`。

`initial`有问题时会出错，但是 `mustInitial` 有问题时会向模板引擎返回错误。

### append, mustAppend

在已有列表中追加一项，创建一个新的列表。

```yaml
$new = append $myList 6
```

上述语句会设置 `$new` 为 `[1 2 3 4 5 6]`。 `$myList`会保持不变。

`append` 有问题时会出错，但 `mustAppend` 有问题时会向模板引擎返回错误。

### prepend, mustPrepend

将元素添加到列表的前面，生成一个新的列表。

```yaml
prepend $myList 0
```

上述语句会生成 `[0 1 2 3 4 5]`。 `$myList`会保持不变。

`prepend` 有问题时会出错，但 `mustPrepend` 有问题时会向模板引擎返回错误。

### concat

将任意数量的列表串联成一个。

```yaml
concat $myList ( list 6 7 ) ( list 8 )
```

上述语句会生成 `[1 2 3 4 5 6 7 8]`。 `$myList` 会保持不变。

### reverse, mustReverse

反转给定的列表生成一个新列表。

```yaml
reverse $myList
```

上述语句会生成一个列表： `[5 4 3 2 1]`。

`reverse` 有问题时会出错，但 `mustReverse` 有问题时会向模板引擎返回错误。

### uniq, mustUniq

生成一个移除重复项的列表。

```yaml
list 1 1 1 2 | uniq
```

上述语句会生成 `[1 2]`

`uniq` 有问题时会出错，但 `mustUniq` 有问题时会向模板引擎返回错误。

### without, mustWithout

`without` 函数从列表中过滤内容。

```yaml
without $myList 3
```

上述语句会生成 `[1 2 4 5]`

一个过滤器可以过滤多个元素：

```yaml
without $myList 1 3 5
```

这样会得到： `[2 4]`

`without` 有问题时会出错，但 `mustWithout` 有问题时会向模板引擎返回错误。

### has, mustHas

验证列表是否有特定元素。

```yaml
has 4 $myList
```

上述语句会返回 `true`, 但 `has "hello" $myList` 就会返回false。

`has` 有问题时会出错，但 `mustHas` 有问题时会向模板引擎返回错误。

### compact, mustCompact

接收一个列表并删除空值项。

```yaml
$list := list 1 "a" "foo" ""
$copy := compact $list
```

`compact` 会返回一个移除了空值(比如， "")的新列表。

`compact` 有问题时会出错，但 `mustCompact` 有问题时会向模板引擎返回错误。

### slice, mustSlice

从列表中获取部分元素，使用 `slice list [n] [m]`。等同于 `list[n:m]`.

- `slice $myList` 返回 `[1 2 3 4 5]`。 等同于 `myList[:]`。
- `slice $myList 3` 返回 `[4 5]`等同于 `myList[3:]`。
- `slice $myList 1 3` 返回 `[2 3]`等同于 `myList[1:3]`。
- `slice $myList 0 3` 返回 `[1 2 3]`等同于 `myList[:3]`。

`slice` 有问题时会出错，但 `mustSlice` 有问题时会向模板引擎返回错误。

### until

`until` 函数构建一个整数范围。

```yaml
until 5
```

上述语句会生成一个列表： `[0, 1, 2, 3, 4]`。

对循环语句很有用： `range $i, $e := until 5`。

### untilStep

类似`until`， `untilStep` 生成一个可计数的整型列表。但允许你定义开始，结束和步长：

```yaml
untilStep 3 6 2
```

上述语句会生成 `[3 5]`，从3开始，每次加2，直到大于等于6。类似于Python的 `range` 函数。

### seq

原理和bash的 `seq` 命令类似。

- 1 单个参数 (结束位置) - 会生成所有从1到包含 `end` 的整数。
- 2 多个参数 (开始， 结束) - 会生成所有包含`start` 和 `end` 的整数，递增或者递减。
- 3 多个参数 (开始， 步长， 结束) - 会生成所有包含 `start` 和 `end` 按 `step`递增或递减的整数。

```yaml
seq 5       => 1 2 3 4 5
seq -3      => 1 0 -1 -2 -3
seq 0 2     => 0 1 2
seq 2 -2    => 2 1 0 -1 -2
seq 0 2 10  => 0 2 4 6 8 10
seq 0 -2 -5 => 0 -2 -4
```

## 数学

Math Functions

除非另外指定，否则所有的math函数都是操作 `int64` 的值。

有以下math函数可用： [add](https://helm.sh/zh/docs/chart_template_guide/function_list/#add), [add1](https://helm.sh/zh/docs/chart_template_guide/function_list/#add1), [ceil](https://helm.sh/zh/docs/chart_template_guide/function_list/#ceil), [div](https://helm.sh/zh/docs/chart_template_guide/function_list/#div), [floor](https://helm.sh/zh/docs/chart_template_guide/function_list/#floor), [len](https://helm.sh/zh/docs/chart_template_guide/function_list/#len), [max](https://helm.sh/zh/docs/chart_template_guide/function_list/#max), [min](https://helm.sh/zh/docs/chart_template_guide/function_list/#min), [mod](https://helm.sh/zh/docs/chart_template_guide/function_list/#mod), [mul](https://helm.sh/zh/docs/chart_template_guide/function_list/#mul), [round](https://helm.sh/zh/docs/chart_template_guide/function_list/#round), and [sub](#sub）。

### add

使用`add`求和。接受两个或多个输入。

```yaml
add 1 2 3
```

### add1

自增加1，使用 `add1`。

### sub

相减使用 `sub`。

### div

整除使用 `div`。

### mod

取模使用`mod`。

### mul

相乘使用`mul`。接受两个或多个输入。

```yaml
mul 1 2 3
```

### max

返回一组整数中最大的整数。

下列会返回`3`:

```yaml
max 1 2 3
```

### min

返回一组数中最小的数。

`min 1 2 3` 会返回 `1`。

### floor

返回小于等于输入值的最大浮点整数。

`floor 123.9999` will return `123.0`。

### ceil

返回大于等于输入值的最小浮点整数。

`ceil 123.001` will return `124.0`。

### round

返回一个四舍五入到给定小数位的数。

`round 123.555555 3` will return `123.556`。

### len

以整数返回参数的长度。

```yaml
len .Arg
```

## Network Functions

Helm提供了一个网络函数： `getHostByName`.

`getHostByName`接收一个域名返回IP地址。

`getHostByName "www.google.com"`会返回对应的`www.google.com`的地址。

## 文件

File Path Functions

Helm模板函数没有访问文件系统的权限，提供了遵循文件路径规范的函数。包括 [base](https://helm.sh/zh/docs/chart_template_guide/function_list/#base), [clean](https://helm.sh/zh/docs/chart_template_guide/function_list/#clean), [dir](https://helm.sh/zh/docs/chart_template_guide/function_list/#dir), [ext](https://helm.sh/zh/docs/chart_template_guide/function_list/#ext), 和 [isAbs](https://helm.sh/zh/docs/chart_template_guide/function_list/#isabs) 。

### base

返回最后一个元素路径。

```yaml
base "foo/bar/baz"
```

返回 "baz"。

### dir

返回目录， 去掉路径的最后一部分。因此 `dir "foo/bar/baz"` 返回 `foo/bar`。

### clean

清除路径

```yaml
clean "foo/bar/../baz"
```

上述语句会清理 `..` 并返回`foo/baz`。

### ext

返回文件扩展。

```yaml
ext "foo.bar"
```

上述结果为： `.bar`.

### isAbs

检查文件路径是否为绝对路径，使用 `isAbs`。

## Reflection Functions

Helm 提供了基本的反射工具。这有助于高级模板开发者理解特定值的基本Go类型信息。Helm是由Go编写的且是强类型的。 类型系统应用于模板中。

Go 有一些原始 *类型*，比如 `string`, `slice`, `int64`, 和 `bool`。

Go 有一个开放的 *类型* 系统，允许开发者创建自己的类型。

Helm 通过 [kind functions](https://helm.sh/zh/docs/chart_template_guide/function_list/#kind-functions) 和 [type functions](https://helm.sh/zh/docs/chart_template_guide/function_list/#type-functions) 提供了一组函数。 [deepEqual](https://helm.sh/zh/docs/chart_template_guide/function_list/#deepequal) 也可以用来比较值。

### Kind Functions

有两个类型函数： `kindOf` 返回对象类型。

```yaml
kindOf "hello"
```

上述语句返回 `string`。对于简单测试(比如在`if`块中)，`Kindis`函数可以验证值是否为特定类型：

```yaml
kindIs "int" 123
```

上述返回 `true`。

### Type Functions

类型处理起来稍微有点复杂，所以有三个不同的函数：

- `typeOf` 返回值的基础类型： `typeOf $foo`
- `typeIs` 类似 `kindIs`， 但针对type： `typeIs "*io.Buffer" $myVal`
- `typeIsLike` 类似 `typeIs`，除非取消指针引用

**注意：** 这些都不能测试是否实现了给定的接口，因为在这之前需要提前编译接口。

### deepEqual

如果两个值相比是 ["deeply equal"](https://golang.org/pkg/reflect/#DeepEqual)，`deepEqual`返回true。

也适用于非基本类型 (相较于内置的 `eq`）。

```yaml
deepEqual (list 1 2 3) (list 1 2 3)
```

上述会返回 `true`。

## Semantic Version Functions

有些版本结构易于分析和比较。Helm提供了适用于 [SemVer 2](http://semver.org/) 版本的函数。包括 [semver](https://helm.sh/zh/docs/chart_template_guide/function_list/#semver)和 [semverCompare](https://helm.sh/zh/docs/chart_template_guide/function_list/#semvercompare)。下面你也能看到使用范围和比较的细节。

### semver

`semver`函数将字符串解析为语义版本：

```yaml
$version := semver "1.2.3-alpha.1+123"
```

*如果解析失败，会由一个错误引起模板执行中断。*

`$version`是一个指向`Version`对象的指针，包含了一下属性：

- `$version.Major`: 主版本号 (上面的`1`)
- `$version.Minor`: 次版本号 (上面的`2`)
- `$version.Patch`: 补丁版本号 (上面的`3`)
- `$version.Prerelease`: 预发布版本号 (上面的`alpha.1`)
- `$version.Metadata`: 构建元数据 (上面的`123`)
- `$version.Original`: 原始版本字符串

另外，你可以使用`Compare`函数比较一个`Version`和另一个`version`：

```yaml
semver "1.4.3" | (semver "1.2.3").Compare
```

上面会返回 `-1`。

返回值可以是：

- `-1` 如果给定的版本大于`Compare`方法调用的版本
- `1` 如果`Compare`调用的版本更大
- `0` 如果版本相同

(注意在语义版本中，`Metadata` 字段在版本比较时不比较)

### semverCompare

一个更健壮的比较函数是`semverCompare`。 这个版本支持版本范围：

- `semverCompare "1.2.3" "1.2.3"` 检查精确匹配
- `semverCompare "~1.2.0" "1.2.3"` 检查主要版本和次要版本，且补丁版本第二个版本是 *大于等于* 第一个。

SemVer函数使用 [semver规划库](https://github.com/Masterminds/semver)，由Sprig作者创建。

### 基本比较

两个元素的比较。首先，比较字符串是以空格或逗号分隔的。然后以|| (OR)分隔。比如：`">= 1.2 < 3.0.0 || >= 4.2.3"` 是要比较大于等于1.2且小于等于3.0.0 或者大于等于4.2.3的。

基本比较符有：

- `=`: 相等
- `!=`: 不相等
- `>`: 大于
- `<`: 小于
- `>=`: 大于等于
- `<=`: 小于等于

### 使用预发布版本

预发布版本，对于那些不熟悉它们的人，是用于稳定版本或一般可用版本之前的软件版本。预发布版本的例子包括开发版、 alpha版、beta版，和rc版本。稳定版`1.2.3`的预发布版本可能是`1.2.3-beta.1`，按照优先顺序，预发布版本在相关版本之前发布。 比如：`1.2.3-beta.1 < 1.2.3` 。

根据语义版本指定的预发布版本可能不与对应的发行版本兼容。

> 预发布版本表示版本不稳定且可能不满足其相关正常版本所表示的预期兼容性要求。

使用不带预发布版本比较器约束的语义版本的比较会跳过预发布版本。比如 `>=1.2.3` 会跳过预发布而`>=1.2.3-0`会计算并查找预发布版本。

按照规范，上例中的`0`作为预发布的版本是因为预发布版本只能包含ASCII字母数字和连字符（以及`.`分隔符）， with `.` separators)，另外排序按照ASCII排序顺序。在ASCII排序中，最小的字符是`0`(查看 [ASCII表](http://www.asciitable.com/))。

理解ASCII排序顺序很重要因为A-Z是在a-z之前，这意味着`>=1.2.3-BETA` 会返回 `1.2.3-alpha`。这里并不适合大小写敏感， 因为是按照ASCII排序规范指定顺序。

### 连字符范围比较

有多个方法处理范围，首先是连字符范围。像这样：

- `1.2 - 1.4.5` 等同于 `>= 1.2 <= 1.4.5`
- `2.3.4 - 4.5` 等同于 `>= 2.3.4 <= 4.5`

### 比较中通配符

`x`, `X`, 和 `*` 可用于通配符。适用于所有比较运算符。当使用`=`运算符时，会返回补丁级别的比较。比如：

- `1.2.x` 相当于 `>= 1.2.0, < 1.3.0`
- `>= 1.2.x` 相当于 `>= 1.2.0`
- `<= 2.x` 相当于 `< 3`
- `*` 相当于 `>= 0.0.0`

### 波浪符号范围比较 (补丁版本)

波浪 (`~`) 比较运算符是补丁级别范围的比较，在指定次要版本和主要版本变化且没有次要版本时使，比如：

- `~1.2.3` 相当于 `>= 1.2.3, < 1.3.0`
- `~1` 相当于 `>= 1, < 2`
- `~2.3` 相当于 `>= 2.3, < 2.4`
- `~1.2.x` 相当于 `>= 1.2.0, < 1.3.0`
- `~1.x` 相当于 `>= 1, < 2`

### 插入符号比较 (主要版本)

插入符(`^`)比较运算是主版本级别改变时使用。在1.0.0 发布之前，次要版本充当API稳定级别版本。 当比较主要的API版本更改时，这很有用，比如：

- `^1.2.3` 相当于 `>= 1.2.3, < 2.0.0`
- `^1.2.x` 相当于 `>= 1.2.0, < 2.0.0`
- `^2.3` 相当于 `>= 2.3, < 3`
- `^2.x` 相当于 `>= 2.0.0, < 3`
- `^0.2.3` 相当于 `>=0.2.3 <0.3.0`
- `^0.2` 相当于 `>=0.2.0 <0.3.0`
- `^0.0.3` 相当于 `>=0.0.3 <0.0.4`
- `^0.0` 相当于 `>=0.0.0 <0.1.0`
- `^0` 相当于 `>=0.0.0 <1.0.0`

## URL Functions

Helm 包含 [urlParse](https://helm.sh/zh/docs/chart_template_guide/function_list/#urlparse), [urlJoin](https://helm.sh/zh/docs/chart_template_guide/function_list/#urljoin), 和 [urlquery](https://helm.sh/zh/docs/chart_template_guide/function_list/#urlquery) 函数可以用做处理URL。

### urlParse

解析URL的字符串并生成包含URL部分的字典。

```yaml
urlParse "http://admin:secret@server.com:8080/api?list=false#anchor"
```

上述结果为： 包含URL对象的字典：

```yaml
scheme:   'http'
host:     'server.com:8080'
path:     '/api'
query:    'list=false'
opaque:   nil
fragment: 'anchor'
userinfo: 'admin:secret'
```

这是使用Go标准库中的URL包实现的。更多信息，请查看 https://golang.org/pkg/net/url/#URL。

### urlJoin

将一个映射(由`urlParse`生成的)连接成URL字符串

```yaml
urlJoin (dict "fragment" "fragment" "host" "host:80" "path" "/path" "query" "query" "scheme" "http")
```

上述结果会生成以下字符串：

```yaml
proto://host:80/path?query#fragment
```

### urlquery

返回作为参数传入的值的转义版本，这样就可以嵌入到URL的查询部分。

```yaml
$var := urlquery "string for query"
```

## UUID Functions

Helm 可以生成UUID v4 通用唯一ID。

```yaml
uuidv4
```

上述结果为： 一个新的v4类型的UUID（随机生成）。

## Kubernetes and Chart Functions

Helm 包含了用于 Kubernetes的函数，包括 [.Capabilities.APIVersions.Has](https://helm.sh/zh/docs/chart_template_guide/function_list/#capabilitiesapiversionshas), [Files](https://helm.sh/zh/docs/chart_template_guide/function_list/#file-functions), 和 [lookup](https://helm.sh/zh/docs/chart_template_guide/function_list/#lookup)。

### lookup

`lookup` 用于在正在运行的集群中查找资源。当和`helm template`命令一起使用时会返回一个空响应。

可以在 [lookup函数文档](https://helm.sh/zh/docs/chart_template_guide/#using-the-lookup-function)查看更多细节。

### .Capabilities.APIVersions.Has

返回API版本或资源是否在集群中可用。

```yaml
.Capabilities.APIVersions.Has "apps/v1"
.Capabilities.APIVersions.Has "apps/v1/Deployment"
```

更多信息可查看 [内置对象文档](https://helm.sh/zh/docs/chart_template_guide/builtin_objects.md)。

### File Functions

有几个函数能使您能够访问chart中的非特殊文件。比如访问应用配置文件。请查看 [模板中访问文件](https://helm.sh/zh/docs/chart_template_guide/accessing_files.md)。

*注意，这里很多函数的文档是来自 Sprig。Sprig是一个适用于Go应用的函数模板库。*

# 流控制

## if/else

用来创建条件语句

```yaml
{{ if PIPELINE }}
  # Do something
{{ else if OTHER PIPELINE }}
  # Do something else
{{ else }}
  # Default case
{{ end }}
```

如果是以下值时，管道会被设置为 *false*：

- 布尔false
- 数字0
- 空字符串
- `nil` (空或null)
- 空集合(`map`, `slice`, `tuple`, `dict`, `array`)

在所有其他条件下，条件都为true。

## 空白

首先，模板声明的大括号语法可以通过特殊的字符修改，并通知模板引擎取消空白。`{{- `(包括添加的横杠和空格)表示向左删除空白， 而` -}}`表示右边的空格应该被去掉。 *一定注意空格就是换行*

> 要确保`-`和其他命令之间有一个空格。 `{{- 3 }}` 表示“删除左边空格并打印3”，而`{{-3 }}`表示“打印-3”。

## with

这个用来控制变量范围。回想一下，`.`是对 *当前作用域* 的引用。因此 `.Values`就是告诉模板在当前作用域查找`Values`对象。

`with`的语法与`if`语句类似：

```yaml
{{ with PIPELINE }}
  # restricted scope
{{ end }}
```

作用域可以被改变。`with`允许你为特定对象设定当前作用域(`.`)。比如，我们已经在使用`.Values.favorite`。 修改配置映射中的`.`的作用域指向`.Values.favorite`

或者，我们可以使用`$`从父作用域中访问`Release.Name`对象。当模板开始执行后`$`会被映射到根作用域，且执行过程中不会更改`{{ $.Release.Name }}`

## range

提供"for each"类型的循环

```yaml
{{- range $.Values.pizzaToppings }}
- {{ . | title | quote }}  # . 代表了列表的单个元素
{{- end }} 
```

`|-`标识在YAML中是指多行字符串。这在清单列表中嵌入大块数据是很有用的技术。

## 变量

Helm模板中，变量是对另一个对象的命名引用。遵循`$name`变量的格式且指定了一个特殊的赋值运算符：`:=`。

```yaml
{{- range $index, $topping := .Values.pizzaToppings }}
  {{ $index }}: {{ $topping }}
{{- end }} 

{{- range $key, $val := .Values.favorite }}
  {{ $key }}: {{ $val | quote }}
  {{- end }}
```

变量一般不是"全局的"。作用域是其声明所在的块。上面我们在模板的顶层赋值了`$relname`。变量的作用域会是整个模板。 但在最后一个例子中`$key`和`$val`作用域会在`{{ range... }}{{ end }}`块内。

# 命名模板

命名模板时要记住一个重要细节：**模板名称是全局的**。如果您想声明两个相同名称的模板，哪个最后加载就使用哪个。 因为在子chart中的模板和顶层模板一起编译，命名时要注意 *chart特定名称*。

目前为止，我们已经使用了单个文件，且单个文件中包含了单个模板。但Helm的模板语言允许你创建命名的嵌入式模板， 这样就可以在其他位置按名称访问。

在编写模板细节之前，文件的命名惯例需要注意：

- `templates/`中的大多数文件被视为包含Kubernetes清单
- `NOTES.txt`是个例外
- 命名以下划线(`_`)开始的文件则假定 *没有* 包含清单内容。这些文件不会渲染为Kubernetes对象定义，但在其他chart模板中都可用。

## define template

`define`操作允许我们在模板文件中创建一个命名模板，语法如下：

```yaml
{{ define "MY.NAME" }}
  # body of template here
{{ end }}
```

现在我们将模板嵌入到了已有的配置映射中，然后使用`template`包含进来：

```yaml
{{- define "mychart.labels" }}
  labels:
    generator: helm
    date: {{ now | htmlDate }}
{{- end }}
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
  {{- template "mychart.labels" }}
data:
  myvalue: "Hello World"
  {{- range $key, $val := .Values.favorite }}
  {{ $key }}: {{ $val | quote }}
  {{- end }}
```

注意：`define`不会有输出，除非像本示例一样用模板调用它。

按照惯例，Helm chart将这些模板放置在局部文件中，一般是`_helpers.tpl`。

按照惯例`define`方法会有个简单的文档块(`{{/* ... */}}`)来描述要做的事。

尽管这个定义是在`_helpers.tpl`中，但它仍能访问`configmap.yaml`

**模板名称是全局的**

要查看渲染了什么，可以用`--disable-openapi-validation`参数重新执行： `helm install --dry-run --disable-openapi-validation moldy-jaguar ./mychart`

```yaml
{{- template "mychart.labels" . }}  #注意传入范围
```

## include

由于`template`是一个行为，不是方法，无法将 `template`调用的输出传给其他方法，数据只是简单地按行插入。

Helm提供了一个`template`的可选项`include`，可以将模板内容导入当前管道，然后传递给管道中的其他方法。

Helm模板中使用`include`而不是`template`被认为是更好的方式 只是为了更好地处理YAML文档的输出格式

# notes.txt

该部分会介绍为chart用户提供说明的Helm工具。在`helm install` 或 `helm upgrade`命令的最后，Helm会打印出对用户有用的信息。 使用模板可以高度自定义这部分信息。

要在chart添加安装说明，只需创建`templates/NOTES.txt`文件即可。该文件是纯文本，但会像模板一样处理， 所有正常的模板函数和对象都是可用的。



# 在模板内部访问文件

在上一节中，我们研究了几种创建和访问模板的方法。这样可以很容易从一个模板导入到另一个模板中。 但有时想导入的是不是模板的文件并注入其内容，而无需通过模板渲染发送内容。

Helm 提供了通过`.Files`对象访问文件的方法。不过，在我们使用模板示例之前，有些事情需要注意：

- 可以添加额外的文件到chart中。虽然这些文件会被绑定。但是要小心，由于Kubernetes对象的限制，Chart必须小于1M。

- 通常处于安全考虑，一些文件无法通过`.Files`对象访问：

  - 无法访问`templates/`中的文件
  - 无法访问使用`.helmignore`排除的文件
- Chart不能保留UNIX模式信息，因此当文件涉及到`.Files`对象时，文件级权限不会影响文件的可用性。

# 子chart

1. 子chart被认为是“独立的”，意味着子chart从来不会显示依赖它的父chart。
2. 因此，子chart无法访问父chart的值。
3. 父chart可以覆盖子chart的值。
4. Helm有一个 *全局值* 的概念，所有的chart都可以访问。

在父chart的values.yaml中

```yaml
子chartname:
  data  #将覆盖子chart vaules.yaml的值
```

## 全局Chart值

全局值是使用完全一样的名字在所有的chart及子chart中都能访问的值。全局变量需要显示声明。不能将现有的非全局值作为全局值使用。

这些值数据类型有个保留部分叫`Values.global`，可以用来设置全局值。

```yaml
# father values.yaml
global:
  data: afa
```

父chart和子chart都可以通过{{ .Values.global.data}}访问

## 与子chart共享模板

父chart和子chart可以共享模板。在任意chart中定义的块在其他chart中也是可用的。

当chart开发者在`include` 和 `template` 之间选择时，使用`include`的一个优势是`include`可以动态引用模板：{{ include $mytemplate }}

上述会取消对`$mytemplate`的引用，相反，`template`函数只接受字符串字符。

## 避免使用块

Go 模板语言提供了一个 `block` 关键字允许开发者提供一个稍后会被重写的默认实现。在Helm chart中， 块并不是用于覆盖的最好工具，因为如果提供了同一个块的多个实现，无法预测哪个会被选定。

建议改为使用`include`。

# .helmignore文件

`.helmignore` 文件用来指定你不想包含在你的helm chart中的文件。

如果该文件存在，`helm package` 命令会在打包应用时忽略所有在`.helmignore`文件中匹配的文件。

这有助于避免不需要的或敏感文件及目录添加到你的helm chart中。

`.helmignore` 文件支持Unix shell的全局匹配，相对路径匹配，以及反向匹配（以！作为前缀）。每行只考虑一种模式。

文件示例：

```yaml
# comment

# Match any file or path named .git
.git

# Match any text file
*.txt

# Match only directories named mydir
mydir/

# Match only text files in the top-level directory
/*.txt

# Match only the file foo.txt in the top-level directory
/foo.txt

# Match any file named ab.txt, ac.txt, or ad.txt
a[b-d].txt

# Match any file under subdir matching temp*
*/temp*

*/*/temp*
temp?
```

一些值得注意的和.gitignore不同之处：

- 不支持'**'语法。
- globbing库是Go的 'filepath.Match'，不是fnmatch(3)
- 末尾空格总会被忽略(不支持转义序列)
- 不支持'!'作为特殊的引导序列

# 调试模板

调试模板可能很棘手，因为渲染后的模板发送给了Kubernetes API server，可能会以格式化以外的原因拒绝YAML文件。

以下命令有助于调试：

- `helm lint` 是验证chart是否遵循最佳实践的首选工具
- `helm install --dry-run --debug` 或 `helm template --debug`：我们已经看过这个技巧了， 这是让服务器渲染模板的好方法，然后返回生成的清单文件。
- `helm get manifest`: 这是查看安装在服务器上的模板的好方法。

注释掉模板中有问题的部分， 然后重新运行:

```
helm install --dry-run --debug
```

# 扩展

https://helm.sh/zh/docs/chart_template_guide/wrapping_up/

文档：https://github.com/whmzsu/helm-doc-zh-cn/releases

# Go 数据类型和模板

Helm 模板语言是用强类型Go编程语言实现的。 因此，模板中的变量是 *有类型的*。大多数情况下，变量将作为以下类型之一显示：

- string: 文本字符串
- bool: `true` 或 `false`
- int: 整型值（包含8位，16位，32位，和64有符号和无符号整数）
- float64: 64位浮点数(也有8位，16位，32位类型)
- 字节切片(`[]byte`)，一般用于保存（可能的）二进制数据
- struct: 有属性和方法的对象
- 上述某种类型的切片(索引列表)
- 字符串键map (`map[string]interface{}`) 值是上述某种类型

Go里面有很多其他类型，有时你需要在模板里转换。调试对象类型最简便的方式是在模板中传递给`printf "%t"`，这样会打印类型。 也可以使用 `typeOf` 和 `kindOf` 函数。