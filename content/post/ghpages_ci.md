---
title: "HUGO+Github+Github Actions实现个人博客搭建和部署"
date: 2020-06-23T22:59:27+08:00
draft: false
tags: ["GitHub Actions", "CI/CD"]
categories: ["GitHub", "踩坑系列"]
---

**前言：**这还要说到昨晚铁群提议我将个人博客部署到GitHub上，他说用GitHub Actions很方便就可以部署，就不用每次写完博客还要手动`hugo`生成，然后`push`到远程。我想，确实我之前也想过这样很麻烦诶，每次写完都要手动生成最新的public文件夹，于是我就按照他发给我的教程开启了部署之路。然而谁也没想到，部署之路漫漫其修远兮，从我一个人晚上11点多弄到了凌晨1点，然后又叫铁群帮我一起弄到了🕛凌晨4点才算完成。

## HUGO搭建个人博客

首先要安装好HUGO，配置环境变量等，总之用HUGO搭建博客这个过程还是很简单的，这里不再赘述。

## GitHub Actions

### 简介

这里主要介绍一下GitHub Actions中的概念和关系，其中的概念主要有workflow（工作流）、job（任务）、step（步骤）和action（动作或叫命令）。一个workflow就是一次持续集成运行的过程，而它是由一个或多个job构成的，每个job又有多个step，每个step会依次完成一个或多个action。

### 举个栗子走一遍流程

这里，我有一个源码的远程仓库（命名为my-blog），还有一个显示博客的仓库（命名为username.github.io）。我希望每次将我的博客源码`push`到my-blog时，它能够自动将我的更新发布在`https://username.github.io/`上。

首先，实现一个workflow，是需要workflow文件的，也就是用YAML语言写的配置文件。配置文件存储在代码仓库的`.github/workflow`文件夹中，后缀名为`.yml`。在Marketplace中针对GitHub Pages的GitHub Actions有很多，这里参考使用[peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)。

默认情况下，我们发布的仓库也正是这个action运行的仓库，但是如果我们希望将更新发布在另一个仓库，就需要用`external_repository: username/external_repo`。

然后，将更新发布到GitHub仓库是需要密钥的，在配置文件中涉及到三种token，分别为`github_token`、`deploy_key`和`personal_token`。而`GITHUB_TOKEN`是无权限访问外部仓库的，因此在这里只能用另外两个之一。

如果用`DEPLOY_KEY`，
+ 首先生成[SSH deploy key](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-create-ssh-deploy-key)，会得到一对密钥；
+ 然后在源码仓库**my-blog**中进入**Settings-Secrets**加入私钥，命名为`ACTIONS_DEPLOY_KEY`（可为其他，但要与deploy_key中的值保持一致）；
+ 最后在发布仓库**username.github.io**中进入**Settings-Deploy key**加入公钥，并且选择**Allow write access**。

如果用`PERSONAL_TOKEN`，按照[官方文档](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line)生成token，然后将其加入源码仓库中的**Secrets**和命名即可。

```YAML
# This is a basic workflow to help you get started with Actions

name: gh-pages CI

on:
  push:
    branches: 
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:

    - name: Checkout
      uses: actions/checkout@v2
      with: 
        submodules: true

    - name: Setup Hugo
      uses: peaceiris/actions-hugo@v2
      with:
        hugo-version: '0.71.1'
          
    - name: Build
      run: hugo

    - name: Deploy
      uses: peaceiris/actions-gh-pages@v3
      with:
        personal_token: ${{ secrets.ACTION_DEPLOY_ACCESS_TOKEN }}
        external_repository: username/username.github.io
        publish_branch: master
        publish_dir: ./public
        commit_message: ${{ github.event.head_commit.message }}
```

## 总结

上面这个配置文件折磨得我上下眼皮不能在想在一起的时候在一起，就是因为下面这段话。我错用了`github_token`这个key，将生成的**personal access token**放在了错误的key下。
> Be careful, GITHUB_TOKEN has no permission to access to external repositories. Please create a personal access token and set it to personal_token like personal_token: ${{ secrets.PERSONAL_TOKEN }}.

其中挺简单的，但是我却整了这么久，还害的铁群不能早点儿睡，真是十分抱歉。希望自己以后吸取教训，不要盲人摸象。
