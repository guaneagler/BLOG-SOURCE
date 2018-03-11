---
title: Drupal8 font
date: 2017-11-12 14:56:45
categories:
  - Drupal8
tags:
  - font
  - drupal8
description: How to include external font and internal font.
---

### Two font libraries website
[Google font](https://fonts.google.com)
[有字库](https://www.youziku.com/)

***Use google font as example.***

### Include internal font in drupal8.
1. Download font library (eg:Lato-BlackItalic) from [Google font](https://fonts.google.com) to theme/css/fonts folder.
2. In the fonts folder, create fonts.css file and include font library in it.
``` css
directory:
/*
- theme
  - css
    - fonts
      - Lato-BlackItalic.ttf
      - fonts.css */
  @font-face {
    font-family: 'Lato';
    src: url('./Lato-BlackItalic.ttf');
    font-weight: normal;
    font-style: normal;
  }
```
3. In the theme.libraries.yml file include fonts.css file.
``` yml
# make sure global-styling include in the theme
global-styling:
  version: VERSION
  css:
    base:
      css/base/elements.css: {}
    component:
      css/fonts/fonts.css: {}
      css/component/header.css: {}
```

4. Then you can use the font Lato in the font-family
``` css
  font-family: Lato, serif;
```

### Include external font in drupal8.

For external font library, add library link in the theme.libraries.yml file, then you can use it.
``` yml
global-styling:
  version: VERSION
  css:
    base:
      css/base/elements.css: {}
    component:
      'https://fonts.googleapis.com/css?family=Lato': { type: external }
```
