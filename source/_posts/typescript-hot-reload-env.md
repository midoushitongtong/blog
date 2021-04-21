---
title: typescript 热部署环境搭建
date: 2018-12-25 15:11:11
categories:
  - 开发杂记
---

##### typescript 热部署环境搭建大致流程

1. 初始化 `pacakge.json`

```bash
npm init -y
```

2. 安装依赖, 用到以下依赖

   - `webpack` `webpack-cli` webpack 以及 webpack 命令行工具
   - `typescript` typescript 命令行工具
   - `webpack-dev-server` webpack 热部署服务器
   - `ts-loader` 用于集成 webpack 和 typescript
   - `html-webpack-plugin` 用于打包 html
   - `clean-webpack-plugin` 用于删除上一次打包的文件

```bash
npm install --save-dev webpack webpack-cli typescript ts-loader webpack-dev-server html-webpack-plugin clean-webpack-plugin
```

<!--more-->

3. 添加 webpack 脚本

`/package.json `

```json
{
  "scripts": {
    "webpack-dev": "webpack --mode development",
    "webpack-pro": "webpack --mode production",
    "start": "webpack-dev-server --mode development"
  }
}
```

4. 配置 webpack

`/webpack.config.js`

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
  devServer: {
    contentBase: path.join(__dirname, 'dist'),
    port: 8080,
  },
  entry: './src/index.ts',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  resolve: {
    extensions: ['.ts', '.js'],
  },
  module: {
    rules: [
      {
        test: /\.ts$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  plugins: [
    // 将指定的 html 文件生成一个新的 html 文件
    new HtmlWebpackPlugin({
      template: 'src/index.html',
    }),
    // 每次打包先删除 output 目录下的所有文件
    new CleanWebpackPlugin(),
  ],
};
```

5. 配置 typescript

`/tsconfig.json`

```json
{
  "compilerOptions": {
    "outDir": "./dist/",
    "noImplicitAny": true,
    "module": "es6",
    "target": "es5",
    "jsx": "react",
    "allowJs": true
  }
}
```

6. 编写测试文件

`/src/index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body>
    hello
  </body>
</html>
```

`/src/index.ts`

```typescript
interface Person {
  name: string;
  age: number;
}

const show = (person: Person) => console.log(person);

show({
  name: 'test',
  age: 123222333,
});
```

7. 启动热部署服务器, 浏览器访问`127.0.0.1:8080`查看控制台打印

```bash
npm run start
```
