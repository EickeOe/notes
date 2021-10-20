# 记JenKins CI部署前端项目时遇到的问题

问题1：CI工具在执`yarn`进行安装依`core-js`包后运行打包命令，报Module not found... core-js/moudles/es6......等等错误。

排除问题：

1、查看CI环境中项目的`node_modules`中的`core-js`包下是否有缺少的文件

2、在CI环境手动安装`core-js`包再次排查是否有缺少的文件

3、确认`yarn` `registry`源是否为官方源，使用 `yarn config list`命令来查看CI环境的源是否为官方源，如果不是官方源地址改为官方源地址：

```bash
yarn config set registry "https://registry.yarnpkg.com"
```

4、清除`yarn`缓存，命令：

```bash
yarn cache clean
```

5、再次使用CI来部署项目，问题解决了。
