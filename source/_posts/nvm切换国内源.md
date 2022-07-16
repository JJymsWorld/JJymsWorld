---
title: nvm切换国内源
categories:
  - [gallery]
tags:
  - [NodeJs]
date: 2022-07-16 16:53:53
---

- 临时方法

```shell
NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
```

- 长效方法

终端文件中写入以下设置(.bashrc或者.zshrc)

```shell
export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
```

