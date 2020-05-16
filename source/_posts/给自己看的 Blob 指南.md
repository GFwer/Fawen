---
title: 给自己看的 Blob 指南
tags: [JavaScript]
category: JavaScript
date: 2020-5-4 21:24
---
# 给自己看的 Blob 指南

## 前言
Blob 是 JavaScript 一个比较特殊的对象，一般来说我们并不使用，不过不代表我们不会使用，通过 Blob 也能完成许多功能。

## 简介
Blob(Binary Large Object) ，代表一个不可变、原始数据的类文件对象。在 JavaScript 中，Blob 通常表示二进制数据，可以获取其大小、对其切割等操作。

### 创建
通过构造函数创建 Blob 对象：
```javascript
var blob = new Blob( array, options );
// array 代表一个 ArrayBuffer, ArrayBufferView, Blob, DOMString 等对象构成的数组
// options 是可选的，它有两个属性：
// type，代表 array 中内容的 MIME 类型（如 text/html、image/jpeg、image/png 等）
// endings，代表行结束符 \n 的格式，可以为 transparent（行结束符保持不变）和 native（行结束符和操作系统保持一致）
```
下面创建一些 Blob 对象：
```javascript
var blob1 = new Blob([1]);
var blob2 = new Blob(["1"]);
var blob3 = new Blob(['{name:"fawen"}']);
var blob4 = new Blob([{ name: "fawen" }]);
var blob5 = new Blob([1, 2]);
var blob6 = new Blob(['<a id="a"><b id="b">hey!</b></a>'], {type : 'text/html'})

console.log(blob1); // Blob {size: 1, type: ""}
console.log(blob2); // Blob {size: 1, type: ""}
console.log(blob3); // Blob {size: 14, type: ""}
console.log(blob4); // Blob {size: 15, type: ""}
console.log(blob5); // Blob {size: 2, type: ""}
console.log(blob6); // Blob {size: 32, type: "text/html"}
```

可以看出，Blob 对象只有两个属性，`size`表示数据大小，type 表示构建时传入的 MIME 类型。

### 方法
Blob 对象方法不多，一一列举：
- slice，用的最多的方法，切割 Blob 对象，返回一个新的 Blob 子对象
- stream，返回一个能够读取其内容的`ReadableStream`
- text，返回一个包含 Blob 所有内容的 UTF-8 格式的 Promise，回调函数中是 字符串
- arrayBuffer，返回一个包含 Blob 所有内容的二进制格式的 Promise，回调函数中是 ArrayBuffer

获取图片并且通过二进制显示：
```javascript
var blob = request(); // 返回 blob 类型的图片
var blob = new Blob([], { type: "image/png" }); // 或者根据内容创建
var url = URL.createObjectURL(blob); // 通过 URL 对象的方法创建一个 blob 链接
img.src = url; // url 可能长这样：blob: 1231451-125124512-125125
// 图片加载完成后需要清除释放 URL 对象
img.onload = () => {
  URL.revokeObjectURL(img.src);
};
```

### File
File 对象是特殊的 Blob 对象，一般来说，File 对象来源于`<input>`上传、拖动到浏览器的`DataTransfer`等，File 对象可以使用 Blob 对象的 slice 方法。

通过`FileReader`、`URL.createObjectURL`都可以处理 File/Blob 对象。

```javascript
// <input type="file" id="input" multiple>
// fileList
const selectedFile = document.getElementById('input').files[0];
```

#### FileReader
fileReader 可以异步读取文件内容：
```javascript
function printFile(file) {
  var reader = new FileReader();
  reader.onload = function (evt) {
    // 读取完成后得到 text
    console.log(evt.target.result);
  };
  reader.readAsText(file);
}
```

fileReader 主要有如下方法：
- FileReader.readAsArrayBuffer()，读取 Blob，完成后回调函数中的是 ArrayBuffer 对象
- FileReader.readAsDataURL()，读取 Blob，完成后回调函数中是 base64 字符串（`data:url`）
- FileReader.readAsText()，读取 Blob，完成后是字符串