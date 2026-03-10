---
layout: post
title: 欢迎来到我的博客！
date: 2024-03-10
categories: [General]
---

👋 你好！这是我的第一个帖子。

这是一使用 **Jekyll** 和 **GitHub Pages** 搭建的极简风格个人博客。特点如下：

## ✨ 为什么选择这个方案？

1. **极简美观** - 干净的布局，专注内容本身
2. **超快加载** - 静态页面秒开体验
3. **完全免费** - GitHub Pages 永久免费托管
4. **易于维护** - Markdown 写作，Git 管理版本
5. **SEO 友好** - Google 收录效果好
6. **自动部署** - Git Push 即自动更新网站

## 📝 关于内容

这里将记录我的：
- 💡 技术思考与学习笔记
- 🔧 项目经验总结
- 📚 阅读心得分享
- 🎨 设计与 UX 洞察

## 🚀 快速上手指南

### 本地开发
```bash
# 1. 克隆仓库
git clone https://github.com/nobugpal/blog.git

cd blog

# 2. 安装依赖（需要 Ruby）
bundle install

# 3. 启动本地服务器
bundle exec jekyll serve

# 4. 访问 http://localhost:4000
```

### 添加文章
在 `_posts/` 目录下创建新文件：
```bash
hugo new posts/my-first-post.md
# 或使用 Jekyll 方式
mkdir -p _posts/2024-03-10-my-post/
touch _posts/2024-03-10-my-post/index.md
```

### 发布文章
```bash
# 编辑内容后提交到 GitHub
git add .
git commit -m "Add new post: My First Post"
git push origin main

# GitHub Pages 会自动构建并发布！
```

## 📖 Markdown 格式示例

**粗体文字**: `**文本**`
*斜体文字*: `*文本*`
`代码块`: `` `inline code` ``

- 列表项 1
- 列表项 2
- 列表项 3

[链接](https://example.com)

## 🎨 个性化配置

修改 `_config.yml` 文件即可自定义网站信息：

```yaml
title: "你的博客标题"
description: "你的博客描述"
url: "https://yourusername.github.io"  # 域名或用户名
author:
  name: 你的名字
```

## 🚀 下一步

1. ✅ 创建更多文章
2. ✅ 添加关于页面
3. ✅ 设置分类和标签
4. ✅ 自定义主题样式
5. ✅ 绑定自定义域名（可选）

---

> 感谢访问！欢迎随时交流，有任何建议请在 GitHub 提交 Issue。

🌟 **如果你喜欢这个项目，别忘了给仓库点个 Star！**