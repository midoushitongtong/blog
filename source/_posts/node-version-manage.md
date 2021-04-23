---
title: node 版本管理工具
date: 2019-01-31 20:05:05
categories:
  - 开发杂记
---

### 前言

最近在搭建一个开源项目的时候，需要 `Node v8` 的环境才能运行，但本地的环境是 `Node v10` , 于是乎在网上寻求解决方案，比较流行的解决方案有两种。

- [n](https://github.com/tj/n 'n')
- [nvm](https://github.com/creationix/nvm)

<!--more-->

### 用 N 还是 nvm ？

- `n` 的优势是方便快捷，因为本身只是一个 `npm package`，用 npm 安装一下就能使用，但很遗憾的是，只支持 linux，不支持 `windows` 。
- `nvm` 的优势是跨平台，即支持 linux 也支持 [windows](https://github.com/coreybutler/nvm-windows 'windows')，但是安装要比 `n` 费劲一点点。
- 综上所述，考虑到日后跨平台的原因，另一方面 github 上 `nvm` 比 `n` 的 star 要高不少，所以我选择了 `nvm` 。

### 在 Linux 中使用 nvm 管理 Node 版本

#### 安装 nvm

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
```

#### 重启 shell

这一步非常重要，不然无法加载 nvm 相关脚本

#### 使用 nvm

```bash
nvm ls-remote #获取可安装的 node 版本
nvm install 11.9.0 #安装指定版本的 node
nvm ls #查看已安装的 node 版本
nvm use 11.9.0 #切换 node 版本
```

![](/images/post/7.png)

nvm 的详细用法请参照[官方文档](https://github.com/creationix/nvm)
