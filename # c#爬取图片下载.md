# c#爬取图片下载

```c#
11111 using System;
using System.IO;
using System.Net;
using System.Text;
using System.Text.RegularExpressions;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            string htmlurl = "https://fabiaoqing.com/biaoqing/lists/page/12.html";
            string urltext = getHtml(htmlurl);

            //构造正则表达式
            string regstr = "http://wx[1-4].sinaimg.cn/bmiddle/.+?.[jg][pi][fg]";
            foreach (Match match in Regex.Matches(urltext, regstr))
            {
                downLoadPic(match.Value);
                Console.WriteLine("下载图片" + match.Value.Substring(match.Value.Length - 10, 10));
            }
            Console.ReadKey();
        }

        public static string getHtml(string url)
        {
            //列表数据，一般通过抓包工具，点击下一页，来发现共同的规则，从而找到对应的URL
            //根据抓包工具，获取需要POST的数据， 并分析对应的数值， 明细这里PageIndex：1 是指第二页。其他的暂不做分析
            string postData = "{ \"CategoryType\":\"SiteHome\",\"ParentCategoryId\":0,\"CategoryId\":808,\"PageIndex\":1,\"TotalPostCount\":4000,\"ItemListActionName\":\"PostList\"}";

            //1.构建Request
            HttpWebRequest req = (HttpWebRequest)WebRequest.Create(url);
            req.Method = "POST";// POST OR GET， 如果是GET, 则没有第二步传参，直接第三步，获取服务端返回的数据
            req.AllowAutoRedirect = false;//服务端重定向。一般设置false
            req.ContentType = "application/x-www-form-urlencoded";//数据一般设置这个值，除非是文件上传

            //2.同过流的形式，传输参数。
            byte[] postBytes = Encoding.UTF8.GetBytes(postData);
            req.ContentLength = postBytes.Length;
            Stream postDataStream = req.GetRequestStream();
            postDataStream.Write(postBytes, 0, postBytes.Length);
            postDataStream.Close();
            
            //3. 获取服务器端的返回数据。
            HttpWebResponse resp = (HttpWebResponse)req.GetResponse();
            string html = new StreamReader(resp.GetResponseStream()).ReadToEnd();
            return html;
        }

        public static void downLoadPic(string picURL)
        {
            WebClient web = new WebClient();
            string targetPath = "C:\\Users\\CH190006\\Desktop\\Test\\img\\" + picURL.Substring(picURL.Length - 10, 10);//构造图片保存的名字
            web.DownloadFile(picURL, targetPath);
        }
    }
}

```

### 异常

**The remote server returned an error: (407) Proxy Authentication Required.”**

App.config

```c#
 <system.net>
    <defaultProxy useDefaultCredentials="true" >
    </defaultProxy>
  </system.net>
```

