+++  
title = 'Hugo + GitHub Pages + Obsidian 博客搭建流程'  
date = '2026-05-24T00:00:00+08:00'  
draft = false  
categories = ['Build']  
tags = ['Hugo', 'GitHub Pages', 'Obsidian', 'Stack']  
description = '记录 Hugo 博客从本地搭建、GitHub Pages 部署、Obsidian 写作流、Stack 主题更换到页面美化的完整流程。'  
image = "cover.png"
+++

## 一、整体方案

本博客采用 **Hugo + GitHub Pages + Obsidian + Stack** 的方案搭建。

各部分的作用如下：

- **Hugo**：负责生成静态博客网站。
    
- **GitHub Pages**：负责将网站发布到线上。
    
- **GitHub Actions**：负责自动构建和部署。
    
- **Obsidian**：负责日常写作和文章管理。
    
- **Stack**：负责博客主题和页面样式。
    

最终形成的工作流是：在 **Obsidian** 中写文章，在 **Hugo** 中本地预览，通过 **Git** 提交修改，再由 **GitHub Actions** 自动部署到 **GitHub Pages**。

## 二、安装基础工具

首先需要安装 Git、Hugo 和 VS Code。

在 Windows PowerShell 中执行：

```powershell
winget install Git.Git
winget install Hugo.Hugo.Extended
winget install Microsoft.VisualStudioCode
```

安装完成后，检查版本：

```powershell
git --version
hugo version
```

如果能正常显示版本号，说明安装成功。

## 三、创建 Hugo 博客项目

进入桌面目录：

```powershell
cd Desktop
```

创建 Hugo 项目：

```powershell
hugo new site myblog
```

进入项目目录：

```powershell
cd myblog
```

初始化 Git：

```powershell
git init
```

此时博客项目的基本结构已经生成：

```text
myblog/
  archetypes/
  assets/
  content/
  data/
  layouts/
  static/
  themes/
  hugo.toml
```

其中，`content` 用来存放文章，`themes` 用来存放主题，`hugo.toml` 是整个博客的配置文件。

## 四、安装基础主题并测试运行

最开始可以先使用 PaperMod 主题测试博客是否能正常运行。

安装 PaperMod：

```powershell
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

然后修改 `hugo.toml`：

```toml
baseURL = "http://localhost:1313/"
languageCode = "zh-cn"
title = "我的博客"
theme = "PaperMod"

[params]
  defaultTheme = "auto"
  ShowReadingTime = true
  ShowToc = true
  ShowCodeCopyButtons = true
```

启动本地预览：

```powershell
hugo server -D
```

浏览器打开：

```text
http://localhost:1313/
```

如果页面能正常显示，说明 Hugo 本地博客已经搭建成功。

## 五、创建第一篇文章

创建文章：

```powershell
hugo new posts/first-post.md
```

打开文件：

```text
content/posts/first-post.md
```

将内容改为：

```markdown
+++
title = '我的第一篇博客'
date = '2026-05-23T22:30:00+08:00'
draft = false
categories = ['博客']
tags = ['Hugo']
description = '这是我的第一篇博客。'
+++

这是我的第一篇博客。

以后我会在这里记录学习笔记、项目经历、读书笔记和阶段性思考。
```

其中，`draft = false` 表示文章正式发布；如果是 `draft = true`，则表示文章仍是草稿。

## 六、部署到 GitHub Pages

在 GitHub 上新建仓库，仓库名需要设置为：

```text
GitHub用户名.github.io
```

例如：

```text
dongit1005-netizen.github.io
```

然后在本地项目中配置 Git 用户信息：

```powershell
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub邮箱"
```

提交本地项目：

```powershell
git add .
git commit -m "init blog"
git branch -M main
```

绑定远程仓库：

```powershell
git remote add origin https://github.com/你的GitHub用户名/你的GitHub用户名.github.io.git
```

推送到 GitHub：

```powershell
git push -u origin main
```

如果远程仓库地址需要修改，可以使用：

```powershell
git remote set-url origin https://github.com/你的GitHub用户名/你的GitHub用户名.github.io.git
```

查看远程仓库地址：

```powershell
git remote -v
```

## 七、配置 GitHub Actions 自动部署

在项目根目录下创建部署文件夹：

```powershell
mkdir .github
mkdir .github\workflows
```

创建部署文件：

```powershell
notepad .github\workflows\hugo.yaml
```

写入以下内容：

```yaml
name: Deploy Hugo site to Pages

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

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: latest
          extended: true

      - name: Build
        run: hugo --gc --minify

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

保存后提交：

```powershell
git add .
git commit -m "add deploy workflow"
git push
```

然后进入 GitHub 仓库的 `Settings → Pages`，在 `Build and deployment` 中将 `Source` 设置为 `GitHub Actions`。

之后每次执行 `git push`，GitHub Actions 都会自动构建并部署博客。

线上访问地址为：

```text
https://你的GitHub用户名.github.io/
```

例如：

```text
https://dongit1005-netizen.github.io/
```

## 八、修改正式 baseURL

本地测试时可以使用：

```toml
baseURL = "http://localhost:1313/"
```

部署到线上后，需要改为正式网址：

```toml
baseURL = "https://dongit1005-netizen.github.io/"
```

修改后提交：

```powershell
git add .
git commit -m "update baseURL"
git push
```

## 九、配置 Obsidian 写作环境

为了获得更舒适的写作体验，可以使用 Obsidian 作为博客文章编辑器。

在 Obsidian 中选择“打开本地文件夹作为仓库”，打开 Hugo 项目中的 `content` 文件夹：

```text
C:\Users\lenovo\Desktop\myblog\content
```

这样 Obsidian 中只会显示文章内容，不会显示主题、部署文件和配置文件。

建议在 Obsidian 中进入 `设置 → 文件与链接`，进行如下配置：

- 关闭“使用 Wiki 链接”。
    
- 将“新附件的默认位置”设置为“当前文件所在文件夹”。
    

这样插入图片时，更适合 Hugo 识别。

## 十、使用文章包模式写作

为了方便管理图片，采用 Hugo 的文章包模式。

标准结构如下：

```text
content/
  posts/
    article-name/
      index.md
      cover.png
      01.png
      02.png
```

在 Obsidian 中操作时，需要先在 `posts` 下新建文章文件夹，再在文件夹中新建名为 `index` 的笔记。

注意，不要在 Obsidian 里新建 `index.md`，因为 Obsidian 会自动补 `.md` 后缀。只需要创建名为 `index` 的笔记即可，最终文件会是 `index.md`。

文章中的图片和 `index.md` 放在同一个文件夹中。插入图片时使用标准 Markdown 格式：

```markdown
![图片说明](01.png)
```

不要使用 Obsidian 的 Wiki 链接格式：

```markdown
![[01.png]]
```

一篇文章的基本结构示例如下：

```markdown
+++
title = '我的博客搭建记录'
date = '2026-05-24T00:00:00+08:00'
draft = false
categories = ['博客']
tags = ['Hugo', 'Obsidian']
description = '记录 Hugo 博客搭建过程。'
image = 'cover.png'
+++

## 前言

这里写正文。

![示例图片](01.png)

## 正文

这里继续写正文。
```

## 十一、本地预览文章

在 PowerShell 中进入博客根目录：

```powershell
cd C:\Users\lenovo\Desktop\myblog
```

启动本地预览：

```powershell
hugo server -D --disableFastRender --ignoreCache
```

浏览器打开：

```text
http://localhost:1313/
```

其中，`localhost:1313` 是本地预览地址，必须保持 Hugo 服务运行才能访问。

线上地址是：

```text
https://dongit1005-netizen.github.io/
```

线上地址不依赖本地 PowerShell 服务。

## 十二、发布文章到线上

文章写完并确认预览正常后，停止 Hugo 服务：

```text
Ctrl + C
```

然后提交并推送：

```powershell
git add .
git commit -m "update blog"
git push
```

等待 GitHub Actions 变成绿色对勾后，线上博客会自动更新。

## 十三、更换 Stack 主题

后续将主题从 PaperMod 更换为 Stack。

先安装 Stack 主题：

```powershell
git submodule add https://github.com/CaiJimmy/hugo-theme-stack.git themes/hugo-theme-stack
```

如果网络不稳定，可以使用：

```powershell
git submodule add --depth=1 https://github.com/CaiJimmy/hugo-theme-stack.git themes/hugo-theme-stack
```

然后修改 `hugo.toml`，将主题改为：

```toml
theme = "hugo-theme-stack"
```

Stack 的基础配置示例如下：

```toml
baseURL = "https://dongit1005-netizen.github.io/"
languageCode = "zh-cn"
title = "DOITSPACE"
theme = "hugo-theme-stack"

[pagination]
  pagerSize = 5

[params]
  mainSections = ["posts"]
  featuredImageField = "image"
  rssFullContent = true
  favicon = ""

[params.footer]
  since = 2026
  customText = ""

[params.sidebar]
  emoji = ""
  subtitle = "记录学习、项目、阅读与阶段性的自我整理。"

[params.article]
  math = false
  toc = true
  readingTime = true

[params.comments]
  enabled = false

[[params.widgets.homepage]]
  type = "search"

[[params.widgets.homepage]]
  type = "categories"

[[params.widgets.homepage]]
  type = "archives"

[[params.widgets.homepage]]
  type = "tag-cloud"

[menu]

[[menu.main]]
  identifier = "home"
  name = "首页"
  url = "/"
  weight = 10

[[menu.main]]
  identifier = "archives"
  name = "归档"
  url = "/archives/"
  weight = 20

[[menu.main]]
  identifier = "categories"
  name = "分类"
  url = "/categories/"
  weight = 30

[[menu.main]]
  identifier = "tags"
  name = "标签"
  url = "/tags/"
  weight = 40

[[menu.main]]
  identifier = "about"
  name = "关于"
  url = "/about/"
  weight = 50
```

修改后本地预览：

```powershell
hugo server -D --disableFastRender --ignoreCache
```

如果页面显示正常，提交并推送：

```powershell
git add .
git commit -m "switch to stack theme"
git push
```

## 十四、创建归档页和关于页

创建归档页：

```powershell
hugo new archives.md
```

修改 `content/archives.md`：

```markdown
+++
title = '归档'
layout = 'archives'
url = '/archives/'
summary = 'archives'
draft = false
+++
```

创建关于页：

```powershell
hugo new about.md
```

修改 `content/about.md`：

```markdown
+++
title = '关于'
date = '2026-05-24T00:00:00+08:00'
draft = false
+++

这里是 DOITSPACE。

这里用于记录学习笔记、项目经历、阅读感受和阶段性的自我整理。
```

提交：

```powershell
git add .
git commit -m "add archives and about pages"
git push
```

## 十五、自定义 Stack 样式

Stack 主题支持通过自定义 SCSS 进行轻量美化。自定义样式不要写进 `.github/workflows/hugo.yaml`，而应放在：

```text
assets/scss/custom.scss
```

如果没有对应文件夹，可以创建：

```powershell
mkdir assets -Force
mkdir assets\scss -Force
notepad assets\scss\custom.scss
```

可以在 `custom.scss` 中调整页面卡片、代码块、图片、引用块等样式。

例如，代码块间距可以通过如下样式调整：

```scss
.article-content .highlight {
  margin: 1.4rem 0 1.8rem 0;
  border-radius: 14px;
  overflow: hidden;
}

.article-content .highlight pre,
.article-content pre {
  padding: 1.2rem 1.4rem;
  border-radius: 14px;
  line-height: 1.7;
}

.article-content pre code {
  white-space: pre;
}
```

需要注意，`.github/workflows/hugo.yaml` 只用于 GitHub Actions 部署，不能写入 CSS 或 SCSS 样式。自定义样式统一放在 `assets/scss/custom.scss` 中。

## 十六、清理 Hugo 自动生成文件

Hugo 本地构建时会生成 `public/` 和 `resources/` 文件夹。这些文件通常不需要提交到 GitHub，因为 GitHub Actions 会在线上重新生成。

在博客根目录新建或打开 `.gitignore`：

```powershell
notepad .gitignore
```

写入：

```gitignore
public/
resources/
.hugo_build.lock
```

然后让 Git 停止跟踪这些已生成文件：

```powershell
git rm -r --cached public resources
```

如果其中某个文件夹不存在，可以分别执行：

```powershell
git rm -r --cached public
git rm -r --cached resources
```

之后提交：

```powershell
git add .gitignore
git commit -m "ignore hugo generated files"
git push
```

以后主要提交文章、配置、主题样式和部署文件，不再提交 Hugo 自动生成的构建产物。

## 十七、添加提示框和时间轴组件

为了让关于页或维护日志更有层次，可以通过 Hugo shortcode 添加提示框和时间轴组件。

首先创建 shortcode 文件夹：

```powershell
mkdir layouts -Force
mkdir layouts\shortcodes -Force
```

创建提示框组件：

```powershell
notepad layouts\shortcodes\notice.html
```

写入：

```html
<div class="custom-notice">
  {{ .Inner | markdownify }}
</div>
```

创建时间轴外壳：

```powershell
notepad layouts\shortcodes\timeline.html
```

写入：

```html
<div class="custom-timeline">
  {{ .Inner }}
</div>
```

创建时间轴单项：

```powershell
notepad layouts\shortcodes\timeline-item.html
```

写入：

```html
<div class="timeline-item">
  <div class="timeline-date">{{ .Get "date" }}</div>
  <div class="timeline-dot"></div>
  <div class="timeline-content">
    <h3>{{ .Get "title" }}</h3>
    <p>{{ .Inner | markdownify }}</p>
  </div>
</div>
```

然后在 `assets/scss/custom.scss` 中加入样式：

```scss
.custom-notice {
  margin: 1.5rem 0;
  padding: 1rem 1.25rem;
  border-radius: 6px;
  background: var(--card-background);
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.08);
  line-height: 1.8;
}

.custom-timeline {
  margin: 2rem 0;
  padding: 1rem 0;
}

.timeline-item {
  display: grid;
  grid-template-columns: 120px 24px 1fr;
  column-gap: 1rem;
  position: relative;
  min-height: 90px;
}

.timeline-date {
  text-align: right;
  font-size: 1rem;
  color: var(--card-text-color-main);
  padding-top: 0.15rem;
}

.timeline-dot {
  position: relative;
  width: 24px;
}

.timeline-dot::before {
  content: "";
  position: absolute;
  top: 0.45rem;
  left: 50%;
  width: 10px;
  height: 10px;
  transform: translateX(-50%);
  border-radius: 50%;
  background: #c8e5f5;
  z-index: 2;
}

.timeline-dot::after {
  content: "";
  position: absolute;
  top: 0;
  bottom: -1rem;
  left: 50%;
  width: 2px;
  transform: translateX(-50%);
  background: #d9edf7;
}

.timeline-content {
  padding-bottom: 2rem;
}

.timeline-content h3 {
  margin: 0 0 0.5rem 0;
  font-size: 1.15rem;
}

.timeline-content p {
  margin: 0;
  color: var(--card-text-color-secondary);
  line-height: 1.7;
}

@media (max-width: 768px) {
  .timeline-item {
    grid-template-columns: 80px 20px 1fr;
    column-gap: 0.6rem;
  }

  .timeline-date {
    font-size: 0.9rem;
  }

  .timeline-content h3 {
    font-size: 1rem;
  }
}
```

之后即可在文章或关于页中使用。

提示框写法：

```markdown
{{< notice >}}
这里可以写提示、说明、感谢或补充内容。
{{< /notice >}}
```

时间轴写法：

```markdown
{{< timeline >}}

{{< timeline-item date="2026-05-24" title="更换 Stack 主题" >}}
完成 Stack 主题更换，并调整基础页面样式。
{{< /timeline-item >}}

{{< timeline-item date="2026-05-24" title="配置 Obsidian 写作流" >}}
采用文章包模式，将文章正文和图片放在同一文件夹中。
{{< /timeline-item >}}

{{< timeline-item date="2026-05-23" title="完成 GitHub Pages 部署" >}}
配置 GitHub Actions，实现 push 后自动构建和部署。
{{< /timeline-item >}}

{{< /timeline >}}
```

## 十八、文章写法整理

为了让文章页面更清爽，普通说明文字不建议放进代码块。

说明类内容适合用普通段落或列表。例如：

- **Hugo**：负责生成静态博客网站。
    
- **GitHub Pages**：负责把网站发布到线上。
    
- **GitHub Actions**：负责自动构建和部署。
    
- **Obsidian**：负责日常写作和文章管理。
    
- **Stack**：负责博客主题和页面样式。
    

真正适合使用代码块的内容包括：

- PowerShell 命令。
    
- 文件目录结构。
    
- 配置文件内容。
    
- Markdown、TOML、YAML、SCSS 等代码。
    

例如命令可以写成：

```powershell
git add .
git commit -m "update blog"
git push
```

文件结构可以写成：

```text
content/
  posts/
    article-name/
      index.md
      cover.png
```

配置文件可以写成：

```toml
baseURL = "https://dongit1005-netizen.github.io/"
theme = "hugo-theme-stack"
```

这样文章会更像正式博客，而不是整篇技术说明书。

## 十九、最终工作流

当前博客的日常使用流程如下。

写文章时，打开 Obsidian，进入 `content/posts`，新建文章文件夹，在文件夹中创建 `index`，正文写在 `index.md` 中，图片放在同一个文件夹里。

本地预览时，在 PowerShell 中执行：

```powershell
cd C:\Users\lenovo\Desktop\myblog
hugo server -D --disableFastRender --ignoreCache
```

发布上线时，执行：

```powershell
git add .
git commit -m "update blog"
git push
```

线上检查地址：

```text
https://dongit1005-netizen.github.io/
```

## 二十、当前博客结构

当前博客基本结构如下：

```text
myblog/
  .github/
    workflows/
      hugo.yaml
  assets/
    scss/
      custom.scss
  content/
    posts/
      article-name/
        index.md
        cover.png
        01.png
    archives.md
    about.md
  layouts/
    shortcodes/
      notice.html
      timeline.html
      timeline-item.html
  static/
  themes/
    hugo-theme-stack/
  .gitignore
  hugo.toml
```

其中，`content/posts` 存放博客文章，`assets/scss/custom.scss` 存放自定义样式，`layouts/shortcodes` 存放自定义短代码，`hugo.toml` 是博客配置文件，`.github/workflows/hugo.yaml` 是 GitHub Actions 部署文件。

## 二十一、总结

至此，博客已经完成了从本地搭建到线上部署的完整流程。

当前方案为 **Hugo + GitHub Pages + GitHub Actions + Obsidian + Stack**。

Hugo 负责生成网站，GitHub Pages 负责发布，Obsidian 负责写作，Stack 负责页面样式。后续只需要按照固定流程写作、预览、提交和推送，即可持续更新博客。