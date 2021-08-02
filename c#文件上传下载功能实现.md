# c#文件上传下载功能实现

## NuGet 安装SqlSugar

### Model文件下新建 DbContext 类

```c#
 public class DbContext
    {
        public DbContext()
        {
            Db = new SqlSugarClient(new ConnectionConfig()
            {
                ConnectionString = "server=localhost;uid=root;pwd=woshishui;database=test",
                DbType = DbType.MySql,
                InitKeyType = InitKeyType.Attribute,//从特性读取主键和自增列信息
                IsAutoCloseConnection = true,//开启自动释放模式和EF原理一样我就不多解释了

            });
            //调式代码 用来打印SQL 
            Db.Aop.OnLogExecuting = (sql, pars) =>
            {
                Console.WriteLine(sql + "\r\n" +
                                  Db.Utilities.SerializeObject(pars.ToDictionary(it => it.ParameterName, it => it.Value)));
                Console.WriteLine();
            };

        }
        //注意：不能写成静态的，不能写成静态的
        public SqlSugarClient Db;//用来处理事务多表查询和复杂的操作
        public SimpleClient<uploading> uploadingdb { get { return new SimpleClient<uploading>(Db); } }//用来处理Student表的常用操作
    }
```

### 建uploading实体类

```c#
[SugarTable("uploading")]
   public class uploading
    {

        //指定主键和自增列，当然数据库中也要设置主键和自增列才会有效
        [SugarColumn(IsPrimaryKey = true, IsIdentity = true)]
        public int id { get; set; }
        public string name { get; set; }
        public string path { get; set; }
    }
```

### Manager文件下建UploadingManager

```c#
 class UploadingManager : DbContext
    {
        public List<uploading> Query()
        {
            try
            {
                List<uploading> data = Db.Queryable<uploading>()
                    .Select(f => new uploading
                    {
                        name = f.name,
                        path = f.path
                    })
                    .ToList();
                return data;
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
                throw;
            }

        }

        public List<string> GetName(string name)
        {
            List<string> data = Db.Queryable<uploading>()
                .Where(w=>w.name== name)
                .Select(f => f.path)
                .ToList();
            return data;

        }
    }
```



## 窗体加载Form1_Load

### 读取到数据库字段name并赋值

```c#
 private void Form1_Load(object sender, EventArgs e)
        {
            List<uploading> data = uploading.Query();
            foreach (var data1 in data)
            {
                comboBox1.Items.Add(data1.name);
            }
            comboBox1.SelectedIndex = 0;

        }
```



### comboBox事件触发条件查询到上传的path

```c#
 private void comboBox1_SelectedIndexChanged(object sender, EventArgs e)
        {
            List<string> data = uploading.GetName(comboBox1.Text);

            for (int i = 0; i < data.Count; i++)
            {
                textBox1.Text = data[0];
            }
        }
```

### 上传事件触发

```c#
  private void Button1_Click(object sender, EventArgs e)
        {
             string path = textBox1.Text;
            CopyDirs(textBox3.Text,
                path);
        }
```

```c#
 private void CopyDirs(string srcPath, string aimPath)
        {
            try
            {
                // 检查目标目录是否以目录分割字符结束如果不是则添加
                if (aimPath[aimPath.Length - 1] != System.IO.Path.DirectorySeparatorChar)
                {
                    aimPath += System.IO.Path.DirectorySeparatorChar;
                }

                // 判断目标目录是否存在如果不存在则新建
                if (!System.IO.Directory.Exists(aimPath))
                {
                    System.IO.Directory.CreateDirectory(aimPath);
                }

                // 得到源目录的文件列表，该里面是包含文件以及目录路径的一个数组
                // 如果你指向copy目标文件下面的文件而不包含目录请使用下面的方法
                // string[] fileList = Directory.GetFiles（srcPath）；
                string[] fileList = System.IO.Directory.GetFileSystemEntries(srcPath);
                // 遍历所有的文件和目录
                foreach (string file in fileList)
                {
                    // 先当作目录处理如果存在这个目录就递归Copy该目录下面的文件
                    if (System.IO.Directory.Exists(file))
                    {
                        CopyDir(file, aimPath + System.IO.Path.GetFileName(file));

                        DisplaylistboxMsg("上传成功");
                    }
                    // 否则直接Copy文件
                    else
                    {
                        System.IO.File.Copy(file, aimPath + System.IO.Path.GetFileName(file), true);
                        DisplaylistboxMsg("上传成功");
                    }
                }
            }
            catch (Exception e)
            {
                DisplaylistboxMsg("上传失败" + e.Message);
            }
        }
```

### 下载事件触发

```c#
private void Button2_Click(object sender, EventArgs e)
        {
            CopyDir(@"\\10.55.2.3\mech_production_line_sharing\Test\" + textBox2.Text, textBox4.Text);
        }

private void CopyDir(string srcPath, string aimPath)
        {
            // 检查目标目录是否以目录分割字符结束如果不是则添加
            if (aimPath[aimPath.Length - 1] != System.IO.Path.DirectorySeparatorChar)
            {
                aimPath += System.IO.Path.DirectorySeparatorChar;
            }

            // 判断目标目录是否存在如果不存在则新建
            if (!System.IO.Directory.Exists(aimPath))
            {
                System.IO.Directory.CreateDirectory(aimPath);
            }

            // 得到源目录的文件列表，该里面是包含文件以及目录路径的一个数组
            // 如果你指向copy目标文件下面的文件而不包含目录请使用下面的方法
            // string[] fileList = Directory.GetFiles（srcPath）；
            string[] fileList = System.IO.Directory.GetFileSystemEntries(srcPath);
            // 遍历所有的文件和目录
            foreach (string file in fileList)
            {
                // 先当作目录处理如果存在这个目录就递归Copy该目录下面的文件
                if (System.IO.Directory.Exists(file))
                {
                    CopyDir(file, aimPath + System.IO.Path.GetFileName(file));
                    DisplaylistboxMsg("下载成功");
                }
                // 否则直接Copy文件
                else
                {
                    System.IO.File.Copy(file, aimPath + System.IO.Path.GetFileName(file), true);
                    DisplaylistboxMsg("下载成功");
                }
            }
        }
```

