# C# File文件类

C# 语言中 File 类同样可以完成与 FileInfo 类相似的功能，但 File 类中也提供了一些不同的方法。

File 类中获取或设置文件信息的常用方法如下表所示。

| 属性或方法                                                   | 作用                                   |
| ------------------------------------------------------------ | -------------------------------------- |
| DateTime GetCreationTime(string path)                        | 返回指定文件或目录的创建日期和时间     |
| DateTime GetLastAccessTime(string path)                      | 返回上次访问指定文件或目录的日期和时间 |
| DateTime GetLastWriteTime(string path)                       | 返回上次写入指定文件或目录的日期和时间 |
| void SetCreationTime(string path, DateTime creationTime)     | 设置创建该文件的日期和时间             |
| void SetLastAccessTime(string path, DateTime lastAccessTime) | 设置上次访问指定文件的日期和时间       |
| void SetLastWriteTime(string path, DateTime lastWriteTime)   | 设置上次写入指定文件的日期和时间       |

### File类的方法

创建文件：File.Create(@"文件路径");
        删除文件（彻底删除）：File.Delete(@"文件路径");
        复制文件内容：File.Copy(被复制文件路径,新文件路径);
        剪切文件：File.Move(被剪切文件路径,新文件路径);

以字节形式读取文件：返回字节数组（可以读取任何文件）


```c#
byte[] buffer = File.ReadAllBytes(@"文件路径");  //返回值为字节数组
//将字节解码，先确定编码方式，再解码字节数组
string s = Encoding.GetEncoding("编码方式").GetString(buffer);
Console.WriteLine(s); 
```

以字节形式写入文件：

```c#
string s = "今天生活美滋滋";  //需要写入文件的字符串
//把字符串用编码转成字节数组
byte[] buffer = Encoding.GetEncoding("编码方式").GetByte(s);
File.WriteAllBytes(@"文件路径",buffer);
```

以行的形式读取文件内容：返回字符串数组（只能读文本文件，不能读取音乐文件或其他多媒体文件）

```c#
string[] str = File.ReadAllLines(@"文件路径",Encoding.GetEncoding("编码方式"));
foreach(string s in str){
	Console.WriteLine(s);  //一次输出一行
}
```

以字符串形式读取文件内容：返回字符串（只能读文本文件，不能读取音乐文件或其他多媒体文件）

```c#
string s = File.ReadAllText(@"文件路径",字符串,Encoding.GetEncoding("编码方式"));
```

以行形式写入文件：

```c#
File.WriteAllLines(@"文件路径",字符串数组,Encoding.GetEncoding("编码方式"));
```

以字符串形式写入文件内容：

```c#
File.WriteAllText(@"文件路径",Encoding.GetEncoding("编码方式"));
```

