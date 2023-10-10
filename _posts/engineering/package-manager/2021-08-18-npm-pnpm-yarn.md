---
layout: post
title: npm pnpm yarn 对比
subtitle:
categories: engineering
tags: [engineering, package manager]
# top: 2
---

## npm3之前的问题

- 嵌套安装依赖，依赖过深

- 重复依赖，存储多个相同的package/module

## npm3/yarn

npm3/yarn解决了上面的问题。使用扁平化的同一层级的文件结构，保存package/module。

### 未解决的问题

根据package.json中的前后顺序，多个版本的包只有最前面的一个能保存在顶级目录。新的问题，package.json中的依赖顺序的不确定，导致顶级目录的文件结构的不确定。

## package-lock.json/yarn-lock.json的作用

解决npm3/yarn的未解决的问题。

使用package-lock.json/yarn-lock.json，确保install后，产生确定的依赖结构

### 未解决的问题

- 依然会存在各种版本的包。

- 幽灵依赖：package没有在package.json中被依赖，但用户却能引用这个package。幽灵依赖，很容易出现包版本的问题。

## 参考资料

https://pnpm.io/motivation

https://www.bilibili.com/video/BV1Ex4y1G78Q/?spm_id_from=333.337.search-card.all.click&vd_source=22af953ea4c09540ad1966711a2d53f0
