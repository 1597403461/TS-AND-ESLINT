# 从 0 - 1 配置 TS 和 TS-ESLINT

## [triumph-github 详细配置信息]（https://github.com/1597403461/TS-AND-ESLINT）

## 初始化

`npm init -y`

## 初始化 webpack

`npm i webpack webpack-cli --save-dev`

## 安装 babel react ts 预设

`npm i @babel/core @babel/preset-env @babel/preset-react @babel/preset-typescript -D`

## 安装 react react-dom

`npm install --save react react-dom @types/react @types/react-dom`

## 安装 ts

`npm install --save-dev typescript awesome-typescript-loader source-map-loader`

## 初始化 ts

`tsc --init`

## 创建 webpack.config.js 文件

```js
// 简单配置
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/index.ts',
    output: {
        filename: 'app.js'
    },
    resolve: {
        extensions: ['.js', '.ts', '.tsx']
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/i,
                use: [
                    {
                        loader: 'awesome-typescript-loader'
                    }
                ],
                exclude: /node_modules/
            },
            { enforce: 'pre', test: /\.js$/, loader: 'source-map-loader' }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './index.html'
        })
    ]
};
```

## 重点 eslint

早期的 TypeScript 项目一般采用 TSLint 进行检查。由于其对 js 生态的兼容性的偏差，TS 的编码规范向 ESLint 靠拢。

TypeScript 和 ESLint 使用不同的 AST 进行解析，因此为了在 ESLint 中支持 TypeScript 代码检查需要制作额外的自定义解析器。为了能够解析 TypeScript 语法并转成与 ESLint 兼容的 AST.

@typescript-eslint/parser 诞生。

@typescript-eslint/parser 和@typescript-eslint/eslint-plugin 配合一起使用，可以设置 TypeScript 的校验规则。

安装依赖`npm i --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin`

使用 react 需要安装 eslint-plugin-react `npm i eslint-plugin-react -D`

```js
// .eslintrc.js
module.exports = {
    root: true,
    parser: '@typescript-eslint/parser', // 使用 ESLint 解析 TypeScript 语法
    plugins: ['@typescript-eslint'], // 在 ESLint 中加载插件 @typescript-eslint/eslint-plugin，该插件可用于配置 TypeScript 校验规则。
    extends: [
        'eslint:recommended',
        'plugin:@typescript-eslint/recommended',
        'plugin:react/recommended'
    ] // 在 ESLint 中使用共享规则配置
};
```

## Prettier

Prettier 与 ESLint 的区别在于 Prettier 专注于统一的格式规则，从而减轻 ESLint 在格式规则上的校验，而对于质量规则 则交给专业的 ESLint 进行处理。(ESLint 是必须的，Prettier 是可选的)

ESLint（TSLint） 和 Prettier 配合使用时格式规则有重复且产生了冲突,此时应该让两者把各自注重的规则功能区分开，使用 ESLint 校验质量规则，使用 Prettier 校验格式规则.

prettier 配置 `npm i prettier eslint-config-prettier eslint-plugin-prettier --save-dev`

prettier: prettier 插件的核心

eslint-config-prettier: 用于解决 ESLint 和 Prettier 配合使用时容易产生的格式规则冲突问题，其作用就是关闭 ESLint 中配置的一些格式规则.使其失效

eslint-plugin-prettier: 将 prettier 作为 ESLint 规则来使用

```js
{
  "extends": [
    "plugin:prettier/recommended", // 使用prettier中的样式规范
    "prettier/@typescript-eslint", // 用于关闭 @typescript-eslint/eslint-plugin 插件相关的格式规则集
  ]
}
```

注意： 此时可能 ESLint 插件并不能正确检验 TSX 文件；需要 VSCode 安装 ESLint 插件，并修改配置

```js
{
    "eslint.options": {    //指定vscode的eslint所处理的文件的后缀
        "extensions": [
            ".js",
            ".vue",
            ".ts",
            ".tsx"
        ]
    },
    "eslint.validate": [     //确定校验准则
        "javascript",
        "javascriptreact",
        {
            "language": "html",
            "autoFix": true
        },
        {
            "language": "vue",
            "autoFix": true
        },
        {
            "language": "typescript",
            "autoFix": true
        },
        {
            "language": "typescriptreact",
            "autoFix": true
        }
    ]
}
```

## commit规范代码

配合 husky 可以防止生成不规范的 Git Commit Message, 从而阻止用户进行不规范的 Git 代码提交.

安装husky `cnpm i husky -D`

```js
 "husky": {
   "hooks": {
      "pre-commit": "npm run lint"
    }
},

```

## 参考链接

[可能是目前最详细从零开始配置 TypeScript 项目的教程](https://mp.weixin.qq.com/s/PVr6Pjuf8Bm69xTLuqLxkw)
