# liucienm's blog

基于 [Astro](https://astro.build) + [Fuwari](https://github.com/saicaca/fuwari) 模板的静态博客，部署在 GitHub Pages。

> 模板上游文档（i18n / 自定义 / Markdown 扩展语法）：<https://github.com/saicaca/fuwari>

## 环境

- Node.js >= 20
- pnpm >= 9（`npm i -g pnpm`）

## 常用命令

```bash
pnpm install        # 安装依赖
pnpm dev            # 本地开发，http://localhost:4321
pnpm build          # 构建到 dist/（含 Pagefind 搜索索引）
pnpm preview        # 预览构建结果
pnpm new-post 我的新文章   # 新建文章：src/content/posts/我的新文章/index.md
```

## 目录速查

| 路径 | 作用 |
| --- | --- |
| `src/config.ts` | **站点配置**：标题、副标题、语言、导航、头像、社交链接、目录深度 |
| `src/content/posts/<slug>/index.md` | 文章正文，同目录可放文章配图 |
| `src/content/spec/about.md` | 关于页 |
| `astro.config.mjs` | 部署相关：`site` 与 `base`（子路径部署时改这里） |
| `.github/workflows/deploy.yml` | push 到 `main` 后自动构建并发布到 GitHub Pages |

## 写一篇文章

```markdown
---
title: 标题
published: 2026-05-11T16:45:27+08:00
description: 列表页和 SEO 用的摘要
image: ./cover.jpg        # 可选，封面图放同目录
category: 随笔
tags: [Thought]
draft: false
---
```

- `published` 用 ISO 格式并带时区（`+08:00`），不要写成 `2026-5-11`。
- 正文中的小标题建议用 `##` / `###`，TOC 只收集 1–3 级标题。

## 部署

1. GitHub 仓库 Settings → Pages → Source 选择 **GitHub Actions**。
2. push 到 `main` 即自动构建上线。
3. 部署到 `https://liucienm.github.io/` 根路径时，把 `astro.config.mjs` 里的 `base` 改成 `"/"`；
   部署到项目子路径（如 `https://liucienm.github.io/blog-v2/`）时用 `base: "/blog-v2/"`。

## 备注

- 已修补模板 `src/styles/markdown.css` 里两处跨文件 `@apply`（`@apply link`、`@apply btn-regular-dark`），
  它们会导致构建随机失败；现已内联为等价工具类。
- 站内搜索由 Pagefind 在构建后生成，无需外部服务。
