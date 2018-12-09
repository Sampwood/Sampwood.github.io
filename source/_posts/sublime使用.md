---
title: sublime使用
categories:
  - coding
tags:
  - environment
date: 2018-10-26 17:23:43
update: 2018-10-26 17:23:43
---

因为喜欢sublime的配色，所以一直都使用sublime来开发前端代码。
下面记录一些常用的配置。

### 下载

官网下载： http://www.sublimetext.com/

然后就是安装`package control`: https://packagecontrol.io/installation

<!-- more -->

### 插件

- A File Icon
- AutoFileName
- BracketHighlighter
- DocBlockr
- Emmet
- Git
- GitGutter
- JS Snippets
- JsFormat
- LESS
- MarkdownPreview
- SideBarEnhancements
- SublimeCodeIntel
- SublimeLinter
- ESLint
- SublimeServer
- Termial
- Vue Svntax Highlight

### 快捷键

```
[
  // 切换Project
  {
      "keys": ["ctrl+alt+p"],
      "command": "prompt_select_workspace"
  },
  // 快速到行首行尾
  { "keys": ["ctrl+left"], "command": "move_to", "args": {"to": "bol", "extend": false} },
  { "keys": ["ctrl+right"], "command": "move_to", "args": {"to": "eol", "extend": false} },
]
```

### 设置

```
{
  "font_size": 11,
  "ignored_packages":
  [
      "Vintage"
  ],
  // 竖线
  "rulers":
  [
      100,
      120
  ],
  "tab_size": 2,
  "translate_tabs_to_spaces": true,
}
```