# Aithos

Web 多智能体对话应用（WebMultiAgentChat）

- 在线体验: https://aithos.pages.dev
- GitHub: https://github.com/excing/WebMultiAgentChat

## 简介
Aithos 是一个轻量、纯前端的多智能体对话应用，专注于流畅的聊天体验与易部署性，适合快速演示与实验多智能体交互场景。

## 功能特性
- 多智能体对话：在同一会话内组织多角色协作与交流
- 消息置底与自动滚动：根据用户位置智能滚动，附带一键返回底部按钮
- 现代化 UI：优化的模态头/尾与侧边栏布局，良好响应式表现
- 纯静态部署：无需后端，开箱即用，易托管到 Cloudflare Pages/GitHub Pages

## 快速开始
无需构建，克隆后即可本地预览：

方式一：直接打开
1. 克隆仓库并进入目录
2. 用浏览器直接打开 `index.html`

方式二：本地静态服务器
- Python: `python3 -m http.server 8080`
- 然后访问: http://localhost:8080

## 项目结构
```
WebMultiAgentChat/
├─ index.html                # 主页面（应用入口）
└─ __index_backup__.html     # 备份页面
```

## 部署（Cloudflare Pages）
1. 在 Cloudflare Pages 新建项目并连接本 GitHub 仓库
2. 框架预设：None（无）
3. 构建命令：无
4. 输出目录：项目根目录（/）
5. 绑定自定义域名（可选）

> 也可使用 GitHub Pages 或任意静态托管，将仓库根目录作为站点根目录即可。

## 贡献
欢迎 Issue 与 PR。提交前请确保基本可用性与样式一致性。
