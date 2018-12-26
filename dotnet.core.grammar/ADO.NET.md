### [ADO.NET 数据库连接技术](#top) <b id="top"></b> :star:

-----
`在.NET Core 2.0时代已经将.NET Framework的ADO.NET整套体系完整覆盖 做了许多的更改,并且在NET Core中不再自动基础 数据库连接实现命名空间` 
[`官网:ADO.NET 4.7.2`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/)

- [x] [`1.准备工作`](#into) 

-----
##### [`1.准备工作`](#top) :triangular_flag_on_post:  <b id="into"></b>
`首先我们需要官方为我们提供的API 文件`
* `链接Sql Server`
  * `首先 你需要下载 System.Data.SqlClient Nuget 包`
  * `Nuget 命令 [安装最新版]`:`dotnet add package System.Data.SqlClient` 
* `链接 Mysql`
  * `你需要下载 Mysql.Data`
  * `Nuget 命令 [安装最新版]`:`dotnet add package Mysql.Data` 
  
`试一试链接`  
```C#
 using System;
using System.Data;
using System.Data.SqlClient;
namespace libCsharp.DataBase
{
    public class ServiceBase
    {
        private readonly String  ConnectionString = @"Data Source=47.98.215.212;Initial Catalog=demo;Pooling =true;User ID=kicker;Password=123456";

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
