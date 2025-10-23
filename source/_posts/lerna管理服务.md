---
title: lerna管理服务
date: 2025-06-22 19:10:06
tags: 脚手架
categories: 工程化
---

# 什么是 lerna

一个优化基于 git+npm 的多 package 项目的管理工具

## 优势

大幅减少重复操作
提升操作的标准化

## 官网

https://lerna.js.org/

# 为什么要 lerna

## 1、重复操作

    多package本地link
    多package依赖安装
    多package单元测试
    多package代码提交
    多package代码发布

## 2、版本一致性

    发布时版本一致性
    发布后相互依赖版本升级

package 越多，管理复杂度越高

## 案例

babel：https://github.com/babel/babel
vue-cli：https://github.com/vuejs/vue-cli
create-react-app：https://github.com/facebook/create-react-app

# lerna 开发脚手架流程

| 1、脚手架项目初始化 |            |                                                        |
| :------------------ | :--------: | -----------------------------------------------------: |
| 初始化 npm 项目     | 安装 lerna | <span style="color: red;">lerna init 初始化项目</span> |

|
V

| 2、创建 package                                            |                    |                     |
| :--------------------------------------------------------- | :----------------: | ------------------: |
| <span style="color: red;">lerna create 创建 package</span> | lerna add 安装依赖 | lerna link 链接依赖 |

|
V

| 3、脚手架开发和测试        |                                                          |                      |                          |
| :------------------------- | :------------------------------------------------------: | -------------------: | ------------------------ |
| lerna exec 执行 shell 脚本 | <span style="color: red;">lerna run 执行 npm 命令</span> | lerna clean 清空依赖 | lerna bootstrap 重装依赖 |

|
V

| 4、脚手架发布上线          |                                        |                      |                                                         |
| :------------------------- | :------------------------------------: | -------------------: | ------------------------------------------------------- |
| lerna version bump version | lerna changed 查看上版本以来的所有变更 | lerna diff 查看 diff | <span style="color: red;">lerna publish 项目发布</span> |
