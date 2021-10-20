---
description: vue cli3 Generator插件开发教程
---

# vue cli3 Generator插件开发教程

### 定制生成插件

vue cli3提供了扩展定制化插件API。

CLI插件是一个可以为`@vue/cli`项目添加额外特性的npm包。

它始终包含一个`Service插件`作为其主要导出，可选包含一个 [Generator](https://cli.vuejs.org/zh/dev-guide/plugin-dev.html#generator) 和一个 [Prompt 文件](https://cli.vuejs.org/zh/dev-guide/plugin-dev.html#%E7%AC%AC%E4%B8%89%E6%96%B9%E6%8F%92%E4%BB%B6%E7%9A%84%E5%AF%B9%E8%AF%9D)。

当前文档讲述如何编写一个[Generator](https://cli.vuejs.org/zh/dev-guide/plugin-dev.html#generator)插件。

Generator插件用于向`package.json`文件注入额外的依赖或字段，并向项目中添加或删除文件。

一个Generator插件的目录解构是这样的：

```
.
├── README.md
├── generator  # generator 插件的文件夹
    ├── index.js  # generator插件
├── index.js      # service 插件
└── package.json
```

Generator插件示例代码：

```
// index.js
module.exports = (api, options, rootOptions, invoking) => {
    const pkg = {
        scripts: {
            test: 'vue-cli-service test'
        },
        dependencies:{
            'vue-router':'^3.0.1',
        }
    }
    // 修改 `package.json` 里的字段
    api.extendPackage(pkg)
    
    // 复制并用 ejs 渲染 `./template` 内所有的文件
    api.render('./template')
    
    // 传入api.render回调函数，可以删除文件或者修改文件,其中render函数依然使用ejs模板
    api.render((files, render) => {
        delete files['src/main.js']
        files['src/main.ts'] = render('./template/main.ts')
    })
​
    if (options.foo) {
        // 有条件地生成文件
    }
})
```

Generator的渲染模板：

当调用`api.render('./template')`时，该Generator插件会使用 [EJS](https://github.com/mde/ejs)渲染`./template`下的所有文件。

还可以使用yaml前置信息继承并替换已有的模板文件的一部分：

```
---
extend: '@vue/cli-service/generator/template/src/App.vue'   // 继承文件
replace: !!js/regexp /<script>[^]*?<\/script>/              // 替换规则
---
​
<script>
export default {
  // 替换默认脚本
}
</script>
```

也可以多出替换，你需要将要替换的字符串用`<%# REPLACE %>` 和 `<%# END_REPLACE %>` 块包裹起来：

```
---
extend: '@vue/cli-service/generator/template/src/App.vue'
replace:
  - !!js/regexp /欢迎来到你的 Vue\.js 应用/   // 第一个替换规则
  - !!js/regexp /<script>[^]*?<\/script>/  // 第二个替换规则
---
​
<%# REPLACE %>      // 对应第一个替换规则
替换欢迎信息
<%# END_REPLACE %>
​
<%# REPLACE %>     // 对应第二个替换规则
<script>
export default {
  // 替换默认脚本
}
</script>
<%# END_REPLACE %>
```

{% hint style="warning" %}
注意：如果你想渲染一个以点开头的模板文件(例如：`.env`)，需要把文件名开头的点改为下划线(因为以点开头的文件会在插件发布到npm的时候忽略)：

```
 _env

 # 调用api.render('./_env')后会在项目目录中渲染为：

 .env
```

当你想渲染以下划线开头的文件时，需要把文件名开头再添加一个下划线：

```
 __env.less

 # 调用api.render('./__env.less')后会在项目目录中渲染为：

 _env.less
```
{% endhint %}

发布插件

为了让一个 CLI 插件能够被其它开发者使用，你必须遵循 `vue-cli-plugin-<name>` 的命名约定将其发布到 npm 上。插件遵循命名约定之后就可以：

* 被 `@vue/cli-service` 发现；
* 被其它开发者搜索到；
* 通过 `vue add <name>` 或 `vue invoke <name>` 安装下来。

使用命令：

```
vue add <name>
```

安装命令：

```
vue invoke <name>
```

至此完成，vue cli Generator插件已经发布成功并且可以使用了
