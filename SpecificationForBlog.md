# 文档说明

- 2025-09-15
	现实生活中有太多事情占用了精力，等到进行写作的时候，对于 posts 的格式、如何上传 posts、如何管理 Blog 项目等细节已经记忆不清楚了，重新学习浪费了本就不多的残余精力。

	为了让精力和时间集中到内容本身的创作之上，遂写下这篇文档记录一些必要细节。

# 写作流程

## 草稿阶段

出于进行备份的需要，每篇文章都会保留草稿。进入 Blog 主文件夹，打开终端，输入

```bash
pnpm new-post <filename>
```

那么 `Blog` 的位置是 `xxx/Blog/src/content/posts`. 这时可以开始文档写作。

每个 Blog 的结尾默认用以下进行收尾：
> ***That's it.***
>
> 
>
> ***Have a nice day & Have yourself.***

## 备份阶段

将写作好的文档、保存图片的文件夹一同复制到 `posts-copy` 文件夹中。

这个阶段打算使用 bash 或者 python 自动化脚本进行。

## 上传图片

使用 Piclist 将所有本地图片更换为外链。然后删除本地保存图片的文件。

- `上传所有本地图片` 的操作目前无法实现！

## Build 并上传 Dist 至服务器

查看目前 blog 样式

```bash
pnpm dev
```

建构出 `dist`

```bash
pnpm build
```

我已经做好本地计算机和服务器的 ssh 连接，别名为 `myblog`，因此只需要输入以下指令，然后耐心等待即可

```bash
scp -r dist myblog:/apps/proletablog
```

顺便说一下，如果要连接服务器，只需要

```bash
ssh myblog
```

## 备份草稿内容至网盘

按时上传即可。

# Blog 项目管理和维护

posts 等非常具体的内容已经被我添加至 `.gitignore`，所以主要是 Blog 的管理问题。

## 同步 fuwari 的最新上游

一般来说，在本地的分支为 `diy`。`main` 被托管在 github 作为备份。

