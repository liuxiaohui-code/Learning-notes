# 引入

## cdn

```
https://unpkg.com/mermaid@<version>/dist/
```

Latest Version: https://unpkg.com/browse/mermaid@8.8.0/

## 部署

node v16，npm

```
1. yarn add mermaid
2. yarn add --dev mermaid
```

## api

```html
<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});
</script>
<div class="mermaid">
  graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
</div>
```

# 流程图

## 标识

`flowchart `

## 结构

```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```



## 流程图导向

- TB - top to bottom  自上而下
- TD - top-down/ same as top to bottom 自上而下
- BT - bottom to top 自下至上
- RL - right to left 自右到左
- LR - left to right 自左到右

## 节点形状

```mermaid
flowchart TD
    [id --> id2{ss}
```



# 时序图

```mermaid
sequenceDiagram
    participant Alice
    participant Bob
    Alice->>John: Hello John, how are you?
    loop Healthcheck
        John->>John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail!
    John-->>Alice: Great!
    John->>Bob: How about you?
    Bob-->>John: Jolly good!
```



# 甘特图

```mermaid
gantt
dateFormat  YYYY-MM-DD
title Adding GANTT diagram to mermaid
excludes weekdays 2014-01-10

section A section
Completed task            :done,    des1, 2014-01-06,2014-01-08
Active task               :active,  des2, 2014-01-09, 3d
Future task               :         des3, after des2, 5d
Future task2               :         des4, after des3, 5d
```

# 类图

```mermaid
classDiagram
Class01 <|-- AveryLongClass : Cool
Class03 *-- Class04
Class05 o-- Class06
Class07 .. Class08
Class09 --> C2 : Where am i?
Class09 --* C3
Class09 --|> Class07
Class07 : equals()
Class07 : Object[] elementData
Class01 : size()
Class01 : int chimp
Class01 : int gorilla
Class08 <--> C2: Cool label
```

# git 图-实验

```mermaid
gitGraph:
options
{
    "nodeSpacing": 150,
    "nodeRadius": 10
}
end
commit
branch newbranch
checkout newbranch
commit
commit
checkout master
commit
commit
merge newbranch
```

# 	实体关系图——实验

```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ LINE-ITEM : contains
    CUSTOMER }|..|{ DELIVERY-ADDRESS : uses
```

# 用户旅行图

```mermaid
journey
    title My working day
    section Go to work
      Make tea: 5: Me
      Go upstairs: 3: Me
      Do work: 1: Me, Cat
    section Go home
      Go downstairs: 5: Me
      Sit down: 5: Me
```