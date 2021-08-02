## 一、概述

HtmlAgilityPack(以下简称HAP)是一个基于.Net的、第三方免费开源的微型类库，主要用于在服务器端解析html文档。

HtmlAgilityPack为网页提供了标准的DOM API和XPath导航。使用WebBrowser和HttpWebRequest下载的网页可以用Html Agility Pack来解析。

Xpath表达式的参考文档可见：http://www.w3school.com.cn/xpath/xpath_syntax.asp



### 参考：

GitHub：https://github.com/zzzprojects/html-agility-pack/releases

官网：https://html-agility-pack.net/

https://www.nuget.org/packages/HtmlAgilityPack/

[回到顶部](https://www.cnblogs.com/springsnow/p/11388673.html#_labelTop)

## 二、属性和方法

HtmlAgilityPack中的HtmlNode类与XmlNode类差不多，HtmlDocument类与XmlDocument类差不多。下面来看看该类提供功能。



### 属性

Attributes 　　　　　　　　　　　　获取节点的属性集合
ChildNodes　　　　　　　　　　　　获取子节点集合(包括文本节点)
Closed　　　　　　　　　　　　　　该节点是否已关闭(</xxx>)
ClosingAttributes 在关闭标签的属性集合
FirstChild 获取第一个子节点
HasAttributes 判断该节点是否含有属性
HasChildNodes　　　　　　　　　　判断该节点是否含有子节点
HasClosingAttributes 判断该节点的关闭标签是否含有属性(</xxx class="xxx">)
Id　　　　　　　　　　　　　　　　 获取该节点的Id属性
InnerHtml　　　　　　　　　　　　 获取该节点的Html代码
InnerText　　　　　　　　　　　　 获取该节点的内容，与InnerHtml不同的地方在于它会过滤掉Html代码，而InnerHtml是连Html代码一起输出
LastChild 获取最后一个子节点
Line　　　　　　　　　　　　　　　 获取该节点的开始标签或开始代码位于整个HTML源代码的第几行(行号)
Name Html元素名
NextSibling　　　　　　　　　　　　获取下一个兄弟节点
NodeType 获取该节点的节点类型
OuterHtml　　　　　　　　　　　　 整个节点的代码
OwnerDocument　　　　　　　　　节点所在的HtmlDocument文档
ParentNode　　　　　　　　　　　　获取该节点的父节点
PreviousSibling　　　　　　　　　　获取前一个兄弟节点
StreamPosition　　　　　　　　　　该节点位于整个Html文档的字符位置
XPath 根据节点返回该节点的XPath



### 方法：

void Load(string path);   从路径中加载一个文档
IEnumerable<HtmlNode> Ancestors(); 　　　　　　　　　　　　　　返回此元素的所有上级节点的集合。
HtmlNode AppendChild(HtmlNode newChild);  将参数元素追加到为调用元素的子元素(追加在最后)
HtmlNode PrependChild(HtmlNode newChild);  将参数中的元素作为子元素，放在调用元素的最前面
IEnumerable<HtmlAttribute> ChildAttributes(string name); 　　　　获取所有子元素的属性(参数名要与元素名匹配)
HtmlNode Clone(); 　　　　　　　　　　　　　　　　　　　　　　　　 本节点克隆到一个新的节点
void CopyFrom(HtmlNode node); 创建重复的节点和其下的子树。
XPathNavigator CreateNavigator(); 　　　　　　　　　　　　　　　　 返回的一个对于此文档的XPathNavigator
static HtmlNode CreateNode(string html);  静态方法，允许用字符串创建一个新节点
XPathNavigator CreateRootNavigator(); 　　　　　　　　　　　　　　创建一个根路径的XPathNavigator
IEnumerable<HtmlNode> DescendantNodes(); 　　　　　　　　　　 获取所有子代节点
HtmlNode Element(string name); 根据参数名获取一个元素
IEnumerable<HtmlNode> Elements(string name); 　　　　　　　　　根据参数名获取匹配的元素集合
bool GetAttributeValue(string name, bool def); 帮助方法，用来获取此节点的属性的值(布尔类型)。如果未找到该属性，则将返回默认值。
HtmlNode InsertAfter(HtmlNode newChild, HtmlNode refChild); 将一个节点插入到第二个参数节点的后面，与第二个参数是兄弟关系
HtmlNode InsertBefore(HtmlNode newChild, HtmlNode refChild); 　　讲一个节点插入到第二个参数节点的后面，与第二个参数是兄弟关系
static bool IsEmptyElement(string name);  确定是否一个空的元素节点。
static bool IsOverlappedClosingElement(string text); 确定是否文本对应于一个节点可以保留重叠的结束标记。
void Remove(); 从父集合中移除调用节点
HtmlNodeCollection SelectNodes(string xpath);　　　　　　　　　　　根据XPath获取一个节点集合
HtmlNode SelectSingleNode(string xpath); 　　　　　　　　　　　　　根据XPath获取唯一的一个节点
HtmlAttribute SetAttributeValue(string name, string value); 设置调用节点的属性
string WriteContentTo(); 将该节点的所有子级都保存到一个字符串中。
string WriteTo(); 　　　　　　　　　　　　　　　　　　　　　　　　　　将当前节点保存到一个字符串中。
void Save(string filename);  将HTML文档保存到指定的路径



## 三、用法 

下面是几个简单使用说明：

1、获取网页title：doc.DocumentNode.SelectSingleNode("//title").InnerText;//XPath中“//title”表示所有title节点。SelectSingleNode用于获取满足条件的唯一的节点。

2、获取所有的超链接：doc.DocumentNode.Descendants("a")

3、获取name为kw的input，也就是相当于getElementsByName()：var kwBox = doc.DocumentNode.SelectSingleNode("//input[@name='kw']");



```c#
private void Form1_Load(object sender, EventArgs e)
{
    List<Result> list = new List<Result>();

    HtmlWeb htmlWeb = new HtmlWeb();

    htmlWeb.OverrideEncoding = Encoding.UTF8;//编码，这里网上有些很多写法都不正确

    HtmlAgilityPack.HtmlDocument htmlDoc = htmlWeb.Load(@"http://www.cnblogs.com/");

    //选择博客园首页文章列表
    htmlDoc.DocumentNode.SelectNodes("//div[@id='post_list']/div[@class='post_item']").//双斜杠“//”表示从跟节点开始查找
        AsParallel().ToList().ForEach(ac =>
        {
            //抓取图片，因为有空的，所以拿变量存起来
            HtmlNode node = ac.SelectSingleNode(".//p[@class='post_item_summary']/a/img");

            list.Add(new Result
            {
                url = ac.SelectSingleNode(".//a[@class='titlelnk']").Attributes["href"].Value,
                title = ac.SelectSingleNode(".//a[@class='titlelnk']").InnerText,
                //图片如果为空，显示默认图片
                img = node == null ? "http ://www.cnblogs.com//Content/img/avatar.png" : node.Attributes["src"].Value,
                content = ac.SelectSingleNode(".//p[@class='post_item_summary']").InnerText
            });
        });

    foreach (Result item in list)
    {
        this.listBox1.Items.Add(item.title);
    }


}/// <summary>
    /// 页面抓取结果
    /// </summary>
public class Result
{
    /// <summary>
    /// 链接
    /// </summary>
    public string url { get; set; }
    /// <summary>
    /// 标题
    /// </summary>
    public string title { get; set; }
    /// <summary>
    /// 头像地址
    /// </summary>
    public string img { get; set; }
    /// <summary>
    /// 正文内容
    /// </summary>
    public string content { get; set; }
}
```

