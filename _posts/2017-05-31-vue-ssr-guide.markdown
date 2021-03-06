---
layout: post
title: vue ssr 基本用法(译)
# date: {site.time}
categories: translate
---

### Installation

### 安装

```bash
npm install vue vue-server-renderer --save
```

We will be using NPM throughout the guide, but feel free to use [Yarn](https://yarnpkg.com/en/) instead.

整个指南我们都用 NPM，不过也可以用 [Yarn](https://yarnpkg.com/en/) 代替。

#### Notes

#### 注意

It's recommended to use Node.js version 6+.
vue-server-renderer
and vue
must have matching versions.

建议 Node v6 以上。
vue-server-renderer
和 vue
必须用对应的版本。
vue-server-renderer
relies on some Node.js native modules and therefore can only be used in Node.js.

vue-server-renderer 依赖 Node 本身的模块，因此只能应用在 node 上面。

We may provide a simpler build that can be run in other JavaScript runtimes in the future.

我们在未来会提供可以跑在其他 js 解析器的更简单的构建。

### Rendering a Vue Instance

### 渲染 Vue 实例

```js
// Step 1: Create a Vue instance
// 第一步： 创建Vue实例
const Vue = require('vue');
const app = new Vue({
  template: `<div>Hello World</div>`
});

// Step 2: Create a renderer
// 第二步：创建渲染器
const renderer = require('vue-server-renderer').createRenderer();

// Step 3: Render the Vue instance to HTML
// 第三步：把Vue实例渲染到HTML
renderer.renderToString(app, (err, html) => {
  if (err) throw err;
  console.log(html);
  // => <div data-server-rendered="true">hello world</div>
});
```

### Integrating with a Server

### 服务端集成

It is pretty straightforward when used inside a Node.js server, for example [Express](https://expressjs.com/):

在 Node 里面使用相当的简单，例如[Express](https://expressjs.com/):

```bash
npm install express --save
```

```js
const Vue = require('vue');
const server = require('express')();
const renderer = require('vue-server-renderer').createRenderer();
server.get('*', (req, res) => {
  const app = new Vue({
    data: { url: req.url },
    template: `<div>The visited URL is: {{ url }}</div>`
  });
  renderer.renderToString(app, (err, html) => {
    if (err) {
      res.status(500).end('Internal Server Error');
      return;
    }
    res.end(
      ` <!DOCTYPE html> <html lang="en"> <head><title>Hello</title></head> <body>${html}</body> </html> `
    );
  });
});
server.listen(8080);
```

### Using a Page Template

### 使用页面模板

When you render a Vue app, the renderer only generates the markup of the app.

当你渲染 Vue 应用的时候，渲染器只生成应用的标记。

In the example we had to wrap the output with an extra HTML page shell.

在案例里，我们用一个额外的 HTML 页面可以包裹输出。

To simplify this, you can directly provide a page template when creating the renderer.

为简化这步操作，你可以在创建渲染器的时候直接提供页面模板。

Most of the time we will put the page template in its own file, e.g. index.template.html:

大多数时候我们把页面模板放在它自己的文件里，例如 index.template.html：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Hello</title>
  </head>
  <body>
    <!--vue-ssr-outlet-->
  </body>
</html>
```

Notice the <!--vue-ssr-outlet--> comment -- this is where your app's markup will be injected.

#### 注意<!--vue-ssr-outlet-->语句，这里将是应用标记要注入的地方。

We can then read and pass the file to the Vue renderer:

我们将读取并传送文件给 Vue 的渲染器：

```js
const renderer = createRenderer({
  template: require('fs').readFileSync('./index.template.html', 'utf-8')
});
renderer.renderToString(app, (err, html) => {
  console.log(html);
  // will be the full page with app content injected.
});
```

### Template Interpolation

### 模板插入

The template also supports simple interpolation.

模板支持简单插入

Given the following template:

上例子：

```html
<html>
 <head>
  <title>title</title>
  <!-- meta -->
 </head>
 <body>
 <!--vue-ssr-outlet-->
 </body>
</html>
```

We can provide interpolation data by passing a "render context object" as the second argument to renderToString
:

我们提供通过传递‘渲染上下文对象’作为第二参数的插入数据：

```js
const context = { title: 'hello', meta: ` <meta ...> <meta ...> ` };
renderer.renderToString(app, context, (err, html) => {
  // page title will be "hello"
  // with meta tags injected
});
```

The context object can also be shared with the Vue app instance, allowing components to dynamically register data for template interpolation.

上下文对象可以和 Vue 应用实例共享，允许组件动态的给模板插入注册数据。

In addition, the template supports some advanced features such as:

此外，模板支持一些高级特性，例如：

Auto injection of critical CSS when using \*.vue components;

在使用 vue 单页组件的时候自动注入 css

Auto injection of asset links and resource hints when using clientManifest;

当使用 clientManifest 的时候，自动注入资源链接和资源提示；

Auto injection and XSS prevention when embedding Vuex state for client-side hydration.

当在客户端混合 Vuex 买点的时候，自动注入和阻止 XSS。

We will discuss these when we introduce the associated concepts later in the guide.

在指南里，我们将在介绍聚合概念的时候继续讨论。
