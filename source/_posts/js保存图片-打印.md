---
categories:
  - coding
  - js
tags:
  - print
  - save html to img
date: 2017-11-21 17:36:34
update: 2017-11-21 17:36:34
title: js保存图片-打印
---

### 保存为图片

对于复杂一些的dom结构，可以使用第三方库（`html2canvas`）把html转换成canvas然后在进行图片保存or打印：
```
import * as html2canvas from 'html2canvas';

let canvasToSave = document.getElementById('myCanvas');
html2canvas(canvasToSave, {
    background: '#D3D3D3',
    onrendered: function(canvas) {
        document.body.style.width = '';
        let aTag = document.createElement('a');
        aTag.href = canvas.toDataURL();
        aTag.download = graphName;
        aTag.click();
    }
});
```
<!--more-->

### 打印
window.print(); 打印的是document.body.innerHTML的内容即全页面内容

@Media print {} 加打印样式

#### canvas

如果要打印页面部分内容，而且其中有canvas时，可以使用window.open()方法新建一个窗口来打印内容。
```javascript
var dataUrl = canvas.toDataURL(); 
var windowContent = '<!DOCTYPE html>';
windowContent += '<html>'
windowContent += '<head><title>Print canvas</title></head>';
windowContent += '<body>'
windowContent += '<img src="' + dataUrl + '">';
windowContent += '</body>';
windowContent += '</html>';
var printWin = window.open('', '');
printWin.document.open();
printWin.document.write(windowContent);
printWin.document.addEventListener('load', function() {
    printWin.focus();
    printWin.print();
    printWin.document.close();
    printWin.close();            
}, true);
```

