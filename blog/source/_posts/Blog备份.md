---
title: Blog备份
date: 2020-01-31 11:56:45
categories: STUDY
tags: Blog
---

### 安装node
mac电脑下
```
brew update
node -v 确认未安装
brew install node
node -v 查看node版本
npm -v 查看npm版本
```

问题一：出现node -v有node版本，但是npm -v没有版本

```
brew uninstall node
brew install nvm
brew install node
node -v 查看node版本
npm -v 查看npm版本
```

### clone blog和next主题

```
git clone https://github.com/Annashuo/blog_source.git
```

### 重新渲染hexo并测试

```
在blog文件夹中
npm install
hexo g
hexo s
然后在localhost:4000查看
```





