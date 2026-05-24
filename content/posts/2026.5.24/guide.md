+++  
title = "DOITSPACE 创作博客操作指南"  
date = "2026-05-24T00:00:00+08:00"  
draft = false  
categories = ["Build"]  
tags = ["Hugo", "Obsidian", "Stack", "GitHub Pages"]  
description = "操作指南"  
+++

## 一、整体工作流

本博客采用 **Hugo + GitHub Pages + GitHub Actions + Obsidian + Stack** 的创作与发布方案。

各部分作用如下：

```text
Hugo：负责生成静态博客网站
GitHub Pages：负责将博客发布到线上
GitHub Actions：负责自动构建和部署
Obsidian：负责日常写作和文章管理
Stack：负责博客主题和页面样式
```

日常创作流程如下：

```text
Obsidian 写文章
        ↓
Hugo 本地预览
        ↓
Git 提交修改
        ↓
GitHub Actions 自动部署
        ↓
GitHub Pages 线上更新
```

其中，**Obsidian** 是主要写作工具，**PowerShell** 主要用于本地预览和发布，**GitHub** 负责线上托管与自动部署。

## 二、新建一篇文章

本博客采用 Hugo 的文章包模式，即每一篇文章单独放在一个文件夹中。

标准结构如下：

```text
content/
  posts/
    2026-05-24/
      article-name/
        index.md
        cover.png
        01.png
        02.png
```

在 Obsidian 中操作时，步骤如下：

```text
1. 打开 Obsidian
2. 进入 content/posts
3. 新建日期文件夹，例如 2026-05-24
4. 在日期文件夹中新建文章文件夹，例如 blog-guide
5. 在文章文件夹中新建笔记，名称只写 index
6. 将封面图和正文图片放在同一个文章文件夹中
```

注意：在 Obsidian 里新建笔记时，只需要写：

```text
index
```

不要写：

```text
index.md
```

因为 Obsidian 会自动补 `.md` 后缀。如果手动写 `index.md`，有可能生成：

```text
index.md.md
```

这样 Hugo 可能无法正确识别文章包。


## 三、文章头部信息模板

每篇文章开头都需要写 front matter，用来告诉 Hugo 文章标题、日期、分类、标签、摘要和封面图。

推荐模板如下：

```markdown
+++
title = "文章标题"
date = "2026-05-24T00:00:00+08:00"
draft = false
categories = ["博客"]
tags = ["Hugo", "Obsidian", "Stack"]
description = "这里写文章摘要，会显示在首页文章卡片中。"
image = "cover.png"
+++
```

各字段含义如下：

```text
title：文章标题
date：文章发布时间
draft：是否为草稿，false 表示发布，true 表示草稿
categories：文章分类
tags：文章标签
description：首页文章卡片显示的摘要
image：首页文章卡片使用的封面图
```

如果文章暂时不想发布，可以写：

```toml
draft = true
```

如果文章要正式显示在网站上，需要写：

```toml
draft = false
```


## 四、正文写作规范

正文建议从二级标题开始写。因为文章标题本身已经是一级标题。

推荐写法：

```markdown
## 一、标题

这里写正文内容。

## 二、标题

这里继续写正文内容。

## 三、标题

这里写第三部分内容。
```

普通说明文字建议使用段落、列表或引用，不要全部放进代码块。

适合普通列表的内容：

```markdown
- **Hugo**：负责生成静态博客网站。
- **GitHub Pages**：负责把网站发布到线上。
- **Obsidian**：负责日常写作和文章管理。
- **Stack**：负责博客主题和页面样式。
```

适合引用块的内容：

```markdown
> 这里可以写一段补充说明、提醒或者阶段性总结。
```

适合代码块的内容包括：

```text
PowerShell 命令
文件目录结构
配置文件内容
Markdown 示例
TOML / YAML / SCSS / HTML 代码
```

不建议把普通说明文字全部写成代码块，否则页面会显得过于生硬。


## 五、图片与封面管理

每篇文章的图片建议和 `index.md` 放在同一个文章文件夹中。

标准结构如下：

```text
article-name/
  index.md
  cover.png
  01.png
  02.png
  03.png
```

封面图在 front matter 中这样写：

```toml
image = "cover.png"
```

正文插图这样写：

```markdown
![图片说明](01.png)
```

不要使用 Obsidian 默认的 Wiki 链接格式：

```markdown
![[01.png]]
```

推荐使用标准 Markdown 图片语法：

```markdown
![图片说明](01.png)
```

图片命名建议使用英文、数字和短横线，例如：

```text
cover.png
01-homepage.png
02-actions.png
03-preview.png
```

不建议使用：

```text
截图 2026年5月24日.png
新建图片.png
Pasted image 202605240001.png
```

这样可以减少路径错误和线上加载失败的问题。


## 六、本地预览

写完文章后，先在本地预览，确认页面、图片、封面、代码块和排版都正常。

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

说明：

```text
localhost:1313 是本地预览地址
必须保持 PowerShell 中 hugo server 正在运行
关闭 Hugo 服务后，本地网页无法访问
```

如果要停止本地预览，在 PowerShell 中按：

```text
Ctrl + C
```


## 七、发布到线上

确认本地预览无误后，将修改提交并推送到 GitHub。

常用发布命令如下：

```powershell
git add .
git commit -m "update blog"
git push
```

推送成功后，进入 GitHub 仓库的 Actions 页面，等待最新一次运行变成绿色对勾。

线上博客地址：

```text
https://dongit1005-netizen.github.io/
```

日常发布流程可以简化为：

```text
写文章
↓
本地预览
↓
git add .
↓
git commit -m "update blog"
↓
git push
↓
等待 GitHub Actions 绿色对勾
↓
打开线上网站检查
```


## 八、常用 Git 命令

查看当前文件状态：

```powershell
git status
```

提交所有修改：

```powershell
git add .
git commit -m "update blog"
```

推送到 GitHub：

```powershell
git push
```

查看远程仓库地址：

```powershell
git remote -v
```

修改远程仓库地址：

```powershell
git remote set-url origin https://github.com/你的GitHub用户名/你的GitHub用户名.github.io.git
```

如果本地已经提交，但网络问题导致无法推送，之后只需要重新执行：

```powershell
git push
```

不需要重复 `git add` 和 `git commit`。


## 九、关于页与归档页

关于页用于介绍博客和作者。

常见位置：

```text
content/about.md
```

或者：

```text
content/about/index.md
```

关于页示例：

```markdown
+++
title = "关于"
date = "2026-05-24T00:00:00+08:00"
draft = false
+++

这里是 DOITSPACE。

我会在这里记录学习笔记、项目经历、阅读感受和阶段性的自我整理。
```

归档页用于按时间查看文章。

创建归档页：

```powershell
hugo new archives.md
```

归档页内容：

```markdown
+++
title = "归档"
layout = "archives"
url = "/archives/"
summary = "archives"
draft = false
+++
```


## 十、分类与标签规范

分类用于划分文章的大方向，标签用于描述文章的具体内容。

建议分类不要太多，初期可以使用：

```text
博客
学习笔记
项目记录
阅读随笔
阶段思考
```

示例：

```toml
categories = ["博客"]
```

标签可以更细，例如：

```toml
tags = ["Hugo", "Obsidian", "Stack"]
```

一篇文章可以有一个分类，也可以有多个标签。

推荐写法：

```toml
categories = ["学习笔记"]
tags = ["控制系统", "Bode图", "自动控制"]
```


## 十一、短代码组件使用

本博客可以通过 Hugo shortcode 实现一些特殊组件，例如提示框和时间轴。

### 1. 提示框

提示框写法：

```markdown
{{< notice >}}
这里可以写提示、说明、感谢或补充内容。
{{< /notice >}}
```

适合用于：

```text
补充说明
注意事项
感谢信息
阶段总结
```

### 2. 时间轴

时间轴写法：


{{< timeline >}}

{{< timeline-item date="2026-05-24" title="完成 Stack 主题更换" >}}
完成博客主题切换，并调整基础页面样式。
{{< /timeline-item >}}

{{< timeline-item date="2026-05-24" title="配置 Obsidian 写作流" >}}
采用文章包模式，将文章正文和图片放在同一文件夹中。
{{< /timeline-item >}}

{{< timeline-item date="2026-05-23" title="完成 GitHub Pages 部署" >}}
配置 GitHub Actions，实现 push 后自动构建和部署。
{{< /timeline-item >}}

{{< /timeline >}}

适合用于：

```text
博客维护日志
项目进度记录
学习计划时间线
阶段性复盘
```


## 十二、自定义样式位置

Stack 主题的自定义样式统一写在：

```text
assets/scss/custom.scss
```

如果没有这个文件，可以创建：

```powershell
mkdir assets -Force
mkdir assets\scss -Force
notepad assets\scss\custom.scss
```

注意：不要把 CSS 或 SCSS 样式写进 GitHub Actions 部署文件。

正确位置：

```text
assets/scss/custom.scss
```

错误位置：

```text
.github/workflows/hugo.yaml
```

`hugo.yaml` 只用于 GitHub Actions 自动部署，不能写样式代码。


## 十三、常用文件位置速查

博客主配置文件：

```text
hugo.toml
```

文章目录：

```text
content/posts/
```

关于页：

```text
content/about.md
```

归档页：

```text
content/archives.md
```

自定义样式：

```text
assets/scss/custom.scss
```

自定义短代码：

```text
layouts/shortcodes/
```

GitHub Actions 部署文件：

```text
.github/workflows/hugo.yaml
```

Git 忽略文件：

```text
.gitignore
```

主题目录：

```text
themes/hugo-theme-stack/
```


## 十四、不要随便修改的文件

以下文件或文件夹不建议随便修改：

```text
themes/hugo-theme-stack/
public/
resources/
```

说明：

```text
themes/hugo-theme-stack/ 是主题源码，尽量不要直接修改
public/ 是 Hugo 生成的静态网页文件
resources/ 是 Hugo 生成的资源缓存文件
```

如果要美化主题，优先修改：

```text
assets/scss/custom.scss
```

如果要新增组件，优先修改：

```text
layouts/shortcodes/
```

不要直接改主题源码，否则后续更新主题时容易冲突。


## 十五、忽略 Hugo 自动生成文件

Hugo 本地构建时会生成 `public/` 和 `resources/`。这些文件通常不需要提交到 GitHub，因为 GitHub Actions 会在线上重新生成。

`.gitignore` 中建议写入：

```gitignore
public/
resources/
.hugo_build.lock
```

如果这些文件已经被 Git 跟踪，可以执行：

```powershell
git rm -r --cached public resources
```

然后提交：

```powershell
git add .gitignore
git commit -m "ignore hugo generated files"
git push
```


## 十六、常见问题排查

### 1. 图片不显示

先检查文件结构是否正确：

```text
article-name/
  index.md
  cover.png
  01.png
```

再检查图片引用是否正确：

```markdown
![图片说明](01.png)
```

不要写成：

```markdown
![[01.png]]
```

也要检查文件名是否完全一致，例如：

```text
01.png
01.PNG
```

线上环境可能区分大小写，建议统一使用小写后缀。

### 2. 封面图不显示

检查文章 front matter：

```toml
image = "cover.png"
```

检查封面图是否和 `index.md` 在同一个文章文件夹中：

```text
article-name/
  index.md
  cover.png
```

如果首页封面仍然不显示，可以重启 Hugo：

```powershell
hugo server -D --disableFastRender --ignoreCache
```

### 3. 文章不显示

检查是否设置为草稿：

```toml
draft = false
```

如果是：

```toml
draft = true
```

文章可能不会正常显示在线上。

同时检查文章是否放在：

```text
content/posts/
```

### 4. 本地网页打不开

如果浏览器显示无法连接：

```text
localhost refused to connect
```

说明 Hugo 本地服务没有运行。重新执行：

```powershell
hugo server -D --disableFastRender --ignoreCache
```

### 5. GitHub 没有更新

先检查是否真的提交并推送：

```powershell
git status
git push
```

然后进入 GitHub Actions 页面，确认最新一次运行是否是绿色对勾。

如果 Actions 没有新记录，通常说明没有成功执行 `git push`。

### 6. GitHub Actions 红叉

先点进红叉，查看具体报错。

重点检查：

```text
.github/workflows/hugo.yaml 是否被误改
hugo.toml 是否有格式错误
assets/scss/custom.scss 是否有语法错误
主题 submodule 是否正常
```

如果 `hugo.yaml` 里出现了 CSS 或 SCSS 内容，说明部署文件被误改，需要恢复。


## 十七、最终日常操作流程

写新文章时：

```text
打开 Obsidian
进入 content/posts
新建日期文件夹
新建文章文件夹
在文章文件夹中新建 index
填写 front matter
写正文
放入 cover.png 和正文图片
```

本地预览时：

```powershell
cd C:\Users\lenovo\Desktop\myblog
hugo server -D --disableFastRender --ignoreCache
```

发布到线上时：

```powershell
git add .
git commit -m "update blog"
git push
```

线上检查：

```text
https://dongit1005-netizen.github.io/
```


## 十八、当前博客结构

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
      2026-05-24/
        article-name/
          index.md
          cover.png
          01.png
    about.md
    archives.md
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

各部分作用如下：

```text
content/posts/               存放博客文章
content/about.md             关于页面
content/archives.md          归档页面
assets/scss/custom.scss      自定义样式
layouts/shortcodes/          自定义短代码
themes/hugo-theme-stack/     Stack 主题文件
.github/workflows/hugo.yaml  GitHub Actions 部署文件
.gitignore                   Git 忽略规则
hugo.toml                    博客主配置文件
```

## 十九、总结

这套博客创作流程的核心是：

```text
内容写作交给 Obsidian
网页生成交给 Hugo
页面样式交给 Stack
版本管理交给 Git
自动部署交给 GitHub Actions
线上访问交给 GitHub Pages
```

后续更新博客时，只需要按照固定流程完成写作、预览、提交和推送即可。配置文件、样式文件和短代码文件可以逐步扩展，但日常创作应尽量保持简单、稳定、可复用。