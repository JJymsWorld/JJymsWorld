---
title: Vscode设置tab键循环代码补全提示
tags:
  - [vscode]
categories:
  - gallery
date: 2022-11-26 17:55:42
---
1. 按下 `ctrl + shift + p`输入 `key`，选择 `首选项: 打开键盘快捷方式(JSON)`
2. 把下面的代码粘贴进去即可

   ```json
   [
       {
           "key": "tab",
           "command": "selectNextQuickFix",
           "when": "editorFocus && quickFixWidgetVisible"
       },
           {
           "key": "shift+tab",
           "command": "selectPrevQuickFix",
           "when": "editorFocus && quickFixWidgetVisible"
       },
           {
           "key": "tab",
           "command": "selectNextSuggestion",
           "when": "editorTextFocus && suggestWidgetMultipleSuggestions && suggestWidgetVisible"
       },
           {
           "key": "shift+tab",
           "command": "selectPrevSuggestion",
           "when": "editorTextFocus && suggestWidgetMultipleSuggestions && suggestWidgetVisible"
       }
   ]

   ```
