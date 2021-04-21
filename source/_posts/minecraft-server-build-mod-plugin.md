---
title: minecraft 服务端搭建, 支持模组(mod) + 插件(plugin)
date: 2020-02-23 02:23:23
categories:
  - 生活札记
---

备注: 此搭建方法不限制与 `linux` 或 `windows`, 且两者的搭建流程完全一致, 这里我将使用 linux 进行搭建。

##### 步骤 1: 你需要准备什么？

- 一台服务器(window, linux 均可)
- java `8` 环境(别问为什么是 8, 问就是兼容性)

##### 步骤 2: 下载服务端

备注: minecraft 的服务端有很多种, 每种服务端都有各自的优缺点，有兴趣的同学可以自行摸索一下, 在这里的话, 因为我们的服务端需要支持安装 mod, 所以我选择使用 minecraft forge 来进行搭建。

<!--more-->

[https://files.minecraftforge.net](https://files.minecraftforge.net)。

![](https://yyccyy.com/wp-content/uploads/2020/02/minecraft-server-build-1.png)

`图中点进去获取真实的下载链接后, 使用 wget 进行下载`

```bash
wget https://files.minecraftforge.net/maven/net/minecraftforge/forge/1.12.2-14.23.5.2768/forge-1.12.2-14.23.5.2768-installer.jar
```

如果服务器的下载速度太慢或者下载失败, 可以通过自己的电脑进行下载, 之后可通过 ftp 上传至服务器。

##### 步骤 3: 安装服务端

```bash
java -jar forge-1.12.2-14.23.5.2768-installer.jar --installServer
```

安装过程中需要下载各种资源包, 如果服务器的下载速度太慢或者下载失败, 可以通过自己的电脑进行安装下载, 之后可通过 ftp 上传至服务器。

`最终安装完成后, 得到的目录结构`

![](https://yyccyy.com/wp-content/uploads/2020/02/minecraft-server-build-2.png)

##### 步骤 4: 一切就绪, 启动服务端

```bash
java -Xmx1333M -Xms1333M -jar forge-1.12.2-14.23.5.2768-universal.jar nogui
```

`第一次启动必然会失败`, 原因是我们没有同意相关的协议。

我们需要同意相关的协议才能启动服务端, 编辑根目录的 `eula.txt` 文件, 启动服务端后自动生成的文件。

将 `eula` 参数设置为 `true` 。

再次启动服务端, 大功告成~

##### （可选）步骤 5: 安装 plugin

在 minecraft forge 的服务端中, 默认是不支持安装 plugin 的, 只支持安装 mod。

但如果想在 minecraft forge 的服务端中安装 plugin, 也是非常非常简单的。

1.我们需要下载 SpongeForge 来作为运行插件的前提, [https://www.spongepowered.org/downloads](https://www.spongepowered.org/downloads)

![](https://yyccyy.com/wp-content/uploads/2020/02/minecraft-server-build-3.png)

2.将下好的 .jar 文件放入 mods 目录中, `客户端不需要放, 只需要放在服务端`。

3.有了运行插件的前提, 现在可以下载你需要的插件, [https://ore.spongepowered.org](https://ore.spongepowered.org), 将下载好的插件(.jar 文件), 同样的放入 mods 目录中, `与上面一样的是, 客户端不需要放, 只需要放在服务端`。

备注: 在 minecraft forge 的服务端中, 不管是 mod 还是 plugin 都放在 `mods` 目录中, 而不像其他服务端可能存在有 `plugins` 的目录。

##### 其他

这里推荐两个 plugin, 用于管理服务器的权限

1.Nucles [https://ore.spongepowered.org/Nucleus/Nucleus](https://ore.spongepowered.org/Nucleus/Nucleus), 提供了各种各样的权限配置, 但配置起来不是很方便, 其官方也是推荐是使用别的 plugin 来简化权限的配置。

2.LuckPerms [https://ore.spongepowered.org/Luck/LuckPerms](https://ore.spongepowered.org/Luck/LuckPerms), 专门用于高度定制化权限, 与 Nucles 使用效果可达最佳化。
