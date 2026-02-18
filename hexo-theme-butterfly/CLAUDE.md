# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

Hexo-theme-butterfly 是一个现代化的 Hexo 主题，采用卡片式设计。本项目是主题包，安装在 Hexo 博客的 `themes/` 目录下。

**官方文档**:
- 中文：https://butterfly.js.org/
- 英文：https://butterfly.js.org/en/
- 快速开始：https://butterfly.js.org/en/posts/butterfly-docs-en-get-started/
- Hexo 官方：https://hexo.io/docs/

**安装方式**（推荐 Git）：
```bash
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

**配置管理**：在博客根目录创建 `_config.butterfly.yml`，Hexo 会自动合并配置，自定义配置优先级更高。

## 核心架构

### 目录结构
```
├── _config.yml          # 主题配置文件
├── plugins.yml          # 外部库 CDN 定义
├── layout/              # Pug 模板（需要 hexo-renderer-pug）
│   ├── *.pug           # 页面模板：index/post/page/archive/category/tag
│   └── includes/       # 可复用组件：header/footer/sidebar 等
├── scripts/            # Hexo 插件（自动加载）
│   ├── helpers/        # 模板函数：related_post/series/page
│   ├── filters/        # 内容处理：post_lazyload/random_cover
│   ├── tag/            # 自定义标签：note/tabs/gallery/mermaid
│   └── events/         # 生命周期钩子
├── source/             # 静态资源
│   ├── css/           # Stylus 样式（需要 hexo-renderer-stylus）
│   └── js/            # JavaScript
└── languages/          # 国际化翻译
```

### 关键机制

**模板渲染流程**: Pug 模板 → Hexo 生成 → HTML
**样式编译**: `source/css/index.styl` → Hexo 编译 → CSS
**脚本加载**: `scripts/` 下所有 `.js` 自动注册到 Hexo
**配置优先级**: 用户博客的 `_config.butterfly.yml` > 主题 `_config.yml`

## 主题配置

### 配置文件位置

推荐在博客根目录创建 `_config.butterfly.yml` 覆盖主题配置，而不是直接修改主题目录的 `_config.yml`。这样便于主题更新和版本控制。

```bash
# 复制主题配置到博客根目录
cp themes/butterfly/_config.yml _config.butterfly.yml
```

### 二次元风格配置示例

**主题颜色**（粉色系）：
```yaml
theme_color:
  enable: true
  main: "#FF69B4"           # 主色调
  paginator: "#FF1493"      # 分页器
  button_hover: "#FFB6C1"   # 按钮悬停
  link_color: "#FF69B4"     # 链接颜色
  scrollbar_color: "#FF69B4" # 滚动条
```

**背景渐变**：
```yaml
background:
  - "linear-gradient(to right, #ffecd2 0%, #fcb69f 100%)"
  - "linear-gradient(120deg, #fccb90 0%, #d57eeb 100%)"
  - "linear-gradient(to top, #fbc2eb 0%, #a6c1ee 100%)"
```

**导航菜单**：
```yaml
menu:
  首页: / || fas fa-home
  归档: /archives/ || fas fa-archive
  标签: /tags/ || fas fa-tags
  分类: /categories/ || fas fa-folder-open
  关于: /about/ || fas fa-heart
```

## 开发工作流

### 测试环境搭建

主题必须在 Hexo 博客中测试：

```bash
# 在 Hexo 博客根目录
cd themes/
ln -s /path/to/hexo-theme-butterfly butterfly

# 安装依赖
npm install hexo-renderer-pug hexo-renderer-stylus --save

# 预览
hexo clean && hexo server
```

### 修改后的重载要求

| 文件类型 | 位置 | 重载方式 |
|---------|------|---------|
| Pug 模板 | `layout/*.pug` | `hexo generate` |
| Stylus 样式 | `source/css/*.styl` | `hexo generate` |
| Scripts 脚本 | `scripts/**/*.js` | 重启 `hexo server` |
| 配置文件 | `_config.yml` | 重启 `hexo server` |

## 扩展开发模式

### 添加自定义标签插件
```javascript
// scripts/tag/your-tag.js
hexo.extend.tag.register('your_tag', function(args, content) {
  return `<div class="your-tag">${content}</div>`;
}, {ends: true});
```

### 添加模板辅助函数
```javascript
// scripts/helpers/your-helper.js
hexo.extend.helper.register('your_helper', function() {
  return 'helper output';
});
// 在 Pug 中使用: #{your_helper()}
```

### 添加内容过滤器
```javascript
// scripts/filters/your-filter.js
hexo.extend.filter.register('after_post_render', function(data) {
  // 处理 data.content
  return data;
});
```

## 重要约束

- **Hexo 版本**: 需要 5.3.0+
- **测试要求**: 必须测试亮色/暗色模式 + 响应式布局
- **国际化**: 用户可见文本需支持 `languages/` 多语言
- **配置原则**:
  - 所有功能可选，可在 `_config.yml` 中禁用
  - 外部服务（评论/统计）需 API 密钥
  - CDN 地址统一在 `plugins.yml` 管理，不硬编码


### 配置修改后的操作

修改 `_config.butterfly.yml` 后需要：
1. 清理缓存：`hexo clean`
2. 重新生成：`hexo generate`
3. 重启服务器：重启 `hexo server`

## 当前项目配置

本博客定位为 Java 学习笔记 + 二次元风格个人博客：

- **主题风格**：粉色系二次元配色
- **内容方向**：Java 基础课程学习记录
- **写作风格**：轻松口语化，小白友好，少代码多叙述
- **部署方式**：Cloudflare Pages（免费 CDN + HTTPS）
- **线上地址**：https://blog-54k.pages.dev
- **GitHub 仓库**：https://github.com/LovePeachBlossom/My_Blog
- **用户信息**：
  - GitHub: https://github.com/LovePeachBlossom
  - B站: https://space.bilibili.com/407681508
  - 邮箱: wf_0904_2024_love@outlook.com

### ⚠️ 重要工作流程

**所有开发和测试必须在 Linux 文件系统中进行**：
- **开发目录**：`~/projects/My_Blog/` （Linux 原生文件系统）
- **博客项目**：`~/projects/My_Blog/blog-test/`
- **主题目录**：`~/projects/My_Blog/hexo-theme-butterfly/`
- **配置文件**：`~/projects/My_Blog/blog-test/_config.butterfly.yml`

**禁止直接修改 Windows 文件系统项目**（`/mnt/d/user/My_Blog/`），用户会自行导出。

### 常用命令

```bash
# 切换到项目目录
cd ~/projects/My_Blog/blog-test

# 新建文章
npx hexo new "文章标题"

# 清理 + 生成 + 预览
npx hexo clean && npx hexo generate && npx hexo server -i 0.0.0.0

# 部署（需配置 deploy）
npx hexo deploy
```

### 当前配置状态（2026-02-19）

**部署方式**：Cloudflare Pages（Git 推送自动构建）
**线上地址**：https://blog-54k.pages.dev
**GitHub 仓库**：https://github.com/LovePeachBlossom/My_Blog
**主题接入方式**：
- 本地开发：软链接 `blog-test/themes/butterfly -> ../../hexo-theme-butterfly`
- CF 构建：`cp -r hexo-theme-butterfly blog-test/themes/butterfly`

**CF Pages 构建配置**：
- 构建命令：`cp -r hexo-theme-butterfly blog-test/themes/butterfly && cd blog-test && npm install && npx hexo clean && npx hexo generate`
- 输出目录：`blog-test/public`
- 环境变量：`NODE_VERSION` = `22`

**已实现功能**：
- ✅ 霞鹜文楷字体（LXGW WenKai）+ Fira Code 代码字体
- ✅ 五颜六色标签（6色循环，支持暗色模式）
- ✅ 全站背景图（压缩至 392KB，亮色 0.3 / 暗色 0.5 透明度）
- ✅ 协调渐变背景（粉紫蓝色调）+ 页脚渐变背景
- ✅ 固定导航栏 + 自定义中文菜单
- ✅ 本地搜索 + 字数统计
- ✅ 深色模式 + 阅读模式
- ✅ 文章增强（TOC、相关文章、版权声明、分页、封面图）
- ✅ 粉色主题色 + 圆角 UI + 侧边栏首页显示
- ✅ 社交链接（哔哩哔哩 + 邮箱）
- ✅ Sitemap + 结构化数据（SEO）
- ✅ 图片懒加载 + 链接预加载（instantpage）
- ✅ 自动清理脚本（hexo.route.remove 移除无用文件）
- ✅ Cloudflare Pages 部署（自动构建 + CDN + HTTPS）
- ✅ `_config.butterfly.yml` 精简至 ~135 行（仅保留差异配置）

**未启用（按需开启）**：
- 评论系统（Disqus/Valine/Twikoo 等）
- 统计分析（GA/百度统计）
- PWA、PJAX

**待完成**：
- [ ] 继续创作 Java 学习笔记内容
