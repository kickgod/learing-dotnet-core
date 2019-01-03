### [Net Core 流操作---文件流 ❕](#top) :grey_exclamation: <b id="top"></b>
`文件流又分为二进制文件和文本文件` [`文件和流`](https://docs.microsoft.com/zh-cn/dotnet/standard/io/) :white_check_mark:
:white_check_mark:

------

- [x] [`1.使用文件流`](#target1)


------

#####  :octocat: [1.使用文件流](#top) <b id="target1"></b> 
`概括`：`要使用FileStream 需要四个信息`
* :one: `要访问的文件`---`文件路径就行 String`
* :two: `打开文件的模式,新建一个文件？ 打开一个现有的文件? 是覆盖原来内容还是追加内容`----`枚举 FileMode` 
* :three: `访问文件的形式,只读还是只写 还是读写`----`枚举 FileAccess`
* :four: `是否共享访问,允许其他流同时访问文件,那么这些流式只读还会只写？`------`枚举 FileShare`


##### 1.FileMode

|`打开方式` | `值`|`描述`|
|---|:----|----|
|`CreateNew` |1 |	`创建新文件。 这需要 Write 权限。 文件已存在，将引发 IOException异常。` |
|`Create`    |2 |`创建新文件。 如果文件已存在，它将被覆盖。 这需要 Write 权限。如果文件不存在，则使用 CreateNew；否则使用 Truncate。 如果该文件已存在但为隐藏文件，则将引发 UnauthorizedAccessException异常。`| 
|`Open`|3 |`指定操作系统应打开现有文件。 打开文件的能力取决于 FileAccess 枚举所指定的值。 如果文件不存在，引发一个 FileNotFoundException 异常。`|
|`OpenOrCreate`|4 |`指定操作系统应打开文件（如果文件存在）；否则，应创建新文件。 如果用 FileAccess.Read 打开文件，则需要 Read权限。 如果文件访问为 FileAccess.Write，则需要 Write权限。 如果用 FileAccess.ReadWrite 打开文件，则同时需要 Read 和 Write权限。`|
|`Truncate`|5 |`指定操作系统应打开现有文件。 该文件被打开时，将被截断为零字节大小。 这需要 Write 权限。 尝试从使用 FileMode.Truncate 打开的文件中进行读取将导致 ArgumentException 异常。`|
|`Append`|6 |`若存在文件，则打开该文件并查找到文件尾，或者创建一个新文件。 这需要 Append 权限。 FileMode.Append 只能与 FileAccess.Write 一起使用。 试图查找文件尾之前的位置时会引发 IOException 异常，并且任何试图读取的操作都会失败并引发 NotSupportedException 异常。`|

##### 2.FileShare
|`打开方式` |`描述`|
|:------|:----|
|`Delete`|`允许随后删除文件`|
|`Read`|`允许随后打开文件读取`|
|`ReadWrite`|`允许随后打开文件读取或写入`|
|`Write`|`允许随后打开文件写入`|
|`Inheritable `|`使文件句柄可由子进程继承。 Win32 不直接支持此功能`|
|`None`|`谢绝共享当前文件。 文件关闭前，打开该文件的任何请求`|


##### 3.FileAccess

|`打开方式` |`描述`|
|:------|:----|
|`Read `|`对文件的读访问。 可从文件中读取数据。 与 Write 组合以进行读写访问`|
|`ReadWrite `|`对文件的读写访问权限。 可从文件读取数据和将数据写入文件`|
|`Write `|`文件的写访问。 可将数据写入文件。 与 Read 组合以进行读写访问`|


##### 创建一个 [API: FileStream](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.filestream?view=netframework-4.7.2)
`FileStream 有12个构造函数,也就是有十二种构造方法....`

```c#
public FileStream(string path, FileMode mode, FileAccess access, FileShare share);

public void UseDmo() {
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), 
    FileMode.Open, FileAccess.Read, FileShare.None)) {
      Console.WriteLine($"是否可读: {stream.CanRead}");
      Console.WriteLine(stream.CanWrite);

    }
}
```










--------------------
`作者:` `模板` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `禁止转载` 
