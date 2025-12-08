---
title: 【2.项目管理和自动化】如何部署并管理你的博客项目？
published: 2025-12-08
description: 利用 git 管理你的项目并自动化部署到服务器
image: ""
tags:
  - ECS
  - git
  - Blogging
category: 站务
draft: false
pinned: false
lang: zh_CN
series: 博客改造
---
# 从原项目获取更新

有些时候原来的项目会添加一些新功能，或者修一些 bug 什么的，可以添加上游远程仓库。

```bash
git remote -v
git remote add upstream https://github.com/saicaca/fuwari.git
```

同步上游更新：

```bash
git fetch upstream
git checkout main # 写你的分支名称
git merge upstream/main
```

处理完所有的合并冲突之后，推送更改即可：

```bash
git push origin main
```

一般来说写帖子不会非常频繁，加上你对原项目进行魔改，一个 `main` 分支就足够了，推送记录还是能保持简洁的。
# 用 obsidian 管理项目

[obsidian](https://obsidian.md/) 管理笔记以“仓库”为单位，比较契合博客的写作，将存帖子的根目录设置为 obsidian 仓库即可，对于 `fuwari` 则是 `your-proj-path/src/content`。一般来说我习惯这样设置：

```
├── posts/
├── posts-copy/
├── spec/
├── templates/
└── config.ts
```

`posts` 存放帖子，`spec` 存放关于、友链等界面。`posts-copy` 存放帖子的备份，`templates` 是帖子的模板。`config.ts` 是 `fuwari` 自动构建的配置文件。

只有 `posts` 和 `spec` 有必要上传至仓库。所以在 `.gitignore` 中可以这样设置，按需修改即可：

```text ins = {2-8}
<!-- your-proj-path/.gitignore --> 
# posts
/src/content/.obsidian
/src/content/.trash
/src/content/posts-copy
# 如果使用图床的话，存放图片的文件也不必上传
/src/content/posts/assets
/src/content/templates
```

我的工作原则是：越**简洁**越好，如无必要不添加额外功能，所以 obsidian 仅使用以下插件就OK：

- [Image Converter](https://github.com/xRyul/obsidian-image-converter)：处理图片路径、命名，并统一转换为 `.webp` 格式。
- [Image Upload Toolkit](https://github.com/addozhang/obsidian-image-upload-toolkit)：上传至图床。
- [Clear Unuserd Images](https://github.com/ozntel/oz-clear-unused-images-obsidian)：清除没有使用的图片。
- [Codeblocks Customizer](https://github.com/mugiwara85/CodeblockCustomizer)：为 obsidian 的代码块添加行号等。

为了避免 biome 令人抓狂的检查，请修改 `biome.json` 来忽略对应的文件。

```json ins = {10-15}
<!-- biome.json --> 
"files": {
	"ignoreUnknown": false,
	"includes": [
		"**",
		"!**/src/**/*.css",
		"!**/src/public/**/*",
		"!**/dist/**/*",
		"!**/node_modules/**/*",
		"!src/utils/tempCodeRunnerFile.ts",
		"!src/content/.obsidian/**",
		"!src/content/.trash/**",
		"!src/content/posts-copy/**",
		"!src/content/posts/assets/**",
		"!src/content/templates/**"
	]
},
```

# GitHub Actions 自动化部署（CI/CD）

## 服务器准备

首先按照[上一篇帖子](https://proleta.org/posts/manage-blogs-1/) 配置好 Nginx 服务器。

出于**安全性**的考虑（虽然没什么必要），我们还是在服务器上创建一个专门用于部署的普通用户，同时确保这个用户有写入权限。

```bash title = "Your VPS"
# 创建新用户
sudo adduser deployuser
# 切换到新用户
sudo -u deployuser -i
```

可以确认一下：

```bash title = "Your VPS"
whoami
```

我们先用 `exit` 退出服务器，然后再用管理员 `root` 登录 。

```bash title = "Your VPS"
# 为用户创建 .ssh 目录并设置权限
sudo mkdir -p /home/deployuser/.ssh
sudo chmod 700 /home/deployuser/.ssh
sudo chown -R deployuser:deployuser /home/deployuser/.ssh
```

`chmod` = change mode，改变目录的访问权限。三个数字从左到右分别表示：所有者 - 组 - 其它用户的权限。每个数字由以下的权限值相加而得：

- `4` = 读取 (r)
- `2` = 写入 (w)
- `1` = 执行 (x)

`chmod` = change owner，改变文件所有者和所属组。`-R` 递归地应用于目录以及所有子目录和文件，改变里面所有文件的所有权。

假设静态网页文件保存在了服务器的 `/websites/myblog` 。首先查看一下 `Nginx` 的进程以哪一个用户和组运行。
```bash title = "Your VPS"
ps aux | grep nginx
```

需要关注 `worker process` 前的信息，例如我的是：

```bash title = "Your VPS" wrap=false
root      385048  0.0  0.0  66884  2588 ?        Ss   Dec05   0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
www-data  385049  0.0  0.2  67192 10540 ?        S    Dec05   0:00 nginx: worker process
www-data  385050  0.0  0.3  68824 12520 ?        S    Dec05   0:05 nginx: worker process
www-data  385051  0.0  0.2  67324 10496 ?        S    Dec05   0:00 nginx: worker process
www-data  385052  0.0  0.2  67656 10764 ?        S    Dec05   0:00 nginx: worker process
root      427356  0.0  0.0   7008  2060 pts/1    S+   15:20   0:00 grep --color=auto nginx
```

接下来改变文件的所有者和所有组。`deployuser:www-data` 对应 `所有者:所有组`。

```bash title = "Your VPS"
sudo chown -R deployuser:www-data /websites/myblog
sudo chmod -R 775 /websites/myblog
```

执行完之后可以进入对应文件检查一下所有者和用户组：

```bash title = "Your VPS"
ls -l yourfile
```

`rsync` 是一个比较高效的文件同步工具，可以在服务器上安装一下。

```bash title = "Your VPS"
sudo apt update
sudo apt install rsync
```

## 生成 SSH 密钥对

回到本地机器，生成 ed25519 密钥。当然你用 RSA 这些也可以，注意设置合适的密钥位数就行。**不要额外设置 passphrase**。

```shell
ssh-keygen -t ed25519 -C "blog-deploy" -f ~/.ssh/blog-deploy-key
```

将公钥（public key）添加到服务器上，假设公网 IP 是`111.22.33.444`：

```shell
ssh-copy-id -i ~/.ssh/blog-deploy-key.pub deployuser@111.22.33.444
```

这一步手动配置也可以，参见[第1篇帖子](https://proleta.org/posts/manage-blogs-1/)。

## 使用 GitHub Secrets

GitHub Secrets 用键替换掉一些敏感信息。打开项目所在仓库，进入：

`Settings` -> `Secrets and variables`-> `Actions` -> `New repository secret`

分别添加以下键值（Name-Secret）：

- SSH_PRIVATE_KEY - 复制私钥文件 `blog-deploy-key.pub` 的全部内容，包括 `BEGIN...` 和 `END...`。
- SERVER_HOST - **服务器公网IP**或者域名。
- SERVER_USERNAME - 用于部署的服务器用户名，此处即 `deployuser`。
- SERVER_PORT - SSH 端口号，可在服务商后台查看，一般为22.
- KNOWN_HOSTS - 服务器的公钥信息。

**KNOWN_HOSTS** 是服务器的公钥信息。包含公钥，以及公钥通过哈希函数处理，得到的能唯一标识公钥身份的指纹。

在你**信任的机器**（比如你的本地电脑）中运行以下指令：

```shell
ssh-keyscan -H 111.22.33.444 | grep -v '^#'
```

`-H` 是使用哈希函数处理，`grep -v '^#'` 主要是为了去掉注释，复制输出中不以 `#` 开头的行即可。

一般来说，客户端（例如 GitHub Actions）持有私钥，服务端持有公钥，我们可以认为攻击者也持有公钥。

服务端认证客户端的身份是容易的，而客户端认证服务端的身份存在**中间人攻击**的风险。客户端验证服务端身份的手段只有公钥和私钥，而攻击者和服务端都持有公钥。确认身份后，客户端与攻击者进行密钥协商，攻击者可以获得了信息和数据。

客户端拥有 known hosts 之后，首先接收来自服务端的公钥。我们认为攻击者几乎不可能和**信任的机器**建立过连接。如果服务端提供的公钥和 known hosts 不匹配，说明服务端没有和**信任的机器**建立过连接，认为服务端身份已经改变。这样就一定程度上避免了中间人攻击。

>[!warning]
> 如果你的域名被服务商**代理**，SERVER_HOST 的值只能是**公网 IP**，或者将代理状态配置为`仅 DNS`。
> 
> 当流量经过代理时，SSH客户端实际上会尝试连接到代理服务（例如Cloudflare）的IP地址，而不是服务器的真实IP地址。由于代理服务并没有配置来监听或转发SSH端口的流量到源服务器，**SSH连接会失败**。

## 创建 Workflow 文件并推送

先在本地创建文件。

```bash
mkdir -p .github/workflows
touch .github/workflows/deploy.yml
```

实际上就是将构建静态网页的指令重新写一遍，根据所选框架的不同可以自行修改。以下是我的配置：

```yml


on:
  push:
    branches:
      - main 

jobs:
  deploy:
  # 在 Ubuntu 虚拟机上运行此作业
    runs-on: ubuntu-latest

    steps:
    # 步骤1: 检出仓库代码
      - name: Checkout code 
        uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '22'
      - name: Install pnpm CLI 
        run: npm install -g pnpm
        
      - name: Install dependencies
        run: pnpm install
         # 步骤2: 构建静态网站
      - name: Build Astro site
        run: pnpm run build
        # 步骤3: 使用 rsync 部署到服务器
      - name: Deploy with rsync 
        uses: easingthemes/ssh-deploy@v4.1.10
        with:
          SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }} # 获取私钥
          known_hosts: ${{ secrets.KNOWN_HOSTS }}  # 获取公钥指纹
          REMOTE_HOST: ${{ secrets.SERVER_HOST }}  # 获取IP/域名
          REMOTE_USER: ${{ secrets.SERVER_USERNAME }} # 获取部署用户名
          REMOTE_PORT: ${{ secrets.SERVER_PORT }} # 获取 SSH 端口
          SOURCE: "dist/"
          TARGET: "/websites/myblog"
          ARGS: "-avz --delete" 
          #archive, verbose, compress, 删除目标中不存在于源的文件
```

>[!TIP]
> 推送前请先在本地运行 `pnpm biome check ./src` 并对代码进行修改。Github Actions 构建失败多半是因为这个原因。
> 
> 最好不要在 Github Actions 上 debug，效率很低，让你的提交乱七八糟而且**很让人抓狂**。

添加提交并推送即可。

```bash
git add .
git commit -m "feat: setup github actions for deployment with ed25519"
git push origin main # 或你的主分支名
```

> 以上。
> 
> 如果要进一步避免直接使用公网IP。也可采用 Zero Trust Tunnel，不过这时候不再推荐使用 SSH。也就是说只是实现了自动化传输静态文件...有点多此一举的感觉，就不介绍了。

