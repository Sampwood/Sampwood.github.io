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
- ProjectManager
- SideBarEnhancements
- SublimeCodeIntel
- SublimeLinter
- SublimeLinter-eslint
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

### SublimeLinter配置

```
// SublimeLinter Settings - User
{
  "debug": true,
  "delay": 0.25,
  "gutter_theme": "Danish Royalty",
  "lint_mode": "background",
  "linters": {
    "eslint": {
      "selector": "text.html.vue, source.js - meta.attribute-with-value",
    },
  },
  "no_column_highlights_line": false,
  "paths": {
    "linux": [],
    "osx": [],
    "windows": ["C:/Users/sampwood/AppData/Roaming/npm/eslint"]
  },
  "show_marks_in_minimap": true,
  "styles": [
    {
      "mark_style": "outline",
      "priority": 1,
      "scope": "region.yellowish markup.changed.sublime_linter markup.warning.sublime_linter",
      "icon": "dot",
      "types": [
          "warning"
      ]
    },
    {
      "mark_style": "outline",
      "priority": 1,
      "scope": "region.redish markup.deleted.sublime_linter markup.error.sublime_linter",
      "icon": "dot",
      "types": [
          "error"
      ]
    }
  ],
  "syntax_map": {
    "html (django)": "html",
    "html (rails)": "html",
    "html 5": "html",
    "javascript (babel)": "javascript",
    "magicpython": "python",
    "php": "html",
    "python django": "python"
  }
}
```

### git配置
```
{
  "statusbar_branch": false,
  "statusbar_status": false,
}
```

### projectManager配置
```
{
  "projects_path": ["D:/sublime-folder"]
}
```

### terminal配置
```
{
  // window下终端路径
  "terminal": "D:\\Program Files\\cmder_mini\\Cmder.exe",
  //  window下终端参数
  "parameters": ["/START", "%CWD%"]
}
```
