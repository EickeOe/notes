# commitlint 校验工具安装及使用

环境要求：

* nodejs
* git

步骤：

1.  初始化nodejs项目：

    ```bash
    npm init -y
    ```

    这条命令会在项目根目录生成package.json配置文件
2.  然后安装husky，commitlint相关依赖：

    ```bash
    npm install --save-dev @commitlint/config-conventional @commitlint/cli husky
    ```
3.  在项目根目录新建commitlint.config.js配置文件，并加入下面的代码：

    ```javascript
    const types = [
      'task',
      'story',
      'bug'
    ];
    ​
    typeEnum={
      rules:{
        'type-enum': [2, 'always', types]
      },
      value:() => types
    }
    ​
    module.exports = {
      parserPreset: './parser-preset',
      extends: [
        "@commitlint/config-conventional"
      ],
      rules: {
        'body-leading-blank': [1, 'always'],
        'footer-leading-blank': [1, 'always'],
        'header-max-length': [2, 'always', 72],
        'scope-case': [2, 'always', 'kebab-case'],
        'subject-case': [2,'always','kebab-case'],
        'subject-empty': [2, 'never'],
        'subject-full-stop': [2, 'never', '.'],
        'type-case': [2, 'always', 'lower-case'],
        'type-empty': [2, 'never'],
        'type-enum': typeEnum.rules['type-enum']
      }
    };
    ```
4.  在项目根目录新建parser-preset.js配置文件，并加入下面的代码：

    ```javascript
    module.exports = {
      parserOpts: {
          headerPattern: /^(task|story|bug)(\(\S+\))?: \d+-(?!-)[\S]+$/,
          headerCorrespondence: ['type', 'scope']
      }
    };
    ```
5.  在package.json中加入下面的代码：

    ```javascript
    {
      "husky": {
        "hooks": {
          "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
        }
      }
    }
    ```

### 怎么配置commitlint？

#### 配置规则

每条规则由名称和配置数组组成，如：`name:[0, 'always', 72]`，数组包含：

* **Level**，0-2：0表示禁用这条规则、1表示警告、2表示错误。
* **Applicable**，`always|never`：`always`表示使用规则，never表示不使用规则。
* **Value**：用于这条规则的值。

规则配置可以写为数组，或者是函数，或者异步函数，或者一个promise，只要函数最终返回的是配置数组就可以。例子：

```javascript
  // 数组
  "rules": {
    "header-max-length": [0, "always", 72],
  }
  
  // 函数
  "rules": {
    "header-max-length": () => [0, "always", 72],
  }
  
  // 异步函数
  "rules": {
    "header-max-length": async () => [0, "always", 72],
  }
  
  // promise
  "rules": {
    "header-max-length": () => Promise.resolve([0, "always", 72]),
  }
```

具体配置如下：

```javascript
module.exports = {
  extends: [
    "@commitlint/config-conventional"
  ],
  rules: {
    'type-enum': [2, 'always', [
      'task','story','bug'
     ]],
    'type-case': [0],
    'type-empty': [0],
    'scope-empty': [0],
    'scope-case': [0],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
    'header-max-length': [0, 'always', 72]
  }
}
```
