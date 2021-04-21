---
title: electron + react + typescript 环境搭建
date: 2019-11-13 21:23:23
categories:
  - 开发杂记
---

##### 前言

最近在学习 electron，技术选型大致为 electron + typescript + react + redux + react-router，为了方便，想寻找现成的脚手架，但发现，现有的脚手架都不太满足我的需求。

`到目前为止 github 上比较流行的相关脚手架`

- [react-boilerplate](https://github.com/electron-react-boilerplate/electron-react-boilerplate) start 数量较多，但不支持 typescript
- [react-typescript-boilerplate](https://github.com/iRath96/electron-react-typescript-boilerplate) 支持 typescript，但 1 年前就已停止维护

`综上所述，打算自己搭建一个满意的开发环境，顺便提高一下自己的动手能力，大致的思路如下`

<!--more-->

- 使用 [create-react-app](https://create-react-app.dev/docs/adding-typescript/) 快速创建 react + typescript 项目，如果有使用 [create-react-app](https://create-react-app.dev/docs/adding-typescript/) 开发的项目，也通过此方式与 electron 进行集成
- 安装 [react-app-rewired](https://github.com/timarney/react-app-rewired) 工具，用于扩展 webpack 相关配置
- 安装 electron 环境
- 修改 electron 入口文件，使用 mainWindow.loadURL 方法加载 react 页面，分为`开发环境`和`生产环境`
- 使用 electron-builder 打包项目

##### 基本环境搭建

1. 创建 react + typescript 项目

```bash
npx create-react-app my-app --typescript
```

2. 安装 react-app-rewired 以及 cross-env

```bash
npm i -D react-app-rewired cross-env
```

3. 创建 react-app-rewired 配置文件 `config-overrides.js` 用于扩展 webpack 配置

`/config-overrides.js`

```javascript
module.exports = (config, env) => {
  // 为了方便使用 electron 以及 node.js 相关的 api
  // 需要将 target 设置为 electron-renderer
  // 设置了 target 之后，原生浏览器的环境将无法运行此 react 项目(因为不支持 node.js 相关的 api)，会抛出 Uncaught ReferenceError: require is not defined 异常
  // 需要在 electron 的环境才能运行(因为支持 node.js 相关的 api)
  // 这一步的操作, 都是为了能与 electron 进行更好的集成
  config.target = 'electron-renderer';
  return config;
};
```

4. 安装 electron 环境

```bash
npm i -D electron
```

5. 创建 electron 入口文件

`/main.js`

```javascript
const { app, BrowserWindow } = require('electron');
const path = require('path');
const url = require('url');

let mainWindow = null;
const createWindow = () => {
  let mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true,
      preload: path.join(__dirname, 'preload.js'),
    },
  });

  /**
   * loadURL 分为两种情况
   *  1.开发环境，指向 react 的开发环境地址
   *  2.生产环境，指向 react build 后的 index.html
   */
  const startUrl =
    process.env.NODE_ENV === 'development'
      ? 'http://localhost:3000'
      : path.join(__dirname, '/build/index.html');
  mainWindow.loadURL(startUrl);

  mainWindow.on('closed', function () {
    mainWindow = null;
  });
};

app.on('ready', createWindow);

app.on('window-all-closed', function () {
  if (process.platform !== 'darwin') app.quit();
});

app.on('activate', function () {
  if (mainWindow === null) createWindow();
});
```

6. 添加相关脚本

`/package.json`

```javascript
  "scripts": {
    "start": "cross-env BROWSER=none react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test --env=jsdom",
    "eject": "react-scripts eject",
    "start-electron": "cross-env NODE_ENV=development electron .",
    "start-electron-prod": "electron ."
  },
```

7. 添加 electron 及 node.js 相关代码，用于测试是否集成完毕

`/src/App.tsx`

```tsx
import React from 'react';
import { remote } from 'electron';
import fs from 'fs';

interface Props {}

interface State {
  txtFileData: string;
}

export default class App extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = {
      txtFileData: '',
    };
  }

  /**
   * 弹出文件选择框，选择 .txt 文件
   * 将选中的 .txt 内容展示到页面
   */
  public readTxtFileData = async () => {
    const result = await remote.dialog.showOpenDialog({
      title: '请选择 .txt 文件',
      filters: [
        {
          name: 'txt',
          extensions: ['txt'],
        },
      ],
    });
    fs.readFile(result.filePaths[0], 'utf-8', (err, data) => {
      if (err) {
        console.error(err);
      } else {
        this.setState({
          txtFileData: data.replace(/\n|\r\n/g, '<br/>'),
        });
      }
    });
  };

  public render = (): JSX.Element => {
    return (
      <section>
        <button onClick={this.readTxtFileData}>读取一个txt文件的内容</button>
        <div dangerouslySetInnerHTML={{ __html: this.state.txtFileData }} />
      </section>
    );
  };
}
```

8. 运行测试

`一个命令行窗口跑 react 项目`

```bash
npm run start
```

`另一个命令行窗口跑 electron 项目`

```bash
npm run start-electron
```

##### 项目打包

1. 添加 electron-builder 工具

```javascript
npm i -D electron-builder
```

2. 添加脚本以及打包相关配置

`/package.json`

```javascript
  "homepage": "./",
  "scripts": {
    "build-electron": "electron-builder"
  },
  "build": {
    "appId": "com.xxx.xxx",
    "productName": "react-electron",
    "extends": null,
    "directories": {
      "output": "build-electron"
    },
    "files": [
      "./build/**/*",
      "./main.js",
      "./package.json"
    ],
    "win": {
      "icon": "src/asset/icon.ico"
    }
  }
```

3. 开始打包

`打包 react`

```bash
npm run build
```

`react 打包完成后，可以运行 electron 生产环境查看一下功能是否正常运行`

```bash
npm run start-electron-prod
```

`打包 electron 项目为安装包，安装包会生成到指定的 build-electron 目录`

```bash
npm run build-electron
```

`以上就是 react + typescript + electron 大致配置流程`
`更详细的配置请查阅官方文档`

##### github

[react-electron-boilerplate](https://github.com/MiDouShiTongTong/react-electron-boilerplate)

##### 参考资料

[https://magicly.me/electron-starter](https://magicly.me/electron-starter)

[https://www.freecodecamp.org/news/building-an-electron-application-with-create-react-app-97945861647c](https://www.freecodecamp.org/news/building-an-electron-application-with-create-react-app-97945861647c)
