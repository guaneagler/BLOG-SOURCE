---
title: Vanessa
date: 2018-07-13 11:14:50
tags:
	- drupal
---

drupal 8 的一些默认的block模板，如site branding，包括logo、slogan、sitename，在7里面这些的修改存在于page.tpl,在8中，这是一个block单独的模板文件，参考 classy/templates/block/block--system-branding-block.html.twig

[wow](https://wowjs.uk/docs.html)
wow.js and animate.css
提供加载时的动画效果，如图片淡入，文字动态等
```
<link rel="stylesheet" href="css/animate.css">
<script src="js/wow.min.js"></script>
<script>
new WOW().init();
</script>
// class 为wow+需要的动态效果,详情参考[wow](https://wowjs.uk/docs.html)
<div class="wow bounceInUp">
	Content to Reveal Here
</div>
```
