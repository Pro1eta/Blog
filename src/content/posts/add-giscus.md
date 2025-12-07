---
title: 为 Fuwari 加入 Giscus 评论系统
published: 2025-07-23
description: ""
image: ""
tags:
  - Blogging
category: 站务
draft: false
pinned: false
lang: zh_CN
series: 博客改造
---
## 安装 Giscus App

[Giscus](https://github.com/giscus/giscus) 是利用 [GitHub Discussions](https://docs.github.com/en/discussions) 实现的评论系统，轻量级、配置容易而且是开源的。

在 [Giscus App](https://github.com/apps/giscus) 为你的 github 安装 giscus。安装时可以选择对所有仓库，也可以先看后文，创建专用于 giscus 的仓库。

![b118bea373e6c2613639327f2aa2d07c](https://image.proleta.org/2025/12/b118bea373e6c2613639327f2aa2d07c.webp)

## 配置 giscus

创建 giscus 连接到的仓库。确保：
1. 该仓库是[公开](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility#making-a-repository-public)的，否则访客将无法查看 discussion。
2. giscus app 已安装，否则访客将无法评论和回应。
3. [Discussions 功能](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/enabling-or-disabling-github-discussions-for-a-repository)已在你的仓库中启用。

> [!TIP]
> 博客的仓库与 Discussion 的仓库不必是同一个。

打开你的仓库。按照 `Setting` -> `Features` -> `Discussion`，勾选它。访问 [giscuss](https://giscus.app/) 的配置页面。后面按说明操作，无特殊要求就按照默认选择。

- Discussion 分类：建议选择其推荐的 Annoucements。

![e2496e3f2dffa811a6e266d71168daac](https://image.proleta.org/2025/12/e2496e3f2dffa811a6e266d71168daac.webp)

- 特性：按照需求选。
- 主题：按照需求选。

## 启用 giscus

```astro
<script src="https://giscus.app/client.js"
        data-repo="[在此输入仓库]"
        data-repo-id="[在此输入仓库 ID]"
        data-category="[在此输入分类名]"
        data-category-id="[在此输入分类 ID]"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="preferred_color_scheme"
        data-lang="zh-CN"
        crossorigin="anonymous"
        async>
</script>
```

将其给出的代码加入到对应的页面即可，例如加入到**文章**界面，需要加到 `</MainGridLayout>` 的上方。

```html title="src/pages/posts/[...slug].astro"
<script src="https://giscus.app/client.js"
        data-repo="[在此输入仓库]"
        data-repo-id="[在此输入仓库 ID]"
        data-category="[在此输入分类名]"
        data-category-id="[在此输入分类 ID]"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="preferred_color_scheme"
        data-lang="zh-CN"
        crossorigin="anonymous"
        async>
</script>

</MainGridLayout>
```

效果：

![c71fbb620c40b50881e37e40ff206308](https://image.proleta.org/2025/12/c71fbb620c40b50881e37e40ff206308.webp)

加入到**关于**界面，则是 `src/pages/about/`。以此类推。

## 使评论界面适应主题明暗

>[!NOTE]
>参见这篇[文章](https://ikamusume7.org/posts/frontend/comments_with_darkmode/)，感谢作者伊卡。

1.  在 `src\components\widget` 之下新建 `Comments.svelte` 文件。标注的部分替换为读者自己的 giscus 代码，其中第 12 行从原先设置的替换为 `data-theme={$mode === DARK_MODE ? 'dark' : 'light'}`。

```astro title="src\components\widget" {2-11} {13-16}
<section>
    <script src="https://giscus.app/client.js"
        data-repo="[在此输入仓库]"
        data-repo-id="[在此输入仓库 ID]"
        data-category="[在此输入分类名]"
        data-category-id="[在此输入分类 ID]"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme={$mode === DARK_MODE ? 'dark' : 'light'}
        data-lang="zh-CN"
        crossorigin="anonymous"
        async>
    </script>
</section>

<script>
import { AUTO_MODE, DARK_MODE } from '@constants/constants.ts'
import { onMount } from 'svelte'
import { writable } from 'svelte/store';
import { getStoredTheme } from '@utils/setting-utils.ts'
const mode = writable(AUTO_MODE)
onMount(() => {
  mode.set(getStoredTheme())
})

function updateGiscusTheme() {
  const theme = document.documentElement.classList.contains('dark') ? 'dark' : 'light'
  const iframe = document.querySelector('iframe.giscus-frame')
  if (!iframe) return
  iframe.contentWindow.postMessage({ giscus: { setConfig: { theme } } }, 'https://giscus.app')
}

const observer = new MutationObserver(updateGiscusTheme)
observer.observe(document.documentElement, { attributes: true, attributeFilter: ['class'] })

window.onload = () => {
  updateGiscusTheme()
}
</script>
```

2. 修改 `src\pages\about.astro` 中的代码。

引入 `Comments` 组件

```astro title="src\pages\friends.astro" ins={8}
---

import { getEntry, render } from "astro:content";
import Markdown from "@components/misc/Markdown.astro";
import I18nKey from "../i18n/i18nKey";
import { i18n } from "../i18n/translation";
import MainGridLayout from "../layouts/MainGridLayout.astro";
import Comments from '@components/widget/Comments.svelte'

const aboutPost = await getEntry("spec", "about");
```

用组件代码代替原先的代码即可。

```astro title="src\pages\friends.astro" ins={16} del={1-15}
<script src="https://giscus.app/client.js"
    data-repo="Pro1eta/Blog-Discussion"
    data-repo-id="R_kgDOPP6WRg"
    data-category="Announcements"
    data-category-id="DIC_kwDOPP6WRs4CtTMh"
    data-mapping="pathname"
    data-strict="0"
    data-reactions-enabled="1"
    data-emit-metadata="0"
    data-input-position="top"
    data-theme="preferred_color_scheme"
    data-lang="zh-CN"
    crossorigin="anonymous"
    async>
</script>
<Comments client:only="svelte"></Comments>

</MainGridLayout>
```

其它界面以此类推。
