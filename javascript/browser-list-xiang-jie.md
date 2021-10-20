# Browser list详解

### browserlist是什么？

browserlist用于在不同前端工具之间共用目标浏览器和node版本的配置工具。

简单来说，就是用来控制浏览器版本的一个插件。

例如：

* 搭配babel转义代码，将老浏览器不支持的新API转义为可运行代码（polyfill）
* 搭配Autoprefixer编译CSS代码，自动添加浏览器前缀

### 怎么用？

browserlist可以用在下面工具中：

* [Autoprefixer](https://github.com/postcss/autoprefixer)
* [Babel](https://github.com/babel/babel/tree/master/packages/babel-preset-env)
* [postcss-preset-env](https://github.com/jonathantneal/postcss-preset-env)
* [eslint-plugin-compat](https://github.com/amilajack/eslint-plugin-compat)
* [stylelint-no-unsupported-browser-features](https://github.com/ismay/stylelint-no-unsupported-browser-features)
* [postcss-normalize](https://github.com/jonathantneal/postcss-normalize)
* [obsolete-webpack-plugin](https://github.com/ElemeFE/obsolete-webpack-plugin)

只要将下面的json内容添加到package.json中，以上所有工具都会自动找到（适配）目标浏览器：

```
  "browserslist": [
    "last 1 version",
    "> 1%",
    "maintained node versions",
    "not dead"
  ]
```

或者在项目根目录的`.browserslistrc`配置文件中添加下面的配置：

```
# Browsers that we support
​
last 1 version
> 1%
maintained node versions
not dead
```

开发者通过设置浏览器版本查询范围，使开发者不用频繁手动更新浏览器版本。browserslist 使用 [Can I Use](https://caniuse.com) 网站的数据来查询浏览器版本范围。

browserslist 提供在线的查询条件练习网站，下面会用到，点击 [这里](https://browserl.ist) 前往。

可以点[这里](https://github.com/browserslist/browserslist-example)来学习每种工具如何使用browserlist。

### 怎么查询？

browserlist会使用下列配置方式来进行查询浏览器和nodejs版本：

* 在package.json中加入`browserslist` 配置项。
* 使用项目根目录或父目录下的`.browserslistrc`配置文件。
* 使用项目根目录或父目录下的`.browserslist`配置文件。
* &#x20;`BROWERSLIST` 环境变量。
* 如果没有以上4种配置方式，browserlist会使用默认的配置：`> 0.5%, last 2 versions, Firefox ESR, not dead`。

#### 查询条件

查询关键字有`or/,`，`and`，`not`，比如有条件`> 0.5%`和`last 2 versions`。

则：

* `> 0.5% or last 2 versions`：取并集。
* `> 0.5% , last 2 versions`：取并集。
* `> 0.5% and last 2 versions`：取交集。
* `> 0.5% not last 2 versions`：取`> 0.5%`的差集。

{% hint style="info" %}
注：`or`/`,`都是取并集。
{% endhint %}

#### 查询条件列表

* `> 5%`: 基于全球使用率统计而选择的浏览器版本范围。`>=`,`<`,`<=`同样适用。
* `> 5% in US` : 同上，只是使用地区变为美国。支持两个字母的国家码来指定地区。
* `> 5% in alt-AS` : 同上，只是使用地区变为亚洲所有国家。[这里](https://github.com/ben-eb/caniuse-lite/tree/master/data/regions)列举了所有的地区码。
* `> 5% in my stats` : 使用[定制的浏览器统计数据](https://github.com/browserslist/browserslist#custom-usage-data)。
* `cover 99.5%` : 使用率总和为99.5%的浏览器版本，前提是浏览器提供了使用覆盖率。
* `cover 99.5% in US` : 同上，只是限制了地域，支持两个字母的国家码。
* `cover 99.5% in my stats` :使用[定制的浏览器统计数据](https://github.com/browserslist/browserslist#custom-usage-data)。
* `maintained node versions` :所有还被 node 基金会维护的 node 版本。
* `node 10` and `node 10.4` : 最新的 node 10.x.x 或者10.4.x 版本。
* `current node` :当前被 browserslist 使用的 node 版本。
* `extends browserslist-config-mycompany` :来自browserslist-config-mycompany包的查询设置
* `ie 6-8` : 选择一个浏览器的版本范围。
* `Firefox > 20` : 版本高于20的所有火狐浏览器版本。`>=`,`<`,`<=`同样适用。
* `ios 7` :ios 7自带的浏览器。
* `Firefox ESR` :最新的火狐 ESR（长期支持版） 版本的浏览器。
* `unreleased versions` or `unreleased Chrome versions` : alpha 和 beta 版本。
* `last 2 major versions` or `last 2 ios major versions` :最近的两个发行版，包括所有的次版本号和补丁版本号变更的浏览器版本。
* `since 2015` or `last 2 years` :自某个时间以来更新的版本（也可以写的更具体`since 2015-03`或者`since 2015-03-10`）
* `dead` :通过`last 2 versions`筛选的浏览器版本中，全球使用率低于0.5%并且官方声明不在维护或者事实上已经两年没有再更新的版本。目前符合条件的有 `IE10`,`IE_Mob 10`,`BlackBerry 10`,`BlackBerry 7`,`OperaMobile 12.1`。
* `last 2 versions` :每个浏览器最近的两个版本。
* `last 2 Chrome versions` :chrome 浏览器最近的两个版本。
* `defaults` :默认配置`> 0.5%, last 2 versions, Firefox ESR, not dead`。
* `not ie <= 8` : 浏览器范围的取反。

{% hint style="info" %}
注：你还可以添加`not`在任和查询条件前面，表示取反。
{% endhint %}

#### 浏览器列表

* `Android` for Android WebView.
* `Baidu` for Baidu Browser.
* `BlackBerry` or `bb` for Blackberry browser.
* `Chrome` for Google Chrome.
* `ChromeAndroid` or `and_chr` for Chrome for Android
* `Edge` for Microsoft Edge.
* `Electron` for Electron framework. It will be converted to Chrome version.
* `Explorer` or `ie` for Internet Explorer.
* `ExplorerMobile` or `ie_mob` for Internet Explorer Mobile.
* `Firefox` or `ff` for Mozilla Firefox.
* `FirefoxAndroid` or `and_ff` for Firefox for Android.
* `iOS` or `ios_saf` for iOS Safari.
* `Node` for Node.js.
* `Opera` for Opera.
* `OperaMini` or `op_mini` for Opera Mini.
* `OperaMobile` or `op_mob` for Opera Mobile.
* `QQAndroid` or `and_qq` for QQ Browser for Android.
* `Safari` for desktop Safari.
* `Samsung` for Samsung Internet.
* `UCAndroid` or `and_uc` for UC Browser for Android.
* `kaios` for KaiOS Browser.
