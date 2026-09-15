## 一、全局配置（每台电脑只做一次）

配置你的 Git 身份信息，所有提交都会显示这个名字和邮箱。

bash

运行

```
# 配置用户名（与 GitHub 用户名保持一致）
git config --global user.name "你的GitHub用户名"

# 配置邮箱（与 GitHub 注册邮箱保持一致）
git config --global user.email "你的GitHub注册邮箱"
```

---

## 二、配置 SSH 免密登录（每台电脑只做一次）

使用 SSH 协议连接 GitHub，避免每次推送都输入密码，且不受网络限制。

bash

运行

```
# 生成 ed25519 密钥对（比 RSA 更安全更快，已有密钥可跳过此步）
ssh-keygen -t ed25519 -C "你的GitHub注册邮箱"
```

- 一路按回车即可（不要设置密码，否则每次操作都需要输入）

bash

运行

```
# 查看并复制公钥
# Mac：
cat ~/.ssh/id_ed25519.pub | pbcopy

# Windows：
cat ~/.ssh/id_ed25519.pub
```

1. 登录 GitHub → 右上角头像 → **Settings** → **SSH and GPG keys**
2. 点击 **New SSH key**
3. Title 填写设备名（如 "MacBook Pro" / "Windows PC"）
4. Key 粘贴刚才复制的公钥内容
5. 点击 **Add SSH key**

**测试连接：**

bash

运行

```
ssh -T git@github.com
```

- 第一次会提示 `Are you sure you want to continue connecting (yes/no)?`
- **必须输入完整的 `yes` 然后回车**（不能只输 `y`）
- 成功会显示：`Hi 你的用户名! You've successfully authenticated, but GitHub does not provide shell access.`

---

## 三、获取仓库到本地（二选一）

### 场景 A：GitHub 上已有仓库，想在本地开始工作

bash

运行

```
# 克隆远程仓库到本地
git clone git@github.com:你的用户名/仓库名.git

# 进入项目目录
cd 仓库名
```

### 场景 B：本地已有项目，想推送到 GitHub 新建的空仓库

> ⚠️ 注意：在 GitHub 网页创建仓库时，**不要勾选 Add a README file**

bash

运行

```
# 在本地项目文件夹下执行
git init
git add .
git commit -m "初始化项目"

# 关联远程仓库
git remote add origin git@github.com:你的用户名/仓库名.git

# 首次推送到 main 分支（-u 建立追踪关系，以后直接 git push 即可）
git push -u origin main
```

---

## 四、日常提交流程（每次修改后必须执行）

> ✅ **黄金法则：先拉取，再修改，最后提交推送**
> 
> 这是避免代码冲突的唯一方法！

bash

运行

```
# 1. 开始工作前：拉取远程最新代码（最重要！）
git pull origin main

# 2. 查看当前修改状态（可选，建议养成习惯）
git status

# 3. 暂存所有修改的文件
git add .

# 4. 提交到本地仓库（双引号内写清楚修改内容）
git commit -m "清晰描述你做了什么修改"

# 5. 推送到 GitHub 远程仓库
git push origin main
```

> 💡 进阶：使用 `git pull --rebase origin main` 代替普通 pull，可以让提交历史更干净，避免产生多余的合并提交。

---

## 五、常见问题解决

### 问题 1：推送时提示 “远程有更新，推送被拒”

说明其他人或其他设备已经推送了新提交，需要先拉取合并：

bash

运行

```
# 拉取远程最新代码
git pull origin main

# 手动解决文件中的冲突（删除 <<<<<<< ======= >>>>>>> 标记，保留正确代码）
# 解决后重新提交
git add .
git commit -m "解决代码冲突"
git push origin main
```

### 问题 2：提示 "fatal: remote origin already exists"

说明本地已经关联过其他远程仓库，先删除旧的再重新关联：

bash

运行

```
git remote remove origin
git remote add origin git@github.com:你的用户名/仓库名.git
```

### 问题 3：本地和远程完全不同步，想强制覆盖本地

⚠️ 警告：此操作会丢失本地所有未提交的修改，请谨慎使用！

bash

运行

```
git fetch origin
git reset --
```