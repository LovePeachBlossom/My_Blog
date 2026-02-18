---
title: 搭个博客记录学习 | Hexo + Butterfly 部署到 Cloudflare
date: 2026-02-18 19:31:55
tags: [博客搭建, Hexo, Butterfly, Cloudflare]
categories: 博客日常
cover: /img/bg.jpg
---

## 为什么要搭博客

最近在学 Java 基础课程，Notion 里记了不少笔记，但总觉得缺点什么。想来想去，还是想有个自己的小窝，把学习过程记录下来，顺便练练手。

选了 Hexo + Butterfly 的组合，主要是看中 Butterfly 的颜值，卡片式设计很适合打造二次元风格的博客。而且 Hexo 写 Markdown 就能生成网页，对新手很友好。最关键的是，部署到 Cloudflare Pages 完全免费，还自带 CDN 加速和 HTTPS，简直完美。

<!-- more -->

## 本地搭建

我用的是 Windows + WSL2 环境，如果你也是这个配置可以直接参考。先确认一下有没有装 Node.js，终端里跑一下 `node --version` 看看。没有的话用 nvm 装一个就行。

**重要提示**：项目必须放在 Linux 文件系统里（比如 `~/projects/`），别放 Windows 盘符下（`/mnt/d/`）。我一开始放错了位置，VSCode 一直提示性能警告，后来搬到 `~/projects/` 速度快了好多。

```bash
mkdir -p ~/projects && cd ~/projects
npx hexo init my-blog && cd my-blog
```

等它跑完就有一个基础的 Hexo 博客了。接下来装 Butterfly 主题：

```bash
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

**容易踩的坑**：主题目录本身也有依赖，必须进去装一下：

```bash
cd themes/butterfly && npm install && cd ../..
```

我第一次就是忘了这步，页面一直报错 `Cannot find module 'hexo-util'`，折腾了半天才发现。装完之后编辑博客根目录的 `_config.yml`，把 `theme` 改成 `butterfly`。

本地预览：

```bash
npx hexo clean && npx hexo generate && npx hexo server
```

浏览器打开 `http://localhost:4000`，看到 Butterfly 主题就成功了。

## 推送到 GitHub

Cloudflare Pages 需要从 Git 仓库拉代码。在 GitHub 创建新仓库后：

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/你的用户名/my-blog.git
git push -u origin main
```

**注意**：如果 `themes/butterfly` 是符号链接，建议直接复制主题文件到项目里，避免 Git 推送问题。

## 部署到 Cloudflare Pages

去 [Cloudflare](https://dash.cloudflare.com/) 注册账号，进入 Pages → 创建项目 → 连接到 Git，选择刚才的仓库。

**构建配置**（框架预设选 Hexo）：
- 构建命令：`npx hexo generate`
- 构建输出目录：`public`
- Node 版本：18+

**常见构建失败原因**：
- `package.json` 缺少 `hexo-renderer-pug` 和 `hexo-renderer-stylus`
- 主题依赖未安装（需要在 `themes/butterfly` 目录执行 `npm install`）
- Node 版本不匹配（可创建 `.node-version` 文件指定版本）

构建成功后会得到 `.pages.dev` 域名，以后每次 push 都会自动部署。

## 主题定制

我给博客做了一些二次元风格的定制：

**字体**：使用霞鹜文楷（LXGW WenKai），手写风格更有动漫感。在 `_config.butterfly.yml` 配置：
```yaml
font:
  font_family: "'LXGW WenKai', 'Noto Sans SC', sans-serif"

blog_title_font:
  font_link: https://cdn.jsdelivr.net/npm/lxgw-wenkai-webfont@1.1.0/style.css
```

**彩色标签**：标签页的标签用 6 种颜色循环显示，支持亮色/暗色模式。创建 `source/css/custom-font-tags.css` 并在配置中注入。

**背景图**：首页使用本地背景图，亮色模式透明度 0.3，暗色模式 0.5。

**其他技巧**：
- 新建文章：`npx hexo new "标题"`
- 自定义域名：在 Cloudflare Pages 设置中添加
- 版本一致性：创建 `.node-version` 文件锁定 Node 版本

## 写在最后

博客搭建完成，主要用来记录 Java 学习笔记。目前已实现：
- ✅ 霞鹜文楷字体
- ✅ 彩色标签（6色循环）
- ✅ 本地背景图（亮/暗色不同透明度）
- ✅ 自动部署（push 即发布）

如果你也想搭建博客，记住两个关键点：项目放 Linux 文件系统，主题依赖别忘装。

---

**参考链接：**
- [Hexo 官方文档](https://hexo.io/zh-cn/docs/)
- [Butterfly 主题文档](https://butterfly.js.org/)
- [Cloudflare Pages 文档](https://developers.cloudflare.com/pages/)
