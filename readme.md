# Personal Website

这是 [Yaoyuan](https://yaoyuanArtemis.github.io/) 的个人主页与技术博客仓库，基于 [Hugo](https://gohugo.io/) 和 Eureka 主题构建，并通过 GitHub Actions 自动发布到 GitHub Pages。

站点主要用于展示个人简介、学习笔记、技术文章、项目文档和公开资料。目前内容重点包括 LLM、LLM Agent、模型微调、Node.js、V8、React、Hugo 等方向。

## 技术栈

- 静态站点生成器：Hugo
- 主题：`wangchucheng.com/hugo-eureka`
- 样式处理：Tailwind CSS、PostCSS、Autoprefixer
- 部署：GitHub Actions + GitHub Pages
- 线上地址：`https://yaoyuanArtemis.github.io/`

## 目录结构

```text
.
├── config/_default/          # Hugo 站点配置、菜单、主题参数、语言配置
├── content/
│   ├── homepage/             # 首页组件内容：About、Posts、Notes 等
│   ├── posts/                # 阶段性博客文章，当前预留
│   ├── notes/                # 长期维护的技术笔记/知识库
│   ├── Projects/             # 项目作品入口
│   ├── details/              # 个人详情页
│   └── authors/              # 作者信息
├── static/                   # 原样拷贝到站点根目录的静态资源
│   ├── images/               # favicon、resume.pdf 等
│   └── mypapaers/            # 论文 PDF 等公开资料
├── assets/                   # Hugo Pipes / PostCSS 处理的资源
├── archetypes/               # `hugo new` 使用的文章模板
├── public/                   # Hugo 构建产物，不需要手动维护
└── .github/workflows/        # GitHub Pages 自动部署流程
```

## 本地开发

建议使用 Hugo Extended 版本。当前 GitHub Actions 使用的是 `HUGO_VERSION=0.103.0`。

安装前端依赖：

```bash
npm install
```

启动本地预览：

```bash
hugo server -D
```

默认访问：

```text
http://localhost:1313/
```

生成静态文件：

```bash
hugo --minify
```

构建结果会输出到 `public/`。

## 内容维护

### 新增博客文章

`content/posts/` 预留给有时间属性的博客文章，例如阶段性总结、踩坑复盘、观点文章。长期维护的技术文档建议放在 `content/notes/`。

推荐使用 Hugo 命令创建文章：

```bash
hugo new --kind default posts/my-new-post/index.md
```

也可以直接在 `content/posts/` 下新增一个文章目录：

```text
content/posts/my-new-post/
├── index.md
└── images/
```

文章图片建议放在同一个文章目录下，Markdown 中用相对路径引用：

```markdown
![示例图片](images/example.png)
```

文章常用 front matter：

```yaml
---
title: "文章标题"
date: 2025-08-02T13:13:14+08:00
draft: false
description: 文章摘要
toc: true
authors:
  - example-author
tags:
  - LLM
  - 前端
categories:
  - 技术
---
```

`draft: true` 的文章只会在 `hugo server -D` 或带草稿参数构建时显示；发布文章时改成 `draft: false`。

### 新增技术笔记

长期维护的系统性技术文档放在 `content/notes/`。当前笔记按主题分组：

```text
content/notes/
├── llm/          # LLM、Agent、模型微调
├── frontend/     # React、V8、浏览器/前端相关
├── backend/      # Node.js、服务端相关
└── site/         # Hugo、博客维护相关
```

例如 LLM 相关笔记位于：

```text
content/notes/llm/
├── _index.md
├── overview/
├── agent/
└── fine-tuning/
```

推荐每篇笔记继续使用 Hugo leaf bundle 结构：

```text
content/notes/llm/my-note/
├── index.md
└── images/
```

### 修改首页

首页内容位于 `content/homepage/`：

- `about.md`：个人介绍、头像、社交链接、教育经历、工作经历、论文
- `pages.md`：首页展示的最新文章，目前设为 draft，等有真正博客文章时再启用
- `notes.md`：首页展示的技术笔记
- `vintage.md`：主题附带的视觉区块

### 修改导航

导航菜单在 `config/_default/menus.yaml` 中维护，目前包含：

- Details
- Notes
- Projects

### 修改站点信息

站点标题、域名、主题和基础构建配置在 `config/_default/config.yaml` 中维护。

主题参数、代码高亮、数学公式、Mermaid 图表等功能在 `config/_default/params.yaml` 中维护。

## 部署

仓库推送到 `main` 分支后，会触发 `.github/workflows/deploy.yml`：

1. 安装 Hugo Extended `0.103.0`
2. 执行 `hugo --minify`
3. 将 `public/` 发布到 `gh-pages` 分支
4. GitHub Pages 从 `gh-pages` 分支提供线上站点

也可以在 GitHub Actions 页面手动触发 `workflow_dispatch` 部署。

## 维护注意

- `public/`、`resources/`、`.hugo_build.lock` 是构建产物或临时文件，不需要提交。
- `.DS_Store` 是 macOS Finder 生成的本地元数据文件，不属于项目内容。
- 新文章目录建议使用英文小写和连字符，例如 `llm-agent`，这样生成的 URL 更稳定。
- `static/` 下的文件会按原路径发布，例如 `static/images/resume.pdf` 会变成 `/images/resume.pdf`。
