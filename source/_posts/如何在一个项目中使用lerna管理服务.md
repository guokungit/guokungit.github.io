---
title: 如何在一个项目中使用lerna管理服务
date: 2025-08-01 18:19:36
tags: 工程化
categories: 前端
---
在项目中使用Lerna进行服务管理，主要分为以下步骤，涵盖从初始化到日常开发管理的完整流程：

### 1. 安装Lerna
首先确保已安装Node.js（建议v14+），然后全局安装Lerna：
```bash
npm install -g lerna
# 或使用yarn
yarn global add lerna
```

### 2. 初始化Lerna项目
创建项目目录并初始化：
```bash
mkdir lerna-project && cd lerna-project
lerna init  # 默认固定版本模式
# 若需独立版本模式（各包版本独立）：
# lerna init --independent
```

初始化后会生成基础结构：
```
lerna-project/
├── lerna.json      # Lerna配置文件
├── package.json    # 根项目配置
└── packages/       # 存放所有子包的目录
```

### 3. 配置Lerna
修改`lerna.json`配置常用选项：
```json
{
  "packages": ["packages/*"],  // 子包路径
  "version": "0.0.0",          // 固定模式下的统一版本；独立模式为"independent"
  "npmClient": "npm",          // 可选"yarn"或"pnpm"
  "command": {
    "bootstrap": {
      "hoist": true  // 提升公共依赖到根目录，减少重复安装
    },
    "publish": {
      "conventionalCommits": true,  // 基于规范提交自动生成CHANGELOG
      "message": "chore(release): publish %s"  // 发布时的commit信息
    }
  }
}
```

### 4. 创建子包（服务）
使用`lerna create`创建子包（如API服务、工具库等）：
```bash
lerna create @myorg/api-service  # 创建@myorg/api-service包
lerna create @myorg/utils        # 创建@myorg/utils工具包
```

创建后`packages`目录会生成对应子包结构，每个子包包含自己的`package.json`。

### 5. 管理依赖
- **安装公共依赖**（如lodash）到所有子包：
  ```bash
  lerna add lodash
  ```

- **安装依赖到指定子包**（如给api-service安装express）：
  ```bash
  lerna add express --scope=@myorg/api-service
  ```

- **子包间相互依赖**（如api-service依赖utils）：
  ```bash
  lerna add @myorg/utils --scope=@myorg/api-service
  ```

- **安装根项目依赖**（如开发工具）：
  ```bash
  npm install -D typescript  # 直接用npm/yarn安装到根目录
  ```

### 6. 开发与构建
- **统一执行命令**（如在所有子包运行`build`脚本）：
  ```bash
  lerna run build  # 运行每个包的package.json中的build脚本
  ```

- **只在有变更的包中执行命令**（提高效率）：
  ```bash
  lerna run build --since main  # 只在main分支之后有变更的包中执行
  ```

- **在指定包中执行任意命令**：
  ```bash
  lerna exec --scope=@myorg/api-service -- node index.js  # 启动api服务
  ```

### 7. 版本管理与发布
- **版本升级**（根据提交记录自动更新版本）：
  ```bash
  lerna version  # 交互式选择版本号，生成CHANGELOG
  # 或自动升级：lerna version patch --yes（补丁版本）
  ```

- **发布到npm**（需先登录npm：`npm login`）：
  ```bash
  lerna publish  # 发布所有更新过的包到npm
  # 首次发布带scope的包（如@myorg/*）需确保package.json中"access": "public"
  ```

### 8. 其他常用操作
- **清理依赖**（删除所有子包的`node_modules`）：
  ```bash
  lerna clean
  ```

- **重新链接依赖**（修复子包间的符号链接）：
  ```bash
  lerna link
  ```

- **查看所有子包**：
  ```bash
  lerna list  # 或 lerna ls
  ```

通过以上步骤，可高效管理多服务/多包项目，实现依赖共享、统一构建、版本协同等功能。实际使用中可根据项目规模调整配置，例如结合Git Hooks确保提交规范，或集成CI/CD实现自动化发布。