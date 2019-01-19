### [ADO.NET 数据库连接技术](#top) <b id="top"></b> :star:

-----
`在.NET Core 2.0时代已经将.NET Framework的ADO.NET整套体系完整覆盖 做了许多的更改,并且在NET Core中不再自动基础 数据库连接实现命名空间` 
[`官网:ADO.NET 4.7.2`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/)

- [x] [`1.准备工作`](#into) 
- [x] [`2.立即链接`](#connect) 
- [x] [`3.执行命令`](#command) 
- [x] [`4.Sql参数`](#parameter)
- [x] [`5.读取Table`](#reader)
- [x] [`6.存放到数据集`](#adapter)
- [x] [`7.执行存储过程`](#store)
- [x] [`8.DataAdapter 提供的分页功能`](#page)
-----
##### [`1.准备工作`](#top) :triangular_flag_on_post:  <b id="into"></b>
`首先我们需要官方为我们提供的API 文件`
* `链接Sql Server`
  * `首先 你需要下载 System.Data.SqlClient Nuget 包`
  * `Nuget 命令 [安装最新版]`:`dotnet add package System.Data.SqlClient` 
* `链接 Mysql`
  * `你需要下载 Mysql.Data`
  * `Nuget 命令 [安装最新版]`:`dotnet add package Mysql.Data` 
  
* `试一试链接`  

##### [`2.立即链接`](#top) :triangular_flag_on_post:  <b id="connect"></b>
`有两种链接方式哟！ 我更加推荐使用 SqlConnectionStringBuilder 更好配置连接池 并且可以防止 通过链接字符串的sql注入攻击`
* `链接字符串 重要的地方`
   * `Persist Security Info=False`:`这可确保不受信任的来源不能访问安全敏感信息 默认值为 false 尽量不要设置为 true 不安全`

##### [`SqlConnection 对象`](#top)
* `SqlConnection 用于链接 SQL Server 具有两个事件 `
  * `InfoMessage`:` SQL SERVER 传递过来的消息或者警告`
  * `StateChange`:`链接状态改变的事件 `
```C#
using System;
using System.Data;
using System.Data.SqlClient;
namespace libCsharp.DataBase
{
    public class ServiceBase
    {
        private readonly String  ConnectionString = 
        @"Data Source=47.98.215.212;Initial Catalog=demo;Pooling =true;User ID=kicker;Password=123456";

        //链接数据库
        public SqlConnection getConnection() {

            SqlConnection connection = new SqlConnection(ConnectionString);

            //事件 SQL SERVER 传递过来的消息或者警告
            connection.InfoMessage += (sender, e) =>
            {
                Console.WriteLine($"{e.Message}");
            };

            //StateChange 事件
            connection.StateChange += (sender, e) =>
            {
                Console.WriteLine($"{e.CurrentState} Last {e.OriginalState}");
            };


            connection.Open();
            return connection;
        }

        //通过 ConnectionBuilder 来创建链接

        public SqlConnection getConnectionByBuilder() {
            SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
            builder.DataSource = "47.98.215.212"; //数据库地址
            builder.InitialCatalog = "demo"; //数据库名称
            builder.IntegratedSecurity = false; //这个意思是 使用windows 方式验证 不要用 一定要设置为 false 默认也是 false

            builder.UserID = "kicker";
            builder.Password = "123456";


            builder.Pooling = true;  //开启连接池
            builder.MinPoolSize = 0;
            builder.MaxPoolSize = 100;
            builder.ConnectTimeout = 1000; //链接超时时间

            SqlConnection connection = new SqlConnection(builder.ConnectionString);


            //事件 SQL SERVER 传递过来的消息或者警告
            connection.InfoMessage += (sender, e) =>
            {
                Console.WriteLine($"{e.Message}");
            };

            //StateChange 事件
            connection.StateChange += (sender, e) =>
            {
                Console.WriteLine($"{e.CurrentState} Last {e.OriginalState}");
            };
            connection.Open();
            return connection;
        }

    }
}
```

##### 链接MYSQL 数据库
`之后 MYSQL和Sql Server的区别仅仅在 一个是 Sql 开头 一个是 Mysql 开头 [链接字符串 还是有区别的]`
* `Server`:`Mysql 使用Server 表示 服务器地址`
* `Data Source`:`Sql Server 使用 Data Source 表示 服务器地址`
```c#
private readonly static  String connectionString = 
			"server=localhost;user id=root;password=Jiangxing627;database=uchat";

public MySqlConnection getConnection() {
     MySqlConnectionStringBuilder builder = new MySqlConnectionStringBuilder();
     builder.Server = "127.0.0.1";
     builder.UserID = "root";
     builder.Password = "Jiangxing627";
     builder.Database = "uchat";
     builder.Pooling = true; //开启线程池
     builder.MinimumPoolSize = 10;
     builder.MinimumPoolSize = 50;
     builder.CharacterSet = "utf8mb4"; //字符集
     MySqlConnection connection = new MySqlConnection(builder.ConnectionString);
     // MySqlConnection connection = new MySqlConnection(connectionString);
     connection.StateChange += (sender, e) =>
     {
          Console.WriteLine(e.CurrentState);
     };
     connection.Open();
     return connection;
}
```


##### [`3.执行命令`](#top) :triangular_flag_on_post:  <b id="command"></b>
`接下来我们如何执行 SQL 命令呢？`
> `包含在 .NET Framework 中的每个 .NET Framework 数据提供程序都拥有自己的继承自 DbCommand 的命令对象。 适用于 OLE DB 的 .NET Framework 数据提供程序包括一个 OleDbCommand 对象，适用于 SQL Server 的 .NET Framework 数据提供程序包括一个 SqlCommand 对象，适用于 ODBC 的 .NET Framework 数据提供程序包括一个 OdbcCommand 对象，适用于 Oracle 的 .NET Framework 数据提供程序包括一个 OracleCommand 对象。 其中每个对象都根据命令的类型和所需的返回值公开用于执行命令的方法，如下表所述。` <br/>

* `对于Sql Server 数据库 使用 SqlCommand类就行了 他就可以执行sql语句`
  * `ExecuteReader`: 	`返回一个 DataReader 对象。`
  * `ExecuteScalar`: 	`返回一个标量值。`
  * `ExecuteNonQuery`: `执行不返回任何行的命令。`
  * `ExecuteXMLReader`: `返回 XmlReader。 只用于 SqlCommand 对象。`
 
* `每个强类型[SqlCommand]命令对象还支持指定如何解释命令字符串的 CommandType 枚举，如下表所述。`
     * `Text `:	`定义要在数据源处执行的语句的 SQL 命令。`
     * `StoredProcedure`:	`存储过程的名称。 您可以使用某一命令的 Parameters 属性访问输入和输出参数，并返回值（无论调用哪种 Execute 方法）。 当使用 ExecuteReader 时，在关闭 DataReader 后才能访问返回值和输出参数。`
     * `TableDirect 	表的名称。`
 
##### 创建 SqlCommand对象的两种方法
* `构造函数方法`
 ```c#
  SqlConnection connection = getConnectionByBuilder();
  SqlCommand cmd = new SqlCommand("select count(*) from students",connection);
 ```
* `通过 SqlConnection对象`
 ```c#
  SqlConnection connection = getConnectionByBuilder();
  SqlCommand command = connection.CreateCommand();
  command.CommandText = "select count(*) from students";
 ```
##### `得到执行结果的第一行第一列`
```c#
public Int32 ExecuteSqlReturnCount() {
    SqlConnection connection = getConnectionByBuilder();
    SqlCommand command = connection.CreateCommand();
    command.CommandText = "select count(*) from students";
    command.CommandType = CommandType.Text;
    Int32 obj = Convert.ToInt32(command.ExecuteScalar());
    Console.WriteLine(obj.ToString());

    //销毁链接
    command.Dispose();
    connection.Close();
    connection.Dispose();
    return obj;
}
```
##### [`4.Sql参数`](#top) :triangular_flag_on_post:  <b id="parameter"></b>
`通过链接字符串固然可以添加sql参数 ,但是那样子会被sql 注入攻击 所以我们使用 实现了扩展了 DbParameter的 对象 和 使用参数占位符`


##### 说明参数
`DbParameter 对象可以通过使用其构造函数来创建，或者也可以通过调用 DbParameterCollection 集合的 Add 方法以将该对象添加到 DbParameterCollection 来创建。 Add 方法将构造函数实参或现有形参对象用作输入，具体取决于数据提供程序。` [`配置参数`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/configuring-parameters-and-parameter-data-types)
* `ParameterDirection 属性`:`在使用存储过程的时候才用得着`

|`数据提供程序`|`参数命名语法`|
|:---|:-----|
|`Input 	`|`该参数为输入参数。 这是默认设置。`|
|`InputOutput `	|`该参数可执行输入和输出。`|
|`Output 	`|`该参数为输出参数。`|
|`ReturnValue 	`|`该参数表示从某操作（如存储过程、内置函数或用户定义的函数）返回的值。`|

##### 使用参数占位符

|`数据提供程序`|` 	参数命名语法`|
|:---|:-----|
|`System.Data.SqlClient`| `	以 @参数名格式使用命名参数。`|
|`System.Data.OleDb`| 	`使用由问号 (?) 指示的位置参数标记。`|
|`System.Data.Odbc`| 	`使用由问号 (?) 指示的位置参数标记。`|
|`System.Data.OracleClient`| `	以 :参数名 （或 参数名）格式使用命名参数。`|

* `利用 SqlCommand的Parameters[SqlParameterCollection 类型] 添加参数`
```node
public void ExecuteWithSqlParamter() {
    String val = @"select count(*) from students where studentAge = @Age and studentSex = @Sex";
    SqlConnection connection = getConnectionByBuilder();
    SqlCommand command = connection.CreateCommand();
    command.CommandText = val;

    command.Parameters.Add("Age", SqlDbType.Int);
    command.Parameters["Age"].Value = 20;
    command.Parameters.Add("Sex", SqlDbType.Bit);
    command.Parameters["Sex"].Value = true;
    command.CommandType = CommandType.Text; //说明类型

    Int32 obj = Convert.ToInt32(command.ExecuteScalar());
    Console.WriteLine(obj.ToString());

    command.Dispose();
    connection.Close();
    connection.Dispose();
}
```
**`设置参数的方法有两种`** <br/>
`先说明类型 再给与值`  	:two_hearts: <br/>
```c#
String val = @"select count(*) from students where studentAge = @Age and studentSex = @Sex";
command.Parameters.Add("Age", SqlDbType.Int);
command.Parameters["Age"].Value = 20;
command.Parameters.Add("Sex", SqlDbType.Bit);
command.Parameters["Sex"].Value = true;
```
`类型和值直接给`:`因为他会自动推断` 	:two_hearts:  <br/>
```node
String val = @"select count(*) from students where studentAge = @Age and studentSex = @Sex";
command.Parameters.AddWithValue("Age", 20);
command.Parameters.AddWithValue("Sex", true);
  ```
##### 一个参数的基本信息包括
```c#
SqlParameter parameter = new SqlParameter();
parameter.ParameterName = "@CategoryName"; //参数名称
parameter.SqlDbType = SqlDbType.NVarChar; //参数类型
parameter.Direction = ParameterDirection.Input; //参数的方向
parameter.Value = "Football"; //参数值

command.Parameters.Add(parameter);
```
  
##### [`5.读取Table`](#top) :triangular_flag_on_post:  <b id="reader"></b>
`执行这个方法 会返回一个SqlDataReader对象 SqlDataReader 使用变比后需要立即销毁 当然我们可以传入一些参数使得它自动被销毁,而不是手动销毁,因为我们总是有遗忘的时候！` ` SqlDataReader reader = command.ExecuteReader(CommandBehavior.CloseConnection);` `SqlConnection 关闭的时候 SqlDataReader也会自动关闭`
* `HasRows`:`判断是否具有数据`
* `Read()`:`向前读取一行,表示是否具有数据`
```c#
public void ExecuteReadTable()
{
    String val = @"select *  from students where studentSex = @Sex";
    SqlConnection connection = getConnectionByBuilder();
    SqlCommand command = connection.CreateCommand();
    command.CommandText = val;
    command.Parameters.AddWithValue("Sex", true);
    command.CommandType = CommandType.Text;

    SqlDataReader reader = command.ExecuteReader(CommandBehavior.CloseConnection);

    if(reader.HasRows) {
        //一行一行的
        while (reader.Read()) {
            int Id = Convert.ToInt32(reader["studentId"]);
            String Name = Convert.ToString(reader["studentName"]);
            int Age = Convert.ToInt32(reader["studentAge"]);
            Boolean Sex = Convert.ToBoolean(reader["studentSex"]);

            Console.WriteLine($"{Id} {Name} {Age} {Sex}");
        }
    }else{
        Console.WriteLine("并没有发现任何数据");
    }
    command.Dispose();
    connection.Close();
    connection.Dispose();
}
```

##### [`6.存放到数据集`](#top) :triangular_flag_on_post:  <b id="adapter"></b>
`SqlDataReader 通过Read 读取的数据是不持久的,并且只能向前读取数据 所以我们要保存读取的数据,怎么办呢？ 我们使用 DataAdapter  填充数据`
`本质上 SqlDataAdapter 里面存储的是一个 DataTable 对象`
* `SqlDataAdapter的 参数配置 ` [`官网`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/dataadapter-parameters)

##### 数据存储体系

![data](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/media/ado-1-bpuedev11.png)

##### DataAdapter
```c#
SqlConnection connection = getConnectionByBuilder();
string queryString = "select *  from students";  
SqlDataAdapter adapter = new SqlDataAdapter(queryString, connection);  
DataSet students = new DataSet();  
adapter.Fill(students, "myStudents");  

```
`DataSet 里面就多了一张表 表名为 myStudents SqlDataAdapter 里面存储的是一个 DataTable 对象`

```c#
public DataTable CarryOutSqlGeDataTable(string SentenceSql)
{
    if (SentenceSql == "")
    {
      return null;
    }
   SqlConnection con = GetSqlConnection();
   if (con.State != ConnectionState.Open)
   {
      con.Open();
   }
   SqlCommand cmd = con.CreateCommand();
   cmd.CommandType = CommandType.Text;
   cmd.CommandText = SentenceSql;
   SqlDataAdapter adapter= new SqlDataAdapter(cmd);
   DataTable td =new DataTable();
   adapter.Fill(td);
   return td;
} 
```
##### 如何读取 DataTable里面的数据呢？
`很简单的 下面一个实例 可供参考`
```c#
  DataTable td = getTable.GetAllDataFromtblProductTypeInfo();
  if (td.Rows.Count > 0)
  {
     foreach (DataRow row in td.Rows)
     {
        ListItem itemSItem = new ListItem();
        itemSItem.Value = row["typeID"].ToString();
        itemSItem.Text = row["typeName_c"].ToString();
        ddlShoosProductTypes.Items.Add(itemSItem);
     }
  }
```

##### [`7.执行存储过程`](#top) :triangular_flag_on_post:  <b id="store"></b>
`我来尝试执行一个存储过程 通过 ADO.NET`

`新建一个存储过程`
```sql
create table lessons(
	lessonId int primary key identity(1,1),
	lessonName varchar(100) not null,
	lessonStatus int 
);

go;

create procedure [dbo].[InsertNewItemToLessons]
(
 @lessonName varchar(100),
 @lessonStatus int,
 n@val int output
)
as
begin
  declare @count int
  select @count = count(*) from lessons where  lessonName = @lessonName
  if @count = 0
  begin
  insert into lessons(lessonName,lessonStatus) values(@lessonName,@lessonStatus); 
     select @val = lessonId from lessons where  lessonName = @lessonName
  end;
  else
  begin
  set @val = -1; -- 插入失败失败
  end
end;

```
`试着执行一下`
```sql
go;

declare @out_val int;

execute [dbo].[InsertNewItemToLessons] '大学数学',30, @out_val output;

select @out_val
```

##### C# 程序
```c#
public void ExecuteProduceStore()
{
    String val = "[dbo].[InsertNewItemToLessons]"; //存储过程
    SqlConnection connection = getConnectionByBuilder();
    SqlCommand command = connection.CreateCommand();
    command.CommandText = val;
    command.CommandType = CommandType.StoredProcedure; //说明类型 是存储过程

    SqlParameter name = new SqlParameter("@lessonName", SqlDbType.VarChar, 100);
    name.Value = "数据结构";
    name.Direction = ParameterDirection.Input;

    SqlParameter age = new SqlParameter("@lessonStatus", SqlDbType.Int);
    age.Value = 30;
    age.Direction = ParameterDirection.Input;

    SqlParameter lessonId = new SqlParameter("@val",SqlDbType.Int);
    lessonId.Direction = ParameterDirection.Output;

    command.Parameters.Add(name);
    command.Parameters.Add(age);
    command.Parameters.Add(lessonId);

    command.ExecuteNonQuery();//执行它
    Console.WriteLine(lessonId.Value);// 返回新插入行的 id

    command.Dispose();
    connection.Close();
    connection.Dispose();
}
```

##### [`8.DataAdapter提供的分页功能`](#top) :triangular_flag_on_post:  <b id="page"></b>
[`看官网吧`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/paging-through-a-query-result)
```c#
int currentIndex = 0;  
int pageSize = 5;  
  
string orderSQL = "SELECT * FROM Orders ORDER BY OrderID";  
 
SqlDataAdapter adapter = new SqlDataAdapter(orderSQL, connection);  
  
DataSet dataSet = new DataSet();  
adapter.Fill(dataSet, currentIndex, pageSize, "Orders");  
```
