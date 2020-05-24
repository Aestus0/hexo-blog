---
title: lerna入门
date: 2019-08-10 15:56:22
tags:
---
![](https://lerna.js.org/images/lerna-hero.svg)

# 从零开始的lerna

## 关于lerna

在维护多个package的时候都会遇到一个选择，*是在一个项目内维护还是还是使用多个项目维护*。
数量较少时多仓库维护不会存在问题。然后随着package数量增加，会暴露出一些问题：

* package之间相互依赖，开发人员需要在本地手动执行npm link，维护版本号的更替。
* issue难以统一追踪，管理，因为其分散在独立的repo里。
* 每一个package都包含独立的node_modules，而且大部分都包含babel,webpack等开发时依赖，安装耗时冗余并且占用过多空间。

在遇到这个问题的时候一些明星仓库，例如：babel使用了lerna。

## 测试使用lerna

### 项目初始化

```
lerna init
```
生成的目录结构如下

```
.
├── lerna.json
├── package.json
└── packages
    ├── test
    │   ├── index.js
    │   ├── node_modules
    │   └── package.json
    └── test2
        ├── index.js
        ├── node_modules
        └── package.json
```

### 增加依赖

我们先给