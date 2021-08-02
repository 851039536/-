# HTML 图像

## 插入图像

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>

<p>
一个图像:
<img src="smiley.gif" alt="Smiley face" width="32" height="32"></p>

<p>
一个动图:
<img src="hackanm.gif" alt="Computer man" width="48" height="48"></p>

<p>
注意插入动图的语法和静态图的语法是一样的。
</p>

</body>
</html>
```

## 从不同的位置插入图像

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>

<p>一个来自文件夹中的图像:</p>
<img src="/images/chrome.gif" alt="Google Chrome" width="33" height="32"><p>一个来自菜鸟教程的图像:</p>
<img src="//www.runoob.com/images/logo.png" alt="runoob.com" width="336" height="69">

</body>
</html>
```

## HTML 图像- 图像标签（ img）和源属性（Src）

**定义图像的语法是：**

`<img src="url" alt="some_text">`

## HTML 图像- Alt属性

alt 属性用来为图像定义一串预备的可替换的文本。

替换文本属性的值是用户定义的。

`<img src="boat.gif" alt="Big Boat">`

在浏览器无法载入图像时，替换文本属性告诉读者她们失去的信息。此时，浏览器将显示这个替代性的文本而不是图像。为页面上的图像都加上替换文本属性是个好习惯，这样有助于更好的显示信息，并且对于那些使用纯文本浏览器的人来说是非常有用的。

## HTML 图像- 设置图像的高度与宽度

height（高度） 与 width（宽度）属性用于设置图像的高度与宽度。

属性值默认单位为像素:

`<img src="pulpit.jpg" alt="Pulpit rock" width="304" height="228">`

**提示:** 指定图像的高度和宽度是一个很好的习惯。如果图像指定了高度宽度，页面加载时就会保留指定的尺寸。如果没有指定图片的大小，加载页面时有可能会破坏HTML页面的整体布局。



## 排列图片

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>

<h4>默认对齐的图像 (align="bottom"):</h4>
<p>这是一些文本。 <img src="smiley.gif" alt="Smiley face" width="32" height="32"> 这是一些文本。</p>

<h4>图片使用 align="middle":</h4>
<p>这是一些文本。 <img src="smiley.gif" alt="Smiley face" align="middle" width="32" height="32">这是一些文本。</p>

<h4>图片使用 align="top":</h4>
<p>这是一些文本。 <img src="smiley.gif" alt="Smiley face" align="top" width="32" height="32">这是一些文本。</p>

<p><b>注意:</b>在HTML 4中 align 属性已废弃，HTML5 已不支持该属性，可以使用 CSS 代替。</p>

</body>
</html>
```

## 浮动图片

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>

<p>
<img src="smiley.gif" alt="Smiley face" style="float:left" width="32" height="32"> 一个带图片的段落，图片浮动在这个文本的左边。
</p>

<p>
<img src="smiley.gif" alt="Smiley face" style="float:right" width="32" height="32"> 一个带图片的段落，图片浮动在这个文本的右边。
</p>

<p><b>注意:</b> 在这里我们使用了 CSS "float" 属性，在HTML 4中 align 属性已废弃，HTML5 已不支持该属性，可以使用 CSS 代替。</p>

</body>
</html>
```

## 设置图像链接

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

## 设置图像映射

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>

<p>点击太阳或其他行星，注意变化：</p>

<img src="planets.gif" width="145" height="126" alt="Planets" usemap="#planetmap">

<map name="planetmap">
  <area shape="rect" coords="0,0,82,126" alt="Sun" href="sun.htm">
  <area shape="circle" coords="90,58,3" alt="Mercury" href="mercur.htm">
  <area shape="circle" coords="124,58,8" alt="Venus" href="venus.htm">
</map>

</body>
</html>

```

## HTML 图像标签

|      |                            |
| ---- | -------------------------- |
| img  | 定义图像                   |
| map  | 定义图像地图               |
| area | 定义图像地图中的可点击区域 |

