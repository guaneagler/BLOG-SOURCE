---
title: css float inner block problem
date: 2017-11-18 15:13:32
categories:
  - Css
tags:
  - css
  - float
description: 内部元素浮动，不能撑起父元素（内部浮动有高度元素，父元素没有高度）
---

Refer:[子div撑不开父div的几种解决方法](http://blog.csdn.net/piazini/article/details/8625935)

``` html
<div>
  <div style="float: left">子元素</div>
</div>
```
## 内部div浮动，父级div没有被内部div撑起来
* 原因：内部div float之后，丢失了clear:both和display:block的样式，所以父级div不会被撑开 *

### 方案1⃣️：
``` html
为父级div添加clearfix class
.clearfix:after {
  content: ".";
  display: block;
  height: 0;
  clear: both;
  visibility: hidden;
}
IE下有问题
.clearfix {height: 1%;}
```
### 方案2⃣️：
父级元素添加 overflow:auto 属性
### 方案3⃣️：
父级元素添加 display: table (推荐)
