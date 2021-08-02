# HTML 链接

HTML 使用超级链接与网络上的另一个文档相连。几乎可以在所有的网页中找到链接。点击链接可以从一张页面跳转到另一张页面。

## HTML 超链接（链接）

HTML使用标签 <a>来设置超文本链接。

超链接可以是一个字，一个词，或者一组词，也可以是一幅图像，您可以点击这些内容来跳转到新的文档或者当前文档中的某个部分。

当您把鼠标指针移动到网页中的某个链接上时，箭头会变为一只小手。

在标签<a> 中使用了href属性来描述链接的地址。

默认情况下，链接将以以下形式出现在浏览器中：

- 一个未访问过的链接显示为蓝色字体并带有下划线。
- 访问过的链接显示为紫色并带有下划线。
- 点击链接时，链接显示为红色并带有下划线。

> 注意：如果为这些超链接设置了 CSS 样式，展示样式会根据 CSS 的设定而显示。

## HTML 链接语法

`<a href="url">链接文本</a>`

## 实例

`<a href="https://www.runoob.com/">访问菜鸟教程</a>`

**提示:** *"链接文本"* 不必一定是文本。图片或其他 HTML 元素都可以成为链接。

## HTML 链接 - target 属性

使用 target 属性，你可以定义被链接的文档在何处显示。

下面的这行会在新窗口打开文档：

## 实例

`<a href="https://www.runoob.com/" target="_blank" rel="noopener noreferrer">访问菜鸟教程!</a>`

## HTML 链接- id 属性

id属性可用于创建在一个HTML文档书签标记。

**提示:** 书签是不以任何特殊的方式显示，在HTML文档中是不显示的，所以对于读者来说是隐藏的。

## 实例

在HTML文档中插入ID:

`<a id="tips">有用的提示部分</a>`

在HTML文档中创建一个链接到"有用的提示部分(id="tips"）"：

`<a href="#tips">访问有用的提示部分</a>`

或者，从另一个页面创建一个链接到"有用的提示部分(id="tips"）"：

`<a href="https://www.runoob.com/html/html-links.html#tips">访问有用的提示部分</a>`

## HTML 链接标签

| 标签 | **描述**         |
| ---- | ---------------- |
| <a>  | 定义一个超级链接 |

## 图片链接

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>

<p>创建图片链接:
<a href="//www.runoob.com/html/html-tutorial.html">
<img  border="10" src="smiley.gif" alt="HTML 教程" width="32" height="32"></a></p>

<p>无边框的图片链接:
<a href="//www.runoob.com/html/html-tutorial.html">
<img border="0" src="smiley.gif" alt="HTML 教程" width="32" height="32"></a></p>

</body>
</html>
```

## 在当前页面链接到指定位置

 

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>

<p>
<a href="#C4">查看章节 4</a>
</p>

<h2>章节 1</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 2</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 3</h2>
<p>这边显示该章节的内容……</p>

<h2><a id="C4">章节 4</a></h2>
<p>这边显示该章节的内容……</p>

<h2>章节 5</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 6</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 7</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 8</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 9</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 10</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 11</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 12</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 13</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 14</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 15</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 16</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 17</h2>
<p>这边显示该章节的内容……</p>

</body>
</html>

```

## 跳出框架

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>

<p>跳出框架?</p> 
<a href="//www.runoob.com/" target="_top">点击这里!</a> 

</body>
</html>
```

##  创建电子邮件链接

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>

<p>
这是一个电子邮件链接：
<a href="mailto:someone@example.com?Subject=Hello%20again" target="_top">
发送邮件</a>
</p>

<p>
<b>注意:</b> 单词之间空格使用 %20 代替，以确保浏览器可以正常显示文本。
</p>

</body>
</html>
```

## 创建电子邮件链接2

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>

<p>
这是另一个电子邮件链接：
<a href="mailto:someone@example.com?cc=someoneelse@example.com&bcc=andsomeoneelse@example.com&subject=Summer%20Party&body=You%20are%20invited%20to%20a%20big%20summer%20party!" target="_top">发送邮件!</a>
</p>

<p>
<b>注意:</b> 单词之间的空格使用 %20 代替，以确保浏览器可以正常显示文本。
</p>

</body>
</html>
```

