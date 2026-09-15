# 📋 Git 每日必用命令速查表(单人 / 多设备开发)

**只保留你每天真的会用到的命令**,按执行顺序排列,复制就能用.

---

## ✅ 核心流程(每天必走,严格按顺序)

> 🚨 **黄金法则:先拉取,再修改,最后提交推送**

bash

运行

```
# 1. 开始写代码前:同步远程最新代码(最重要!)
git pull origin main

# 2. 写完代码后:暂存所有修改
git add .

# 3. 提交到本地仓库(写清楚改了什么)
git commit -m "修改说明"

# 4. 推送到 GitHub(第一次加 -u,之后直接 git push 就行)
git push origin main
```

---

## 🔍 每日辅助命令(随时可用)

bash

运行

```
# 查看当前修改状态(哪些文件改了、哪些没提交)
git status

# 查看最近的提交记录(按 q 退出)
git log --oneline

# 检查远程有没有新提交(不自动合并,先看看)
git fetch origin

# 查看本地和远程的差异(有没有没拉下来的代码)
git log main..origin/main
```

---

## 🚨 每日常见错误快速修复

都是你之前遇到过的问题,复制对应命令解决:

### 1. 推送失败:提示 "远程有更新,推送被拒"

bash

运行

```
# 先拉取合并,再重新推送
git pull origin main
# 解决冲突后
git add .
git commit -m "解决冲突"
git push origin main
```

### 2. 拉取失败:提示 "有未提交的更改"

bash

运行

```
# 先暂存本地修改
git stash
# 再拉取
git pull origin main
# 恢复暂存的修改(如果需要)
git stash pop
```

### 3. 本地改乱了,想恢复到远程最新版

⚠️ 会丢失本地所有未提交的修改!

bash

运行

```
git fetch origin
git reset --hard origin/main
```

---

## 📌 一句话总结

每天打开电脑写代码:

**`git pull` → 写代码 → `git add .` → `git commit` → `git push`**