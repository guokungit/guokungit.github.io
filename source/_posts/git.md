---
title: 实战中开发git
date: 2022-05-22 21:34:00
tags: git
categories: 仓库管理
comments: true
---
# 初始化仓库

git init

## 克隆仓库

git clone git地址

## 设置用户名和邮箱(--global 为全局参数，表明本地所有Git仓库都会使用这个配置)

git config --global user,name "用户名"
git config --global user.email "用户邮箱"

## 链接远程仓库

git remote add origin git地址

### 当修改密码后需要git push 时报http denied错误时
输入”git config –system –unset credential.helper”命令（重置远程端的用户名和密码）
或
git config --global http.emptyAuth true 
### 当本地查看远程分支时
###     ** git fetch origin。拉取远程分支列表

|git branch  |  |  |
| --- | --- | --- |
| | 空 |查看本地分支|
|  |-r  | 查看远程分支 |
|  | -a  | 查看全部焚毁  |

# 撤销git add . 的方法 -> git restore .

## 撤销git commit -m "注释"，

| git reset | --hard | 删除工作空间改动代码，撤销git add，撤销commit . 注意完成这个操作后，就恢复到了上一次的commit状态。 |
| --- | --- | --- |
|  | --mixed | 不删除工作空间改动代码，撤销git add，撤销commit .为默认参数,git reset --mixed HEAD^==git reset HEAD^ |
|  | --sort | 不删除工作空间改动代码，不撤销git add ，撤销commit|
| git commit  | --amend | 如果commit注释写错了，改一下注释 |

## 推送仓库

| git push | -u | 建立+推送远程分支 |
| --- | --- | --- |
|  | -f | 强制覆盖远程分支 |

### 暂时存储工作区代码，（可以在分支间横跳代码）

| git stash save "save message" | 执行存储时，添加备注，方便查找，只有git stash 也要可以的，但查找时不方便识别。 |
| --- | --- |
| git stash list | 查看stash了哪些存储 |
| git stash show | 显示做了哪些改动，默认show第一个存储,如果要显示其他存贮，后面加stash@{$num}，比如第二个 git stash show stash@{1} |
| git stash show -p | 显示第一个存储的改动，如果想显示其他存存储，命令：git stash show stash@{$num} -p ，比如第二个：git stash show stash@{1} -p |
| git stash apply | 应用某个存储,但不会把存储从存储列表中删除，默认使用第一个存储,即stash@{0}，如果要使用其他个，git stash apply stash@{$num} ， 比如第二个：git stash apply stash@{1} |
| git stash pop | 命令恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下,默认为第一个stash,即stash@{0}，如果要应用并删除其他stash，命令：git stash pop stash@{$num} ，比如应用并删除第二个：git stash pop stash@{1} |
| git stash drop stash@{$num} | 丢弃stash@{$num}存储，从列表中删除这个存储 |
| git stash clear | 删除所有缓存的stash |

# 创建分支

git branch test: 基于当前commit创建test分支。.git/HEAD 文件中记录了当前分支名字。
git checkout -b test; 创建并切换到test分支

# 删除分支

git branch -d test：删除本地test分支

git branch -D test： test分支还没有合入当前分支，所以要用-D参数才能删掉。

git push origin --delete test 删除远程test分支

git push origin :test 删除远程test分支

# 查看分支

|git  | branch |空  |列出当前分支清单  |
| --- | --- | --- | --- |
|  |  | -r | 查看远程分支 |
|  |  |-a  | 查看远程分支和本地分支 |
|  |  | -v |查看各个分支最后一个提交信息  |
|  |  |--merged  | 查看哪些分支已经合并入当前分支 |


# 拉取分支

git fetch origin 同步远程服务器的数据到本地

git checkout -b test origin/test\_remote 将远程分支test\_remote拉取下来到本地test分支

git checkout test 将远程分支test拉取下来到本地test分支

git pull test从远程分支test 中checkout下来的本地分支test成为跟踪分支，使用git pull或者git push就会操作到对应的远程分支test

## git merge的参数
merge的两个分支要有历史关联，没有的话需要添加git merge master --allow-unrelated-histories，参数。

|git  |  merge |  | 合并分支到当前分支 |
| --- | --- | --- | --- |
|  |  | --ff  | 快速合并，这个是默认的参数。如果合并过程出现冲突，Git会显示出冲突并等待手动解决 |
|  |  |--ff-only  | 只有能快速合并的情况才合并。如果合并过程出现冲突，Git会自动abort此次merge  |
|  |  |--no-ff   | 不使用快速合并。会生成一次新的提交记录，这个记录只是标识在这里进行了一次merge操作（目前还没想到应用场景） |
|  |  | --squash | 压缩合并。将待合并的分支的内容压缩成一个新的提交合并进来 |

## git rebase的参数

| git | rebase | 分布湿合并分支 |
| --- | --- | --- |
|-i HEAD~num  | 合并后num个commit记录 | p表示采用，s表示将当前commit合并到上一commit，d表示删除当前commit，。。。 |
|-i [start] [end]  | 左开右闭 | start和end分别代码commit id |
|-i [start] [end]  | onto [newbase] | 将部分分支复制到新commit上，实现中间一大段commit的删除 |
通过git rebase -i [start] [end] 或者 git rebase -i HEAD~num将本地的多次提交合并为一个，以简化提交历史。(左开右闭)

![截屏2021-12-01 18.38.28.png](gitrebase.png)
在用d时可删除部分分支。s时前面得有pick，
本地有多个提交时,如果不进行这一步,在git rebase master时会多次解决冲突(最坏情况下,每一个提交都会相应解决一个冲突)
### 复制部分分支

git rebase \[startpoint\] \[endpoint\] \-\-onto \[branchName\] （\[startpoint\] \[endpoint\]指定的是一个前开后闭的区间，将该区间中的分支复制到另一个分支上）
运行git rebase --continue命令继续变基。
运行git rebase --abort命令回到rebase之前的状态。
个人见解，rebase就是操作本地的提交记录实现对commit的简化。git rebase branchname，在次合并分支，解决冲突，将冲突解决在开发分支上，避免主分支上出现脏分支记录。

# git commit 记录查询

|  git | show | 空 |.查看最新的commit|
| --- | --- | --- | --- |
|  |  | commitId  | .查看指定commit hashID的所有修改：|
|  |  | commitId fileName |.查看某次commit中具体某个文件的修改： |

`git tag` 是 Git 中用于管理标签（tag）的命令，标签主要用于标记代码库中的重要节点（如版本发布），方便后续查阅和回溯。

## 以下是 `git tag` 常用的操作：

1. **查看所有标签**
```bash
git tag
```
按字母排序显示所有标签

2. **创建轻量标签（lightweight）**
```bash
git tag v1.0.0
```
轻量标签仅包含标签名，不附带额外信息

3. **创建附注标签（annotated）**
```bash
git tag -a v1.0.0 -m "版本1.0.0发布"
```
- `-a` 表示创建附注标签（会存储在 Git 数据库中）
- `-m` 指定标签说明信息

4. **为历史提交打标签**
```bash
git tag -a v0.9.0 8f937c6 -m "为历史提交打标签"
```
其中 `8f937c6` 是历史提交的哈希值前几位

5. **删除标签**
```bash
git tag -d v1.0.0
```

6. **推送标签到远程仓库**
```bash
# 推送单个标签
git push origin v1.0.0

# 推送所有未推送的标签
git push origin --tags
```

7. **检出标签**
```bash
git checkout v1.0.0
```
检出标签会使仓库处于分离头指针（detached HEAD）状态，通常用于查看历史版本

8. **搜索标签**
```bash
git tag -l "v1.0.*"
```
列出所有以 `v1.0.` 开头的标签

标签与分支的区别：标签是固定的，通常用于标记发布版本；而分支是动态的，用于开发过程中代码的分离与合并。

要删除远程仓库的分支和标签（tag），需要通过本地 Git 命令向远程仓库发送删除请求。以下是具体操作：


### **1. 删除远程分支**
格式：`git push <远程仓库名> --delete <分支名>`

```bash
# 删除远程的 feature-branch 分支
git push origin --delete feature-branch
```

- `origin` 是远程仓库的默认名称（可替换为实际远程仓库名）
- `--delete` 表示删除操作


### **2. 删除远程标签（tag）**
有两种等效方式：

#### 方式一：使用 `--delete` 参数
```bash
# 删除远程的 v1.0.0 标签
git push origin --delete v1.0.0
```

#### 方式二：推送空引用到远程标签
```bash
# 效果同上，删除远程的 v1.0.0 标签
git push origin :refs/tags/v1.0.0
```


### 注意事项：
1. **权限要求**：需要有远程仓库的写入权限才能执行删除操作。
2. **本地同步**：删除远程分支/标签后，本地不会自动删除对应的分支/标签，如需清理本地残留：
   ```bash
   # 删除本地分支
   git branch -d 本地分支名
   
   # 删除本地标签
   git tag -d 标签名
   ```
3. **谨慎操作**：远程分支/标签删除后难以恢复，建议删除前确认是否有必要备份。

`git config pull` 用于配置 Git 拉取（pull）操作的默认行为。Git 的 `pull` 操作本质上是 `fetch`（获取远程更新）加上 `merge`（合并到本地分支）或 `rebase`（变基）的组合，通过配置可以指定默认使用哪种方式。

常用的配置命令如下：

1. **设置默认合并方式为 `merge`**（Git 默认行为）：
   ```bash
   git config --global pull.rebase false
   ```
   执行 `git pull` 时，会将远程分支的更新 `fetch` 到本地后，通过 `merge` 合并到当前分支，可能产生合并提交。

2. **设置默认合并方式为 `rebase`**：（变基，解决远程分支与本地分支不同基时）
   ```bash
   git config --global pull.rebase true
   ```
   执行 `git pull` 时，会将远程分支的更新 `fetch` 到本地后，通过 `rebase` 方式将本地提交“嫁接”到远程更新之后，保持提交历史线性。

3. **更精细的 rebase 配置**：
   ```bash
   git config --global pull.rebase preserve
   ```
   在 rebase 时保留本地已提交的合并记录（适用于需要保留复杂分支合并历史的场景）。

4. **查看当前 pull 配置**：
   ```bash
   git config --global --get pull.rebase
   ```

配置的作用域：
- 加上 `--global` 表示全局配置（对当前用户所有仓库生效）
- 不加则只对当前仓库生效

选择哪种方式取决于团队协作规范和个人习惯，`rebase` 通常能保持更整洁的提交历史，而 `merge` 会明确保留分支合并的轨迹。

`git revert` 是 Git 中用于撤销某次提交的命令，它通过创建一个**新的提交**来抵消指定提交的更改，而不是直接删除历史记录，这使得它在协作场景中非常安全。

### 基本用法
```bash
# 撤销最近一次提交
git revert HEAD

# 撤销指定 commit（commit-hash 是提交的哈希值）
git revert <commit-hash>
```

### 工作原理
- 执行 `git revert <commit>` 时，Git 会生成一个新的提交，该提交的内容与被撤销的提交完全相反（比如删除变成添加，修改变成反向修改）。
- 原提交记录会被保留在历史中，只是新增了一个"抵消"它的提交，保证了历史的完整性。

### 常见场景
1. **撤销单个提交**：
   ```bash
   # 先查看提交历史，找到要撤销的 commit-hash
   git log --oneline
   
   # 假设要撤销的提交哈希是 a1b2c3d
   git revert a1b2c3d
   ```
   执行后会自动打开编辑器，让你填写撤销提交的说明（默认信息已包含被撤销的提交哈希），保存退出即可完成操作。

2. **撤销多个连续提交**（左开右闭区间，不包含 start-commit）：
   ```bash
   git revert <start-commit>..<end-commit>
   ```

3. **撤销合并提交**：
   合并提交（merge commit）有两个父提交，撤销时需要指定 `-m` 参数说明以哪个父提交为基准：
   ```bash
   git revert -m 1 <merge-commit-hash>
   ```
   通常 `-m 1` 表示以主分支（被合并的分支）为基准。

### 与 `git reset` 的区别
| 特性               | `git revert`                  | `git reset`                    |
|--------------------|-------------------------------|--------------------------------|
| 历史记录           | 保留原提交，新增撤销提交      | 直接删除指定提交之后的历史     |
| 协作安全性         | 安全，不修改共享历史          | 危险，修改已推送的历史会冲突   |
| 适用场景           | 撤销已推送（public）的提交    | 撤销本地未推送（private）的提交 |

使用建议：如果提交已经推送到远程仓库，优先使用 `git revert`；如果只是本地修改且未推送，可考虑 `git reset`。