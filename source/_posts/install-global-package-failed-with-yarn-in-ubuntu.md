---
title: 解决在 Ubuntu 下使用 yarn 安装全局依赖不成功的问题
date: 2018-07-04 21:13:42
tags:
---
## 问题描述
本人在 ubuntu 16.04 下使用 yarn 安装 hexo，出现下面的提示，
```
yarn global v0.16.1
warning No license field
[1/4] Resolving packages...
[2/4] Fetching packages...
warning fsevents@1.0.14: The platform "linux" is incompatible with this module.
info "fsevents@1.0.14" is an optional dependency and failed compatibility check. Excluding it from installation.
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Installed hexo-cli@1.0.2 with binaries:
- hexo
Done in 2.45s.
```
看上去安装成功了，但在 terminal 打 hexo 的时候却提示
```
hexo: command not found
```

如果只是使用 `npm` 安装的话，hexo 是可以正常安装的。

在 Google 上搜索后发现是在 Ubuntu 下手动安装 `yarn`，使用 `nvm` 才会出现的问题，原因可能是 node 没有找到 yarn 的命令行位置，使用下列命令解决：
```
export PATH="$PATH:$(yarn global bin)"
```

删除原有的包, 重新安装：
```
yarn global remove hexo
yarn global remove hexo-cli
yarn global add hexo
```
成功。