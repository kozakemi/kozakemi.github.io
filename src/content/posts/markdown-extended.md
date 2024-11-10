---
title: Markdown 扩展功能
published: 2020-05-01
description: '了解更多关于 Fuwari 中的 Markdown 功能'
image: ''
tags: [Demo, Example, Markdown, Fuwari]
category: 'Examples'
draft: false 
---

## GitHub 仓库卡片
您可以在页面加载时添加动态卡片，链接到 GitHub 仓库，仓库信息将从 GitHub API 中提取。

::github{repo="Fabrizz/MMM-OnSpotify"}

使用代码 ::github{repo="<owner>/<repo>"} 创建一个 GitHub 仓库卡片。

```markdown
::github{repo="saicaca/fuwari"}
```

## 提示框

支持以下类型的提示框：note tip important warning caution

:::note
突出显示用户即使在浏览时也应该注意的信息。
:::

:::tip
帮助用户更加成功的可选信息。
:::

:::important
用户成功所需的至关重要的信息。
:::

:::warning
由于潜在风险，需要立即引起用户注意的关键内容。
:::

:::caution
某项操作的负面潜在后果。
:::
### 基本语法

```markdown
:::note
突出显示用户即使在浏览时也应该注意的信息。
:::

:::tip
帮助用户更加成功的可选信息。
:::
```

### 自定义标题

提示框的标题可以自定义。

:::note[我的自定义标题]
这是一个带有自定义标题的提示。
:::

```markdown
:::note[我的自定义标题]
这是一个带有自定义标题的提示。
:::
```

### GitHub 语法

> [!TIP]
> [The GitHub syntax](https://github.com/orgs/community/discussions/16925) 也被支持。

```
> [!NOTE]
> [!NOTE]
> GitHub 语法也被支持。

> [!TIP]
> GitHub 语法也被支持。
```