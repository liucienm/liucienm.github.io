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

- 正式站点：<https://liucienm.github.io/>，仓库 `liucienm/liucienm.github.io`，`main` 分支是**源码**（不是构建产物）。
- `astro.config.mjs` 里 `site: "https://liucienm.github.io"`、`base: "/"`（部署到子路径时才需要改 `base`）。
- GitHub 仓库 Settings → Pages → Source 选 **GitHub Actions**；push 到 `main` 即由 `.github/workflows/deploy.yml` 构建并发布。
- 旧版本存档（只读，不再维护）：
  - `legacy-hexo`：迁移前的 Hexo 生成产物（12 次 "Site updated" 提交）
  - `legacy-hexo-source`：迁移前的 Hexo 源码（tag `hexo-final`）

## 背景图（banner）

- 图片：`src/assets/images/banner.jpg`，构建时自动转 WebP。
- 开关 / 对齐方式 / 署名：`src/config.ts` 的 `siteConfig.banner`。
- **首页：背景图固定在视口内，内容从其上方滑过**。
  主题默认 banner 是 `position: absolute`，会跟着文档滚走（下滑后只剩页面底色）；
  这里改成 `position: fixed` 铺满整个视口（`100dvh`），`object-fit: cover`，
  并叠一层自上而下的暗色渐变，保证滑到页脚时文字仍可读。
  内页（文章/归档/关于）仍用主题默认的 35vh 背景带。
  相关规则集中在 `src/layouts/Layout.astro` 末尾的 `.banner-natural.enable-banner …`。
  注意：`cover` 会按视口比例裁切（16:9 屏幕几乎不裁；竖屏或超高窗口会裁掉两侧/上下更多）。
- **内页（文章/归档/关于）仍是主题默认的 35vh 背景带**（`object-cover` 按需裁剪），避免正文被顶得太低。
  想让内页也按原比例：把规则里的 `.banner-natural.enable-banner` 改成 `.enable-banner`。
- 其它效果均为主题自带：下滑隐藏导航栏、页面切换动画（swup）。

## 性能与 SEO

- **字体**：只引 Roboto 的 latin 子集（`@fontsource/roboto/latin-{400,500,700}.css`），JetBrains Mono 去掉斜体变体。
  中文正文走系统字体栈（苹方/微软雅黑），不引中文 Web 字体——单字重就要 1MB+，不划算。
  KaTeX 的公式字体（约 1MB）仍在产物里，但只有页面真的渲染公式时浏览器才会下载。
- **favicon**：`public/favicon/` 下的 `favicon.ico` + `icon-{32,128,180,192}.png`，由 `siteConfig.favicon` 数组声明；
  `icon-180.png` 同时作为 iOS 的 apple-touch-icon。
- **社交分享图**：`public/og-default.jpg`，**1200×1200 方图**，所有文字/标志压在画面中央横带（y≈300–900）内。
  原因：微信把链接缩略图中心裁成 1:1、Telegram/Twitter 裁成 1.91:1，方图 + 中心安全区能同时满足两者；
  图片本身做了轻微模糊和压暗，提高微信二次压缩后的可读性。
  需要按页面覆盖时，给 `Layout`（或 `MainGridLayout`）传 `ogImage` 属性。生成脚本思路见提交记录。
- **canonical**：每页输出 `<link rel="canonical">`，取 `Astro.site + pathname`，自动去掉查询参数。
- 站点地图 `/sitemap-index.xml` 与 `/rss.xml` 由构建生成，提交一次到 Google Search Console / Bing 即可。

## 备注

- 已修补模板 `src/styles/markdown.css` 里两处跨文件 `@apply`（`@apply link`、`@apply btn-regular-dark`），
  它们会导致构建随机失败；现已内联为等价工具类。
- **不要把本目录随手挪动**：pnpm 在 Windows 上用 junction（绝对路径）链接依赖，移动目录后 `node_modules` 会失效，
  需要删掉 `node_modules` 重新 `pnpm install`。
- 站内搜索由 Pagefind 在构建后生成，无需外部服务。
