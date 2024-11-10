---
title: Fuwari 的简单指南
published: 2024-04-01
description: "如何使用此博客模板。"
image: "./cover.jpeg"
tags: ["Fuwari", "Blogging", "Customization"]
category: Guides
draft: false
---

> 封面图片来源: [Source](https://image.civitai.com/xG1nkqKTMzGDvpLrqFT7WA/208fc754-890d-4adb-9753-2c963332675d/width=2048/01651-1456859105-(colour_1.5),girl,_Blue,yellow,green,cyan,purple,red,pink,_best,8k,UHD,masterpiece,male%20focus,%201boy,gloves,%20ponytail,%20long%20hair,.jpeg)


这个博客模板是使用 [Astro](https://astro.build/) 构建的。对于本指南中未提及的内容，你可以在 [Astro Docs](https://docs.astro.build/) 中找到答案。

## 文章的 Front-matter

```yaml
---
title: 我的第一个博客文章
published: 2023-09-09
description: 这是我新 Astro 博客的第一篇文章。
image: ./cover.jpg
tags: [Foo, Bar]
category: 前端
draft: false
---
```
| 属性         | 
描述                                                                                                                                                                                                 |
|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `title`     | 文章的标题。                                                                                                                                                                                         |
| `published` | 文章发布的日期。                                                                                                                                                                                     |
| `description` | 文章的简短描述。显示在索引页面上。                                                                                                                                                        |
| `image`     | 文章封面图片的路径。<br/>1. 以 `http://` 或 `https://` 开头：使用网络图片<br/>2. 以 `/` 开头：`public` 目录中的图片<br/>3. 没有前缀：相对于 Markdown 文件的相对路径 |
| `tags`      | 文章的标签。                                                                                                                                                                                         |
| `category`  | 文章的分类。                                                                                                                                                                                         |
| `draft`     | 如果这篇文章还是草稿，它不会被显示。                                                                                                                                                         |

## 放置文章文件的位置



你的帖子文件应该放在  `src/content/posts/` directory. 目录中。你也可以创建子目录来更好地组织你的文章和资源。

```
src/content/posts/
├── post-1.md
└── post-2/
    ├── cover.png
    └── index.md
```
