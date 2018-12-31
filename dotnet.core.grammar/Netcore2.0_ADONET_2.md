### [ADO.NET 数据库连接高级技术](#top) <b id="top"></b> :star:

-----
`ADO.NET 提供数据访问的异步,和 事务处理 这都是很重要的 并且NET CORE2.0版本之后对 ADO.NET 也有了很多新的的改动需要我们知道` 
[`官网:ADO.NET 4.7.2`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/retrieving-and-modifying-data)

> - [x] [`1.ADO.NET 事务处理`](#transaction) 
> - [x] [`2.ADO.NET DataAdapter 深入`](#adapter) 
> - [x] [`3.ADO.NET DataAdapter 事件`](#event) 
> - [x] [`4.检索标识或自动编号值`](#identity) 
> - [x] [`5.异步编程`](#async) 

-----
##### [`1.ADO.NET 事务处理`](#top) :triangular_flag_on_post:  <b id="transaction"></b>
> `每个 .NET Framework 数据提供程序使用自己的 Transaction 对象来执行本地事务。 如果要求事务在 SQL Server 数据库中执行，则
选择 System.Data.SqlClient 事务。 对于 Oracle 事务，使用 System.Data.OracleClient 提供程序。 此外，还有DbTransaction可
用于写入需要事务的独立于提供程序的代码的类。`

##### 一个例子 
`ADO.NET 事务要借助 `：`SqlTransaction 类型`
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
##### [`2.ADO.NET DataAdapter 深入`](#top) :triangular_flag_on_post:  <b id="adapter"></b>
`ADO.NET DataSet 是数据的内存驻留表示形式，它提供了独立于数据源的一致关系编程模型。 DataSet 表示整个数据集，其中包含表、约束和表之间的关系。 由于 DataSet 独立于数据源，因此 DataSet 可以包含应用程序本地的数据，也可以包含来自多个数据源的数据。 与现有数据源的交互通过 DataAdapter来控制。`

```c#
private static void AdapterUpdate(string connectionString)
{
    using (SqlConnection connection =
               new SqlConnection(connectionString))
    {
        SqlDataAdapter dataAdpater = new SqlDataAdapter(
          "SELECT CategoryID, CategoryName FROM Categories",
          connection);

        dataAdpater.UpdateCommand = new SqlCommand(
           "UPDATE Categories SET CategoryName = @CategoryName " +
           "WHERE CategoryID = @CategoryID", connection);

        dataAdpater.UpdateCommand.Parameters.Add(
           "@CategoryName", SqlDbType.NVarChar, 15, "CategoryName");

        SqlParameter parameter = dataAdpater.UpdateCommand.Parameters.Add(
          "@CategoryID", SqlDbType.Int);
        parameter.SourceColumn = "CategoryID";
        parameter.SourceVersion = DataRowVersion.Original;

        DataTable categoryTable = new DataTable();
        dataAdpater.Fill(categoryTable);

        DataRow categoryRow = categoryTable.Rows[0];
        categoryRow["CategoryName"] = "New Beverages";

        dataAdpater.Update(categoryTable);

        Console.WriteLine("Rows after update.");
        foreach (DataRow row in categoryTable.Rows)
        {
            {
                Console.WriteLine("{0}: {1}", row[0], row[1]);
            }
        }
    }
}
```

##### DataAdapter的命令 属性 和方法
`1.SelectCommand`:`Command 对象` <br/>
`2.DeleteCommand`:`Command 对象` <br/>
`3.InsertCommand`:`Command 对象` <br/>
`4.UpdateCommand`:`Command 对象` <br/>
`5.Fill(dataset,"tablename")`:`Fill 将要填充的 DataTable 放到和 DataSet 对象中` <br/>

```c#
string queryString = "SELECT CustomerID, CompanyName FROM dbo.Customers";  

SqlDataAdapter adapter = new SqlDataAdapter(queryString, connection);    
DataSet customers = new DataSet();  
adapter.Fill(customers, "Customers");  
```
`此示例中所示的代码不显式打开和关闭 Connection。 如果 Fill 方法发现连接尚未打开，它将隐式地打开 Connection 正在使用的 DataAdapter 。
如果 Fill 已打开连接，则它还将在 Fill 完成时关闭连接。`

##### 参数
```javascript
adapter.InsertCommand = 
    new SqlCommand("INSERT INTO Customers (CustomerID, CompanyName) VALUES (@CustomerID, @CompanyName)",
    connection);
adapter.InsertCommand.Parameters.Add("@CustomerID", SqlDbType.Char, 5, "CustomerID");
adapter.InsertCommand.Parameters.Add("@CompanyName",SqlDbType.VarChar, 40, "CompanyName");
adapter.UpdateCommand.Parameters.Add("@CustomerID", SqlDbType.Char, 5, "CustomerID");
```
##### [`3.ADO.NET DataAdapter 事件`](#top) :triangular_flag_on_post:  <b id="event"></b>
[`官方事件说明`](https://docs.microsoft.com/zh-cn/dotnet/framework/data/adonet/handling-dataadapter-events)<br/>
`RowUpdating`: `事件对象 RowUpdatingEventArgs `,`将要开始对某行执行 UPDATE、INSERT 或 DELETE 操作（通过调用 Update 方法之一）。` <br/>
`RowUpdated`: `事件对象 RowUpdatedEventArgs `,`对某行的 UPDATE、INSERT 或 DELETE 操作（通过调用 Update 方法之一）已完成。`<br/>
`FillError`: `事件对象 FillErrorEventArgs`	` 执行 Fill 操作期间出错。`<br/>
* `Errors 	已发生的 Exception。`
* `DataTable 	出错时所填充的 DataTable 对象。`
* `Values 	一个对象数组，它包含出错时所添加的行的值。 Values 数组的序号引用与所添加的行的列的序号引用相对应。 例如，Values[0] 是作为当前行的第一列添加的值。`
* `Continue`:`用于选择是否引发异常。 如果将 Continue 属性设置为 false，则会暂停当前 Fill 操作并将会引发异常。 如果将 Continue 设置为 true，那么即使出错，仍将继续执行 Fill 操作。`
* `RowUpdatingEventArgs` `RowUpdatedEventArgs 包含的属性`
   * `Command 属性，它引用用来执行更新的 Command 对象；`
   * `Row 属性，它引用包含更新信息的 DataRow 对象；`
   * `StatementType 属性，它指示所执行的更新类型；`
   * `Status 操作的状态 `
   
|`Status 枚举值`|`说明`|
|:----|:---|
|`Continue` |	`继续执行更新操作。`|
|`ErrorsOccurred`| 	`中止更新操作并引发异常。`|
|`SkipCurrentRow` 	|`忽略当前行并继续执行更新操作。`|
|`SkipAllRemainingRows `	|`中止更新操作但不引发异常。`|

##### [`4.检索标识或自动编号值`](#top) :triangular_flag_on_post:  <b id="identity"></b>
`使用 Microsoft SQL Server 时，可以创建通过输出参数返回插入行标识值的存储过程。 下表说明 SQL Server 中可用来检索标识列值的三个 Transact-SQL 函数。` `你调用存储过程啊...使用 output参数`

|`函数` |	`描述`|
|:----|:---|
|`SCOPE_IDENTITY`| 	`返回当前执行范围内的最后一个标识值。 对于多数方案，建议使用 SCOPE_IDENTITY。`|
|`@@IDENTITY `	|`包含当前会话中任何表生成的最后一个标识值。 @@IDENTITY触发器可能会影响和可能不会返回预期的标识值。`|
|`IDENT_CURRENT`| 	`返回在任何会话中以及任何范围内为特定表生成的最后一个标识值。`|

```sql
CREATE PROCEDURE dbo.InsertCategory
  @CategoryName nvarchar(15),
  @Identity int OUT
AS
INSERT INTO Categories (CategoryName) VALUES(@CategoryName)
SET @Identity = SCOPE_IDENTITY()
```

##### [`5.异步编程`](#top) :triangular_flag_on_post:  <b id="async"></b>
`在 .NET Framework 4.5 之前，使用 SqlClient 进行的异步编程是通过以下方法和 Asynchronous Processing=true 连接属性完成的：`
**`从开始.NET Framework 4.5，这些旧方法不再需要Asynchronous Processing=true连接字符串中。`**
* `SqlCommand.BeginExecuteNonQuery`
* `SqlCommand.BeginExecuteReader`
* `SqlCommand.BeginExecuteXmlReader` <br/>
```xml
.NET Framework 4.5 中的 SqlClient 中仍保留了此功能。
```
##### 异步链接
```c#
using System;  
using System.Data.SqlClient;  
using System.Threading.Tasks;  
  
class A {  
  
   static async Task<int> Method(SqlConnection conn, SqlCommand cmd) {  
      await conn.OpenAsync();  
      await cmd.ExecuteNonQueryAsync();  
      return 1;  
   }  
  
   public static void Main() {  
      using (SqlConnection conn = 
      new SqlConnection("Data Source=(local); Initial Catalog=NorthWind; Integrated Security=SSPI")){   
         SqlCommand command = new SqlCommand("select top 2 * from orders", conn);  
  
         int result = A.Method(conn, command).Result;  
  
         SqlDataReader reader = command.ExecuteReader();  
         while (reader.Read())  
            Console.WriteLine(reader[0]);
      }  
   }  
}  
```

##### 执行命令
```c#
using System;  
using System.Data.Common;  
using System.Data.SqlClient;  
using System.Threading.Tasks;  
  
class A {  
   static async Task PerformDBOperationsUsingProviderModel
   (string connectionString, string providerName) {  
      DbProviderFactory factory = DbProviderFactories.GetFactory(providerName);  
      using (DbConnection connection = factory.CreateConnection()) {  
         connection.ConnectionString = connectionString;  
         await connection.OpenAsync();  
  
         DbCommand command = connection.CreateCommand();  
         command.CommandText = "SELECT * FROM AUTHORS";  
  
         using (DbDataReader reader = await command.ExecuteReaderAsync()) {  
            while (await reader.ReadAsync()) {  
               for (int i = 0; i < reader.FieldCount; i++) {  
                  // Process each column as appropriate  
                  object obj = await reader.GetFieldValueAsync<object>(i);  
                  Console.WriteLine(obj);  
               }  
            }  
         }  
      }  
   }  
  
   public static void Main()   
   {  
       SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();  
       // replace these with your own values  
       builder.DataSource = "your_server";  
       builder.InitialCatalog = "pubs";  
       builder.IntegratedSecurity = true;  
       string provider = "System.Data.SqlClient";  
  
       Task task = PerformDBOperationsUsingProviderModel(builder.ConnectionString, provider);  
       task.Wait();  
   }  
}  
```
