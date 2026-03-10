# 📝 我的博客

> A clean, minimalist Jekyll blog powered by GitHub Pages.

一个极简风格的个人技术博客，基于 **Jekyll** 和 **GitHub Pages** 构建。

---

## ✨ 特性

- ⚡️ **极速加载** - 静态页面毫秒级响应
- 🎨 **简约设计** - 专注内容，无多余干扰
- 🔧 **易于维护** - Markdown 写作，Git 版本控制
- 🆓 **永久免费** - GitHub Pages 托管零成本
- 📱 **响应式设计** - 完美支持移动设备
- 🔍 **SEO 优化** - Google 收录友好

---

## 🚀 快速开始

### 本地运行

```bash
# 1. 克隆项目
git clone https://github.com/nobugpal/blog.git
cd blog

# 2. 安装依赖
bundle install

# 3. 启动开发服务器
bundle exec jekyll serve --livereload

# 4. 访问 http://localhost:4000
```

### 添加新文章

```bash
# 在 posts 目录下创建 markdown 文件
touch _posts/2024-03-15-your-post-title.md

# 编辑文章内容（YAML frontmatter 已预置）
nano _posts/2024-03-15-your-post-title.md

# 提交到 GitHub，自动部署！
git add .
git commit -m "Add new post"
git push origin main
```

---

## 📂 项目结构

```
blog/
├── _config.yml           # Jekyll 配置文件
├── Gemfile               # Ruby 依赖文件
├── index.html            # 首页布局
├── .gitignore            # Git 忽略配置
├── README.md             # 项目说明文档
├── _layouts/             # 页面模板
│   ├── default.html      # 基础布局
│   └── home.html         # 首页布局
├── assets/               # 静态资源（CSS、JS）
├── _posts/               # 博客文章目录
│   └── YYYY-MM-DD-title.md  # 文章文件
└── .github/
    └── workflows/        # GitHub Actions 工作流（可选）
```

---

## 🎯 核心优势

### Why Jekyll + GitHub Pages?

| 特性 | Jekyll on GitHub Pages |
|------|-------|
| **成本** | 💰 免费托管 |
| **维护** | 🔧 零运维负担 |
| **速度** | ⚡️ CDN 加速全球访问 |
| **安全** | 🛡️ GitHub 基础设施保障 |
| **版本** | 📝 Git 自动记录所有变更 |
| **CI/CD** | 🔁 推送即部署（GitHub Actions） |

### 技术栈

- **Jekyll** - Ruby 静态站点生成器
- **Minima Theme** - GitHub 官方极简主题
- **Markdown** - 轻量级标记语言
- **Noto Sans SC** - Google Fonts 中文字体
- **GitHub Pages** - 免费托管平台

---

## 📝 文章写作规范

### Frontmatter 格式

```yaml
---
layout: post
title: 文章标题
date: 2024-03-10T08:00:00+08:00
categories: [General, Technology]
tags: [jekyll, github-pages, blog]
---
```

### 文件命名

```bash
# 格式：YYYY-MM-DD-title.md
2024-03-10-welcome-to-my-blog.md
2024-03-15-jekyll-tutorial.md
2024-03-20-github-pages-setup.md
```

---

## 🎨 自定义主题

### 修改配色

编辑 `_layouts/default.html` 中的 CSS，例如：

```css
body {
  color: #24292e;
  background-color: #fafbfc;
}

a {
  color: #0366d6;
}
```

### 修改布局

在 `_layouts/` 目录下创建新的模板文件，自定义页面结构。

### 添加自定义样式

在 `assets/` 目录下创建 CSS 文件并导入：

```html
<!-- 在 layout 中引入 -->
<link rel="stylesheet" href="/assets/custom.css">
```

---

## 📚 资源推荐

### Jekyll 官方文档
- [Jekyll Docs](https://jekyllrb.com/)
- [GitHub Pages Guide](https://docs.github.com/en/pages)
- [Minima Theme](https://github.com/jekyll/minima)

### Markdown 指南
- [Markdown Tutorial](https://www.markdownguide.org/)
- [CommonMark Spec](https://commonmark.org/)

### 开发工具
- **VS Code** - 推荐编辑，支持 Markdown Preview
- **GitKraken** - Git GUI 客户端
- **Jekyll Desktop** - Jekyll 图形化运行工具

---

## 🔄 GitHub Actions 自动部署

（可选）创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.0'
          bundler-cache: true
      - run: bundle exec jekyll build
      - uses: actions/upload-pages-artifact@v2
      - uses: actions/deploy-pages@v2
```

---

## 🚀 部署到 GitHub Pages

### 方式一：User Site（推荐）

1. 创建仓库命名为：`yourusername.github.io`
2. 将项目代码推送到该仓库
3. GitHub Pages 会自动构建并发布！

### 方式二：Project Site

1. 在任何仓库中添加此项目
2. 进入 Settings → Pages
3. Source: Deploy from a branch
4. Branch: main, Folder: / (root)
5. URL: `https://yourusername.github.io/your-repo`

---

## 🎯 性能优化建议

1. **图片压缩** - 使用 TinyPNG 等工具
2. **懒加载** - 为长篇文章添加图片懒加载
3. **CDN 加速** - GitHub Pages 自带 CDN，无需额外配置
4. **减少请求** - 合并 CSS/JS 文件
5. **PWA** - 可转换为渐进式 Web App

---

## 📊 SEO 优化清单

- ✅ 语义化 HTML 标签
- ✅ meta description 描述完整
- ✅ robots.txt 配置文件自动创建
- ✅ sitemap.xml 自动生成（通过 jekyll-sitemap）
- ✅ Open Graph 协议支持（jekyll-seo-tag）
- ✅ Google Search Console 验证

---

## 🛠️ 常见问题 FAQ

### Q: 如何修改网站标题和描述？
A: 编辑 `_config.yml`，修改 `title` 和 `description` 字段。

### Q: 如何实现夜间模式？
A: CSS 中添加媒体查询：
```css
@media (prefers-color-scheme: dark) {
  body { background: #1e2329; color: #f6f8fa; }
}
```

### Q: 如何添加联系表单？
A: 使用 Formspree 等第三方服务，或使用 GitHub Issues。

### Q: 支持多语言吗？
A: 可以手动创建不同语言版本的页面。

---

## 📈 Analytics

推荐工具：
- **Google Analytics** - 标准分析工具
- **Plausible** - 隐私友好型统计
- **Umami** - 开源统计方案

配置方式：在 `_layouts/default.html` 添加跟踪代码。

---

## 🎉 结语

这是一个经过精心设计的极简博客模板，适合技术开发者、设计师、写作者等多种人群使用。通过 Jekyll + GitHub Pages 的组合，你可以专注于内容创作，而不必担心服务器维护和技术复杂性。

**开始你的写作之旅吧！✍️**

---

## 📝 License

MIT License - 自由使用和修改。

---

<p align="center">
Made with ❤️ by <a href="https://github.com/nobugpal">nobugpal</a> | Powered by <a href="https://jekyllrb.com">Jekyll</a>
</p>