# SSH 提交到 GitHub 完整流程

> 一份文档走完全程：从新电脑零配置，到每天写代码的提交推送。
> 全程只用 SSH 地址，不用 HTTPS，不用输密码，无代理冲突。

---

## 目录

1. [一次性配置（每台电脑只做一次）](#一一次性配置每台电脑只做一次)
2. [首次关联仓库（每个项目只做一次）](#二首次关联仓库每个项目只做一次)
3. [日常提交流程（每天必用）](#三日常提交流程每天必用)
4. [常见错误与解决](#四常见错误与解决)
5. [快速参考速查表](#五快速参考速查表)

---

## 一、一次性配置（每台电脑只做一次）

### 1.1 配置 Git 身份

所有提交都会显示这个名字和邮箱，必须与 GitHub 账号一致。

```bash
# 与 GitHub 用户名保持一致
git config --global user.name "你的用户名"

# 与 GitHub 注册邮箱保持一致
git config --global user.email "你的邮箱"
```

### 1.2 生成 SSH 密钥

```bash
# ed25519 比 RSA 更安全更快；已有密钥可跳过此步
ssh-keygen -t ed25519 -C "你的邮箱"
```

- 一路按回车即可，**不要设置密码**，否则每次操作都要输入。

```bash
# 查看并复制公钥内容
# Mac：
cat ~/.ssh/id_ed25519.pub | pbcopy

# Windows：
cat ~/.ssh/id_ed25519.pub
```

### 1.3 添加公钥到 GitHub

1. 登录 GitHub → 右上角头像 → **Settings** → **SSH and GPG keys**
2. 点击 **New SSH key**
3. Title 填设备名（如 `Windows PC` / `MacBook Pro`）
4. Key 粘贴刚才复制的公钥内容
5. 点击 **Add SSH key**

### 1.4 测试 SSH 连接（提交前必做）

```bash
ssh -T git@github.com
```

- 第一次会提示 `Are you sure you want to continue connecting (yes/no)?`
- **必须输入完整的 `yes` 再回车**（不能只输 `y`）
- ✅ 成功输出：`Hi 你的用户名! You've successfully authenticated, but GitHub does not provide shell access.`

### 1.5 网络不通时：配置代理

如果 `ssh -T` 失败（连接超时），检查 `C:\Users\1900\.ssh\config` 文件，确保包含：

```config
Host github.com
    HostName github.com
    User git
    ProxyCommand connect -S 127.0.0.1:7897 %h %p
```

> `7897` 是本地代理端口，按你实际使用的代理软件端口修改。
> 修改后重新执行 `ssh -T git@github.com` 验证。

---

## 二、首次关联仓库（每个项目只做一次）

### 场景 A：GitHub 上已有仓库，想拉到本地工作

```bash
# 克隆远程仓库到本地
git clone git@github.com:你的用户名/仓库名.git

# 进入项目目录
cd 仓库名
```

### 场景 B：本地已有项目，想推送到 GitHub 新建的空仓库

> ⚠️ 在 GitHub 网页创建仓库时，**不要勾选 Add a README file**，否则首次推送会冲突。

```bash
# 在本地项目文件夹下执行
git init
git add .
git commit -m "初始化项目"

# 关联远程仓库（永远用 SSH 格式，不要用 HTTPS）
git remote add origin git@github.com:你的用户名/仓库名.git

# 首次推送（-u 建立追踪关系，以后直接 git push 即可）
git push -u origin main
```

### 管理远程地址（改地址 / 清理）

```bash
# 查看当前所有远程仓库
git remote -v

# 情况 1：已有远程，想改成 SSH（最常用）
git remote set-url origin git@github.com:你的用户名/仓库名.git

# 情况 2：远程已存在，想重新关联
git remote remove origin
git remote add origin git@github.com:你的用户名/仓库名.git

# 情况 3：删除多余的远程（只保留 origin）
git remote remove 其他名字
```

✅ 验证：`git remote -v` 必须只输出下面两行，且为 SSH 格式：

```plaintext
origin  git@github.com:你的用户名/仓库名.git (fetch)
origin  git@github.com:你的用户名/仓库名.git (push)
```

---

## 三、日常提交流程（每天必用）

> 🚨 **黄金法则：先拉取，再修改，最后提交推送** —— 这是避免冲突的唯一方法。

```bash
# 1. 开始工作前：拉取远程最新代码（最重要！）
git pull origin main

# 2. 写完代码后：查看修改状态（可选，建议养成习惯）
git status

# 3. 暂存所有修改的文件
git add .

# 4. 提交到本地仓库（双引号内写清楚改了什么）
git commit -m "清晰描述你做了什么修改"

# 5. 推送到 GitHub
git push origin main
```

> 💡 进阶：用 `git pull --rebase origin main` 代替普通 `pull`，历史更干净，不产生多余的合并提交。

### 一键提交（懒人版）

```bash
git add . && git commit -m "更新代码" && git push origin main
```

---

## 四、常见错误与解决

### 错误 1：推送被拒 —— `non-fast-forward` / "远程有更新，推送被拒"

远程有了新提交，需要先拉取合并：

```bash
git pull origin main

# 若提示 failed to merge unrelated histories，改用：
git pull origin main --allow-unrelated-histories
# 若弹出 Vim 编辑器，输入 :wq 回车保存

# 手动解决冲突后（删除 <<<<<<< ======= >>>>>>> 标记，保留正确代码）
git add .
git commit -m "解决冲突"
git push origin main
```

### 错误 2：`remote origin already exists`

本地已关联过远程，先删旧的再添加：

```bash
git remote remove origin
git remote add origin git@github.com:你的用户名/仓库名.git
```

### 错误 3：拉取失败 —— "有未提交的更改"

```bash
git stash            # 暂存本地修改
git pull origin main # 拉取
git stash pop        # 恢复暂存的修改
```

### 错误 4：本地改乱了，想恢复到远程最新版

> ⚠️ 会丢失本地所有未提交的修改，谨慎使用！

```bash
git fetch origin
git reset --hard origin/main
```

---

## 五、快速参考速查表

| 操作 | 命令 |
| --- | --- |
| 测试 SSH 连接 | `ssh -T git@github.com` |
| 克隆仓库 | `git clone git@github.com:你的用户名/仓库名.git` |
| 添加 SSH 远程 | `git remote add origin git@github.com:你的用户名/仓库名.git` |
| 修改为 SSH 远程 | `git remote set-url origin git@github.com:你的用户名/仓库名.git` |
| 查看远程配置 | `git remote -v` |
| 删除远程 | `git remote remove origin` |
| 首次推送 | `git push -u origin main` |
| 日常标准提交 | `git add . && git commit -m "说明" && git push origin main` |
| 解决推送被拒 | `git pull origin main` 后重新 `git push origin main` |
| 查看提交记录 | `git log --oneline` |

---

## 💡 永久记住

以后所有 Git 操作，**永远只使用 SSH 地址**，格式是：

```
git@github.com:你的用户名/仓库名.git
```

只要记住这个格式，并且每次提交前先执行 `ssh -T git@github.com` 验证连接，就再也不会遇到连接错误。
