# c# Path路径类

Path类是static类型

### 常用方法

```c#
Path.GetFullPath(file) 取全路径
Path.GetFileName(file) 取文件名，包含扩展名
Path.GetFileNameWithoutExtension(file) 取文件名，不包含扩展名
Path.GetExtension(file) 取扩展名
Path.GetDirectoryName(file) 取路径名
Path.GetPathRoot(file)  取盘符
Path.Combine(file1，file2) 合并2个路径
```

### 实现

```c#
string str = @"C:\Users\Administrator\Desktop\ceshi.txt";
            //获得文件名
            Console.WriteLine(Path.GetFileName(str));
            //获得不包含扩展名的文件名
            Console.WriteLine(Path.GetFileNameWithoutExtension(str));
            //获得文件所在文件夹的名称
            Console.WriteLine(Path.GetDirectoryName(str));
            //获得文件所在的全路径
            Console.WriteLine(Path.GetFullPath(str));
            //拼接路径字符串
            Console.WriteLine(Path.Combine(@"D:\a\b\","c.txt"));
            Console.ReadKey();
```

### 属性方法

| 属性或方法                                            | 作用                                     |
| ----------------------------------------------------- | ---------------------------------------- |
| string ChangeExtension(string path, string extension) | 更改路径字符串的扩展名                   |
| string Combine(params string[] paths)                 | 将字符串数组组合成一个路径               |
| string Combine(string path1, string path2)            | 将两个字符串组合成一个路径               |
| string GetDirectoryName(string path)                  | 返回指定路径字符串的目录信息             |
| string GetExtension(string path)                      | 返回指定路径字符串的扩展名               |
| string GetFileName(string path)                       | 返回指定路径字符串的文件名和扩展名       |
| string GetFileNameWithoutExtension(string path)       | 返回不具有扩展名的指定路径字符串的文件名 |
| string GetFullPath(string path)                       | 返回指定路径字符串的绝对路径             |
| char[] GetInvalidFileNameChars()                      | 获取包含不允许在文件名中使用的字符的数组 |
| char[] GetInvalidPathChars()                          | 获取包含不允许在路径名中使用的字符的数组 |
| string GetPathRoot(string path)                       | 获取指定路径的根目录信息                 |
| string GetRandomFileName()                            | 返回随机文件夹名或文件名                 |
| string GetTempPath()                                  | 返回当前用户的临时文件夹的路径           |
| bool HasExtension(string path)                        | 返回路径是否包含文件的扩展名             |
| bool IsPathRooted(string path)                        | 返回路径字符串是否包含根                 |