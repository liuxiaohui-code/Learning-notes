# json schema

[JSON Schema 规范（中文版）](https://json-schema.apifox.cn/)

## 全部匹配,可以接受任何内容

- 使用空对象 `{}`
- 还可以使用 true 代替空对象来表示匹配任何内容的模式，或者 false 表示不匹配任何内容的模式。(Draft 6)

## 标识 JSON schema 版本

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$schema": "http://json-schema.org/schema#", // (deft 4)
  "$id": "http://yourdomain.com/schemas/myschema.json" // 唯一标识  在draft 4 中，$id 只是 id（没有$符号）
}
```

## 类型关键字 type

type 关键字可以是一个字符串或数组：

如果是字符串，则是上述基本类型之一的名称。
如果是数组，则必须是字符串数组，其中每个字符串是其中一种基本类型的名称，每个元素都是唯一的。在这种情况下，如果 JSON 片段与*任何*给定类型匹配，则它是有效的。

```json
{
  "type": "string",
  "type": "number",
  "type": "object",
  "type": "array",
  "type": "boolean",
  "type": "null",
  "type": ["number", "string"]
}
```

## 字符串 string

```json
{
  "type": "string",
  // 限制字符串的长度,该值必须是非负数。
  "minLength": 2,
  "maxLength": 3,
  // 正则表达式  JavaScript（特别是ECMA 262 ）中定义的
  "pattern": "^(\\([0-9]{3}\\))?[0-9]{3}-[0-9]{4}$",
  // 格式 该format关键字允许对常用的某些类型的字符串值进行基本语义验证。这允许限制值超出 JSON 模式中的其他工具，包括 正则表达式可以做的。
  "format": "date-time", //2018-11-13T20:20:39+00:00。
  "format": "time", //20:20:39+00:00  deft7
  "format": "date", //2018-11-13 deft7
  "format": "email", //
  "format": "idn-email", //
  "format": "hostname", ///
  "format": "idn-hostname", //
  "format": "ipv4", //
  "format": "ipv6", //
  "format": "uri", //
  "format": "uri-reference", //
  "format": "iri", //
  "format": "iri-reference", //
  "format": "uri-template", //
  "format": "json-pointer", //
  "format": "relative-json-pointer", //
  "format": "regex" //
}
```

## 数字 number

```json
{
  "type": "number", //用于任何数字类型，整数或浮点数。
  "type": "integer", // 整数 1.0 也是整数
  // 使用multipleOf关键字将数字限制为给定数字的倍数 。它可以设置为任何正数。
  "multipleOf": 10,
  // 数字的范围是使用minimum和maximum关键字的组合指定的 （或exclusiveMinimum和 exclusiveMaximum用于表示排他范围）。
  "maximum": 100, //  x ≤ maximum
  "minimum": 0, // x >= minimum
  "exclusiveMaximum": 100, // x < exclusiveMaximum
  "exclusiveMinimum": 0 // x > exclusiveMinimum
}
```

## 对象 object

对象是 JSON 中的映射类型。他们将“键”映射到“值”。在 JSON 中，“键”必须始终是字符串。这些对中的每一个通常被称为“值”。

```json
{
  "type": "object",
  // properties的值是一个对象，其中每个键是属性的名称，每个值是用于验证该属性的模式。此properties关键字将忽略与关键字中的任何属性名称不匹配的任何属性。
  //默认情况下，省略属性是有效的,即使是空对象也是有效的,提供附加属性是有效的
  "properties": {
    "number": { "type": "number" },
    "street_name": { "type": "string" },
    "street_type": { "enum": ["Street", "Avenue", "Boulevard"] }
  },
  // 默认情况下，properties不需要关键字定义的属性。但是，可以使用required关键字提供所需属性的列表。
  // required关键字采用零个或多个字符串的数组。这些字符串中的每一个都必须是唯一的。
  "required": ["number", "street_name"],
  // patternProperties ,它将正则表达式映射到模式。如果属性名称与给定的正则表达式匹配，则属性值必须针对相应的架构进行验证。任何与任一正则表达式都不匹配的属性将被忽略。
  "patternProperties": {
    "^S_": { "type": "string" },
    "^I_": { "type": "integer" }
  },
  //该additionalProperties关键字用于控制的额外的东西，
  "additionalProperties": false, // 不允许其他属性
  //使用非布尔模式对实例的其他属性设置更复杂的约束
  "additionalProperties": { "type": "string" },//可以允许额外的属性，但前提是它们都是一个字符串
  // 属性名称 draft6 中的新内容,可以根据模式验证属性名称，而不管它们的值
  "propertyNames": {
    "pattern": "^[A-Za-z_][A-Za-z0-9_]*$"
  },
  // 属性数量 可以使用minProperties和maxProperties关键字来限制对象上的属性数量 。这些中的每一个都必须是非负整数。
  "minProperties": 2,
  "maxProperties": 3
}
```
## 数组 array
```json
{ 
  "type": "array",
  // 列表验证,每个项目都匹配相同的模式
  "items": { // 匹配[1,2,3]
    "type": "number"
  },
  //当数组是一个元素的集合时，元组验证很有用，其中每个项目都有不同的架构并且每个项目的序数索引是有意义的
  //将items关键字设置为一个数组，其中每个项目都是一个模式，对应于文档数组的每个索引。也就是说，一个数组，其中第一个元素验证输入数组的第一个元素，第二个元素验证输入数组的第二个元素，依此类推。
  "items": [  // 匹配:[1,"1","Street","NW"]
    { "type": "number" },
    { "type": "string" },
    { "enum": ["Street", "Avenue", "Boulevard"] },
    { "enum": ["NW", "NE", "SW", "SE"] }
  ],
  // 附加元素,使用additionalItems关键字控制如果有超过元组内items属性定义的附加元素，元组是否有效。
  "additionalItems": false,//禁止数组中的额外项目
  "additionalItems": { "type": "string" },// 通过使用非布尔模式来限制附加项可以具有的值来表达更复杂的约束。在这种情况下，我们可以说允许附加元素，只要它们都是字符串：
  // Draft 6 中的新内容,只需要针对数组中的一项或多项进行验证。
  "contains": {
     "type": "number"
  },
  // 长度,使用minItems和 maxItems关键字指定数组的长度。每个关键字的值必须是非负数。
  "minItems": 2,
  "maxItems": 3,
  // 唯一性,只需将uniqueItems关键字设置为true，可以限制数组中的每个元素都是唯一的。
  "uniqueItems": true,
}
```
## 布尔值 boolean
布尔类型只匹配两个特殊值：true和 false。请注意，模式不接受_其他约定_为true或 false的值，例如 1 和 0。
```json
{ 
  "type": "boolean",
}
```
## NULL（null）

当一个模式指定 type为null时，它只有一个可接受的值：null。

```json
{ "type": "null" }
```
## 通用关键字

JSON Schema 包含一些关键字，它们并不严格用于验证，而是用于描述模式的一部分。这些“注释”关键字都不是必需的，但鼓励使用为了良好实践，并且可以使您的模式“自我记录”。

```json
{
  "title": "Match anything", // 标题
  "description": "This is a schema that matches anything.", // 描述
  "default": "Default value",
  "examples": [ // deft6
    "Anything",
    4035
  ],
  "deprecated": true, //Draft2019-09
  "readOnly": true,//deft7
  "writeOnly": false,//deft7
  "$comment":"",//严格用于向模式添加注释。
}
```

## 枚举值

enum关键字用于将值限制为一组固定的值。它必须是一个包含至少一个元素的数组，其中每个元素都是唯一的。

```json
{
  "enum": ["red", "amber", "green", null, 42]
}
```

## 常量值

Draft 6 中的新内容 const关键字被用于限制值为一个常量值。

```json
{
  "properties": {
    "country": {
      "const": "United States of America"  //出于出口原因仅支持运送到美国：
    }
  }
}
```
## Media：字符串编码非 JSON 数据

Draft 7 中的新内容

JSON 模式有一组关键字来描述和可选地验证存储在 JSON 字符串中的非 JSON 数据。由于很难为许多媒体类型编写验证器，因此不需要 JSON 模式验证器根据这些关键字验证 JSON 字符串的内容。但是，这些关键字对于使用经过验证的 JSON 的应用程序仍然有用。
```json
{
  "type": "string",
  // 内容编码 7bit，8bit，binary， quoted-printable，base16，base32，和base64,如果未指定，则编码与包含的 JSON 文档相同。
  "contentEncoding": "base64",
  // 内容媒体类型
  "contentMediaType": "text/html"
}
```

## Schema 组合

```json
{
  //给定的数据必须针对给定的所有子模式有效。
  "allOf": [
    { "type": "string" },
    { "maxLength": 5 }
  ],
  //数据必须满足任意一个或多个给定子模式。
  "anyOf": [
    { "type": "string", "maxLength": 5 },
    { "type": "number", "minimum": 0 }
  ],
  //数据必须满足且只满足一个给定的子模式
  "oneOf": [
    { "type": "number", "multipleOf": 5 },
    { "type": "number", "multipleOf": 3 }
  ]
}
```
