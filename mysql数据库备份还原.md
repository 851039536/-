# mysql数据库备份还原

###  引用dll

MySql.Data.dll,  MySqlbackup.dll

### 建一个数据连接静态类

```c#
public static class mysql
{
public static string constr = "database=test;Password=密码;user ID=root;server=ip地址";
public static MySqlConnection conn = new MySqlConnection(constr);
}
```

### 建winform窗体

#### 备份代码

```c#
DialogResult result = MessageBox.Show("备份路径默认在当前程序下", "提示", MessageBoxButtons.YesNo, MessageBoxIcon.Question);
if (result == DialogResult.Yes)
{
string time1 = System.DateTime.Now.ToString("d").Replace("/", "-");
string file = ".//mysql/" + time1 + "_test.sql";
using (MySqlCommand cmd = new MySqlCommand())
{
using (MySqlBackup mb = new MySqlBackup(cmd))
{
cmd.Connection = mysql.conn;
mysql.conn.Open();
mb.ExportToFile(file);
mysql.conn.Close();
MessageBox.Show("已备份");
}
}
}
else
{
return;
}
```

#### 还原代码

```c#
string file = textBox1.Text;
if (file == "")
{
MessageBox.Show("不能为空");
return;
}
DialogResult result = MessageBox.Show("确定还原吗？", "还原", MessageBoxButtons.YesNo, MessageBoxIcon.Question);
if (result == DialogResult.Yes)
{
try
{
using (MySqlCommand cmd = new MySqlCommand())
{
using (MySqlBackup mb = new MySqlBackup(cmd))
{
cmd.Connection = mysql.conn;
mysql. conn.Open();
mb.ImportFromFile(file);
mysql. conn.Close();
MessageBox.Show("已还原");
}
}
}
catch (Exception ex)
{
MessageBox.Show(ex.Message);
}
}
else
{
return;
}
```

