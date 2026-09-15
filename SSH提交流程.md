# SSH 协议完整提交流程（复制粘贴即用）
**全程无代理冲突**
---
## ✅ 第一步：先验证 SSH 连接（必做，先确认网络通）
bash
运行
```
# 万能测试命令，只要这条成功，所有提交都能成功
ssh -T git@github.com
```
✅ 成功输出：`Hi JiaJeang! You've successfully authenticated, but GitHub does not provide shell access.`

❌ 如果失败：重新配置 `C:\Users\1900\.ssh\config` 文件，确保有以下内容：
config
```
Host github.com
    HostName github.com
    User git
    ProxyCommand connect -S 127.0.0.1:7897 %h %p
```
---
## ✅ 第二步：配置远程仓库（只需要做一次）

### 情况 1：新仓库，还没添加过远程
bash
运行
```
# 添加 SSH 远程地址（永远用这个格式，不要用 HTTPS）
git remote add origin git@github.com:JiaJeang/你的仓库名.git
```
### 情况 2：已经有远程，想改成 SSH（最常用！）
bash
运行
```
# 修改现有远程地址为 SSH（不要用 add，会重复）
git remote set-url origin git@github.com:JiaJeang/你的仓库名.git
```
### 情况 3：有多个多余的远程仓库（清理干净）
bash
运行
```
# 查看所有远程仓库
git remote -v

# 删除所有多余的远程（只保留 origin）
git remote remove two
git remote remove 学习
git remote remove 其他名字
```
### 验证远程配置正确
bash
运行
```
git remote -v
```
✅ 必须只输出这两行，不能有其他任何远程：
plaintext
```
origin  git@github.com:JiaJeang/你的仓库名.git (fetch)
origin  git@github.com:JiaJeang/你的仓库名.git (push)
```
---
## ✅ 第三步：标准提交流程（每天必用）
bash
运行
```
# 1. 查看当前状态（可选，但建议养成习惯）
git status

# 2. 添加所有修改的文件
git add .

# 3. 提交到本地仓库（双引号里写清楚改了什么）
git commit -m "提交说明：比如完成flex布局练习"

# 4. 推送到 GitHub 远程仓库
git push origin main
```
---
## ✅ 第四步：解决最常见的两个错误
### 错误 1：`non-fast-forward`（远程有内容，本地没有）
bash
运行
```
# 安全合并（推荐，不会丢数据）
git pull origin main --allow-unrelated-histories
# 如果弹出 Vim 编辑器，直接输入 :wq 回车保存

# 然后再推送
git push origin main
```
### 错误 2：`remote origin already exists`
bash
运行
```
# 先删除旧的，再添加新的
git remote remove origin
git remote add origin git@github.com:JiaJeang/你的仓库名.git
```
---
## ⚡ 一键提交命令（懒人必备）
把这三条命令合并成一条，复制粘贴直接执行：
bash
运行
```
git add . && git commit -m "更新代码" && git push origin main
```
---
## 📌 快速参考表
表格

| 操作                  | 命令                                                                         |
| ------------------- | -------------------------------------------------------------------------- |
| 验证 SSH 连接           | `ssh -T git@github.com`                                                    |
| 添加 SSH 远程           | `git remote add origin git@github.com:JiaJeang/仓库名.git`                    |
| 修改为 SSH 远程          | `git remote set-url origin git@github.com:JiaJeang/仓库名.git`                |
| 查看远程配置              | `git remote -v`                                                            |
| 删除远程                | `git remote remove origin`                                                 |
| 标准提交                | `git add . && git commit -m "说明" && git push origin main`                  |
| 解决 non-fast-forward | `git pull origin main --allow-unrelated-histories && git push origin main` |

---
## 💡 永久记住

以后所有 Git 操作，**永远只使用 SSH 地址**，格式是：
`git@github.com:你的用户名/仓库名.git`
只要记住这个格式，并且每次提交前先执行 `ssh -T git@github.com` 验证连接，你就再也不会遇到任何连接错误了。