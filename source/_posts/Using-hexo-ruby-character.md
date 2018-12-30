---
title: 使用hexo-ruby-character在汉字上面添加假名
date: 2018-12-28 16:58:48
tags:
- hexo
- Japanese
categories: Technology
---

# 用法和效果
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

# 在VS Code中插入代码片段
使用Visual Studio Code可以为这个加上一个snippet。
方法：
1. 菜单（文件 - 首选项 - 用户代码片段 - markdown.json）
2. 添加以下部分：
    ``` json
    "Ruby blockquote": {
        "prefix": "ruby",
        "body": ["{% ruby ${TM_SELECTED_TEXT}|$2 %}"],
        "description": "Insert ruby blockquote"
    }
    ```
3. 按F1，输入Configure Language Specific Settings，选择Markdown，在用户设置中添加：
    ``` json
    "[markdown]": {
        "editor.quickSuggestions": {
            "other": true,
            "comments": false,
            "strings": false
        }
    }
    ```
4. 以后输入ruby就可以弹出提示了。
