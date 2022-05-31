# 用 Gitbook 写此书

记录使用 Gitbook 的一些经验。
<br>

### 发布流程

本地使用 VS Code 编辑，git 同步到 github，然后在 gitbook 上再同步一次。

### 编辑器

不要使用网站在线编辑.

### 如何插入空行

用 HTML 标记：`<br>`

### md 不如预期怎么办

直接上 HTML

### 如何组织目录结构

先用英文或拼音，有了md文件后再把章节名称更新成中文。

### 目录会无效

左右切页面时，导航会无效。暂时无解。

### 编译成epub/mobi

电脑上装 gitbook。

```bash
gitbook epub ./ book.epub
```
