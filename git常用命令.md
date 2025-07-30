# 📘 Git 常用命令速查表

## 📌 基础配置

```bash
git config --global user.name "你的用户名"     # 设置全局用户名
git config --global user.email "你的邮箱"     # 设置全局邮箱
git config --list                            # 查看当前配置
```

## 📂 仓库操作

```bash
git init                                     # 初始化本地仓库
git clone <仓库地址>                         # 克隆远程仓库
```

## 📄 文件操作

```bash
git status                                   # 查看文件状态
git add <文件名>                             # 添加文件到暂存区
git add .                                    # 添加所有文件到暂存区
git reset <文件名>                           # 取消暂存文件
git rm <文件名>                              # 删除文件
```

## 💾 提交更改

```bash
git commit -m "提交信息"                     # 提交更改
git commit -am "信息"                        # 跳过add直接提交（已跟踪文件）
```

## 🔁 分支操作

```bash
git branch                                   # 查看本地分支
git branch <分支名>                          # 创建分支
git checkout <分支名>                        # 切换分支
git checkout -b <分支名>                     # 创建并切换到新分支
git merge <分支名>                           # 合并分支
git branch -d <分支名>                       # 删除分支
```

## 🌐 远程操作

```bash
git remote add <仓库名> <仓库地址>             # 添加远程仓库
git remote -v                                # 查看远程地址
git remote rename 原名称 新名称               #远程仓库重命名
git push -u <仓库名> <分支名>                 # 第一次 push（并关联分支）
git push                                     # 推送本地代码
git pull                                     # 拉取远程代码并合并
git fetch                                    # 拉取远程代码（不合并）
```

## 📜 查看日志

```bash
git log                                      # 查看提交日志
git log --oneline                            # 简洁日志
git log --graph                              # 图形方式显示分支
git reflog                                   # 查看本地操作记录（含 push）
```

## 🔄 版本操作

```bash
git diff                                     # 查看工作区和暂存区差异
git diff <commitID>                          # 查看某次提交的差异
git checkout <commitID>                      # 回退到某次提交（暂时性）
git reset --hard <commitID>                  # 强制回退到某次提交
```

## 🌿 标签管理

```bash
git tag                                      # 查看所有标签
git tag <标签名>                             # 创建标签
git push origin <标签名>                     # 推送标签
git tag -d <标签名>                          # 删除本地标签
git push origin :refs/tags/<标签名>          # 删除远程标签
```

## 🧹 清理缓存

```bash
git clean -f                                 # 清除未追踪文件
git clean -fd                                # 连文件夹也删除
```

## 🔐 SSH 公钥配置（部署常用）

```bash
ssh-keygen -t ed25519 -C "你的邮箱"          # 生成公钥私钥（新版本）
ssh-keygen -t rsa -C "你的邮箱"              # 生成公钥私钥（旧版本）
cat ~/.ssh/id_ed25519.pub                   # 查看公钥内容，粘贴到平台（新版本）
cat ~/.ssh/id_rsa.pub                       # 查看公钥内容，粘贴到平台（旧版本）
```

## ✅ 推荐别名设置（提升效率）

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.st status
```