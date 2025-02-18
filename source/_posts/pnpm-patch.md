---
title: 使用 pnpm patch 给第三方库打补丁
date: 2025-02-18 13:46:00
categories:
  - 开发杂记
---


### 起因

最近遇到了一个问题，就是在使用第三方库的时候，有些配置项是在库的源代码中直接写死的，在使用的时候没办法进行配置，一般遇到这种问题解决不了的时候， 通常会寻找其它合适的第三方的库，但有时候结果不是很理想，只有当前这个库能满足现有的需求，就会比较头疼，比较合理的解决方式是在 github 上提 issue 然后等待处理，或者自己 forke 仓库修复之后提交 PR，但是有时候项目可能着急上线等不了那么久，那么有没有其它方式能解决这个问题呢？有的，那就是使用 pnpm patch 给第三方库打补丁。

<!--more-->

### 什么是 pnpm patch

pnpm patch 是 pnpm 提供的一种给第三方库打补丁的功能，它允许我们直接修改第三库中的代码 (node_modules 中的代码)，然后将修改后的代码应用到项目中，这个功能刚好能满足我的需求，同时这个功能非常适合一些小修小补，或者紧急修复。

### 如何使用 pnpm patch 给第三方库打补丁

1. 先执行 `pnpm patch <package-name>@<version>` 命令，创建临时文件夹

    比如我要修改 `@shadergradient/react@2.0.19` 这个库那么就执行

    ```bash
    pnpm patch @shadergradient/react@2.0.19
    ```

    执行之后会生成一个临时文件夹，我们可以在控制台中看到这个临时文件夹的路径。

    ![](/images/post/pnpm-patch-1.png)

    之后我们使用编辑器打开这个临时文件夹，找到我们想要修改的文件，进行修改即可。

2. 使用 `pnpm commit <path>` 命令，保存我们刚刚修改的补丁文件

    ```bash
    pnpm patch-commit "D:\project-ksh\tgr-web\node_modules\.pnpm_patches\@shadergradient\react@2.0.19"
    ```
    
    执行完成后，会发现在根目录中看到生成了一个 `patchs` 文件夹，里面的补丁文件中含了我们刚刚修改的记录，同时在 `package.json` 中也会生成一个补丁文件对应的配置项

    ![](/images/post/pnpm-patch-2.png)

3. 大功告成，在之后的每次的 pnpm install 中都会自动应用我们刚刚的补丁，这样就解决了我们之前的问题。

