### [ADO.NET 数据库连接技术](#top) <b id="top"></b> :star:

-----
`在.NET Core 2.0时代已经将.NET Framework的ADO.NET整套体系完整覆盖 做了许多的更改,并且在NET Core中不再自动基础 数据库连接实现命名空间` 
[`官网:ADO.NET 4.7.2`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/)

- [x] [`1.准备工作`](#into) 
- [x] [`2.立即链接`](#connect) 
- [x] [`3.执行命令`](#command) 
- [x] [`4.Sql参数`](#parameter)

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
* `SqlConnection 具有两个事件`
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
            builder.IntegratedSecurity = true; //这个意思是 使用windows 方式验证 不要用

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
`通过链接字符串固然可以添加sql参数 ,但是那样子会被sql 注入攻击 所以我们使用 SqlParameter对象 和 @符号`
* `利用 SqlCommand的Parameters[SqlParameterCollection 类型] 添加参数`
```c#
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
* `设置参数的方法有两种`
  * `先说明类型 再给与值`
  
  ```c#
     String val = @"select count(*) from students where studentAge = @Age and studentSex = @Sex";
     command.Parameters.Add("Age", SqlDbType.Int);
     command.Parameters["Age"].Value = 20;
     command.Parameters.Add("Sex", SqlDbType.Bit);
     command.Parameters["Sex"].Value = true;
  ```
  
  * `类型和值直接给`:`因为他会自动推断`
  
  ```java
    String val = @"select count(*) from students where studentAge = @Age and studentSex = @Sex";
    command.Parameters.AddWithValue("Age", 20);
    command.Parameters.AddWithValue("Sex", true);
  ```
