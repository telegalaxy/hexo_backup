---
title: 一种很新的博客搭建方式，Codespace + Github Action 真低代码建站
cover: 'https://static.tblog.life/hugo-g1.png'
categories: 折腾分享
tags:
  - 博客
  - 教程
cc: 原创
abbrlink: 7891
excerpt: >-
  这篇文章介绍了如何使用Hugo工具搭建博客，通过简单的步骤介绍了从创建主题到配置博客、部署至Github和Netlify的流程。作者提供了一个简单易操作的方案，适合小白快速上手。需要准备一台能打开Github的电脑和学习Markdown的能力。
date: 2019-09-07 00:00:00
---
## 文章的初衷

博客的目的是写作而不是展示自己技术的地方，所以本文以方便快速的角度出发，带小白们认识一下hugo的魅力。本文的方案全网最简，但是有一定缺陷。

## 需要准备的

* 能快速打开Github的电脑（推荐挂梯子，否则写文章容易丢失）
* 学习Markdown（很简单，工具推荐Typora，0学习成本，像编辑Word文件一样编辑Markdown）

## 搭建博客

![Step 1](https://static.tblog.life/img/202304081650756.png)

2. 完成操作后，点击<kbd>Code</kbd>创建新的Codespace
![Sept 2](https://static.tblog.life/img/202304081652357.webp)

## 配置博客

打开Codespace，打开`config/_default/`这里的文件就是博客配置。

### 修改config.toml

下面是各行需要修改的地方

| 名称                   | 需要的操作                                                   |
| ---------------------- | ------------------------------------------------------------ |
| baseurl                | 修改为自己博客的网址，格式：（//:网址）                      |
| languageCode           | 修改为网站的语言，这里写zh-cn                                |
| paginate               | 每页显示文章数，保持原样即可                                 |
| title                  | 网站标题，如"百度"                                           |
| DefaultContentLanguage | 支持en, fr, id, ja, ko, pt-br, zh-cn, zh-tw, es, de, nl, it, th, el, uk, ar。填写zh-cn |

其他的不用管

### 修改menu.toml

在基础上进行修改即可

### 修改params.toml

- footer

  since:填写博客创建日期，如2023

  customText:自定义页脚文字，效果见本站页脚

- dateFormat

  不用改，改了可能会出错

- sidebar

  emoji:头像右下角的图标，填写emoji即可

  subtitle:博客头像下的一段文字，不建议太长

- sidebar.avatar

  **local:头像是否在本地，如果在本地请填写true！**

  src:头像链接

其他的不用管

## 部署博客

修改`.github/workflows`内的deploy.yml(没有的新建)，删除内容后填写：

```yaml
name: Deploy to Github Pages
on:
    push:
        branches: [master]
    pull_request:
        branches: [master]
jobs:
    build:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/checkout@v2
            - name: Cache Hugo resources
              uses: actions/cache@v2
              env:
                  cache-name: cache-hugo-resources
              with:
                  path: resources
                  key: ${{ env.cache-name }}
            - uses: actions/setup-go@v2
              with:
                  go-version: "^1.17.0"
            - run: go version
            - name: Cache Go Modules
              uses: actions/cache@v2
              with:
                  path: |
                      ~/.cache/go-build
                      ~/go/pkg/mod
                  key: ${{ runner.os }}-go-${{ hashFiles('**/go.sum') }}
                  restore-keys: |
                      ${{ runner.os }}-go-
            - name: Setup Hugo
              uses: peaceiris/actions-hugo@v2
              with:
                  hugo-version: "latest"
                  extended: true
            - name: Build
              run: hugo --minify --gc
            - name: Deploy 🚀
              uses: JamesIves/github-pages-deploy-action@v4
              with:
                  branch: gh-pages
                  folder: public
                  clean: true
                  single-commit: true
```

新建分支

![New branch](https://static.tblog.life/img/202304081652867.webp)

点击 New branch

![Create a branch](https://static.tblog.life/img/202304081655231.png)

打开仓库>settings>Actions>General，按图修改

![Action](https://static.tblog.life/img/202304081656265.webp)

上传到Github

输入

```bash
git init
git add .
git commit -m "commit"
git push origin master
```

## 部署至Netlify

先注册一个账号，然后新建站点，选择从Git仓库导入

![导入](https://static.tblog.life/img/202304081656546.webp)

点击Github后等弹窗显示文字，关闭即可看到仓库，选择博客仓库，选择gh-pages

![示意图](https://static.tblog.life/img/202304081657681.webp)

其他的不用管点Deploy site。到首页访问链接即可看到博客（每次更新博客不用手动重新部署，过程是自动的）