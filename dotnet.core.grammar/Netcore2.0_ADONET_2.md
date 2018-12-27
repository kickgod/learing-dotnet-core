### [ADO.NET 数据库连接高级技术](#top) <b id="top"></b> :star:

-----
`ADO.NET 提供数据访问的异步,和 事务处理 这都是很重要的 并且NET CORE2.0版本之后对 ADO.NET 也有了很多新的的改动需要我们知道` 
[`官网:ADO.NET 4.7.2`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/retrieving-and-modifying-data)

- [x] [`1.ADO.NET 事务处理`](#transaction) 


-----
##### [`1.ADO.NET 事务处理`](#top) :triangular_flag_on_post:  <b id="transaction"></b>
> `每个 .NET Framework 数据提供程序使用自己的 Transaction 对象来执行本地事务。 如果要求事务在 SQL Server 数据库中执行，则
选择 System.Data.SqlClient 事务。 对于 Oracle 事务，使用 System.Data.OracleClient 提供程序。 此外，还有DbTransaction可
用于写入需要事务的独立于提供程序的代码的类。`

##### 一个例子 
* `ADO.NET 事务要借助 `：`SqlTransaction 类型`
  * `public override void Commit()`:`提交是我`
  * `public override void Rollback();`:`回滚事务`
  * `public void Rollback(string transactionName);`:`回滚到某一个保存点`
  * `public void Save(string savePointName);`:`设置保存点`
```node
public void Transeaction()
{
    String val =
    @"insert into lessons(lessonId,lessonName,lessonStatus) values(@lessonId,@lessonName,@lessonStatus)";
    
    SqlConnection connection = getConnectionByBuilder();
    SqlCommand command = connection.CreateCommand();
    command.CommandText = val;

    command.Transaction = connection.BeginTransaction();//开始事务

    try
    {
        command.Parameters.AddWithValue("lessonId", 1); 
        command.Parameters.AddWithValue("lessonName", "大学物理");
        command.Parameters.AddWithValue("lessonStatus", 30);
        command.CommandType = CommandType.Text; //说明类型

        Int32 obj = Convert.ToInt32(command.ExecuteScalar());
        Console.WriteLine(obj.ToString());
        command.Transaction.Commit();//成功那么提交数据
    }
    catch (Exception ex)
    {
        command.Transaction.Rollback();//失败 那么回滚事务
    }
    finally {
        command.Dispose();
        connection.Close();
        connection.Dispose();
    }
}
```
