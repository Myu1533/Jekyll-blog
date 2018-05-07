---
layout: post
title: 利用canvas将html生成为图片
# date: {site.time}
categories: html5
---

## 需求
1. HTML内容定制编辑后，把完整的HTML块生成成图片
2. 提供预览
3. 图片清晰

## 选型
1. 现成库直接上的原则：[html2canvas](https://github.com/niklasvh/html2canvas),star 10k+
需求1完美解决

2. 造轮子，自己定制一个简单的生成工具

## 实现
html2canvas就不细说了，google/baidu的出来很多采坑方案，总体使用没问题，稍微看一下官方的特性说明，一些css属性是不支持的。

### 下面是造轮子时间：
提供思路，细节还需慢慢完善

html/css：
```html
<style>
  .demo{
    position: relative;
    width: 300px;
    height: 500px;
    margin: 0 auto 10px;
    border: 1px solid #333;
  }
  .rect1{
    width: 30%;
    height: 40%;
    background-color: lightblue;
  }
  .rect2{
    position: absolute;
    top: 20%;
    right: 30%;
    width: 30%;
    height: 40%;
    background-color: lightcoral;
  }
  .rect3{
    position: absolute;
    top: 40%;
    width: 100%;
    height: 40%;
    background-color: lightgreen;
  }
  .btns{
    text-align: center;
  }
  .preview{
    text-align: center;
  }
</style>

<section class="demo">
  <div class="rect1"></div>
  <div class="rect2"></div>
  <div class="rect3"></div>
</section>
```

布局上涉及了基础定位，position定位，为了简化造轮子的过程，我把布局按层叠进行了顺序调整，有需要的在布局阶段需要额外的增加元素的层级排序。

预览：

![img](/assets/images/20180429/html-preview.jpg)

### javascript片段

#### 初始化
1. 需要根据目标HTML块的大小来设置canvas的大小
2. 将初始化好的canvas插入到body中，必要时设置隐藏
```js
function init(target) {
  this.targetDOMTree.target = document.querySelector(target);
  this.canvas = document.createElement('canvas');
  this.canvas.width = this.targetDOMTree.target.offsetWidth;
  this.canvas.height = this.targetDOMTree.target.offsetHeight;
  this.ctx = this.canvas.getContext('2d');
  document.querySelector('body').appendChild(this.canvas);
}
```

### 画到canvas
1. 取出html块的元素
2. 根据内容判断画图，画文字还是继续查找
3. 取得需要画到canvas的元素的必要css信息，如背景色，高宽，位置等
4. 画起来

代码片段提供简单的是否有文字的判断

```js
function renderToCanvas() {
  let allChildren = this.targetDOMTree.target.children;
  for (let i = 0, l = allChildren.length; i < l; i++) {
    if (!allChildren[i].innerTEXT) {
      this.ctx.save();
      this.ctx.fillStyle = window.getComputedStyle(allChildren[i], null).backgroundColor;
      this.ctx.fillRect(allChildren[i].offsetLeft, allChildren[i].offsetTop, allChildren[i].offsetWidth, allChildren[i].offsetHeight)
      this.ctx.restore();
    }
  }
}
```

### 生成图片

canvas.toDataURL(type, quality):

返回值：
  - 图片数据地址

参数：
  - type: 可以指定image/jpeg或者image/png，默认： image/png
  - quality: 图片质量，设置范围： 0~1.0

```js
function saveToImage() {
  return this.canvas.toDataURL();
}
```

### 结语

这是思路，因为项目比较赶，有些渲染部分都是写死的，有兴趣的可以直接参看html2canvas的源码，或者自己尝试。

#### TIPS
提供几条在实际项目中遇到的问题：

1. 需求2的图片预览：
    - pc无问题
    - android无问题
    - ios有问题：ios的安全机制，不允许直接使用修改`img src`的方式来改变图片，新建img标签也会有这个问题，hack方法：设置`background-image`来替代img，不同尺寸的高宽变化就需要专门解决一下
2. 图片生成会出现模糊：

    - 问题的本质是在高清屏的显示实现精度不同：

    - 解决方案： 不管当前的devicePixelRatio的值是多少，统一将canvasDOM节点的width属性设置为其csswidth属性的两倍，同理将height属性也设置为cssheight属性的两倍

  ```js
  var getPixelRatio = function(context) {  
    var backingStore = context.backingStorePixelRatio ||  
        context.webkitBackingStorePixelRatio ||  
        context.mozBackingStorePixelRatio ||  
        context.msBackingStorePixelRatio ||  
        context.oBackingStorePixelRatio ||  
        context.backingStorePixelRatio || 1;  
  
    return (window.devicePixelRatio || 1) / backingStore;  
  };  
  ```
  参考：
  [Canvas 在高清屏下绘制图片变模糊的解决方法](https://blog.csdn.net/qyaroon/article/details/51916150)


