# Vue 项目部署到 Nginx 服务器的完整过程总结

## 🧱 项目环境

- 系统：Ubuntu（云服务器）
- 前端框架：Vue + Vite
- 部署端口：2222
- 项目路径：`/www/it-tools`
- Web 服务器：Nginx

---

## ✅ 部署目标

将本地构建好的 Vue 项目部署到 Ubuntu 服务器，通过 Nginx 监听 `http://<服务器IP>:2222` 对外访问。

---

## 🪜 部署完整步骤

### 1. 构建 Vue 项目

```bash
pnpm install
pnpm build
```

> 若出现 `NODE_OPTIONS` 报错，在 Windows 上可替换构建命令为：
>
> ```bash
> cross-env NODE_OPTIONS=--max_old_space_size=4096 vite build
> ```

如果提示 `cross-env` 未找到，先安装它：

```bash
pnpm add -D cross-env
```

### 2. 上传构建后的文件到服务器

将 `dist/` 中的文件拷贝到服务器 `/www/it-tools` 目录：

```bash
scp -r dist/* root@<服务器IP>:/www/it-tools/
```

或者在服务器上操作：

```bash
mkdir -p /www/it-tools
cp -r dist/* /www/it-tools/
```

确保该目录包含 `index.html`、`assets/` 等文件：

```bash
ls /www/it-tools
```

---

### 3. 配置 Nginx

#### 创建配置文件

路径：`/etc/nginx/sites-available/it_tools`

内容如下：

```nginx
server {
    listen 2222;
    server_name _;

    root /www/it-tools;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

#### 创建软链接启用配置

```bash
sudo ln -s /etc/nginx/sites-available/it_tools /etc/nginx/sites-enabled/it_tools
```

#### 检查配置并重启 Nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

### 4. 开放防火墙端口

如果你服务器启用了防火墙，记得开放 `2222` 端口：

#### Ubuntu（UFW）

```bash
sudo ufw allow 2222
```

#### CentOS（firewalld）

```bash
sudo firewall-cmd --add-port=2222/tcp --permanent
sudo firewall-cmd --reload
```

---

### 5. 验证部署是否成功

#### 本地 curl 测试：

```bash
curl http://localhost:2222
```

应该返回 HTML 内容。

#### 浏览器访问：

```
http://<你的服务器公网IP>:2222
```

应成功打开 Vue 页面。

---

## ❌ 遇到的问题与解决方案

| 问题描述 | 原因 | 解决方法 |
|----------|------|-----------|
| `curl: Failed to connect to localhost port 2222` | Nginx 未监听该端口 | 检查 Nginx 配置是否加载，执行 `sudo nginx -t`，确认配置文件正确，并已软链接到 `sites-enabled/` |
| `HTTP ERROR 503` | Nginx 启动了但无法提供内容 | 构建文件未部署、路径错误、权限错误或代理目标未启动 |
| `cross-env 不是内部命令` | Windows 环境未安装 cross-env | 执行 `pnpm add -D cross-env` 安装后再构建 |
| `NODE_OPTIONS 不是命令` | Windows cmd/bash 不支持 `NODE_OPTIONS=...` | 改用 `cross-env` |
| `nginx: open() "/etc/nginx/sites-enabled/it-tools"` failed | 配置文件名拼写错误，或软链接文件缺失 | 确保软链接文件名与实际一致，如：`it_tools` |

---

## ✅ 附加命令汇总

```bash
# 检查 Nginx 配置是否生效
sudo nginx -t

# 重启/重新加载 Nginx
sudo systemctl reload nginx
sudo systemctl restart nginx

# 查看是否监听了 2222 端口
sudo lsof -i:2222
sudo netstat -tnlp | grep 2222

# 本地 curl 测试服务
curl http://localhost:2222
```

---

## ✅ 结果

通过以上步骤，Vue 项目成功部署在服务器，通过如下地址访问：

```
http://<你的公网 IP>:2222
```

并支持前端路由，刷新页面不会报 404 错误。

---