---
layout: post
title: 在window安装Jekyll
# date: {site.time}
categories: Jekyll
---

## 准备

Ruby环境：
- [Ruby](https://rubyinstaller.org/downloads/) ，官方推荐安装<strong>Ruby+Devkit 2.4.X</strong>,保证扩展库的可用性

Python环境（安装python环境是为了提供代码高亮的支持，没需求的可以忽略）：
- [Python](https://www.python.org/downloads/windows/), 根据自己系统，我选择的是<strong>Windows x86-64 executable installer</strong>

## 步骤

1. 打开下载的Ruby的包，选下路径，一路下一步

2. 安装Jekyll：

```bash
gem install Jekyll --version=[version]
```

版本号根据需要选择，不加就安装最新的

3. 安装bundle：

```bash
gem install bundle --version=[version]
```

Jekyll官方文档已经明确说明，版本大于3.8的需要安装bundle

3. 安装成功，更新gem

```bash
gem update
```

这里是我在安装的时候遇到的，Jekyll的依赖出现各种版本不匹配或者缺失的问题

4. gem更新完成，可以使用Jekyll的命令，例如：

```bash
jekyll serve 
```

如果还会出现报错，可以使用：

```bash
bundle exec jekyll serve  
```

后续其他命令也会需要加上<strong>bundle exec</strong>

到这里Jekyll已经可以使用了，如果还需要增加代码高亮的支持，那么：

5. 安装python，安装包一路下一步，顶多改改安装路径

6. 安装Pygments：

```bash
pip install Pygments
```

到此安装结束，可以愉快的写博客了。

## 参考

- [Jekyll官方文档](https://jekyllrb.com/docs/windows/)，官方推荐的是on Ubuntu on Windows,要硬装在windows上就按上面的步骤来吧
- [颜大大的文档](http://yanhaijing.com/jekyll/2011/12/30/run-jekyll-on-window/)，年份久了点
- [官方ISSUE](https://github.com/jekyll/jekyll/issues/6227)，主要就是会遇到需要利用bundle exec的命令的问题
- [Gem指令](https://blog.csdn.net/orangleliu/article/details/25080309)，可以了解一下
- [markdown代码高亮](http://istoney.github.io/jekyll/2016/03/10/set-markdown-hightlighter)，我是不喜欢minia（Jekyll默认主题）的高亮样式，所以找了一个修改方案






