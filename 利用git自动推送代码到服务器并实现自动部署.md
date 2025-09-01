# 利用 Git 推送代码到服务器并实现自动部署

本教程将完整讲解如何将本地项目推送到服务器上的 Git 仓库，并实现自动部署（自动将代码同步到工作目录）。

---

## 一、准备工作

### 1. 安装 Git

#### macOS（终端输入）：

```bash
brew install git  # 使用 Homebrew 安装 Git
```

#### Ubuntu 服务器（root权限）：

```bash
sudo apt update  # 更新软件源
sudo apt install git  # 安装 Git
```

---

## 二、在服务器上初始化 Git 裸仓库

```bash
cd /www  # 切换到部署目录
mkdir weChat.git  # 创建裸仓库目录
cd weChat.git  # 进入仓库目录
git init --bare  # 初始化裸仓库，不带工作区
```

---

## 三、配置本地 Git SSH 连接

### 1. 生成本地 SSH 密钥

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"  # 生成 SSH 密钥对，使用 ed25519 算法
# 按提示操作，默认保存在 ~/.ssh/id_ed25519
# 查看公钥
cat ~/.ssh/id_ed25519.pub
```

### 2. 将公钥上传到服务器

```bash
ssh-copy-id root@43.159.41.42  # 自动将公钥添加到服务器 authorized_keys 中
```

或手动将 `~/.ssh/id_ed25519.pub` 内容追加到服务器 `/root/.ssh/authorized_keys` 文件中。

---

## 四、将远程仓库添加为远程源

在本地项目根目录下：

```bash
git remote add server ssh://root@43.159.41.42:/www/weChat.git  # 添加远程服务器为名为 server 的远程仓库
```

---

## 五、推送代码到服务器

```bash
git push server master  # 将本地 master 分支推送到远程 server 仓库
```

---

## 六、在服务器设置自动部署

### 1. 切换到裸仓库 hooks 目录

```bash
cd /www/weChat.git/hooks  # 进入 hooks 脚本目录
```

### 2. 新建 `post-receive` 脚本

```bash
vim post-receive  # 创建并编辑部署脚本
```

### 3. 添加如下内容（自动将代码部署到工作区）

```bash
#!/bin/bash
GIT_WORK_TREE=/www/weChat git checkout -f master  # 将仓库内容强制更新到 /www/weChat
```

> 注意：`/www/weChat` 是实际代码部署目录，需手动创建一次。

### 4. 授权可执行

```bash
chmod +x post-receive  # 设置 post-receive 脚本为可执行
```

---

## 七、第一次手动部署（可选）

若已推送代码，但还未触发 `post-receive`，可手动部署：

```bash
GIT_WORK_TREE=/www/weChat git --git-dir=/www/weChat.git checkout -f master  # 手动将代码部署到工作目录
```

---

## 八、Nginx 配置示例（部署网页/接口）

```nginx
server {
    listen 80;  # 监听80端口
    server_name your.domain.com;  # 配置域名

    location / {
        root /www/weChat;  # 网页静态文件目录
        index index.html index.htm;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:端口号;  # 转发接口请求到本地服务
        proxy_http_version 1.1;
        proxy_set_header Host $host;
    }
}
```

---

## 九、常用命令速查

| 操作          | 命令                              |
| ------------- | --------------------------------- |
| 添加远程仓库  | `git remote add server ssh://...` |
| 推送到服务器  | `git push server master`          |
| 初始化裸仓库  | `git init --bare`                 |
| 自动部署脚本  | `post-receive`                    |
| 添加 SSH 公钥 | `ssh-copy-id user@ip`             |

---

## 十、可能遇到的问题

- `Permission denied`：SSH 公钥未配置成功
- `fatal: not a git repository`：服务器端路径配置错误
- `502 Bad Gateway`：Nginx 未启动、端口未监听、服务未部署或报错
- `post-receive` 没反应：脚本未赋权或路径错误

---

## ✅ 结语

通过以上步骤，你已经完成了：

- 本地生成 SSH 密钥并绑定
- 建立远程 Git 仓库
- 实现自动部署代码到生产环境

如需添加自动重启服务、拉依赖、构建项目等操作，可在 `post-receive` 中扩展。