# HTML  head

## HTML base 元素

`<base>`标签描述了基本的链接地址/链接目标，该标签作为HTML文档中所有的链接标签的默认链接:

```html
<head>
<base href="http://www.runoob.com/images/" target="_blank">
</head>
```

## HTML  link 元素

标签定义了文档与外部资源之间的关系。

标签通常用于链接到样式表:

```html
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

## HTML style 元素

标签定义了HTML文档的样式文件引用地址.

元素中你也可以直接添加样式来渲染 HTML 文档:

```html
<head>
<style type="text/css">
body {background-color:yellow}
p {color:blue}
</style>
</head>
```

## HTML meta 元素

标签描述了一些基本的元数据。

标签提供了元数据.元数据也不显示在页面上，但会被浏览器解析。

元素通常用于指定网页的描述，关键词，文件的最后修改时间，作者，和其他元数据。

##  meta 标签- 使用实例

为搜索引擎定义关键词:

```html
<meta name="keywords" content="HTML, CSS, XML, XHTML, JavaScript">
```

为网页定义描述内容:

```html
<meta name="description" content="免费 Web & 编程 教程">
```

定义网页作者:

```html
<meta name="author" content="Runoob">
```

每30秒钟刷新当前页面:

```html
<meta http-equiv="refresh" content="30">
```

## HTML  script 元素

script 标签用于加载脚本文件，如： JavaScript。

## HTML head 元素

|        |                                    |
| ------ | ---------------------------------- |
| title  | 定义了文档的标题                   |
| base   | 定义了页面链接标签的默认链接地址   |
| link   | 定义了一个文档和外部资源之间的关系 |
| meta   | 定义了HTML文档中的元数据           |
| script | 定义了客户端的脚本文件             |
| style  | 定义了HTML文档的样式文件           |
| head   | 定义了文档的信息                   |

