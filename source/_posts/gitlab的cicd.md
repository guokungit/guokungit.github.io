---
title: gitlab的cicd
date: 2025-08-14 12:12:21
tags: cicd
categories: 运维
---
我了解 Git CI/CD。Git CI/CD 是基于 Git 版本控制系统的持续集成（Continuous Integration，CI）和持续部署（Continuous Deployment，CD）实践，旨在通过自动化流程提高软件开发效率和质量。
#### 一切都要在gitlab上注册过runner才能进行，（运维）
### 核心概念：
1. **持续集成（CI）**：
   - 开发者频繁将代码提交到 Git 仓库（如 GitHub、GitLab 等）
   - 每次提交后自动触发构建、测试流程
   - 快速发现代码集成问题，减少合并冲突

2. **持续部署（CD）**：
   - 代码通过 CI 验证后，自动部署到测试、预生产或生产环境
   - 减少人工操作，加速软件交付周期

### 常见工具：
- **GitHub Actions**：与 GitHub 深度集成的 CI/CD 工具，通过仓库中的 YAML 配置文件定义工作流
- **GitLab CI/CD**：GitLab 内置的 CI/CD 功能，使用 `.gitlab-ci.yml` 配置
- **Jenkins**：老牌 CI/CD 工具，可与 Git 仓库集成，支持复杂流水线配置

### 典型流程：
1. 开发者提交代码到 Git 仓库
2. 触发 CI 流水线：自动拉取代码、安装依赖、编译构建
3. 运行自动化测试（单元测试、集成测试等）
4. 测试通过后，CD 流程自动部署到目标环境
5. 全程生成日志报告，便于问题追踪

通过 Git CI/CD，团队可以更频繁、更可靠地交付软件，同时减少手动操作带来的错误。

在 GitLab 上为 Vue 前端项目配置 CI/CD 可以实现自动构建、测试和部署，大幅提升开发效率。以下是详细的配置步骤和示例：

### 核心流程设计
1. 当代码推送到 GitLab 仓库时，自动触发流水线
2. 执行依赖安装、代码检查、构建等操作
3. 构建成功后，自动部署到目标环境（如测试服、生产服）

### 配置文件示例
在项目根目录创建 `.gitlab-ci.yml` 文件，这是 GitLab CI/CD 的核心配置文件：

```
# 定义流水线执行阶段
stages:
  - install       # 安装依赖
  - lint          # 代码检查
  - test          # 运行测试
  - build         # 构建项目
  - deploy_test   # 部署到测试环境
  - deploy_prod   # 部署到生产环境

# 缓存node_modules，加速后续构建
cache:
  paths:
    - node_modules/

# 安装依赖阶段
install_deps:
  stage: install
  image: node:16-alpine  # 使用Node.js 16的轻量镜像
  script:
    - npm install --registry=https://registry.npm.taobao.org  # 使用国内镜像加速
  artifacts:
    paths:
      - node_modules/   # 保存依赖供后续阶段使用

# 代码检查阶段
code_lint:
  stage: lint
  image: node:16-alpine
  dependencies:
    - install_deps  # 依赖安装阶段的输出
  script:
    - npm run lint  # 假设package.json中定义了lint脚本

# 测试阶段
unit_test:
  stage: test
  image: node:16-alpine
  dependencies:
    - install_deps
  script:
    - npm run test:unit  # 运行单元测试

# 构建阶段
build_project:
  stage: build
  image: node:16-alpine
  dependencies:
    - install_deps
  script:
    - npm run build  # 构建生产版本
  artifacts:
    paths:
      - dist/  # 保存构建结果供部署使用
  only:
    - develop  # 只在develop分支执行
    - main     # 只在main分支执行

# 部署到测试环境
deploy_to_test:
  stage: deploy_test
  image: alpine:latest
  dependencies:
    - build_project
  script:
    # 安装ssh客户端
    - apk add --no-cache openssh-client
    # 配置SSH（需要在GitLab项目设置中添加SSH私钥作为CI/CD变量）
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan -H $TEST_SERVER_HOST >> ~/.ssh/known_hosts
    # 部署到测试服务器
    - scp -r dist/* $TEST_SERVER_USER@$TEST_SERVER_HOST:$TEST_SERVER_PATH
  only:
    - develop  # 只在develop分支部署到测试环境

# 部署到生产环境
deploy_to_prod:
  stage: deploy_prod
  image: alpine:latest
  dependencies:
    - build_project
  script:
    # 类似测试环境部署步骤，替换为生产服务器信息
    - apk add --no-cache openssh-client
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY_PROD" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan -H $PROD_SERVER_HOST >> ~/.ssh/known_hosts
    - scp -r dist/* $PROD_SERVER_USER@$PROD_SERVER_HOST:$PROD_SERVER_PATH
  only:
    - main  # 只在main分支部署到生产环境
  when: manual  # 需要手动触发，防止误部署

```



### 配置说明

1. **前置准备**：
   - 确保项目中有 `package.json` 并定义了 `lint`、`test:unit`、`build` 等脚本
   - 在 GitLab 项目的「Settings → CI/CD → Variables」中添加必要的环境变量：
     - 测试服务器相关：`TEST_SERVER_HOST`、`TEST_SERVER_USER`、`TEST_SERVER_PATH`
     - 生产服务器相关：`PROD_SERVER_HOST`、`PROD_SERVER_USER`、`PROD_SERVER_PATH`
     - SSH 密钥：`SSH_PRIVATE_KEY`（测试环境）、`SSH_PRIVATE_KEY_PROD`（生产环境）
![cicd变量配置](image.png)
2. **阶段说明**：
   - `install`：安装依赖并缓存，加速后续构建
   - `lint`：执行 ESLint 等代码检查
   - `test`：运行单元测试，确保代码质量
   - `build`：构建生产版本的静态文件
   - `deploy_test`：自动部署 develop 分支到测试环境
   - `deploy_prod`：手动触发 main 分支到生产环境的部署

3. **安全提示**：
   - 所有敏感信息（如 SSH 密钥、服务器地址）都应通过 GitLab 变量管理，不要硬编码在配置文件中
   - 生产环境部署设置为手动触发，避免代码提交后自动部署带来的风险

配置完成后，每次向 GitLab 推送代码时，都会自动触发相应的流水线，你可以在 GitLab 项目的「CI/CD → Pipelines」中查看执行状态和日志。


`SSH_PRIVATE_KEY` 是用于服务器身份验证的私钥，并非从某个地方"查看"，而是需要你**提前生成**并配置。以下是具体步骤：


### 1. 生成 SSH 密钥对（本地操作）
在你的本地电脑（或用于部署的机器）上生成 SSH 密钥对（公钥 + 私钥）：
```bash
# 打开终端，执行以下命令
ssh-keygen -t ed25519 -C "your_email@example.com"
```
- 按提示操作，可设置密钥保存路径（默认 `~/.ssh/id_ed25519`）
- 建议设置密码（passphrase）增强安全性


### 2. 获取私钥内容（`SSH_PRIVATE_KEY`）
生成后，私钥文件默认路径为 `~/.ssh/id_ed25519`（Windows 通常在 `C:\Users\你的用户名\.ssh\id_ed25519`）。

查看并复制私钥内容：
```bash
# 终端中查看私钥
cat ~/.ssh/id_ed25519
```
复制输出的**全部内容**（包括 `-----BEGIN OPENSSH PRIVATE KEY-----` 和 `-----END OPENSSH PRIVATE KEY-----` 标记）。


### 3. 将公钥配置到目标服务器
为了让 CI/CD 流水线能通过 SSH 连接服务器，需要将**公钥**添加到目标服务器的授权列表中：

1. 查看公钥内容：
   ```bash
   cat ~/.ssh/id_ed25519.pub  # 注意文件名末尾有 .pub
   ```

2. 将公钥内容添加到服务器的 `~/.ssh/authorized_keys` 文件中：
   ```bash
   # 替换为你的服务器信息，执行后输入服务器密码
   ssh user@server_ip "echo '你的公钥内容' >> ~/.ssh/authorized_keys"
   ```


### 4. 在 GitLab 中配置 `SSH_PRIVATE_KEY` 变量
1. 进入你的 GitLab 项目 → **Settings** → **CI/CD** → **Variables**（变量）
2. 点击 **Add variable**，填写：
   - **Key**：`SSH_PRIVATE_KEY`（必须与 `.gitlab-ci.yml` 中使用的变量名一致）
   - **Value**：粘贴步骤 2 中复制的**私钥完整内容**
   - 勾选 **Mask variable**（隐藏变量值，安全起见）
   - 勾选 **Protect variable**（如果只允许受保护分支使用，可选）
3. 点击 **Add variable** 保存


### 注意事项
- 私钥是敏感信息，**绝对不能泄露**或提交到代码仓库
- 如果私钥设置了密码（passphrase），需要额外配置 `SSH_KEY_PASSPHRASE` 变量
- 若服务器使用非默认 SSH 端口（如 2222），需在部署脚本中添加 `-P 2222` 参数

配置完成后，GitLab CI/CD 流水线就能通过这个私钥自动连接服务器进行部署了。

GitLab Runner 是执行 CI/CD 流水线任务的代理程序，需要正确配置才能运行你的 Vue 项目构建和部署流程。以下是详细的设置步骤：


### 一、Runner 类型选择
GitLab 提供两种 Runner：
- **共享 Runner**：由 GitLab 官方或你的团队管理员维护，所有项目可共享使用
- **特定 Runner**：仅用于你的项目，需自己安装和管理（推荐用于需要特殊环境的项目）


### 二、设置特定 Runner（推荐）
#### 1. 安装 Runner 到服务器
根据你的服务器系统选择安装方式（以 Linux 为例）：

```bash
# 1. 添加 GitLab 官方仓库
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash

# 2. 安装 Runner
sudo apt-get install gitlab-runner

# 3. 验证安装
sudo gitlab-runner --version  # 显示版本号即成功
```

其他系统（Windows/macOS）安装教程：[官方文档](https://docs.gitlab.com/runner/install/)


#### 2. 注册 Runner 到你的项目
需要从 GitLab 项目中获取**注册令牌**，然后将 Runner 与项目绑定：

##### 步骤 1：获取注册令牌
1. 进入你的 GitLab 项目 → **Settings** → **CI/CD** → 展开 **Runners** 配置
2. 在 **Specific runners** 部分，找到 `Registration token`（注册令牌）并复制


##### 步骤 2：注册 Runner
在安装 Runner 的服务器上执行注册命令：

```bash
sudo gitlab-runner register
```

按提示输入以下信息：
- `GitLab instance URL`：你的 GitLab 地址（如 `https://gitlab.com` 或自建 GitLab 域名）
- `Registration token`：粘贴步骤 1 复制的令牌
- `Description`：给 Runner 起个名字（如 `vue-deploy-runner`）
- `Tags`：设置标签（如 `vue,deploy`，后续可在 `.gitlab-ci.yml` 中指定使用该 Runner）
- `Executor`：选择执行器（推荐 `docker`，适合前端项目）
- 若选择 `docker`，还需指定默认镜像（如 `alpine:latest`）


#### 3. 启动 Runner 服务
```bash
# 启动服务
sudo gitlab-runner start

# 查看状态（确保显示 running）
sudo gitlab-runner status
```


### 三、配置 Runner 权限（关键）
如果 Runner 用 `docker` 执行器，需要赋予足够权限：
```bash
# 将 gitlab-runner 用户加入 docker 组（避免权限问题）
sudo usermod -aG docker gitlab-runner

# 重启 Runner 生效
sudo gitlab-runner restart
```


### 四、在流水线中指定 Runner
在 `.gitlab-ci.yml` 中通过 `tags` 指定使用你的 Runner（与注册时设置的标签一致）：

```yaml
# 例如在部署阶段指定
deploy_to_test:
  stage: deploy_test
  tags:  # 匹配 Runner 的标签
    - vue
    - deploy
  script:
    # 部署命令...
```


### 五、管理 Runner
在 GitLab 项目的 `Settings → CI/CD → Runners` 中可以：
- 启用/禁用 Runner
- 修改 Runner 标签
- 查看 Runner 状态（是否在线）
- 删除无用的 Runner


### 常见问题
1. **Runner 显示离线**：  
   检查 Runner 服务是否启动，以及服务器能否访问 GitLab 地址（网络/防火墙问题）。

2. **权限错误（如无法拉取代码、执行 docker）**：  
   确保 `gitlab-runner` 用户有足够权限（参考步骤 3）。

3. **构建速度慢**：  
   给 Runner 所在服务器增加资源（CPU/内存），或配置缓存（如 `node_modules` 缓存）。

配置完成后，你的流水线会自动使用指定的 Runner 执行任务，避免因共享 Runner 资源不足导致的卡住或失败问题。