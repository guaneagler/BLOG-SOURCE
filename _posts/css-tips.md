---
title: Css tips
date: 2018-07-02 09:31:06
tags:
  -- Css
categories: Css
description: Some tips for css.
---

box 高度不接受百分比，接受px和em，默认情况下box高度是跟随内容变化的
border不接受百分比
当两个box的margin重叠的时候，margin不叠加，取较大值

overflow
当box的高度宽度固定的时候，如果内容超出边界，可以通过定义overflow来修改内容显示效果
如果定义为auto，内容超出的时候会出现滚动条
如果定义为hidden的时候，超出部分被隐藏
如果定义为visible的时候，超出部分会超越边界显示，这个在大多数时候是默认值

background-clip
.default     { background-clip: border-box;  } // 背景显示到border，包括border
.padding-box { background-clip: padding-box; } // 背景显示到padding，border以及border以外遮盖背景
.content-box { background-clip: content-box; } // 背景只显示在content部分

width config
针对布局
```
width: 70%;
max-width: 1280px; // 最大宽度，达到最大宽度后，width不再变化
min-width: 480px; // 最小宽度,达到最小宽度后，不再变化
margin: 0 auto; // 居中
```
针对图片
```
display: block;
margin: 0 auto; 居中
max-width: 100%; 让图片宽度跟随父级元素变化，但是达到图片原始宽度后，不再变化
```

box-sizing
默认值: content-box，即默认的盒子模型，width和height定义的是content的宽高
如果设置为border-box，那么width和height定义的为content+padding+border width

display
list of display values
block 盒子模型
inline 与盒子模型完全相反，元素不会有border，width，heigth等元素，margin和padding值的设置也不会正常作用，它不会换行，会像普通文本一样显示
inline-block 结合了上面两种，它有盒子模型的各种属性，但是不会在前后添加换行

border
border的默认颜色和text颜色相同，默认宽度为3px
可以单独定义某个方向的border，如
```
border-bottom: 1px solid red;
```
border-radius  圆角
支持ie9及以上的浏览器
border-radius也是shorthand的属性
```
border-radius: 10 20 2 4; //顺序为 左上 右上 右下 左下
```

position
static 正常的文档流
relative，可以设置top/left/right/bottom 值来调整element相对于正常文档流的位置
absolute,可以设置top/left/right/bottom值来调整element相对与html文档的位置，它的默认相对父元素即html；当你设置absolute元素的某个父元素为relative的时候，那么这个父元素就变成了absolute元素的相对元素；多个absolute元素之间重叠的话用z-index来调整优先级；
fixed,与absolute类似，不同的是，absolute相对于html的位置，当文档过长发生滚动的时候，absolute元素也会跟着文档进行滚动，fixed的不同点就在这里，fixed元素是相对于页面显示部分(viewport)的，它不会随着文档进行滚动
sticky,这是个有意思的元素，默认情况下，如果元素所在文档的位置相对viewport的位置没有达到设定限制，这个元素会像static元素一样显示，当滚动文档的时候，到达设定限制的时候，该元素就回变成一个fixed元素，位置固定。 （https://codepen.io/guaneagler/pen/yEwxLa）
