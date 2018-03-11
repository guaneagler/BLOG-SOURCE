---
title: Hello Hexo
categories:
  - Hexo
tags:
  - hexo
  - hello-world
  - github-page
  - next
description: 创建hello hexo项目，将项目部署到GitHub，并通过GitHub Pages进行访问。更换主题为NexT，修改基本配置。
---

## Hello Hexo in Local.

### 安装环境

首先要安装git和nodejs
Mac环境的nodejs使用Homebrew来安装
``` bash
brew install node
```

### 安装hexo

``` bash
$ npm install -g hexo-cli
```
*我的mac环境安装过程中报了permission的错误，修改权限之后可以安装了*
```bash
$ sudo chown -R `whoami` /usr/local/lib/node_modules
```

### 初始化hexo项目

``` bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```

### 测试本地环境

``` bash
$ hexo server
```
访问[http://localhost:4000](http://localhost:4000)，看看hello world是否成功


## Hello Hexo in GitHub Pages.

### 创建一个仓库
在GitHub创建一个名字为:[username].github.io，不需要readme文件。
*[username] 是GitHub的username*

### 在hexo项目配置文件中添加deploy选项
``` yml
deploy:
  type: git
  repo: git@github.com:[username]/[username].github.io.git
  branch: master
```

### 生成hexo文件
``` bash
# 清除之前生成的文件
$ hexo clean
# 生成新的html文件
$ hexo publish
```

### 部署新生成的html到GitHub仓库
*部署前请确认本机的publish key已经配置到GitHub.*
``` bash
$ hexo deploy
```

### 配置GitHub Page
在新创建的GitHub仓库中，点击Settings；GitHub Pages部分的source选择master branch，保存配置

### 访问
访问[username].github.io，就可以看到自己初始化的hexo项目了

*GitHub Pages可以将自己的域名绑定到github配置上，在source配置的下面，绑定了是http的不喜欢，还没找到解决方案*


## 修改主题和配置

### 安装next主题
``` bash
$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

### 启用主题
在项目的配置文件中添加
``` yml
theme: next
```

### 验证主题
开启服务，访问localhost:4000查看是否修改成功

> 参考
>> [Hexo 文档](https://hexo.io/docs/)
>> [GitHub Pages](https://pages.github.com/)
>> [NexT主题帮助](http://theme-next.iissnan.com/getting-started.html)
>> [主题配置](http://theme-next.iissnan.com/theme-settings.html)
