---
title: 使用hexo-ruby-character在汉字上面添加假名
date: 2018-12-28 16:58:48
tags:
- hexo
- Japanese
categories: Technology
---

效果是这样的：今日は２０１８年{% ruby １２月|じゅうにがつ %}{% ruby ２８日|にじゅうはちにち %}。
代码：
```
今日は２０１８年{% ruby １２月|じゅうにがつ %}{% ruby ２８日|にじゅうはちにち %}。
```
还可以显示拼音：{% ruby 鬼魅魍魉|鬼魅魍魉 %}
```
{% ruby 鬼魅魍魉|鬼魅魍魉 %}
```

项目网址：https://github.com/JamesPan/hexo-ruby-character