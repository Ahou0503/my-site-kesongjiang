# 戴佳峰 · 个人主页

一个集成了大模型数字分身的个人主页。访客可以浏览个人信息和作品，并与基于 DeepSeek API 的数字分身实时对话。

## 项目架构

```
my_site/
├── index.html                    # 前端页面（HTML + CSS + JavaScript）
├── soul.md                       # 数字分身的人设定义（唯一来源）
├── .env                          # 环境变量（DEEPSEEK_API_KEY）
├── package.json                  # 构建脚本配置
├── scripts/
│   └── sync-prompt.js            # 构建脚本：soul.md → functions/api/prompt.js
├── functions/
│   └── api/
│       ├── chat.js               # Cloudflare Pages Functions 后端
│       └── prompt.js             # 由 sync-prompt.js 自动生成，不手动修改
└── cartoon/
    └── Standing.glb              # 3D 虚拟形象模型（点击可挥手）
```

- **index.html**：整站只有一个页面，包含 3D 虚拟形象（model-viewer）、自我介绍、作品展示、聊天区、联系方式。样式和逻辑都在此文件内。
- **functions/api/chat.js**：Cloudflare Pages Functions 服务端，提供 `/api/chat` 接口，将前端的聊天请求转发到 DeepSeek API，以流式（SSE）返回结果。API Key 通过环境变量注入，不暴露给前端。
- **soul.md**：**唯一的人设来源**。修改人设后运行 `npm run sync-prompt` 同步到 Cloudflare Functions。
- **scripts/sync-prompt.js**：构建脚本，读取 `soul.md` 并生成 `functions/api/prompt.js`。
- **cartoon/Standing.glb**：3D 角色模型，点击头像切换为挥手动画，1 秒后自动恢复站立。

## 部署到 Cloudflare Pages

### 1. 推送代码到 GitHub

```bash
git add .
git commit -m "迁移至 Cloudflare Pages Functions"
git push
```

### 2. 在 Cloudflare 仪表板配置

1. 进入 **Cloudflare Dashboard** → **Workers & Pages** → **Pages** → **创建项目**
2. 关联你的 GitHub 仓库，构建设置：
   - **构建命令**：`npm run build`
   - **输出目录**：`/`
3. 在 **环境变量** 中添加：
   - `DEEPSEEK_API_KEY` = 你的 DeepSeek API 密钥

### 3. 本地开发（可选）

```bash
# 安装依赖
npm install

# 启动本地开发（自动同步 soul.md 并启动 wrangler）
npm run dev
```

启动后可访问 `http://localhost:8788` 查看效果。

> **修改人设后**：只需编辑 `soul.md`，运行 `npm run dev` 会自动同步。如果只单独同步，可以运行 `npm run sync-prompt`。
