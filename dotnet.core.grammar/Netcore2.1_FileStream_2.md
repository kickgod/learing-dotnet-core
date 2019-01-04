### [Net Core 流操作---文件流 ❕](#top) :grey_exclamation: <b id="top"></b>
`文件流又分为二进制文件和文本文件` [`文件和流`](https://docs.microsoft.com/zh-cn/dotnet/standard/io/) :white_check_mark:
:white_check_mark:

------

- [x] [`1.使用文件流`](#target1)
- [x] [`2.FileStream`](#filestream)

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


#####  :octocat: [2.FileStream](#top) <b id="filestream"></b> 
`非常常用的文件操作流` [`官方API说明--!`](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.filestream?view=netframework-4.7.2)
##### 1.FileStream 流属性
* `CanRead`:`获取一个值，该值指示当前流是否支持读取。`
* `CanSeek`:`获取一个值，该值指示当前流是否支持查找。`
* `CanTimeout`:`获取一个值，该值确定当前流是否可以超时。`
* `CanWrite`:`获取一个值，该值指示当前流是否支持写入。`
* `Handle`:`获取当前 FileStream 对象所封装文件的操作系统文件句柄。`
* `IsAsync`:`获取一个值，它指示 FileStream 是异步打开还是同步打开的。`
* `Length`:`获取用字节表示的流长度。`
* `Name`:`获取 FileStream 中已打开的文件的绝对路径。`
* `Position`:`获取或设置此流的当前位置。`
* `ReadTimeout`:`获取或设置一个值（以毫秒为单位），该值确定流在超时前尝试读取多长时间。`
* `SafeFileHandle`:`获取 SafeFileHandle 对象，它代表当前 FileStream 对象所封装的文件的操作系统文件句柄。`
* `WriteTimeout`:`获取或设置一个值（以毫秒为单位），该值确定流在超时前尝试写入多长时间。`

```c#
public void UseDmo() {
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), FileMode.Open, 
    FileAccess.ReadWrite, FileShare.None)) {
        Console.WriteLine($"是否可读: {stream.CanRead}");
        Console.WriteLine($"是否可写: {stream.CanWrite}");
        Console.WriteLine($"是否执行随机: {stream.CanSeek}");
        Console.WriteLine($"是否支持超时: {stream.CanTimeout}");
        Console.WriteLine($"Stream长度: {stream.Length}");
        Console.WriteLine($"游标位置: {stream.Position}");
    }
}
```
##### 2.分析文本文件的编码
`对于文本文件,下一步就是读取流中第一个字节----序言.序言提供了文件如何编码的信息(使用的文本格式),这也称为顺序标记`
* `读取一个流的时候,利用ReadByte可以从流中读取一个字节`
* `使用Read方法可以填充一个字节数组.第二个参数是字节数组中的偏移量 第三个参数是可用于填充的字节数 返回读取的字节数`
* `GetEncoding 方法创建了一个包含五个字节的数组`

##### 3.编码格式
* `ASCII`:`使用八位存储一个字节 包含小写大写字母,控制字符 每一个字符 七位`
* `Unicode 编码`:`有小或大端顺序法存储,FF紧随FE之后,表示小端字节序,而FE后跟FF,就表示使用大端字节序`
* `UTF-16`:`16位两个字节表示一个字符,但是数量还是不够表示所有的字符`
* `UTF-32`:`32位 四个字节表示一个字符`
* `UTF-8`:`采取可编程的字符定义,一个字符定义使用为1~6 个字符 字符串序列为 ：0xEF 0xBB 0xBF`

`位置方法`:
* `public override long Seek (long offset, System.IO.SeekOrigin origin);`
    * `offset 相对于 origin 的点，从此处开始查找。`
    * `origin 使用 SeekOrigin 类型的值，将开始位置、结束位置或当前位置指定为 offset 的参考点。`
`返回流的编码格式`
```c#
public System.Text.Encoding GetEncoding(Stream stream) {
    if (!stream.CanSeek) {
        throw new ArgumentException("错误:流不能随机读取");
    }
    Encoding encoding = Encoding.ASCII;

    byte[] bom = new byte[5];
    int nRead = stream.Read(bom, 0, 5);//从0 开始读取五个字节

    if (bom[0] == 0xff && bom[1] == 0xfe && bom[2] == 0 && bom[3] == 0)
    {
        stream.Seek(4, SeekOrigin.Begin);
        return Encoding.UTF32;
    }
    else if (bom[0] == 0xff && bom[1] == 0xfe)
    {
        stream.Seek(2, SeekOrigin.Begin);
        return Encoding.Unicode;
    }
    else if (bom[0] == 0xfe && bom[1] == 0xff)
    {
        stream.Seek(2, SeekOrigin.Begin);
        return Encoding.BigEndianUnicode;
    }
    else if (bom[0] == 0xef && bom[1] == 0xbb && bom[2] == 0xbf) {
        stream.Seek(3, SeekOrigin.Begin);
        return Encoding.UTF8;
    }
    stream.Seek(0, SeekOrigin.Begin);
    return encoding;
}

```

##### 4.读取流
`打开文件创建流之后,使用Read方法读取文件,重复此过程直到读取到0为止` `注意`: `1.要记得Dispose方法关闭流,或者 using自动关闭`
* `Read(Byte[], Int32, Int32)`:	`从流中读取字节块并将该数据写入给定缓冲区中。`
* `Read(Span<Byte>)`:`Inherited from Stream`
* `ReadAsync(Byte[], Int32, Int32) `:`从当前流异步读取字节序列，并将流中的位置提升读取的字节数。(Inherited from Stream)`
* `ReadAsync(Byte[], Int32, Int32, CancellationToken)`

从当前流异步读取字节的序列
```c#
public void UseDmo() {
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), FileMode.Open,
    FileAccess.ReadWrite, FileShare.None)) {
        Byte[] buffer = new byte[256];

        Encoding encoder = GetEncoding(stream); //什么编码格式

        Console.WriteLine(encoder.BodyName);

        while (true) {
            int readByteCount = stream.Read(buffer, 0, buffer.Length);
            if (readByteCount == 0)
            {
                break;
            } else if (readByteCount < buffer.Length)
            {
                Array.Clear(buffer, readByteCount, buffer.Length - readByteCount);
            }

            String result = encoder.GetString(buffer, 0, readByteCount);

            Console.WriteLine($"Read Count:{readByteCount} ---Text:{result}");
        }
    }
}
```
##### 5.写入流
* `public override void Write (byte[] array, int offset, int count);`
    * `array :包含要写入该流的数据的缓冲区。` 
    * `array 中的从零开始的字节偏移量，从此处开始将字节复制到该流。`
    * `count 最多写入的字节数。`
* `将字节的序列异步写入当前流，将该流中的当前位置提升写入的字节数，并监视取消请求。` [`API 说明`](https://docs.microsoft.com/zh-cn/dotnet/api/system.io.filestream.writeasync?view=netframework-4.7.2)
```c#
[System.Runtime.InteropServices.ComVisible(false)]
public override System.Threading.Tasks.Task WriteAsync 
(byte[] buffer,
int offset, 
int count, 
System.Threading.CancellationToken cancellationToken);
```
* `public override void WriteByte (byte value)`:`一个字节写入文件流中的当前位置。`
`可以借助File类打开一个写入流`
```c#
FileStream stream_ = File.OpenWrite(Path.GetFullPath(@"Resources/TextFile/User.txt"));
```
`正常打开`:`记住正常打开的流要记得关闭,并且流写入的字节要使用编码器 转换字符得到字符的编码`
```c#
public void WriteStream()
{
    //FileStream stream_ = File.OpenWrite(Path.GetFullPath(@"Resources/TextFile/User.txt"));

    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), FileMode.Append, FileAccess.Write, FileShare.None))
    {
        byte[] data = Encoding.UTF8.GetBytes("我是你们的爸爸啊，儿子！");
        stream.Write(data,0,data.Length);
    }
}
```

##### 6.复制流
* `CopyTo(Stream) `:`从当前流中读取字节并将其写入到另一流中。(Inherited from Stream) `
* `CopyTo(Stream, Int32)`:`使用指定的缓冲区大小，从当前流中读取字节并将其写入到另一流中。(Inherited from Stream) `
* `CopyToAsync(Stream)`:`从当前流中异步读取字节并将其写入到另一个流中。(Inherited from Stream) `
* `CopyToAsync(Stream, Int32, CancellationToken)`:`使用指定的缓冲区大小和取消令牌，从当前流中异步读取字节并将其写入到另一个流中。(Inherited from Stream) `
* `Flush()`:`清除此流的缓冲区，使得所有缓冲数据都写入到文件中。`
* `Flush(Boolean)`:`清除此流的缓冲区，将所有缓冲数据都写入到文件中，并且也清除所有中间文件缓冲区。`
* `FlushAsync()`:`异步清除此流的所有缓冲区并导致所有缓冲数据都写入基础设备中。(Inherited from Stream) `


```c#
public void CopyStream() {
    using (var stream = new FileStream(Path.GetFullPath(@"Resources/TextFile/User.txt"), 
    FileMode.Open, FileAccess.Read, FileShare.None))
    {
        FileStream fileStream = File.OpenWrite(@"Resources/TextFile/CopyUser.txt");
        stream.CopyTo(fileStream);
        fileStream.Flush();
        Console.WriteLine("Copy成功");
    }
}
```



--------------------
`作者:` `模板` 
`完成时间`:`2018年12月31日18:33:38`
`备注信息`: `禁止转载` 
