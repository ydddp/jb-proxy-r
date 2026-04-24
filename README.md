# JB-Proxy

基于 JetBrains AI 的 API 网关，提供 OpenAI / Anthropic / Responses 兼容接口，附带 Web 管理面板。

反代内核参考自 [jb-ai-proxy](https://github.com/Kazuki-0147/jb-ai-proxy) by [@Kazuki-0147](https://github.com/Kazuki-0147)，在此基础上增加了多账号管理、Web 管理面板、使用统计等功能。

[English](#english)

## 功能

- **标准接口**：兼容 OpenAI (`/v1/chat/completions`)、Anthropic (`/v1/messages`)、Responses (`/v1/responses`)
- **凭据管理**：Web 面板统一管理 JetBrains AI 凭据
- **使用分析**：Token 用量统计、缓存命中分析、趋势图表
- **客户端密钥**：为不同客户端分配独立 API Key，独立统计用量
- **运行日志**：面板内查看实时日志

## 快速部署

**前置条件**：已安装 [Docker](https://docs.docker.com/get-docker/)

```bash
# 1. 下载部署文件
curl -O https://raw.githubusercontent.com/ydddp/jb-proxy-r/main/docker-compose.yml

# 2. 设置管理面板密码
echo "ADMIN_PASSWORD=your_password" > .env

# 3. 启动
docker compose up -d
```

访问 `http://<你的服务器IP>` 进入管理面板。

## 环境变量

| 变量             | 必填 | 说明                           |
| ---------------- | ---- | ------------------------------ |
| `ADMIN_PASSWORD` | 推荐 | 管理面板登录密码               |
| `SESSION_SECRET` | 否   | 会话签名密钥，未设置则自动生成 |
| `HTTP_PORT`      | 否   | 对外端口，默认 `80`            |

## 客户端配置

在管理面板创建 API Key 后：

- Base URL：`http://<你的服务器IP>/v1`
- API Key：面板生成的密钥

兼容任何支持 OpenAI / Anthropic 接口的客户端（Cherry Studio、Cursor、Open WebUI 等）。

## 从旧版本迁移（源码部署 → 镜像部署）

**如果你在原来的 `JetBrains-Proxy/` 目录操作**，数据会自动保留，直接执行：

```bash
docker compose down
curl -O https://raw.githubusercontent.com/ydddp/jb-proxy-r/main/docker-compose.yml
docker compose up -d
```

**如果你换了目录**，需要先导出旧数据再导入：

```bash
# 1. 停止旧服务（在旧目录执行）
docker compose down

# 2. 备份旧 volume（volume 名称通常为 <旧目录名小写>_proxy-data）
docker run --rm \
  -v jetbrains-proxy_proxy-data:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/proxy-data.tar.gz -C /data .

# 3. 在新目录启动一次（会自动创建新 volume）
docker compose up -d
docker compose down

# 4. 将数据导入新 volume
docker run --rm \
  -v <新目录名>_proxy-data:/data \
  -v $(pwd):/backup \
  alpine tar xzf /backup/proxy-data.tar.gz -C /data

# 5. 启动
docker compose up -d
```

## 更新

```bash
docker compose pull && docker compose up -d
```

## 数据备份

运行数据位于 Docker volume，备份命令：

```bash
docker run --rm \
  -v $(basename $(pwd) | tr '[:upper:]' '[:lower:]')_proxy-data:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/proxy-data-$(date +%F).tar.gz -C /data .
```

---

## English

A JetBrains AI API gateway with OpenAI / Anthropic / Responses compatible endpoints and a web admin panel.

The proxy core is based on [jb-ai-proxy](https://github.com/Kazuki-0147/jb-ai-proxy) by [@Kazuki-0147](https://github.com/Kazuki-0147), extended with multi-account management, web admin panel, and usage analytics.

### Quick start

**Requires**: [Docker](https://docs.docker.com/get-docker/)

```bash
# 1. Download compose file
curl -O https://raw.githubusercontent.com/ydddp/jb-proxy-r/main/docker-compose.yml

# 2. Set admin password
echo "ADMIN_PASSWORD=your_password" > .env

# 3. Start
docker compose up -d
```

Visit `http://<your-server-ip>` to access the admin panel.

### Environment variables

| Variable         | Required    | Description                                       |
| ---------------- | ----------- | ------------------------------------------------- |
| `ADMIN_PASSWORD` | Recommended | Admin panel login password                        |
| `SESSION_SECRET` | No          | Session signing secret; auto-generated if not set |
| `HTTP_PORT`      | No          | Public web port, default `80`                     |

### Client configuration

After creating an API key in the admin panel:

- Base URL: `http://<your-server-ip>/v1`
- API Key: generated in the admin panel

### Update

```bash
docker compose pull && docker compose up -d
```

---

> **免责声明**：本项目仅供学习与技术交流，请勿用于任何商业或违反相关服务条款的用途。使用本项目所产生的一切后果由使用者自行承担，作者不承担任何法律责任。
>
> **Disclaimer**: This project is intended for learning and technical exchange purposes only. Do not use it for any commercial purposes or in violation of any applicable terms of service. The author assumes no legal responsibility for any consequences arising from the use of this project.
