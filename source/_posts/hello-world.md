---
title: "Hexo + NexT 博客搭建指南：从零到 GitHub Pages 部署"
date: 2026-04-14 14:00:00
tags:
  - hexo
  - next
  - github-pages
  - 教程
categories:
  - 教程
---

<!-- TOC -->

## 简介

**Hexo** 是一个基于 Node.js 的快速、简洁且高效的静态博客框架，配合 **NexT** 主题和 **GitHub Pages** 免费托管，可以零成本搭建一个专业的个人博客。

本文将完整演示从零开始搭建博客的全流程：创建 Hexo 项目 → 安装配置 NexT 主题 → 编写发布文章 → 自动部署到 GitHub Pages。

## 环境要求

| 项目 | 要求 | 版本参考 |
|------|------|----------|
| Node.js | 18+ | 20 LTS |
| pnpm | 8+ | 9.x |
| Git | 2.x+ | 最新版 |
| GitHub 账号 | 需要 | — |
| 编辑器 | 任意 | VS Code 推荐 |

## 创建 Hexo 项目

### 安装 Hexo CLI

```bash
npm install -g hexo-cli
```

### 初始化项目

```bash
hexo init myblog
cd myblog
npm install
```

初始化完成后，项目结构如下：

```
myblog/
├── _config.yml          # 站点主配置
├── package.json         # 依赖与脚本
├── scaffolds/           # 文章模板
├── source/              # 源文件
│   ├── _posts/          # 博客文章（Markdown）
│   └── about/           # 关于页面
├── themes/              # 主题目录
└── public/              # 生成的静态文件（自动生成，勿手动修改）
```

### 本地预览

```bash
hexo server
```

浏览器访问 `http://localhost:4000`，可以看到默认的 Hexo 欢迎页面。

> `hexo server` 支持实时预览，修改 `source/` 下的文件后页面会自动刷新。

## 安装与配置 NexT 主题

NexT 是 Hexo 生态中最受欢迎的主题之一，简洁优雅，功能丰富。

### 安装 NexT

```bash
npm install hexo-theme-next
```

### 启用主题

在 `_config.yml` 中将 `theme` 字段改为 `next`：

```yaml
# _config.yml
theme: next
```

### 创建主题配置文件

在项目根目录创建 `_config.next.yml`，这是 NexT 8.x 推荐的**独立配置文件**方式，避免与主配置混淆。

```bash
touch _config.next.yml
```

### 基础配置示例

以下是一份推荐的 NexT 基础配置：

```yaml
# _config.next.yml

# 主题风格：Pisces（双栏布局）
scheme: Pisces

# 导航菜单
menu:
  home: / || fa fa-home
  about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive

# 侧边栏
sidebar:
  position: left
  display: post
  padding: 18
  offset: 12

# 代码块复制按钮
copycode:
  enable: true

# 回到顶部
back2top:
  enable: true
  scrollpercent: true
```

> NexT 提供 4 种 *Scheme*：Muse、Mist、Pisces、Gemini。**Pisces** 是最常用的双栏布局，推荐新手使用。

### 创建页面

NexT 默认不会自动生成标签页和分类页，需要手动创建：

```bash
hexo new page tags
hexo new page categories
hexo new page about
```

编辑生成的页面文件，添加 `type` 字段：

```yaml
# source/tags/index.md
---
title: 标签
type: tags
---
```

```yaml
# source/categories/index.md
---
title: 分类
type: categories
---
```

```yaml
# source/about/index.md
---
title: 关于
---
这里写关于你自己的介绍。
```

### 站点配置要点

`_config.yml` 中需要关注的关键配置：

```yaml
# _config.yml

# 站点信息
title: 你的博客名称
subtitle: ''
description: 博客描述
author: 你的名字
language: en            # NexT 主题用 en，即使写中文文章
timezone: ''

# URL 设置（GitHub Pages 部署关键）
url: https://username.github.io
permalink: :year/:month/:day/:title/

# 每页文章数
index_generator:
  per_page: 10
  order_by: -date
```

## 编写与发布文章

### 创建新文章

```bash
hexo new "My New Post"
```

这会在 `source/_posts/` 下生成 `My-New-Post.md`，文件内容包含 Frontmatter：

```yaml
---
title: My New Post
date: 2026-04-14 14:00:00
tags:
  - 标签1
  - 标签2
categories:
  - 分类名
---
```

### 文章写作规范

- 文章存放在 `source/_posts/` 目录下，格式为 Markdown
- Frontmatter 中 `title`、`date` 为必填字段
- 代码块需标注语言：```` ```bash ```、```` ```python ``` 等
- 图片支持相对路径和外部链接

### 常用 Hexo 命令

| 命令 | 说明 |
|------|------|
| `hexo new "标题"` | 创建新文章 |
| `hexo new page "页面名"` | 创建新页面 |
| `hexo server` | 启动本地预览服务器 |
| `hexo generate` | 生成静态文件到 `public/` |
| `hexo clean` | 清除缓存和生成文件 |
| `hexo deploy` | 部署到远程站点 |

## 部署到 GitHub Pages

### 创建 GitHub 仓库

1. 在 GitHub 上创建新仓库，命名为 `<username>.github.io`
2. 仓库保持 **Public**（GitHub Pages 免费版要求公开仓库）

### 初始化 Git 并推送

```bash
git init
git add .
git commit -m "init Hexo blog with NexT theme"
git remote add origin https://github.com/<username>/<username>.github.io.git
git branch -M main
git push -u origin main
```

### 配置 GitHub Actions 自动部署

在项目根目录创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Hexo to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup pnpm
        uses: pnpm/action-setup@v4
        with:
          version: 9

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm

      - name: Install dependencies
        run: pnpm install

      - name: Build Hexo
        run: pnpm run build

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 启用 GitHub Pages

1. 进入仓库 **Settings → Pages**
2. **Source** 选择 **GitHub Actions**
3. 推送代码后，Actions 会自动构建并部署

> 首次启用可能需要等待几分钟，部署成功后访问 `https://<username>.github.io` 即可看到博客。

### 日常发布流程

完成以上配置后，日常发布文章只需：

```bash
# 1. 创建文章
hexo new "文章标题"

# 2. 编辑文章
# 用编辑器打开 source/_posts/文章标题.md 进行写作
或者使用AI工具辅助创作

# 3. 本地预览（可选）
hexo server

# 4. 提交并推送
git add .
git commit -m "new post: 文章标题"
git push
```

推送后 GitHub Actions 会自动构建并部署，无需手动执行 `hexo generate` 或 `hexo deploy`。

## 推荐的 package.json 脚本

在 `package.json` 中配置常用命令的快捷方式：

```json
{
  "scripts": {
    "build": "hexo generate",
    "clean": "hexo clean",
    "deploy": "hexo deploy",
    "server": "hexo server"
  }
}
```

配置后可以使用 `pnpm run server` 代替 `hexo server`。

## 常见问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 页面样式丢失 | 主题未正确安装或配置文件路径错误 | 确认 `theme: next` 且 `_config.next.yml` 存在于根目录 |
| 文章列表不显示 | Frontmatter 格式错误 | 检查 YAML 格式，确保 `---` 分隔符完整 |
| GitHub Pages 404 | 仓库命名不正确或 Pages 未启用 | 仓库名必须是 `<username>.github.io`，检查 Pages Source 设置 |
| Actions 构建失败 | 依赖版本不兼容 | 检查 Actions 日志，确认 Node.js 和 pnpm 版本 |
| 本地预览正常但部署后异常 | 缓存问题 | 推送前执行 `hexo clean`，或在 Actions 中添加清理步骤 |
| 图片不显示 | 图片路径问题 | 使用 Hexo 的 post_asset_folder 功能或外部图床 |

## 总结

本文涵盖了 Hexo 博客搭建的完整流程：

1. **创建项目** — `hexo init` 初始化，`npm install` 安装依赖
2. **配置主题** — 安装 NexT 主题，创建 `_config.next.yml` 独立配置
3. **编写文章** — `hexo new` 创建文章，Markdown 格式写作
4. **自动部署** — GitHub Actions 推送即部署，零手动操作

整个搭建过程只需一次配置，之后专注于内容创作即可。`git push` 之后，剩下的交给自动化流程。
