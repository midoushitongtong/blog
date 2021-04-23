---
title: react 服务端渲染之 next.js
date: 2019-01-15 13:25:25
categories:
  - 开发杂记
---

### 为什么使用 Next.js 来做 React 服务端渲染 ?

- 轻量级，简单易懂，仅需学习几个核心的 API 就可上手开发
- 易扩展, 与 [express](https://github.com/zeit/next.js/tree/7.0.0-canary.8/examples/custom-server-express 'express'), [koa](https://github.com/zeit/next.js/tree/7.0.0-canary.8/examples/custom-server-koa 'koa') 等 node 框架无缝接入
- [支持缓存](https://github.com/zeit/next.js/tree/7.0.0-canary.8/examples/ssr-caching '缓存 ssr 页面')
- typescript 支持

<!--more-->

### 快速搭建 Next.js 环境

#### 初始化 `pacakge.json`

```bash
npm init -y
```

#### 安装相关依赖

```bash
npm install --save next react react-dom
```

#### 添加 next.js 相关的脚本

`/package.json`

```json
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

#### 编写测试文件

`/pages/index.js`

```jsx
import Head from 'next/head';

export default () => (
  <div>
    <Head>
      <title>My page title</title>
    </Head>
    <p>Hello world!</p>
  </div>
);
```

#### 运行项目, 浏览器访问 `http://localhost:3000`

```bash
npm run dev
```

next.js 的详细用法请参照[官方文档](https://github.com/zeit/next.js/)
