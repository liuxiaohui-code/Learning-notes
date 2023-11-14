# computed 和 watch 之间的区别

1. computed 能完成的功能，watch 都可以完成。
2. watch 能完成的功能，computed 不一定能完成，例如：watch 可以进行异步操作。

​ 两个重要的小原则： 1.所被 Vue 管理的函数，最好写成普通函数，这样 this 的指向才是 vm 或 组件实例对象。 2.所有不被 Vue 所管理的函数（定时器的回调函数、ajax 的回调函数等、Promise 的回调函数），最好写成箭头函数，这样 this 的指向才是 vm 或 组件实例对象。

# 子组件生命周期的执行顺序

挂载 mounted()顺序，先子组件内部的路由，在子组件，在父组件

# @-src

vue-cli 默认@ 为 src

在 js 文件或者 vue 文件的 script 标签中使用：'@/xxx'

css（scss）文件在 scss 或者 vue 的 style 标签中导入示例: '~@/xxx'

在 vue 的 template 模板标签中引入图片路径示例（两种方式均可使用）

@智能提示

在项目根目录下新建 jsconfig.json 文件

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"],
      "components/*": ["src/components/*"],
      "assets/*": ["src/assets/*"],
      "views/*": ["src/views/*"],
      "common/*": ["src/common/*"]
    }
  },
  "exclude": ["node_modules", "dist"]
}
```

# vue 关于 this 指向

(1).组件配置中：

data 函数、methods 中的函数、watch 中的函数、computed 中的函数 它们的 this 均是【VueComponent 实例对象】。

(2).new Vue(options)配置中：

data 函数、methods 中的函数、watch 中的函数、computed 中的函数 它们的 this 均是【Vue 实例对象】。

# TypeError: file.split is not a function
