---
layout: post
title:  vue ssr 简介(译)
# date: {site.time}
categories: translate
---

### Vue.js Server-Side Rendering Guide

### Vue.js 服务端渲染指南

###### **Note:** this guide requires the following minimum versions of Vue and supporting libraries:

###### **注：**该指南至少需要的最低版本的 Vue 核心库和支持库：

###### vue & vue-server-renderer >= 2.3.0

###### vue-router >= 2.5.0

###### vue-loader >= 12.0.0 & vue-style-loader >= 3.0.0

If you have previously used Vue 2.2 with SSR, you will notice that the recommended code structure is
now [a bit different](https://ssr.vuejs.org/en/structure.html) (with the new [runInNewContext](https://ssr.vuejs.org/en/api.html#runinnewcontext) option set to false
).

若你已经用 Vue 2.2 做服务端渲染（SSR）, 你会发现建议代码实践的提示现在[有些不同](https://ssr.vuejs.org/en/structure.html)了(当[runInNewContext](https://ssr.vuejs.org/en/api.html#runinnewcontext)选项设置为 false 的时候)

Your existing app should continue to work, but it's recommended to migrate to the new recommendations.

你的应用会继续工作，但是规范将会迁移到新的推荐规范。

### What is Server-Side Rendering (SSR)?

### 什么是服务端渲染（SSR）？

Vue.js is a framework for building client-side applications.

Vue.js 是构建客户端应用的框架。

By default, Vue components produce and manipulate DOM in the browser as output.

默认，Vue 组件在浏览器端输出和操作 DOM。

However, it is also possible to render the same components into HTML strings on the server,send them directly to the browser,and finally "hydrate" the static markup into a fully interactive app on the client.

然而，同样可以在服务端渲染相同组件到 HTML 里面，把他们直接发送到浏览器，最后在客户端把静态标签变为富交互的应用。

A server-rendered Vue.js app can also be considered "isomorphic" or "universal",in the sense that the majority of your app's code runs on both the server **and** the client.

一个服务端渲染的 Vue.js 应用能被认为是同构的或者是通用的，你应用的大部分代码是运行在服务端和客户端的。

### Why SSR?

### 为什么要用 SSR？

Compared to a traditional SPA (Single-Page Application),the advantage of SSR primarily lies in:

和传统的单页应用相比，SSR 的主要优势有：

Better SEO, as the search engine crawlers will directly see the fully rendered page.

更好的 SEO，搜索引擎的爬虫能直接看到完整的渲染页面。

Note that as of now, Google and Bing can index synchronous JavaScript applications just fine.

到目前为止，google 和 bing 可以很好的索引和同步 JavaScript 应用。

Synchronous being the key word there.

同步是关键。

If your app starts with a loading spinner, then fetches content via Ajax,the crawler will not wait for you to finish.

如果你的应用在启动的时候有个载入列表，通过 AJAX 获取内容，爬虫可不会等着你完成。

This means if you have content fetched asynchronously on pages where SEO is important,SSR might be necessary.

这意味着在重点 SEO 的页面是异步加载的，那么服务器渲染是必然的。

#### Faster time-to-content,

#### 更快的内容加载，

#### especially on slow internet or slow devices.

#### 特别是在低速网络或者运行慢的设备上。

Server-rendered markup doesn't need to wait until all JavaScript has been downloaded and executed to be
displayed, so your user will see a fully-rendered page sooner.

服务器端显示不需要等到所有 js 下载完成和执行才显示，所以你的用户将很快看到整个页面。

This generally results in better user experience, and can be critical for applications where time-to-content is directly associated with conversion rate.

这个结果在用户体验方面是更好的，也能用于评定渲染时间直接关系到转换率的应用。

### There are also some trade-offs to consider when using SSR:

### 在用 SSR 的时候,有几点需要权衡：

Development constraints. Browser-specific code can only be used inside certain lifecycle hooks;some external libraries may need special treatment to be able to run in a server-rendered app.

开发限制。依赖浏览器特性的代码只能在内部关键的生命周期钩子上；一些外部库在服务端渲染应用上也需要特殊处理。

More involved build setup and deployment requirements.

更多的构建设置和发布需求的耦合。

Unlike a fully static SPA that can be deployed on any static file server,a server-rendered app requires an environment where a Node.js server can run.

不在像完整个的静态单页应用该可以被发布在任何静态服务器上，服务端渲染应用更需要一个 node 服务环境。

### More server-side load.

### 更多的服务端加载。

Rendering a full app in Node.js is obviously going to be more CPU-intensive than just serving static files,so if you expect high traffic, be prepared for corresponding server load and wisely employ caching strategies.

node 渲染一个完整应用，光服务静态文件就需要更多的密集型 CPU。因此如果你期望有高通信，就需要准备相应的服务载入和合理的使用缓存策略。

Before using SSR for your app, the first question you should ask it whether you actually need it.

在应用 SSR 之前，先问问自己是否真的需要。

It mostly depends on how important time-to-content is for your app.

主要依赖于渲染时间对应用的重要程度。

For example, if you are building an internal dashboard where an extra few hundred milliseconds on initial load doesn't matter that much, SSR would be an overkill.

例如，你在做一个内部仪表盘，额外初始化的时候额外多几百号面也没什么影响，用 SSR 就过分了。

However, in cases where time-to-content is absolutely critical, SSR can help you achieve the best possible initial load performance.

然而，渲染时间是必要的评定标准，SSR 将帮助你在初始化表现上达到最好的可能。

### SSR vs Prerendering

If you're only investigating SSR to improve the SEO of a handful of marketing pages (e.g. /
, /about
, /contact
, etc), then you probably want **prerendering** instead.

如果你只是需要 SSR 在少数交易页面的 SEO 提升，那么预加载是替代方案。

Rather than using a web server to compile HTML on-the-fly, prerendering simply generates static HTML files for specific routes at build time.

相比于网页编译即时 HTML，在构建的时候预加载可以以简单方式生成特殊路由的静态 HTML 文件。

The advantage is setting up prerendering is much simpler and allows you to keep your frontend as a fully static site.

设置预加载的优势是更简单，允许你保持前端是一个完整的前端站点。

If you're using Webpack, you can easily add prerendering with the [prerender-spa-plugin](https://github.com/chrisvfritz/prerender-spa-plugin).

如果你用 Webpack，你用 [prerender-spa-plugin](https://github.com/chrisvfritz/prerender-spa-plugin)就可以轻易的增加预加载功能。

It's been extensively tested with Vue apps - and in fact, [the creator](https://github.com/chrisvfritz) is a member of the Vue core team.

事实上，这已经被广泛的应用在 Vue 应用上，[创造者](https://github.com/chrisvfritz)来自于 Vue 核心代码团队。

### About This Guide

### 关于这份指南

This guide is focused on server-rendered Single-Page Applications using Node.js as the server.

这份指南专注于用 Node 作为服务器的服务器端渲染的单页应用。

Mixing Vue SSR with other backend setups is a topic of its own and is not covered in this guide.

在其他后端服务用 Vue SSR 是另外一个话题，本指南将不会包含这方面的内容。

This guide will be very in-depth and assumes you are already familiar with Vue.js itself, and have decent working knowledge of Node.js and webpack.

这份指南非常深入解析 Vue SSR，是你以已经熟悉 Vue.js,掌握 node 和 webpack 的相关知识为前提。

If you prefer a higher-level solution that provides a smooth out-of-the-box experience, you should probably give [Nuxt.js](http://nuxtjs.org/) a try.

如果你更倾向于用过更高级的，提供封装完好的体验的解决方案，你应该试试 [Nuxt.js](http://nuxtjs.org/) 。

It's built upon the same Vue stack but abstracts away a lot of the boilerplate, and provides some extra features such as static site generation.

它同样建立在 Vue 的堆栈上面，但是抽了很多引用，并提供一些额外特性，例如静态站点迭代器。

However, it may not suit your use case if you need more direct control of your app's structure.

然而，如果你对你的应用该结构需要更多直接操作，[Nuxt.js](http://nuxtjs.org/) 可能不适合。

Regardless, it would still be beneficial to read through this guide to better understand how things work together.

无论如何，通过阅读本指南有助于理解所有事务是如何一起共工作的。

As you read along, it would be helpful to refer to the official [HackerNews Demo](https://github.com/vuejs/vue-hackernews-2.0/), which makes use of most of the techniques covered in this guide.

就像你看到的，参考官方案例[HackerNews Demo](https://github.com/vuejs/vue-hackernews-2.0/)，因为里面对指南里涉及的大多数技术的用法都有包含。

Finally, note that the solutions in this guide are not definitive - we've found them to be working well for us, but that doesn't mean they cannot be improved.

最后，注意指南里的解决方案不是一定的，虽然对于我们来说他们工作的很好，并不意味着它们是不可改进的。

They might get revised in the future - and feel free to contribute by submitting pull requests!

他们在未来可能会被修改，欢迎提交 pr 来提交贡献。
