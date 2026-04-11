---
title: Git Practices
slug: git-practices
categories: 后端开发
date: 2026-04-11
---

##  常见分支的命名含义

| 分支      | 用途           | 示例                   |
| ------- | ------------ | -------------------- |
| main    | 主干分支，保持随时可发布 | `main`               |
| dev     | 开发主线         | `dev-turingzy`       |
| feat    | 新功能开发        | `feat/user-auth`     |
| bugfix  | 缺陷/漏洞修复      | `bugfix/login-crash` |
| release | 版本发布         | `release/2.10.0`     |
| revert  | 回滚特定提交       | `revert-2282ea88`    |

具体命名以各自仓库的规范为准！

## 常用命令

### 基础配置与初始化

```bash
# 配置全局用户名
git config --global user.name "name"
# 配置全局邮箱
git config --global user.email "email"
# 查看全局配置
git config --global --list
# 初始化一个新的本地仓库
git init
# 克隆一个远程仓库到本地
git clone <url>
```

### 日常修改与提交

```bash
# 查看当前工作区和暂存区的状态
git status
# 将特定文件添加到暂存区
git add <file>
# 将所有修改和新增的文件添加到暂存区
git add .
# 提交暂存区的修改到本地仓库，并附带说明
git commit -m "first commit"
```

### 分支”增删查”

```bash
# 查看本地所有分支（带 * 的是当前分支）
git branch
# 查看本地和远程的所有分支
git branch -a
# 创建一个新分支（但停留在当前分支）
git branch <branch-name>
# 切换到指定分支 (传统用法)
git checkout <branch-name>
# 创建并立即切换到新分支 (传统经典用法)
git checkout -b <branch-name>
# 切换到指定分支 (Git 2.23+ 引入的新命令，语意更清晰)
git switch <branch-name>
# 创建并切换到新分支 (Git 2.23+ 新命令)
git switch -c <branch-name>
# 删除本地分支（需先切换到其他分支）
git branch -d <branch-name>
# 强制删除本地分支（即使有未合并的代码）
git branch -D <branch-name>
```

### 分支操作

```bash
# 拉取最新代码
git fetch origin
# 拉取最新代码并与本地合并
git pull
# 拉取最新代码并变基 (多人开发同一分支防分叉神器)
git pull --rebase
# 推送本地提交到远程
git push -u（可选，设置分支上游关系） origin <branch_name>
# 直接推送（已有分支上游关系）
git push
# 强制推送
git push --force-with-lease
# 将目标分支合并到当前分支
git merge <branch-name>
# 摘取某个特定的 commit 补丁到当前分支
git cherry-pick <commit-hash>

# 安全撤销某次历史提交 (生成反向提交，不篡改历史)
git revert <commit-hash>
# 查看极简版历史提交记录 (一行一个，非常清爽)
git log --oneline -n（可选，前 n 条）
# 查看当前还没 commit 的代码具体改了哪些内容
git diff
# 终极时光机：查看所有历史操作轨迹 (找回误删 commit 必备)
git reflog
```

## 整体开发流程

![](https://cdn.img.turingzy.cn/2026/20260411211123645.png)

为了稳妥起见，正式上线通常不会一次性全量，而是采用灰度发布的方式。

## 不同场景下的 Git 使用

### 场景一：新功能开发

**步骤 1**：基于 main 的最新提交创建 feat/user-permission 分支

```bash
git checkout main
git pull origin main
git checkout -b feat/user-permission
```

**步骤 2**：在功能分支上迭代开发

```bash
git add .
git commit -m "feat: 用户权限模块初始化"
git commit -m "feat: 添加角色管理接口"
git commit -m "feat: 权限校验中间件集成"
```

**步骤 3**：推送到远程

```bash
git push -u origin feat/user-permission
```

![](https://cdn.img.turingzy.cn/2026/20260411211123647.png)

> Tips：第一次推送用 `-u` 建立上游跟踪关系，后续直接 `git push` 即可

### 场景二：MR 时代码冲突

功能分支开发完成后，提交 MR 提示与 main 存在冲突，无法自动合并。

![](https://cdn.img.turingzy.cn/2026/20260411211123648.png)

**解决方式一**：在本地将 main 的最近一次提交合并到功能分支，解决冲突后再推送。

```bash
git checkout feat/user-permission
git fetch origin
git merge origin/main

# 出现冲突提示，手动解决冲突文件
# <<<<<<< HEAD
# ...
# =======
# ...
# >>>>>>> origin/main

# 解决后标记并提交
git add .
git commit -m "resolve conflict with main"
git push origin feat/user-permission
```

![](https://cdn.img.turingzy.cn/2026/20260411211123649.png)

**解决方式二**：在本地将 feat/user-permission 变基（Rebase）到 main 的最新提交，解决冲突后再**强制推送**。

变基解决冲突的流程与合并略有不同。最大的区别在于：变基是在 “重放” 提交，所以如果有多个 commit 冲突，就需要多次解决冲突；并且解决完后不需要新建 commit。

```bash
git checkout feat/user-permission
git fetch origin
git rebase origin/main

# 此时 Git 会暂停变基过程，提示出现冲突文件

# <<<<<<< HEAD
# ... (这里现在是 origin/main 的最新代码，即你新的"基底")
# =======
# ... (这里才是 feat/user-permission 分支上对应的 commit)
# >>>>>>> commit message

# 解决完冲突后，将修改加入暂存区标记为已解决
git add .

# 告诉 Git 冲突已解决，继续重放下一个 commit
git rebase --continue

# 重复上述 `解决 conflict -> add -> continue` 的步骤，直到提示 rebase 成功

# 变基完成后，因为你本地的提交历史（Hash值）已经全部改变了，正常的 push 会被远程仓库拒绝，必须使用强制推送覆盖远程的旧分支
git push origin feat/user-permission --force-with-lease
```

![](https://cdn.img.turingzy.cn/2026/20260411211123650.png)

> Tips：永远不要对已经推送到公共仓库（且有其他人依赖）的分支执行 Rebase

### 场景三：同步远程更新（解决分叉）

用户C 在本地的 co-test 提交了代码，但远程 co-test 已被其他人更新，导致 push 失败。

**解决方式一**：merge（保留合并记录）

联调分支（co-test）上多人频繁推送时，merge 更安全，不会改写他人历史。

```bash
git checkout co-test
git pull origin co-test # 自动 Merge
git push origin co-test
```

![](https://cdn.img.turingzy.cn/2026/20260411211123651.png)

**解决方式二**：rebase（保持线性历史）

rebase 将本地提交 "重放" 到远程最新提交之后，历史保持一条直线，没有合并提交。

```bash
git pull --rebase origin co-test
git push origin co-test
```

![](https://cdn.img.turingzy.cn/2026/20260411211123652.png)

场景二是冲突发生在两个不同的分支合并时，场景三则是是冲突发生在把本地的同名分支推送到远程的同名分支

### 场景四：多人并行开发与集成

用户A 在做支付模块、用户B 在做通知服务、用户C 在做数据报表，三条线并行开发，需要联调测试。

**解决方式**：基于 main 创建一个多人测试分支 co-test。

![](https://cdn.img.turingzy.cn/2026/20260411211123653.png)

将功能分支分别合并到 co-test 进行联调。

```bash
git checkout co-test
git pull origin co-test
git merge feature/payment

# 如果有冲突，解决后继续
git add .
git commit -m "Merge branch 'feature/payment' into co-test"
git push origin co-test
```

### 场景五：紧急回滚

#### 阶段一：紧急止血（安全回滚 Merge Commit）

功能分支 feat/payment 刚刚通过 MR 合入 main 并发布，引发了线上 Bug，需要马上撤销该功能的代码。

```bash
git log --oneline --merges -10
# -m 1：保留主干（main）的代码，只撤销作为第二条线并入的功能分支的代码
git revert -m 1 commit-hash
git push origin main
```

![](https://cdn.img.turingzy.cn/2026/20260411211123654.png)

注意，在多人协作的 main 分支上，绝对禁止使用 `git reset --hard`（强行回退历史）。必须使用 `git revert` 生成一个反向提交来抵消错误代码。

执行前：

![](https://cdn.img.turingzy.cn/2026/20260411211123655.png)

执行后：

![](https://cdn.img.turingzy.cn/2026/20260411211123656.png)
#### 阶段二：复杂情境（回滚时已有新功能合入）

在 feat/payment 合入后，又合入了 feat/notify。此时发现 feat/payment 有 bug 需要回滚。

直接对 feat/payment 的 MR 执行 `git revert -m 1 commit-hash` 依然是安全的。因为 revert 具有精确的针对性，它只会生成一个专门反转 feat/payment 变更的新 commit，不会直接抹除或影响  feat/notify 中新增的代码。

⚠️ 如果 feat/notify 强依赖了 feat/payment 中新增的函数或组件，单方面撤销代码会导致 feat/notify 功能瞬间崩溃。执行此操作前，务必通知相关人员，并在回滚后立即检查编译状态。

![](https://cdn.img.turingzy.cn/2026/20260411211123657.png)

#### 阶段三：修复并重新上线

线上危机解除了，现在你需要修复原本的 Bug 并重新发布。

不要再用分支 feat/payment 开发了。

因为已经被合并过的 commit，在 Git 的历史树上会被永久打上**已合并/已处理**的记录。执行 `git revert` 只是提了一个新的 commit，并没有清除那个记录。所以直接再次合并 **feat**/payment 时，Git 会自动无视那些老 commit，只抓取新写的几行 bugfix。

**Revert Revert-Commit** 是处理“由于 Bug 导致紧急回退，后续又需重新上线”最标准、也最不容易出错的做法。

```bash
git checkout main
git pull origin main
git checkout -b feature/payment-v2
# 撤销那个“撤销提交”，瞬间找回 feat-1, 2, 3 的全部代码
git revert <R1_commit_hash> 
# 开始修 bug
git add .
git commit -m "fix: resolve the logic issue"
```

![](https://cdn.img.turingzy.cn/2026/20260411211123658.png)

## 参考

- [git book](https://git-scm.com/book/zh/v2)